# angular-oauth2 [![Build Status](https://travis-ci.org/ewkonzo/angular-oauth2.svg)](https://travis-ci.org/ewkonzo/angular-oauth2)

AngularJS OAuth2 authentication module written in ES6.

---
###Getting this error.. any idea, i think the switch could be successful
   [$injector:modulerr] Failed to instantiate module angular-oauth2 due to:
    Error: [$injector:modulerr] Failed to instantiate module undefined due to:
    Error: [ng:areq] Argument 'module' is not a function, got undefined
    http://errors.angularjs.org/1.5.5/ng/areq?p0=module&p1=not%20a%20function%2C%20got%20undefined

---
## Installation

Choose your preferred method:

* Bower: `bower install angular-oauth2`
* NPM: `npm install --save angular-oauth2`
* Download: [angular-oauth2](https://raw.github.com/ewkonzo/angular-oauth2/master/dist/angular-oauth2.min.js)

## Usage

###### 1. Download `angular-oauth2` dependencies.

* [angular](https://github.com/angular/bower-angular)
* [angular-local-storage](https://github.com/grevory/angular-local-storage)
* [query-string](https://github.com/sindresorhus/query-string)

If you're using `bower` they will be automatically downloaded upon installing this library.

###### 2. Include `angular-oauth2` and dependencies.

```html
<script src="<VENDOR_FOLDER>/angular/angular.min.js"></script>
<script src="<VENDOR_FOLDER>/angular-local-storage/angular-local-storage.min.js"></script>
<script src="<VENDOR_FOLDER>/query-string/query-string.js"></script>
<script src="<VENDOR_FOLDER>/angular-oauth2/dist/angular-oauth2.min.js"></script>
```

###### 3. Configure `OAuth` (required) and `OAuthToken` (optional):

```js
angular.module('myApp', ['angular-oauth2'])
  .config(['OAuthProvider', function(OAuthProvider) {
    OAuthProvider.configure({
      baseUrl: 'https://api.website.com',
      clientId: 'CLIENT_ID',
      clientSecret: 'CLIENT_SECRET' // optional
    });
  }]);
```

###### 4. Catch `OAuth` errors and do something with them (optional):

```js
angular.module('myApp', ['angular-oauth2'])
  .run(['$rootScope', '$window', 'OAuth', function($rootScope, $window, OAuth) {
    $rootScope.$on('oauth:error', function(event, rejection) {
      // Ignore `invalid_grant` error - should be catched on `LoginController`.
      if ('invalid_grant' === rejection.data.error) {
        return;
      }

      // Refresh token when a `invalid_token` error occurs.
      if ('invalid_token' === rejection.data.error) {
        return OAuth.getRefreshToken();
      }

      // Redirect to `/login` with the `error_reason`.
      return $window.location.href = '/login?error_reason=' + rejection.data.error;
    });
  }]);
```

## API

#### OAuthProvider

Configuration defaults:

```js
OAuthProvider.configure({
  baseUrl: null,
  clientId: null,
  clientSecret: null,
  grantPath: '/oauth2/token',
  revokePath: '/oauth2/revoke'
});
```

#### OAuth

Check authentication status:

```js
/**
 * Verifies if the `user` is authenticated or not based on the `token`
 * cookie.
 *
 * @return {boolean}
 */

OAuth.isAuthenticated();
```

Get an access token:

```js
/**
 * Retrieves the `access_token` and stores the `response.data` on localstorage
 * using the `OAuthToken`.
 *
 * @param {object} user - Object with `username` and `password` properties.
 * @param {object} config - Optional configuration object sent to `POST`.
 * @return {promise} A response promise.
 */

OAuth.getAccessToken(user, options);
```

Refresh access token:

```js
/**
 * Retrieves the `refresh_token` and stores the `response.data` on localstorage
 * using the `OAuthToken`.
 *
 * @return {promise} A response promise.
 */

OAuth.getRefreshToken()
```

Revoke access token:

```js
/**
 * Revokes the `token` and removes the stored `token` from localstorage
 * using the `OAuthToken`.
 *
 * @return {promise} A response promise.
 */

OAuth.revokeToken()
```

**NOTE**: An *event* `oauth:error` will be sent everytime a `responseError` is emitted:

* `{ status: 400, data: { error: 'invalid_request' }`
* `{ status: 400, data: { error: 'invalid_grant' }`
* `{ status: 401, data: { error: 'invalid_token' }`
* `{ status: 401, headers: { 'www-authenticate': 'Bearer realm="example"' } }`

#### OAuthTokenProvider

`OAuthTokenProvider` uses [angular-local-storage](https://github.com/angular/bower-angular-local-storage) to store the localstorage. Check the [available options](https://code.angularjs.org/1.4.0/docs/api/nglocalstorage/service/$localstorage).

Configuration defaults:

```js
OAuthTokenProvider.configure({
  name: 'token',
  options: {
    secure: true
  }
});
```

#### OAuthToken

If you want to manage the `token` yourself you can use `OAuthToken` service.
Please check the [OAuthToken](https://github.com/ewkonzo/angular-oauth2/blob/master/src/providers/oauth-token-provider.js#L45) source code to see all the available methods.

## Contributing & Development

#### Contribute

Found a bug or want to suggest something? Take a look first on the current and closed [issues](https://github.com/ewkonzo/angular-oauth2/issues). If it is something new, please [submit an issue](https://github.com/ewkonzo/angular-oauth2/issues/new).

#### Develop

It will be awesome if you can help us evolve `angular-oauth2`. Want to help?

1. [Fork it](https://github.com/ewkonzo/angular-oauth2).
2. `npm install`.
3. Do your magic.
4. Run the tests: `gulp test`.
5. Build: `gulp build`
6. Create a [Pull Request](https://github.com/ewkonzo/angular-oauth2/compare).

*The source files are written in ES6.*

## Reference

* http://tools.ietf.org/html/rfc2617
* http://tools.ietf.org/html/rfc6749
* http://tools.ietf.org/html/rfc6750
* https://tools.ietf.org/html/rfc7009