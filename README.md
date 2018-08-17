# puppet-master

puppet-master is a tool that executes website interaction code, written in ES6, in a highyl scalable and flexible manner. Head over to the [Docs](https://docs.puppet-master.io) for more information about setup, configuration and usage. [Signup for the hosted cloud version](https://puppet-master.io/register) or [host your own instance](https://docs.puppet-master.io/#/self_hosted).


## docs

Documentation is available at [Docs](https://docs.puppet-master.io), which is the GitHub pages deployment of the `docs` directory. The docs are using [docsify](https://github.com/docsifyjs/docsify/) and can be served locally by installing [docsify-cli](https://github.com/docsifyjs/docsify-cli):

```bash
# either
npm i docsify-cli -g
# or
yarn global add docsify-cli

# then serve the docs:
docsify serve docs
```


## Examples

There is an example `docker-compose.yml` within the `examples/self_hosted` directory available as a quickstart reference. For more details please have a look at the [self hosted section of the docs](https://docs.puppet-master.io/#self_hosted).

## License

Copyright 2018 Scalify GmbH

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

		http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
