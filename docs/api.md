# HTTP API

puppet-master is controlled via public API where the current user identifies using an api token. The current api `v1` can be accessed at

    https://puppet-master.io/api/v1

## Authorization

The public API uses a token based `Bearer` auth. To create an api token log in to you [Puppet-Master Account](https://puppet-master.io/settings#/api), go to _Settings > API_, enter a name and enable the required abilities.

> **Note**: The token you create will only be able to do the things you allow it to, so make sure to check all required abilities. If you are unsure, go with the smallest amount and revisit this one later, you can simply change the abilities of tokens that are already in use.

The token is then used in an `Authorization` header like this:

    Authorization: Bearer theapitokenigot

## Swagger Spec

The API is documented as [OpenAPI v3 spec](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md) using [Swagger](https://swagger.io/) and is hosted publicly at [SwaggerHub](https://swagger.io/tools/swaggerhub/):

> https://app.swaggerhub.com/apis/scalify/puppet-master.io/0.1.0
