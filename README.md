# Lamer News API Client

[![npm version](https://badge.fury.io/js/lamernews-api-client.svg)](https://www.npmjs.com/package/lamernews-api-client)

A JavaScript/TypeScript API client for the Lamer News API.

**Note this package is still under development.**

Source code is generated from the [OpenAPI spec](./api/lamernews-schema.yml) and using the [typescript-fetch](https://openapi-generator.tech/docs/generators/typescript-fetch/) template.

## Getting Started

Install:

```bash
npm install lamernews-api-client --save
```

Usage:

```js
import { Configuration, NewsApi } from "lamernews-api-client";

const configParams = {
  basePath: "https://echojs.com",
};

const apiConfig = new Configuration(configParams);

const newsApi = new NewsApi(apiConfig);

newsApi
  .getNews({
    sort: "top",
    start: 0,
    count: 30,
  })
  .then((response) => {
    console.log(response);
  });
```
