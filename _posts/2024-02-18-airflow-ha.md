---
title: Airflow HA Guide
author: longpt 
date: 2024-02-18 20:52:00 +0700
categories: [Blogging]
tags: [HA, Airflow]
math: true
mermaid: true
image:
  src: https://longpt233.github.io/images/2024-02-22%2021-44-01.png
---

In this article, I will guide installing Airflow and setting up HA for it. The environment used is VirtualBox virtual machines.

<details markdown="1">
<summary>Create 2 virtual machines with fixed addresses, add user and grant SSH permissions from host</summary>

```
VAGRANT_COMMAND = ARGV[0]

Vagrant.configure("2") do |config|

    if VAGRANT_COMMAND == "ssh"
      config.ssh.username = 'vagrant'
    end
    config.vm.box = "ubuntu/bionic64" # Choose the box you want to use

    # Initialize the first virtual machine
    config.vm.define "machine1" do |machine1|
    machine1.vm.network "private_network", ip: "192.168.56.2"
    machine1.vm.provider "virtualbox" do |vb|
          vb.memory = "2048" # 2GB RAM
          vb.cpus = 1       # 1 core CPU
        end

    machine1.vm.provision "shell", inline: <<-SHELL
          adduser airflow
          sudo su - airflow -c $'\
          whoami && \
          mkdir .ssh && \
          echo "ssh-rsa xxxx" > .ssh/authorized_keys && \
          chmod 700 .ssh && \
          chmod 600 .ssh/authorized_keys && \
          file_path=".ssh/authorized_keys" && \
          echo "cat file $file_path after make change" && \
          cat $file_path '
        SHELL
    end
end
```

</details>


# Concepts

- Allows building task flows
- Provides convenient UI for monitoring and centralized task management
- Limitations: not used to transfer large data between tasks. Not used with infinite tasks (like streaming)

# Components of Airflow

scheduler

executor -> worker

<details markdown="1">
<summary>executor</summary>

Types of executors

- local: local executor, sequential executor
- remote: celery executor, kubernetes executor

Should use celery executor because it can scale the number of workers through celery backend (rabbitMQ, redis). Executor is like a way for tasks to be assigned from scheduler to worker (standing between the scheduling place and the task execution place)

![](https://longpt233.github.io/images/2024-02-22%2021-44-01.png)

</details>

Additionally: metadata db, dag dir, web server


# Installing Airflow

<details markdown="1">
<summary>Install Airflow from pip</summary>

```
sudo apt update
sudo apt install python3-pip -y

export AIRFLOW_HOME=~/airflow

AIRFLOW_VERSION=2.5.1
PYTHON_VERSION="$(python3 --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"

echo $CONSTRAINT_URL

python3 -m pip install --upgrade pip (when setup tools error)
pip3 install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"

or simpler (but many errors :v)
python3.8 -m pip install --upgrade pip
python3.8 -m pip install apache-airflow==2.5.1

```

</details>

<details markdown="1">
<summary>Initialize database</summary>

```

python3 -m airflow db init

# error ModuleNotFoundError: No module named 'apt_pkg'
sudo apt-get install python-apt

# at this point the AIRFLOW_HOME directory is created
# default sql_alchemy_conn = sqlite:////home/airflow/airflow2/airflow.db

# change db to mysql then run db init again
CREATE DATABASE airflow_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'airflow_user' IDENTIFIED BY 'airflow_pass';
GRANT ALL PRIVILEGES ON airflow_db.* TO 'airflow_user';
FLUSH PRIVILEGES;

vi airflow/airflow.cfg
mysql+mysqldb://airflow_db:airflow_pass@192.168.56.1:3306/airflow_db  # some error
mysql+mysqlconnector://airflow_user:airflow_pass@192.168.56.1:3306/airflow_db
```

</details>

<details markdown="1">
<summary>Run webserver and scheduler components</summary>

```
airflow users create \
    --username admin \
    --firstname Peter \
    --lastname Parker \
    --role Admin \
    --email spiderman@superhero.org

# then enter password

airflow webserver --port 8080 [-D, -h]

airflow scheduler [-D, -h]

# kill daemon
kill $(ps -ef | grep "gunicorn" | awk '{print $2}')

```

</details>

<details markdown="1">
<summary>Install RabbitMQ</summary>

```
sudo apt update && sudo apt upgrade -y
sudo apt install rabbitmq-server -y
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
sudo systemctl status rabbitmq-server
sudo rabbitmq-plugins enable rabbitmq_management
sudo rabbitmqctl add_user airflow airflow
sudo rabbitmqctl set_user_tags airflow administrator
```

</details>

<details markdown="1">
<summary>Switch to CeleryExecutor</summary>

However, running it that way also has errors ```The scheduler does not appear to be running. Last heartbeat was received 14 minutes ago. ```

Switch to another standard executor to see

```
executor = CeleryExecutor
```

Change celery broker + result backend

```
broker_url = redis://redis:6379/0
broker_url = amqp://airflow:airflow@192.168.56.10:5672/
result_backend = db+mysql://airflow_user:airflow_pass@192.168.56.1:3306/airflow_db

# set mysql+mysqlconnector error
# install celery additionally if not already installed
pip install 'apache-airflow[celery]'


```

</details>

<details markdown="1">
<summary>Run flower to view jobs</summary>

Monitoring Tasks

```
# flower
# Celery Flower is a sweet UI for Celery. Airflow has a shortcut to start
# it ``airflow celery flower``. This defines the IP that Celery Flower runs on
flower_host = 0.0.0.0

# The root URL for Flower
# Example: flower_url_prefix = /flower
flower_url_prefix = /flower

# This defines the port that Celery Flower runs on
flower_port = 5556

# error amqp.exceptions.NotAllowed: Connection.open: (530) NOT_ALLOWED - access to vhost '/' refused for user 'airflow'

sudo rabbitmqctl list_permissions -p / 
sudo rabbitmqctl set_permissions -p "/" "airflow" ".*" ".*" ".*"

```

Various errors because haven't installed things

```
sudo apt-get install python3.8-dev
# Duy said every time installing must follow the set python3-pip python3.10-dev python3.10-venv

```

Jobs when running will be pushed to queue

![](https://longpt233.github.io/images/2024-03-01%2014-56-52.png)

On Airflow UI will see tasks queued but not running


However, Flower hasn't shown tasks because I haven't started the worker yet

</details>

<details markdown="1">
<summary>Run worker</summary>

```
airflow celery worker

# Note: worker can be attached to 1 queue
airflow celery worker -q spark,quark
```

![](https://longpt233.github.io/images/2024-03-01%2015-18-45.png)



</details>

# HA Model


Web server. Run on the second machine with identical config: still works, proving that just running 2 machines at the same time with HA IP is fine. Proving webserver only needs db? (synchronized through db)

Scheduler. Run on the second machine, everything still normal on machine 1 (flower, rabbitMQ, worker on machine 1 still receive)

<details markdown="1">
<summary>Web server, Scheduler on second machine</summary>

```
ImportError: No module named 'mysql'
pip install mysql-connector-python-rf

sqlalchemy.exc.NotSupportedError: (mysql.connector.errors.NotSupportedError) Authentication plugin 'caching_sha2_password' is not supported
pip install mysql-connector-python

```
```
ModuleNotFoundError: No module named 'MySQLdb'
pip install mysqlclient
# still error
sudo apt-get install python3.8-dev
# still error
sudo apt-get install python3-dev default-libmysqlclient-dev build-essential pkg-config

# install celery additionally if not already installed
pip install 'apache-airflow[celery]'

```

</details>



Message queue, mysql have their own HA methods

Because when 2 schedulers run, jobs will be duplicated x2, so only 1 machine must run -> need HA Scheduler


```
pip3 install git+https://github.com/teamclairvoyant/airflow-scheduler-failover-controller.git@v1.0.8
scheduler_failover_controller init  # append HA config to airflow.cfg
scheduler_failover_controller start

# commands to run, stop are in the config file
# Note config scheduler_nodes_in_cluster List of potential nodes that can act as Schedulers (Comma Separated List)
# change log to debug will see scheduler_failover_controller check by 
Running Command: ps -eaf | grep 'airflow scheduler'
```

<details markdown="1">
<summary>Error cannot run</summary>


```

in configuration.py:     


def get_sql_alchemy_conn(self):
        return self.get_config("core", "SQL_ALCHEMY_CONN")


but maybe airflow.cfg, the section is [database]. so that engine can be null

add sql_alchemy_conn both [database], [core] in airflow.cfg can fix this issue

https://github.com/teamclairvoyant/airflow-scheduler-failover-controller/issues/43
```
</details>



# Notes

- xcom (cross-communications) to pass information between tasks in dag
- get_pty=True to be able to kill tasks on UI
- do_xcom_push=False to avoid cases where xcom stores output > 65kb leading to task failure (use print or return function)



# References

[lotus doc - hiephm](https://lotus.vn/w/blog/gioi-thieu-ve-airflow-va-trien-khai-kien-truc-ha-348074727123714048.htm)

[git ha](https://github.com/teamclairvoyant/airflow-scheduler-failover-controller)

[doc](https://airflow.apache.org/docs/apache-airflow/stable/start.html)

[executor classification](https://viblo.asia/p/hieu-don-gian-ve-airflow-executor-3kY4g52yLAe)

[original article - longpt233](https://longpt233.github.io/airflow-ha/)

