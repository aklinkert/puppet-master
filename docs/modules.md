# ES6 (shared) modules

Puppet-master is completely build around ES6 and runs with enabled module flag. This is especially useful when writing reusable, shared modules for your application or tool. Having that said, here is an example showing how to use them:


```js
// code.mjs
import {getIp} from 'shared';

await page.goto(vars.page);
const ip = await getIp(page);

logger.info(ip);
results.ip = ip;
```

```js
// shared.mjs
export async function getIp(page) {
  const text = await page.evaluate(() => document.querySelector('body').textContent);
  return text.split(":")[1];
}
```

Passing those to the API should look as follows:

```json
{
	"code": "import {getIp} from 'shared';\n\nawait page.goto(vars.page);\nconst ip = await getIp(page);\n\nlogger.info(ip);\nresults.ip = ip;",
    "vars": {
        "page": "http://ifcfg.co"
    },
    "modules": {
        "shared": "export async function getIp(page) {\n  const text = await page.evaluate(() => document.querySelector('body').textContent);\n  return text.split(\":\")[1];\n}"
    }
}
```

Example execution response:

``` json
{
    "data": {
        "uuid": "f7732589-3dd2-4a45-bc28-3f9a6a318ceb",
        "status": "done",
        "code": "import {getIp} from 'shared';\n\nawait page.goto(vars.page);\nconst ip = await getIp(page);\n\nlogger.info(ip);\nresults.ip = ip;",
        "vars": {
            "page": "http://ifcfg.co"
        },
        "modules": {
            "shared": "export async function getIp(page) {\n  const text = await page.evaluate(() => document.querySelector('body').textContent);\n  return text.split(\":\")[1];\n}"
        },
        "error": null,
        "logs": [
            {
                "time": "2018-08-14T18:21:37.014Z",
                "level": "DEBUG",
                "message": "Setting page viewport to width 1920 / height 1080"
            },
            {
                "time": "2018-08-14T18:21:37.015Z",
                "level": "DEBUG",
                "message": "Setting default language to en"
            },
            {
                "time": "2018-08-14T18:21:37.018Z",
                "level": "DEBUG",
                "message": "Linking module shared to file:///puppet-master/code (Available: shared)"
            },
            {
                "time": "2018-08-14T18:21:37.71Z",
                "level": "INFO",
                "message": "144.76.114.135"
            },
            {
                "time": "2018-08-14T18:21:37.71Z",
                "level": "INFO",
                "message": "Code took 692ms to execute."
            }
        ],
        "results": {
            "ip": "127.0.0.1"
        },
        "started_at": "2018-08-14T18:21:37+00:00",
        "finished_at": "2018-08-14T18:21:37+00:00",
        "duration": 692,
        "created_at": "2018-08-14T18:21:34+00:00",
        "updated_at": "2018-08-14T18:21:37+00:00"
    }
}
```
