# Web Crawler
#### Simple web crawler built using Node.js


## Getting Started

- Clone the repo or download the zip file, and `cd` into the project folder.
- Install dependencies and run the crawler on your site!

```
npm install
node crawler.js --domain https://github.com/ --batch 3
```
###Arguments:
- `domain`: sub-domain on which you want to run the crawler. Please note that this crawler will not cross sub-domains.
- `depth`: instead of searching all pages, it restricts the depth of the link, it restricts the number to the depth size you enter.

**Note:** A batch gets only five tries before next one begins. That's why you shoud not keep very high batch size. 5 is the default.

PS: 3 is a default value.

## Features
This crawler is designed for gathering a subdomain's assets:

- Crawls all pages of a subdomain within depth. Excludes other subdomains.
- Generates JSON output listing the URLs of every static asset(images), grouped by page.
- Respects `robots.txt` of a site.
- Keeps saving the output to `output.json` file locally. So even if you stop it midway, you will get your results.
- Only crawls `html` pages. Ignores images and pdfs.
- Gracefully ignores `404` pages.


## Components

```
.
├── README.md // you are here
├── crawlSinglePage.js // crawls one page at a time
├── crawler.js // manages the crawler. script starts here
├── node_modules // dependencies
├── output.json // final output
├── package.json // meta data about app
├── parseUrl.js // parses input url
├── robots.js // parses robots.txt
└── robots.txt // stores a site's robots.txt if one exists
```

## How it works

###Overview:
- Input url is parsed.
- Crawling starts at the given page. Each crawl:
  - returns that pages's images
  - more hyperlinks from that page
- Newly discovered urls are added to the pool.
- Crawler goes to more urls, and extract assets and even more urls.
- Once all urls are crawled, program ends.

###Inside code:
- Code starts with `crawler.js`, where `init` function:
  - parses the input url using `parseUrl.js` module and adds it to url pool.
  - fetches `robots.txt` using `robots.json` and stores banned urls in `bannedUrls` array.
  - calls `checkCrawlingStatus` function.
- Next, `checkCrawlingStatus` keeps an eye on url pool. If all urls in the pool are processed it ends the program, if not, it calls `processUrlPool` function. It keeps calling `processUrlPool` every 5 seconds.
- `processUrlPool` makes batches of certain size and fetches data from those pages using `crawlSinglePage.js` module. Only after finishing current batch, next one gets formed.
- `crawlSinglePage.js` sends `GET` requests to individual pages and returns an object containing input url, page assets and newly discovered links.
- The newly discovered urls are added to the pool and `checkCrawlingStatus.js` notices them in the next cycle.
- Output file is appended after each batch is processed.
- Once all the urls in the pool are done, program stops.