# Cloud Hosted Version

The cloud hosted version implements the exact same API as the self hosted version as documented in [API](api.md) with the only difference that the path contains the teamSlug you want to manage the jobs of and authorization, where the current user identifies using an api token. The current api `v1` can be accessed at

    https://puppet-master.io/api/v1

## Authorization

The public API uses a token based `Bearer` auth. To create an api token log in to you [Puppet-Master Account](https://puppet-master.io/settings#/api), go to _Settings > API_, enter a name and enable the required abilities.

> **Note**: The token you create will only be able to do the things you allow it to, so make sure to check all required abilities. If you are unsure, go with the smallest amount and revisit this one later, you can simply change the abilities of tokens that are already in use.

The token is then used in an `Authorization` header like this:

    Authorization: Bearer theapitokenigot

## Pricing

> 0.01€ per job execution

The usage of puppet-master.io is billed monthly based on the amount of executed jobs. So for example when you execute 1000 jobs a month we will chage you a total of 10€ plus VAT, without any additional charges.
