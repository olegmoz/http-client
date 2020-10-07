<img src="https://www.artipie.com/logo.svg" width="64px" height="64px"/>

[![Maven build](https://github.com/artipie/http-client/workflows/Maven%20Build/badge.svg)](https://github.com/artipie/http-client/actions?query=workflow%3A%22Maven+Build%22)
[![PDD status](http://www.0pdd.com/svg?name=artipie/http-client)](http://www.0pdd.com/p?name=artipie/http-client)
[![License](https://img.shields.io/github/license/artipie/http-client.svg?style=flat-square)](https://github.com/artipie/http-client/blob/master/LICENSE)

Artipie HTTP client implementation.

To install add this dependency to `pom.xml` file:
```xml
<dependency>
  <groupId>com.artipie</groupId>
  <artifactId>http-client</artifactId>
  <version><!-- use latest version --></version>
</dependency>
```

Artipie HTTP module provides HTTP abstractions. Client module targeted to implement
HTTP client on top of these abstractions.

- `ClientSlice` - is a `Slice` that sends request to remote TCP endpoint and returns
`Response`. In that sense it is similar to an HTTP connection.
- `ClientSlices` - collection of slices that pools resources
and builds `ClientSlice` by specified protocol (HTTP or HTTPS), host and port.

`ClientSlices` is entry point for usage of this module:

```java
ClientSlices slices = new JettyClientSlices();
Slice slice = slices.https("artipie.com");
Response response = slice.request(
  "GET /index.html HTTP/1.1",
  Headers.EMPTY,
  Content.EMPTY
);
response.send(
  (status, headers, body) -> {
    // handle recieved data
  }
);
``` 

## Create client for URI

It is common and convenient to specify a target for HTTP client using URI,
as it combines protocol, host and port in one string. URI may also contain non-empty path,
so it will be prepended to every HTTP request sent to remote endpoint.
`UriClientSlice` may be used to create client `Slice` from URI in the following way:

```java
ClientSlices slices = new JettyClientSlices();
Slice slice = new UriClientSlice(
  slices,
  new URI("https://central.artipie.com:54321/my-repo")
);
```

## Authentication

Target endpoint may require authentication over HTTP protocol. 
`AuthClientSlice` performs authentication automatically using provided `Authenticator` instance.
Different `Authenticator` instances may support different authentication schemes 
such as `Basic`, `Bearer`, `Digest` etc. 
`GenericAuthenticator` performs authentication using scheme requested by target server 
using specified username and password. 
To enable authentication support using all supported authentication schemes 
wrap `Slice` into `AuthClientSlice` and supply it with `GenericAuthenticator`:

```java
Slice slice = ...;
Slice authenticated = new AuthClientSlice(
  slice,
  new GenericAuthenticator("username", "password")
);
```

## How to contribute

Fork repository, make changes, send us a pull request. We will review
your changes and apply them to the `master` branch shortly, provided
they don't violate our quality standards. To avoid frustration, before
sending us your pull request please run full Maven build:

```
$ mvn clean install -Pqulice
```

To avoid build errors use Maven 3.3+.
