# Code execution - Tools, Tips and available shared methods

## global objects

### Vars

When passing a `vars` object to the API those values will be passed down to the execution context and available as `vars`. For example:

```js
await page.goto(vars.page);
```

### Results

Often we want to return values from scraped websites or tests. To do so, just assign them to the global `results` object:

```js
await page.goto(vars.page);
const text = await page.evaluate(() => document.querySelector('body').textContent);
results.text = text;
results.ip = text.split(":")[1];
```

### Logger

It is often useful to log something during execution of the script. To save logs use the global `loggger` object which has the following methods:

```js

logger.dir(...obj); // logs object structures as with console.dir
logger.log(...msg);
logger.info(...msg);
logger.debug(...msg);
logger.warn(...msg);
logger.warning(...msg);
logger.error(...msg);
```

> **Note**: All of the log parameter are formatted using [`util.format`](https://nodejs.org/api/util.html#util_util_format_format_args)

> **Note**: For convenience and compatibility the logger is also available as `console`.

usage example:

```js
await page.goto(vars.page);
const text = await page.evaluate(() => document.querySelector('body').textContent);
logger.debug(text);
logger.info(text.split(":")[1]);
```


## additional utility functions

### sleep

Sleeps for the given amount of milliseconds and pauses the execution. Useful e.g. to wait for page rendering.

```js
// sleep for 500 ms
await sleep(500);
```

### scroll

Scrolls the page by one window height (roughly 1000 px).

```js
await scroll(page);
```

### scrollByPx

Scrolls the page by the given amount of pixels.

```js
// Scroll the page by 100 pixels
await scrollByPx(page, 100);
```


### getElementText

Returns the text of the given element by getting the plain `jsonValue` of the `textContent`. Can be used to e.g. retrieve the text of a button.

```js
const btn = await page.$("body > button#follow");
if (!btn) {
    throw new Error(`Cannot find follow button`);
}

const text = await getElementText(btn);
if (text === "Following") {
    logger.warning(`Already following user ${vars.follow_user}`);
}
```

### format

The pure node `util.format` method as documented in [`util.format`](https://nodejs.org/api/util.html#util_util_format_format_args).


```js
results.str = util.format("hey %s, patrick ate all of the burgers!", "captain");
// --> results.str: "hey captain, patrick ate all of the burgers!"
```

### sanitize

Replaces multiple ocurrences of `\r\n` and `\n` with single `\n`.

```js
const saneText = sanitize("some text\n\n\n with an absurd number of \n\n\n newlines           and           \n\r\n      newlines      and stuff")
// --> saneText: "some text\nwith an absurd number of \nnewlines and \nnewlines and stuff"
```
