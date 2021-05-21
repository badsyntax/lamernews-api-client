# Lamer News API Client

[![npm version](https://badge.fury.io/js/lamernews-api-client.svg)](https://www.npmjs.com/package/lamernews-api-client)

A JavaScript API client for the Lamer News API with included TypeScript definitions.

**Note this package is still under development.**

Source code is generated from the [OpenAPI spec](./api/lamernews-schema.yml) using the [typescript-fetch](https://openapi-generator.tech/docs/generators/typescript-fetch/) template.

## Getting Started

Install:

```bash
npm install lamernews-api-client --save
```

Usage:

```js
import { Configuration, NewsApi, SortType } from 'lamernews-api-client';

const apiConfig = new Configuration({
  basePath: 'https://echojs.com',
});

const newsApi = new NewsApi(apiConfig);

newsApi
  .getNews({
    sort: SortType.Top,
    start: 0,
    count: 30,
  })
  .then((response) => {
    console.log(response);
  });
```
