# Diffbot API Groovy client

---

[Diffbot](http://www.diffbot.com/) is a developer of machine learning and computer vision algorithms and public APIs for extracting data from web pages / web scraping.

## Installation

To install Diffbot API Groovy client just follow these simple steps:

1. Include `diffbot.jar` in your project libs folder
2. Add [http-builder library](http://groovy.codehaus.org/modules/http-builder/download.html) as dependency.

You may use external libraries, particularly a [JSON implementation](http://mvnrepository.com/artifact/org.json/json/20090211) might be useful.

## Configuration

Configuration includes preparing `developer token`, `url` and `optional arguments` to be passed to API:

- `developer token` is an unique sequence of characters, some kind of licence key that defines your permissions to use Diffbot services. [Sign Up for a Diffbot Plan](http://www.diffbot.com/pricing).
- `url` is an URL to the web-resource you want to analyze.
- `optional arguments` is a list of parameters that is used to accept necessary restrictions to the result. Particular arguments depend on type of Diffbot API.

## Example: Call the Article API

This type of call is used to analyse news article, blog post and similar text-heavy web pages:

```groovy
import com.diffbot.DiffbotArticle

String token = '<your token here>'
String url = '<URL of web-page to be analyzed>'

HashMap result = DiffbotArticle.analyze(token, url)
// or with optional arguments
HashMap result = DiffbotArticle.analyze(token, url, [timeout: '5000', fields: 'meta,querystring,images(*)'])
```

If you are using external JSON library, just set type of result to necessary class:

```groovy
import com.diffbot.DiffbotArticle
import net.sf.json.JSONObject

String token = '<your token here>'
String url = '<URL of web-page to be analyzed>'

JSONObject result = DiffbotArticle.analyze(token, url)
// or with optional arguments
JSONObject result = DiffbotArticle.analyze(token, url, [timeout: '5000', fields: 'meta,querystring,images(*)'])
```

If your content is not publicly available (e.g., behind a firewall), you can pass markup directly to the Article API for analysis:

```groovy
import com.diffbot.DiffbotArticle

String token = '<your token here>'
// URL is still required because it's used to resolve any relative links contained in the markup.
String url = '<URL of web-page to be analyzed>'
String content = '<your page content to be analysed>'

HashMap result = DiffbotArticle.analyze(token, url, content)
// or with optional arguments
HashMap result = DiffbotArticle.analyze(token, url, content, [timeout: '5000', fields: 'meta,querystring,images(*)'])
```

Example result of a call Article API:

```json
{
  "type": "article",
  "icon": "http://www.diffbot.com/favicon.ico",
  "title": "Diffbot's New Product API Teaches Robots to Shop Online",
  "author": "John Davi",
  "date": "Wed, 31 Jul 2013 08:00:00 GMT",
  "videos": [
    {
      "primary": "true",
      "url": "http://www.youtube.com/embed/lfcri5ungRo?feature=oembed",
    }
  ],
  "tags": [
    "e-commerce",
    "SaaS"
  ]
  "url": "http://blog.diffbot.com/diffbots-new-product-api-teaches-robots-to-shop-online/",
  "humanLanguage": "en",
  "text": "Diffbot's human wranglers are proud today to announce the release of our newest product: an API for... products!\nThe Product API can be used for extracting clean, structured data from any e-commerce product page. It automatically makes available all the product data you'd expect: price, discount/savings amount, shipping cost, product description, any relevant product images, SKU and/or other product IDs.\nEven cooler: pair the Product API with Crawlbot, our intelligent site-spidering tool, and let Diffbot determine which pages are products, then automatically structure the entire catalog. Here's a quick demonstration of Crawlbot at work:\nWe've developed the Product API over the course of two years, building upon our core vision technology that's extracted structured data from billions of web pages, and training our machine learning systems using data from tens of thousands of unique shopping sites. We can't wait for you to try it out.\nWhat are you waiting for? Check out the Product API documentation and dive on in! If you need a token, check out our pricing and plans (including our Free plan).\nQuestions? Hit us up at support@diffbot.com ."
}
```

## Example: Call the Frontpage API

This type of call is used to analyse a multifaceted homepage:

```groovy
// use DML (XML) as result
import com.diffbot.DiffbotFrontpage

String token = '<your token here>'
String url = 'http://www.huffingtonpost.com'

String result = DiffbotFrontpage.analyze(token, url) // or DiffbotFrontpage.analyze(token, url, [format: 'xml'])
// use groovy.util.XmlSlurper to parse XML (see official groovy documentation http://groovy.codehaus.org/Reading+XML+using+Groovy%27s+XmlSlurper)
String rootNode = new XmlSlurper().parseText(result)
```

```groovy
// use JSON as result
import com.diffbot.DiffbotFrontpage
import net.sf.json.JSONObject

String token = '<your token here>'
String url = 'http://www.huffingtonpost.com'

HashMap result = DiffbotFrontpage.analyze(token, url, [format: 'json', timeout: '5000'])
// or
JSONObject result = DiffbotFrontpage.analyze(token, url, [format: 'json'])
```

If your content is not publicly available (e.g., behind a firewall), you can pass markup directly to the Frontpage API for analysis:

```groovy
import com.diffbot.DiffbotFrontpage

String token = '<your token here>'
String url = 'http://www.huffingtonpost.com'
String content = '<your page content to be analysed>'

String result = DiffbotFrontpage.analyze(token, url, content)
// or with optional arguments
HashMap result = DiffbotFrontpage.analyze(token, url, content, [timeout: '5000', format: 'json'])
```

Frontpage API returns DML(Diffbot Markup Language) as XML or JSON. A DML consists of a single info section and a list of items:

 INFO FIELD  | TYPE   | DESCRIPTION
 ----------- | ------ | ---
 `id`        | long   | DMLID of the URL
 `title`     | string | Extracted title of the page
 `sourceURL` | url    | The URL this was extracted from
 `icon`      | url    | A link to a small icon/favicon representing the page
 `numItems`  | url    | The number of items in this DML document

Some of the fields found in Items:

 ITEM FIELD    | TYPE                     | DESCRIPTION
 ------------- | ------------------------ | ---
 `id`          | long                     | Unique hashcode/id of item
 `title`       | string                   | Title of item
 `description` | string                   | innerHTML content of item
 `xroot`       | xpath                    | XPATH of where item was found on the page
 `pubDate`     | timestamp                | Timestamp when item was detected on page
 `link`        | URL                      | Extracted permalink (if applicable) of item
 `type`        | {IMAGE,LINK,STORY,CHUNK} | Extracted type of the item, whether the item represents an image, permalink, story (image+summary), or html chunk.
 `img`         | URL                      | Extracted image from item
 `textSummary` | string                   | A plain-text summary of the item
 `sp`          | double<-[0,1]            | Spam score - the probability that the item is spam/ad
 `sr`          | double<-[1,5]            | Static rank - the quality score of the item on a 1 to 5 scale
 `fresh`       | double<-[0,1]            | Fresh score - the percentage of the item that has changed compared to the previous crawl

## Example: Call the Product API

This type of call is used to analyse a shopping or e-commerce product page and returns information on the product:

```groovy
import com.diffbot.DiffbotProduct

String token = '<your token here>'
String url = 'http://www.overstock.com/Home-Garden/iRobot-650-Roomba-Vacuuming-Robot/7886009/product.html'

HashMap result = DiffbotProduct.analyze(token, url)
// or with optional arguments
HashMap result = DiffbotProduct.analyze(token, url, [timeout: timeout, '5000': fields: 'products(offerPrice,sku)'])
```

```groovy
import com.diffbot.DiffbotProduct
import net.sf.json.JSONObject

String token = '<your token here>'
String url = 'http://www.overstock.com/Home-Garden/iRobot-650-Roomba-Vacuuming-Robot/7886009/product.html'

JSONObject result = DiffbotProduct.analyze(token, url)
```

Example result of a call Product API:

```json
{
  "type": "product",
  "products": [
    {
      "title": "Before I Go To Sleep",
      "description": "Memories define us. So what if you lost yours every time you went to sleep? Your name, your identity, your past, even the people you love -- all forgotten overnight. And the one person you trust may be telling you only half the story. Before I Go To Sleep is a disturbing psychological thriller in which an amnesiac desperately tries to uncover the truth about who she is and who she can trust.",
      "offerPrice": "$7.99",
      "regularPrice": "$9.99",
      "saveAmount": "$2.00",
      "media": [
        {
          "height": 480,
          "width": 340,
          "link": "http://cdn.shopify.com/s/files/1/0184/6296/products/BeforeIGoToSleep_large.png?946",
          "type": "image",
          "xpath": "/HTML[@class='no-js']/BODY[@id='page-product']/DIV[@class='content-frame']/DIV[@class='content']/DIV[@class='content-shop']/DIV[@class='row']/DIV[@class='span5']/DIV[@class='product-thumbs']/UL/LI[@class='first-image']/A[@class='single_image']/IMG"
        }
      ]
    }
  ],
  "url": "http://store.livrada.com/collections/all/products/before-i-go-to-sleep"
}
```

## Example: Call the Image API

This type of call is used to analyse a web page and returns its primary image(s):

```groovy
import com.diffbot.DiffbotImage

String token = '<your token here>'
String url = 'http://images.search.yahoo.com/'

HashMap result = DiffbotImage.analyze(token, url)
// or with optional arguments
HashMap result = DiffbotImage.analyze(token, url, [timeout: '5000', fields: 'products(offerPrice,sku)'])
```

```groovy
import com.diffbot.DiffbotImage
import net.sf.json.JSONObject

String token = '<your token here>'
String url = 'http://images.search.yahoo.com/'

JSONObject result = DiffbotImage.analyze(token, url)
```

Example result of a call Image API:

```json
{
  "title": "The National Flower - Rose",
  "type": "image",
  "url": "http://www.statesymbolsusa.org/National_Symbols/National_flower.html"
  "images": [
    {
      "attrAlt": "Red rose in full bloom - click to see state flowers",
      "height": 371,
      "width": 300,
      "displayWidth": 300,
      "meta": [
          "[Jpeg] Compression Type - Baseline",
          "[Jpeg] Data Precision - 8 bits",
          "[Jpeg] Image Height - 371 pixels",
          "[Jpeg] Image Width - 300 pixels",
          "[Jpeg] Number of Components - 3",
          "[Jpeg] Component 1 - Y component: Quantization table 0, Sampling factors 2 horiz/2 vert",
          "[Jpeg] Component 2 - Cb component: Quantization table 1, Sampling factors 1 horiz/1 vert",
          "[Jpeg] Component 3 - Cr component: Quantization table 1, Sampling factors 1 horiz/1 vert",
          "[Jfif] Version - 1.2",
          "[Jfif] Resolution Units - none",
          "[Jfif] X Resolution - 100 dots",
          "[Jfif] Y Resolution - 100 dots",
          "[Adobe Jpeg] DCT Encode Version - 1",
          "[Adobe Jpeg] Flags 0 - 192",
          "[Adobe Jpeg] Flags 1 - 0",
          "[Adobe Jpeg] Color Transform - YCbCr"
          ],
      "url": "http://www.statesymbolsusa.org/IMAGES/rose_usda-web.jpg",
      "size": 12328,
      "displayHeight": 371,
      "xpath": "/HTML[1]/BODY[1]/DIV[1]/TABLE[3]/TBODY[1]/TR[2]/TD[4]/DIV[1]/DIV[1]/H6[1]/SPAN[1]/A[1]/IMG[1]"
    },
    {
      "attrAlt": "Yellow rose - click to see state flowers",
      "height": 304,
      "width": 380,
      "displayWidth": 380,
      "meta": [
          "[Jpeg] Compression Type - Baseline",
          "[Jpeg] Data Precision - 8 bits",
          "[Jpeg] Image Height - 304 pixels",
          "[Jpeg] Image Width - 380 pixels",
          "[Jpeg] Number of Components - 3",
          "[Jpeg] Component 1 - Y component: Quantization table 0, Sampling factors 2 horiz/2 vert",
          "[Jpeg] Component 2 - Cb component: Quantization table 1, Sampling factors 1 horiz/1 vert",
          "[Jpeg] Component 3 - Cr component: Quantization table 1, Sampling factors 1 horiz/1 vert",
          "[Jfif] Version - 1.2",
          "[Jfif] Resolution Units - none",
          "[Jfif] X Resolution - 100 dots",
          "[Jfif] Y Resolution - 100 dots",
          "[Adobe Jpeg] DCT Encode Version - 1",
          "[Adobe Jpeg] Flags 0 - 192",
          "[Adobe Jpeg] Flags 1 - 0",
          "[Adobe Jpeg] Color Transform - YCbCr"
          ],
      "url": "http://www.statesymbolsusa.org/IMAGES/rose_yellow-380.jpg",
      "size": 12142,
      "displayHeight": 304,
      "xpath": "/HTML[1]/BODY[1]/DIV[1]/TABLE[3]/TBODY[1]/TR[2]/TD[4]/DIV[1]/DIV[1]/TABLE[1]/TBODY[1]/TR[1]/TD[1]/DIV[1]/H6[1]/SPAN[1]/A[1]/IMG[1]"
    }
  ]
}
```

-Initial commit by Aliaksandr Pyrkh-
