# Procore JS SDK

[![CircleCI](https://circleci.com/gh/procore/node-procore.svg?style=svg&circle-token=b24f4748ba5d14817088d02a0e14d376e1461c60)](https://circleci.com/gh/procore/node-procore)

A node.js and browser compatible JS SDK for the procore API.

## Installation
```bash
yarn add @procore/sdk
```
We recommend installing the package with [yarn](http://yarnpkg.com)

## Example

[Setting up a server](/guides/setup.md)

```javascript
import 'isomorphic-fetch';
import { client, oauth, refresher, me, projects, images } from '@procore/sdk';

const token = document.head.querySelector('value=auth_token').getAttribute('content');

const authorizer = oauth({ token });

const refreshToken = token => fetch(
  '/oauth/procore/refresh',
  { method: 'POST', headers: { 'Authorization': `Bearer ${token}` } }
);

const procore = client(
  refresher(authorizer, refreshToken)
);

Promise.all([
  procore.get(me()),
  procore.get(projects({ company_id: 2 })),
  procore.get(images({ action: 'most_recent' }))
])
.then(onSuccess);
```


## Tests
```
yarn test
```

## Endpoint Generator

[`node-procore-endpoints`](https://github.com/procore/js-sdk-endpoints) generates interfaces and endpoint functions for improved developer experience. See the project for more details.

```typescript
interface DirectCosts {
  action: string;
  qs?: any;
  id?: number;
  project_id: number;

}

function directCosts({ action, qs, id, project_id }: DirectCosts): any {
  return {
    base: '/vapid/projects/{project_id}/direct_costs',
    action,
    params: { id, project_id },
    qs
  }
}

export default directCosts

```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/procore/js-sdk. This project is
intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the
[Contributor Covenant](http://contributor-covenant.org) code of conduct.


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
