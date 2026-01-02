---
title: Installing Selenium Middleware for Scrapy
author: trannguyenhan 
date: 2024-12-03 20:52:00 +0700
categories: [Crawler]
tags: [Crawler, Selenium, Scrapy]
math: true
mermaid: true
image:
  src: https://raw.githubusercontent.com/demanejar/image-collection/refs/heads/main/Scrapy-Selenium/scrapy_selenium_middleware.png
---

Scrapy provides the [scrapy-selenium](https://pypi.org/project/scrapy-selenium/) library that allows using Selenium to extract website data before returning to the spider for processing. However, in some cases, for example, I don't want to use normal Selenium but want to use undetected-chromedriver because it helps bypass Cloudflare on websites, want to add proxy through Chrome extension, want to scroll up and down and some actions before going to the spider,..., then using the library will be very limited. Using the library is also very convenient in some simple cases, and I'll introduce it in a later article. In this article, I'll talk about using [undetected-chromedriver](https://pypi.org/project/undetected-chromedriver/) to build SeleniumMiddleware to bypass Cloudflare websites.

## Initializing Project

Initialize a Scrapy project:

```bash
scrapy startproject seleniummiddlewarescrapydemo
```

Create a spider to crawl data. Here I crawl data from the `vinpearl.com/` page:

```bash
scrapy genspider vinpearl_dot_com https://vinpearl.com/vi/news/ha-noi
```

Install necessary libraries:

```bash
pip install scrapy
pip install scrapy-selenium
```

## SeleniumMiddleware

Create the `SeleniumBaseMiddleware` class as follows:

```python
class SeleniumBaseMiddleWare(object):
    @classmethod
    def from_crawler(cls, crawler):
        middleware = cls()
        crawler.signals.connect(middleware.spider_opened, signals.spider_opened)
        crawler.signals.connect(middleware.spider_closed, signals.spider_closed)
        return middleware

    def get_chrome_options(self):
        options = uc.ChromeOptions()
        return options

    def spider_opened(self, spider):
        try:
            options = self.get_chrome_options()

            self.driver = uc.Chrome(
                options=options,
                version_main=127
            )
        except Exception as e:
            logging.error(f"Failed to open spider and create driver: {e}")
            raise

    def process_request(self, request, spider):
        logging.info('SELENIUM FETCH URL {}'.format(request.url))
        self.driver.get(request.url)

        request.meta.update({'driver': self.driver})
        content = self.driver.page_source
        return HtmlResponse(
            request.url, encoding="utf-8", body=content, request=request
        )

    def process_response(self, request, response, spider):
        return response
    
    def spider_closed(self, spider):
        self.driver.quit()
```

- `spider_opened()` initializes `driver` and necessary parameters, for example like `options`, or if there are additional proxies, we'll also add proxies here.

- `spider_closed()` closes `driver` after finishing crawling.

- `process_request()` handles extracting data, assigns `request.meta.update({'driver': self.driver})` to use the driver inside the spider if needed.

In the `vinpearl_dot_com` spider, if there are tasks that need to use `driver` like scrolling up and down, clicking buttons, then get the `driver` as follows:

```python
driver = response.request.meta["driver"]
driver.execute_script(
    "window.scrollTo(0, document.body.scrollHeight);"
)
```

On the condition that `driver` has been updated into the request of the middleware before `request.meta.update({'driver': self.driver})` as in SeleniumBaseMiddleware.

In the `settings` file, add SeleniumBaseMiddleware to DOWNLOADER_MIDDLEWARES:

```python
DOWNLOADER_MIDDLEWARES = {
   "seleniummiddlewarescrapydemo.middlewares.SeleniumBaseMiddleWare": 543,
}
```

Now crawling websites is very easy when no longer blocked by Cloudflare and all Javascript code of dynamic websites has been generated into HTML for easy analysis.

View the entire project at: [https://github.com/demanejar/selenium-middleware-scrapy-demo](https://github.com/demanejar/selenium-middleware-scrapy-demo)

