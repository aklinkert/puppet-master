# puppet-master.io - website interaction made easy


## What? What is this? Why does it exist?

Over the last year we came across the need to abstract the way we interact with websites. For some different projects we built this product to centralize the code execution within a single app. Think about puppet-master as a modern replacement for selenium or similar products - you write frontend code and give it to an API -   a browser is launched, the code is executed and results and logs are returned. Simple as \*\*\*\*. :flushed:

## How does it work?

Puppet-Master is using [puppeteer](https://pptr.dev/) ([GitHub](https://github.com/GoogleChrome/puppeteer)) to control the browser and thus is only able to execute code in Chrome, which in our experience is sufficient for most requirements.

You can talk to puppet-master via the [HTTP API](api.md). There are already some clients available, check out the [clients](clients.md) section. An extensive documentation of features, tools and functions available during code execution can be found in the [code execution](execution.md) section.


## Complete Example

Let's take the [google example from puppeteers Github repo](https://github.com/GoogleChrome/puppeteer/blob/d68033aeca234a93c9ac5298258a5d324748466a/examples/search.js) and execute it using puppet-master. This is the example code:

```js
'use strict';

const puppeteer = require('puppeteer');

(async() => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  await page.goto('https://developers.google.com/web/');

  // Type into search box.
  await page.type('#searchbox input', 'Headless Chrome');

  // Wait for suggest overlay to appear and click "show all results".
  const allResultsSelector = '.devsite-suggest-all-results';
  await page.waitForSelector(allResultsSelector);
  await page.click(allResultsSelector);

  // Wait for the results page to load and display the results.
  const resultsSelector = '.gsc-results .gsc-thumbnail-inside a.gs-title';
  await page.waitForSelector(resultsSelector);

  // Extract the results from the page.
  const links = await page.evaluate(resultsSelector => {
    const anchors = Array.from(document.querySelectorAll(resultsSelector));
    return anchors.map(anchor => {
      const title = anchor.textContent.split('|')[0].trim();
      return `${title} - ${anchor.href}`;
    });
  }, resultsSelector);
  console.log(links.join('\n'));

  await browser.close();
})();
```

We can make some small changes to the code: Add variables and export the results as well as remove the async/await wrapper, since puppet-master is already wrapping the code inside of an asnyc function for you.

```js
await page.goto('https://developers.google.com/web/');
await page.type('#searchbox input', vars.term);

// Wait for suggest overlay to appear and click "show all results".
const allResultsSelector = '.devsite-suggest-all-results';
await page.waitForSelector(allResultsSelector);
await page.click(allResultsSelector);

// Wait for the results page to load and display the results.
const resultsSelector = '.gsc-results .gsc-thumbnail-inside a.gs-title';
await page.waitForSelector(resultsSelector);

// Extract the results from the page.
const links = await page.evaluate(resultsSelector => {
  const anchors = Array.from(document.querySelectorAll(resultsSelector));
  return anchors.map(anchor => {
    const title = anchor.textContent.split('|')[0].trim();
    return `${title} - ${anchor.href}`;
  });
}, resultsSelector);

results.links = links;
```

Creating a job at the API may look like this:

```json
{
    "code": "await page.goto('https://developers.google.com/web/');\nawait page.type('#searchbox input', vars.term);\n\n// Wait for suggest overlay to appear and click \"show all results\".\nconst allResultsSelector = '.devsite-suggest-all-results';\nawait page.waitForSelector(allResultsSelector);\nawait page.click(allResultsSelector);\n\n// Wait for the results page to load and display the results.\nconst resultsSelector = '.gsc-results .gsc-thumbnail-inside a.gs-title';\nawait page.waitForSelector(resultsSelector);\n\n// Extract the results from the page.\nconst links = await page.evaluate(resultsSelector => {\n  const anchors = Array.from(document.querySelectorAll(resultsSelector));\n  return anchors.map(anchor => {\n    const title = anchor.textContent.split('|')[0].trim();\n    return `${title} - ${anchor.href}`;\n  });\n}, resultsSelector);\n\nresults.links = links;",
    "vars": {
        "term": "Headless Chrome"
    },
    "modules": {}
}
```

response:
```json
{
    "data": {
        "status": "created",
        "code": "await page.goto('https://developers.google.com/web/');\nawait page.type('#searchbox input', vars.term);\n\n// Wait for suggest overlay to appear and click \"show all results\".\nconst allResultsSelector = '.devsite-suggest-all-results';\nawait page.waitForSelector(allResultsSelector);\nawait page.click(allResultsSelector);\n\n// Wait for the results page to load and display the results.\nconst resultsSelector = '.gsc-results .gsc-thumbnail-inside a.gs-title';\nawait page.waitForSelector(resultsSelector);\n\n// Extract the results from the page.\nconst links = await page.evaluate(resultsSelector => {\n  const anchors = Array.from(document.querySelectorAll(resultsSelector));\n  return anchors.map(anchor => {\n    const title = anchor.textContent.split('|')[0].trim();\n    return `${title} - ${anchor.href}`;\n  });\n}, resultsSelector);\n\nresults.links = links;",
        "vars": {
            "term": "Headless Chrome"
        },
        "modules": {},
        "uuid": "4caa2f0b-4dc4-4833-b5de-06002f728c24",
        "updated_at": "2018-08-14T12:48:24+00:00",
        "created_at": "2018-08-14T12:48:24+00:00"
    }
}
```

As you can see, the API returns the job with a status `created`, which means that it is scheduled for execution. When calling the API a few seconds later and ask for the current state, we get the following:

```json
{
    "data": {
        "uuid": "4caa2f0b-4dc4-4833-b5de-06002f728c24",
        "status": "done",
        "code": "await page.goto('https://developers.google.com/web/');\nawait page.type('#searchbox input', vars.term);\n\n// Wait for suggest overlay to appear and click \"show all results\".\nconst allResultsSelector = '.devsite-suggest-all-results';\nawait page.waitForSelector(allResultsSelector);\nawait page.click(allResultsSelector);\n\n// Wait for the results page to load and display the results.\nconst resultsSelector = '.gsc-results .gsc-thumbnail-inside a.gs-title';\nawait page.waitForSelector(resultsSelector);\n\n// Extract the results from the page.\nconst links = await page.evaluate(resultsSelector => {\n  const anchors = Array.from(document.querySelectorAll(resultsSelector));\n  return anchors.map(anchor => {\n    const title = anchor.textContent.split('|')[0].trim();\n    return `${title} - ${anchor.href}`;\n  });\n}, resultsSelector);\n\nresults.links = links;",
        "vars": {
            "term": "Headless Chrome"
        },
        "modules": {},
        "error": null,
        "logs": [
            {
                "time": "2018-08-14T12:48:25.941Z",
                "level": "DEBUG",
                "message": "Setting page viewport to width 1920 / height 1080"
            },
            {
                "time": "2018-08-14T12:48:25.942Z",
                "level": "DEBUG",
                "message": "Setting default language to en"
            },
            {
                "time": "2018-08-14T12:48:30.264Z",
                "level": "INFO",
                "message": "Code took 4319ms to execute."
            }
        ],
        "results": {
            "links": [
                "Getting Started with Headless Chrome - https://developers.google.com/web/updates/2017/04/headless-chrome",
                "Automated testing with Headless Chrome - https://developers.google.com/web/updates/2017/06/headless-karma-mocha-chai",
                "Headless Chrome: an answer to server-side rendering JS sites ... - https://developers.google.com/web/tools/puppeteer/articles/ssr",
                "Puppeteer - https://developers.google.com/web/tools/puppeteer/",
                "New in Chrome 59 - https://developers.google.com/web/updates/2017/05/nic59",
                "Examples - https://developers.google.com/web/tools/puppeteer/examples",
                "Articles by Eric Bidelman - https://developers.google.com/web/resources/contributors/ericbidelman",
                "All Updates tagged: headless - https://developers.google.com/web/updates/tags/headless",
                "Troubleshooting - https://developers.google.com/web/tools/puppeteer/troubleshooting",
                "ヘッドレス Chrome ことはじめ - https://developers.google.com/web/updates/2017/04/headless-chrome?hl=ja"
            ]
        },
        "started_at": "2018-08-14T12:48:25+00:00",
        "finished_at": "2018-08-14T12:48:30+00:00",
        "duration": 4319,
        "created_at": "2018-08-14T12:48:24+00:00",
        "updated_at": "2018-08-14T12:48:30+00:00"
    }
}
```

Pretty cool, huh? We get a lot of information out there: How long the code took to execute, what logs were yielded and the results, in this case the links we scraped. You can find more about the execution context and available utils as well as some internals at the [execution](execution.md) section.

> **Note**: Did you notice the `modules` object? Head over to the [modules](modules.md) section to learn more about that.


---------------------------------

[puppet-master.io](https://puppet-master.io) is a project developed by [Scalify](https://www.scalify.me) and is open source using a [Apache License Version 2.0](https://github.com/Scalify/puppet-master-client-go/blob/master/LICENSE).
