# Procore JS SDK

[![CircleCI](https://circleci.com/gh/procore/js-sdk.svg?style=svg&circle-token=b24f4748ba5d14817088d02a0e14d376e1461c60)](https://circleci.com/gh/procore/js-sdk)

A node.js and browser compatible JS SDK for the procore API.

## Installation
```bash
yarn add @procore/js-sdk
```
We recommend installing the package with [yarn](http://yarnpkg.com)

## Example

### Authorization Code Flow

[Setting up a server](/guides/setup.md)

```javascript
import 'isomorphic-fetch';
import { client, oauth, refresher } from '@procore/js-sdk';

const token = window.document.querySelector('meta[name="accessToken"]').getAttribute('content');

const authorizer = oauth(token);

const refreshToken = token => fetch(
  '/oauth/procore/refresh',
  { method: 'POST', headers: { 'Authorization': `Bearer ${token}` } }
);

const procore = client(
  refresher(authorizer, refreshToken)
);

Promise.all([
  procore.get({base: '/me'}),
  procore.get({base: '/me', version: 'v1.0'}),
  procore.get({base: '/me', version: 'vapid'})
])
.then([me1, me2, me3] => {
  console.log(me1.body);
  console.log(me2.body);
  console.log(me3.body);
});
```

All paths are relative, the `@procore/js-sdk` will handle expanding them. An API version may
be specified in the `version:` argument, or the default version is used. The
default version is `v1.0` unless otherwise configured.

| Example | Requested URL |
| --- | --- |
| `procore.get({base: '/me'})` | `https://app.procore.com/rest/v1.0/me` |
| `procore.get({base: '/me', version: 'v1.0'})` | `https://app.procore.com/rest/v1.0/me` |
| `procore.get({base: '/me', version: 'vapid'})` | `https://app.procore.com/vapid/me` |


### Implicit Grant Flow
When creating an app, register the redirect URI as your app's root URL. E.g. `https://example.com`

```javascript
import 'isomorphic-fetch';
import qs from 'qs';
import { client, oauth, implicit, me, projects, images } from '@procore/js-sdk';

const clientId = document.head.querySelector('value=client_id').getAttribute('content');
const redirectUri = document.head.querySelector('value=redirect_uri').getAttribute('content');

const accessToken = qs.parse(window.location).access_token;

if ( !accessToken ) {
  window.location = implicit({ id: clientId, uri: redirectUri });
}

const procore = client(oauth(accessToken));
Promise.all([
    procore.get({base: '/me'}),
    procore.get({base: '/me', version: 'v1.0'}),
    procore.get({base: '/me', version: 'vapid'})
  ])
  .then([me1, me2, me3] => {
    console.log(me1.body);
    console.log(me2.body);
    console.log(me3.body);
  });
```

## Responses
A single API response contains the response body (JSON parsed), original request, and complete response.
[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) is the underlying http library, so both the request and response follow its specification. See [docs](https://github.github.io/fetch/) for more details.

```javascript
  procore
    .get({ base: '/projects', params: { company_id: 1 } })
    .then({ body, response, request } => {
      console.log(body[0].name); // ACME Construction LLC.
      console.log(response.headers.get('Total')) // 865 (Total records for the resource)
    });
```

### Formatting the response

By default, the SDK tries to format the response as JSON, you can control the
response formatting passing the `formatter` option as follow:

```tsx
// Create your own formatter
function formatter(response: Response): Promise<unknown> {
  return response.text()
}

// Pass the formatter configuration
procore.get({base: '/me'}, { formatter })
```

## Tests
```
yarn test
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/procore/js-sdk. This project is
intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the
[Contributor Covenant](http://contributor-covenant.org) code of conduct.

1. Create PR with version change `npm version minor`
2. Merge PR
3. Circle ci will release a new version of the package

## License

The package is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

## About Procore

<img
  src="https://www.procore.com/images/procore_logo.png"
  alt="Procore Logo"
  width="250px"
/>

Manage Version is maintained by Procore Technologies.

Procore - building the software that builds the world.

Learn more about the #1 most widely used construction management software at [procore.com](https://www.procore.com/)
