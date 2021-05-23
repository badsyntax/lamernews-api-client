# Lamer News API Client

[![npm version](https://badge.fury.io/js/lamernews-api-client.svg)](https://www.npmjs.com/package/lamernews-api-client)

A JavaScript API client for the Lamer News API. This package exports ES modules and includes TypeScript definitions.

**Note this package is still under development.**

Source code is generated from the [OpenAPI spec](./api/lamernews-schema.yml) using the [typescript-fetch](https://openapi-generator.tech/docs/generators/typescript-fetch/) template.

## Getting Started

### Install

```bash
npm install lamernews-api-client --save
```

### Basic Usage

```ts
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

### Usage with Middleware & TypeScript

```ts
import {
  AuthApi,
  CommentsApi,
  Configuration,
  ConfigurationParameters,
  FetchParams,
  GenericResponse,
  Middleware,
  NewsApi,
  ResponseContext,
  RequestContext,
  ResponseStatus,
} from 'lamernews-api-client';

class ApiMiddleWare implements Middleware {
  public async pre(context: RequestContext): Promise<FetchParams | void> {
    return Promise.resolve(context);
  }

  public async post(context: ResponseContext): Promise<Response | void> {
    // Treat successful responses with response.status === 'err' as errors
    if (
      context.response.ok &&
      context.response.headers
        .get('content-type')
        ?.startsWith('application/json')
    ) {
      const response = (await context.response
        .clone()
        .json()) as GenericResponse;
      if (response.status === ResponseStatus.Err) {
        return Promise.reject(context.response);
      }
    }
    return Promise.resolve(context.response);
  }
}

const configParams: ConfigurationParameters = {
  basePath: 'https://echojs.com',
  middleware: [new ApiMiddleWare()],
};

const apiConfig = new Configuration(configParams);

export const apiClient = {
  newsApi: new NewsApi(apiConfig),
  commentsApi: new CommentsApi(apiConfig),
  authApi: new AuthApi(apiConfig),
};

export type ApiClient = typeof apiClient;
```

### Authentication

Here's an example of how to authenticate, then upvote a new entry:

```ts
authApi
  .login({
    username: 'username',
    password: 'password',
  })
  .then((data) => {
    const { auth, apisecret } = data;
    document.cookie =
      'auth=' + auth + '; expires=Thu, 1 Aug 2030 20:00:00 UTC; path=/';
    return newsApi.voteNews({
      newsId: newsId,
      voteType: VoteType.Up,
      apisecret: apisecret,
    });
  });
```

### Authentication in React Native

Similar to the previous example we need to manually set the cookie. This can be achieved by updating the api config.

First you need to expose a function to mutate the config:

```ts
const configParams = {
  basePath: 'https://echojs.com/api',
  middleware: [new ApiMiddleWare()],
};

const apiConfig = new Configuration(configParams);

export const apiClient = {
  newsApi: new NewsApi(apiConfig),
  commentsApi: new CommentsApi(apiConfig),
  authApi: new AuthApi(apiConfig),
};

export function updateApiConfig(config: ConfigurationParameters): void {
  Object.assign(configParams, config);
}
```

Now after a successful login, add the auth cookie header to the API config:

```ts
authApi
  .login({
    username: 'username',
    password: 'password',
  })
  .then((data) => {
    const { auth } = data;
    updateApiConfig({
      headers: {
        cookie: `auth=${auth}`,
      },
    });
  });
```

All subsequent requests will now include this request header containing the cookie.

React-Native's `fetch` implementation does not support posting a `URLSearchParam` instance as the body. You need to adjust the request body to stringify the `URLSearchParams`:

```ts
export class ApiMiddleWare implements Middleware {
  public async pre(context: RequestContext): Promise<FetchParams | void> {
    if (context.init.body instanceof URLSearchParams) {
      context.init.body = context.init.body.toString();
    }
    return Promise.resolve(context);
  }
}
```
