---
title: PHP Scraper
author: trannguyenhan 
date: 2023-03-15 20:52:00 +0700
categories: [Crawler]
tags: [Crawler, PHP, PHP crawler]
math: true
mermaid: true
image:
  src: https://raw.githubusercontent.com/demanejar/image-collection/main/scrapy/scraping-with-php-image-1.png
---

When talking about crawling, everything probably focuses on Python and frameworks built on Python like Scrapy, Beautiful Soup, or Selenium using Python,... In this article, let's talk a bit off-topic about a language that's not very strong in this area: PHP.

There will be many times when Python won't solve all your crawling problems and you'll need to use the language you're using for your website to do that. PHP is currently the most popular language for websites, so there will definitely be times when you have to use PHP to crawl.

## Installing Library

The library I mention in this article is `guzzle_requests`. Create a `composer.json` file and add the `guzzle_requests` library:

```json
{
    "require": {
        "guzzlehttp/guzzle": "^7.4"
    }
}
```

Run `composer install` to download the library for the project.

## Analyzing Website

Create a `guzzle_requests.php` file to write code to analyze the website.

In this article, I'll use the website [https://meridiano.net/beisbol/beisbol-venezolano/249167/lvbp--tigres-de-aragua-aseveran-que-miguel-cabrera-tiene--mucha-posibilidad--de-jugar.html](https://meridiano.net/beisbol/beisbol-venezolano/249167/lvbp--tigres-de-aragua-aseveran-que-miguel-cabrera-tiene--mucha-posibilidad--de-jugar.html) as an example, extract the title, description, article description image, content, and write to an output result file.

Import the library and declare an httpClient:

```php
require 'vendor/autoload.php';
$httpClient = new \GuzzleHttp\Client();
```

Get the HTML of the website to analyze:

```php
$response = $httpClient->get('https://meridiano.net/beisbol/beisbol-venezolano/249167/lvbp--tigres-de-aragua-aseveran-que-miguel-cabrera-tiene--mucha-posibilidad--de-jugar.html');
$htmlString = (string) $response->getBody();
```

Now to extract each piece of information, we'll use xpath to extract each component:

```php
$title = $xpath->evaluate('//meta[contains(@property,"og:title")]/@content');

$description = $xpath->evaluate('//meta[contains(@property,"og:description")]/@content');

$image = $xpath->evaluate('//div[contains(@class,"News Detail")]//img/@src');

$text = $xpath->evaluate('//*[@id="med"]');
```

After extracting, write them to a file. Combined, we have a project as follows:

```php
<?php

require 'vendor/autoload.php';
$httpClient = new \GuzzleHttp\Client();

$response = $httpClient->get('https://meridiano.net/beisbol/beisbol-venezolano/249167/lvbp--tigres-de-aragua-aseveran-que-miguel-cabrera-tiene--mucha-posibilidad--de-jugar.html');
$htmlString = (string) $response->getBody();

// HTML is often wonky, this suppresses a lot of warnings
libxml_use_internal_errors(true);

$doc = new DOMDocument();
$doc->loadHTML($htmlString);
$xpath = new DOMXPath($doc);

$file = fopen("test.txt", "w");

$title = $xpath->evaluate('//meta[contains(@property,"og:title")]/@content');
if(count($title) > 0){
    echo "Title: " . $title[0]->textContent.PHP_EOL;
    fwrite($file, "Title: " . $title[0]->textContent.PHP_EOL);
}

$description = $xpath->evaluate('//meta[contains(@property,"og:description")]/@content');
if(count($title) > 0){
    echo "Description: " . $description[0]->textContent.PHP_EOL;
    fwrite($file, "Description: " . $description[0]->textContent.PHP_EOL);
}

$image = $xpath->evaluate('//div[contains(@class,"News Detail")]//img/@src');
if(count($image) > 0){
    echo "Image link: " . $image[0]->textContent.PHP_EOL;
    fwrite($file, "Image link: " . $image[0]->textContent.PHP_EOL);
}

$text = $xpath->evaluate('//*[@id="med"]');
if(count($image) > 0){
    echo "Text content: " . $text[0]->textContent.PHP_EOL;
    fwrite($file, "Text content: " . $text[0]->textContent.PHP_EOL);
}
fclose($file);
```

You can view the entire project at [https://github.com/trannguyenhan/php-scraper](https://github.com/trannguyenhan/php-scraper).

