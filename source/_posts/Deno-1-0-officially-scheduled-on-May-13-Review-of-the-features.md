---
title: 'Deno: 1.0 officially scheduled on May, 13! Review of the features'
published: true
description: 'Explanation of the features coming in Deno 1.0'
devto_publication: true
devto_draft: false
date: 2020-05-08 23:46:08
cover_image:
tags: deno, typescript, nodejs, node.js, release
series:
canonical_url:
---
# 🤠 Introduction
It all started 2 years ago, Ryan Dahl, the creator of Node.js gave a talk at the JSConf 2018 "10 Things I Regret About Node.js":

{% youtube M3BM9TB-8yA %}

It was actually 7 things 🙃he regrets about Node.js: 

1. **Not sticking with Promises:** he added them in June 2009 but removed them in February 2010, with Promises in Node.js, It could have sped up the delivery of a standard on async/await

2. **Security:** Node process has wide access including system calls, network, disk I/O

3. **The build system:** You need GYP for compiling native C library and link it to Node.js modules, Chrome used to use GYB but now Node is the sole user

4. **package.json:** it includes unnecessary information such as LICENCES and repository - also it uses a centralized repository for modules

5. **node_modules:** maybe the worse for him, resolution is complex, folder size is frequently huge...

6. **require without extension:** the module load has to guess the extension and this is not how browser works

7. **index.js:** it complicates the module loading system

# 🌈 Deno was born
With all the things learned by building Node.js, Ryan came up with a new idea named [Deno](https://deno.land/):

> A secure runtime for JavaScript and TypeScript.
> Deno aims to provide a productive and secure scripting environment for the modern programmer. It is built on top of V8, Rust, and TypeScript.

Instead of using C++ as in Node.js, It's built on top of [Rust](https://www.rust-lang.org/) and It uses [Tokio](https://tokio.rs) under the hood.
It brings many of the best open-source technologies together.

# 🎉 Deno 1.0 - Release scheduled on May 13

So after almost 2 years, the API has been officially frozen and the launch of the 1.0 is scheduled on May 13. It addresses the design flaws that Ryan talked about in his lecturez.

## Getting started 🙌

To install Deno, here are the instructions: 

**Using Shell:**

```bash
curl -fsSL https://deno.land/x/install/install.sh | sh
```

**Or using PowerShell:**
```bash
iwr https://deno.land/x/install/install.ps1 -useb | iex
```

**Using Homebrew (macOS or Linux):**
```bash
brew install deno
```

**Using Chocolatey (Windows):**
```bash
choco install deno
```

See [deno_install](https://github.com/denoland/deno_install) for more installation options.

## What's in the 1.0 release ? 👀
Deno 1.0 comes fully loaded with many useful features for modern development. In the following section we are going to cover all the greatest features coming in the new release.

### Supports TypeScript out of the box

Well, everything is in the title. You can also bring your own `tsconfig.json` by using the following command:
```
deno run -c tsconfig.json [program.ts]
```

### Security by design

Programs run with no permissions **by default** and if the code needs permissions will be alerted. 

You need to use command-line options to tell Deno what permissions the program needs. By running `deno run -h` you will see the full list of permissions:
```
-A, --allow-all                    Allow all permissions
    --allow-env                    Allow environment access
    --allow-hrtime                 Allow high resolution time measurement
    --allow-net=<allow-net>        Allow network access
    --allow-plugin                 Allow loading plugins
    --allow-read=<allow-read>      Allow file system read access
    --allow-run                    Allow running subprocesses
    --allow-write=<allow-write>    Allow file system write access
    --cached-only                  Require that remote dependencies are already cached
    --cert <FILE>                  Load certificate authority from PEM encoded file
-c, --config <FILE>                Load tsconfig.json configuration file
-h, --help                         Prints help information
    --importmap <FILE>             UNSTABLE: Load import map file
    --inspect=<HOST:PORT>          activate inspector on host:port (default: 127.0.0.1:9229)
    --inspect-brk=<HOST:PORT>      activate inspector on host:port and break at start of user script
    --lock <FILE>                  Check the specified lock file
    --lock-write                   Write lock file. Use with --lock.
-L, --log-level <log-level>        Set log level [possible values: debug, info]
    --no-remote                    Do not resolve remote modules
-q, --quiet                        Suppress diagnostic output
-r, --reload=<CACHE_BLACKLIST>     Reload source code cache (recompile TypeScript)
    --seed <NUMBER>                Seed Math.random()
    --unstable                     Enable unstable APIs
    --v8-flags=<v8-flags>          Set V8 command line options. For help: --v8-flags=--help
```

### ECMAScript modules built-in
Deno does not support `require()`, It uses ES Modules:
```
import * as log from "https://deno.land/std/log/mod.ts";
```
The package management is super simple, just provide the URL of what you want to use. As the URL may change, for security purposes, by using a lock file (using the --lock command line flag) you can ensure you're running the code you expect to be.

### Super simple package management

Deno does not use npm. It uses modules referenced as URLs or file paths:
```
import { serve } from "https://deno.land/std@v0.42.0/http/server.ts";

const s = serve({ port: 8000 });

for await (const req of s) {
  req.respond({ body: "Hello World\n" });
}
```
You can specify version of the package in directly in the URL. For example `https://deno.land/std@v0.42.0/http/server.ts`.

Also, Deno offers a built-in dependency inspector (`deno info`).

### Use `deps.ts` instead of `package.json`
The Deno convention for dependency management is using a unique file called `deps.ts` for storing all the dependency. For example, we can look at the `deps.ts` of [oak](https://github.com/oakserver/oak), the popular middleware framework for Deno's http server inspired by Koa:

```js
// Copyright 2018-2020 the oak authors. All rights reserved. MIT license.

// This file contains the external dependencies that oak depends upon

export {
  HTTPOptions,
  HTTPSOptions,
  Response,
  serve,
  Server,
  ServerRequest,
  serveTLS,
} from "https://deno.land/std@v1.0.0-rc1/http/server.ts";
export {
  Status,
  STATUS_TEXT,
} from "https://deno.land/std@v1.0.0-rc1/http/http_status.ts";
export {
  Cookies,
  Cookie,
  setCookie,
  getCookies,
  delCookie,
} from "https://deno.land/std@v1.0.0-rc1/http/cookie.ts";
export {
  basename,
  extname,
  join,
  isAbsolute,
  normalize,
  parse,
  resolve,
  sep,
} from "https://deno.land/std@v1.0.0-rc1/path/mod.ts";
export { HmacSha256 } from "https://deno.land/std@v1.0.0-rc1/util/sha256.ts";
export { assert } from "https://deno.land/std@v1.0.0-rc1/testing/asserts.ts";
export {
  contentType,
  lookup,
} from "https://deno.land/x/media_types@v2.0.0/mod.ts";
```
(Source: https://github.com/oakserver/oak/blob/master/deps.ts)

### JSDoc built-in with `deno doc`
We strive for complete documentation. Deno has JSDoc built-in so you can use write JSDoc comments in files.

### Test runner with `Deno.test()` 
`std/testing/asserts.ts` module provides range of assertion helpers:
- `equal()` 
- `assert()`
- `assertEquals()` 
- `assertNotEquals()`
- `assertStrictEq()` 
- `assertStrContains()`
- `assertMatch()`
- `assertArrayContains()`
- `assertThrows()`
- `assertThrowsAsync()`
- `unimplemented()` 
- `unreachable()`

For example:
```js
import { assertEquals } from "https://deno.land/std/testing/asserts.ts";

Deno.test({
  name: "testing example",
  fn(): void {
    assertEquals("world", "world");
    assertEquals({ hello: "world" }, { hello: "world" });
  },
});
```
(Source: https://github.com/denoland/deno/tree/master/std/testing)

Run `deno test file.spec.ts`:
```bash
Compile file.spec.ts
Download https://deno.land/std/testing/asserts.ts
Download https://deno.land/std/fmt/colors.ts
Download https://deno.land/std/testing/diff.ts
running 1 tests
test testing example ... ok (9ms)

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out (9ms)
```

### Formatting with `deno fmt <files>` 
It is provided by [dprint](https://github.com/dprint/dprint), an alternative to the famour Prettier.

### Compiling and bundling with `deno bundle`
It is currently marked as unstable. So use it at your own risk.

### Debugger `deno run -A --inspect-brk fileToDebug.ts`
With this you can open the Chrome debugger (`chrome://inspect`) and start inspect the process!

### Reviewed (audited) Standard library
The standard library is garanteed to work with Deno, they do not have external dependencies and they are reviewed by the Deno core team. Modules are tagged in accordance with Deno releases, so version v0.4.0 of a standard library is garanteed to work with Deno v0.4.0.

List of modules:
- [colors](https://github.com/denoland/deno/blob/master/std/fmt/colors.ts)
- [datetime](https://github.com/denoland/deno/blob/master/std/datetime/README.md)
- [encoding](https://github.com/denoland/deno/blob/master/std/encoding/README.md)
- [examples](https://github.com/denoland/deno/blob/master/std/examples/README.md)
- [flags](https://github.com/denoland/deno/blob/master/std/flags/README.md)
- [fs](https://github.com/denoland/deno/blob/master/std/fs/README.md)
- [http](https://github.com/denoland/deno/blob/master/std/http/README.md)
- [log](https://github.com/denoland/deno/blob/master/std/log/README.md)
- [node](https://github.com/denoland/deno/blob/master/std/node/README.md)
- [testing](https://github.com/denoland/deno/blob/master/std/testing/README.md)
- [uuid](https://github.com/denoland/deno/blob/master/std/uuid/README.md)
- [ws](https://github.com/denoland/deno/blob/master/std/ws/README.md)

### Usage of W3C web standards

Deno provides the following W3C web standards APIs:
- `Request`
- `Response`
- `addEventListener`
- `atob`
- `btoa`
- `ReadableStream`
- `clearInterval`
- `clearTimeout`
- `dispatchEvent`
- `fetch`
- `queueMicrotask`
- `removeEventListener`
- `setInterval`
- `setTimeout`
- `AbortSignal`
- `Blob`
- `File`
- `FormData`
- `Headers`
- `URL`
- `URLSearchParams`
- `console`
- `isConsoleInstance`
- `location`
- `onload`
- `onunload`
- `self`
- `window`
- `AbortController`
- `CustomEvent`
- `DOMException`
- `ErrorEvent`
- `Event`
- `EventTarget`
- `MessageEvent`
- `TextDecoder`
- `TextEncoder`
- `Worker`
- `ImportMeta`
- `Location`

## Example: Build a simple HTTP Server using Deno 🚀
```js
import { Application } from "https://deno.land/x/oak/mod.ts";

const app = new Application();

// Logger
app.use(async (ctx, next) => {
  await next();
  const rt = ctx.response.headers.get("X-Response-Time");
  console.log(`${ctx.request.method} ${ctx.request.url} - ${rt}`);
});


// Hello World!
app.use((ctx) => {
  ctx.response.body = "Hello World!";
});

await app.listen({ port: 8000 });
```

Let's run it using `deno server.ts`: 
```
Compile file:///.../server.ts
Download https://deno.land/x/oak/mod.ts
Download https://deno.land/x/oak/application.ts
Download https://deno.land/x/oak/context.ts
Download https://deno.land/x/oak/cookies.ts
Download https://deno.land/x/oak/httpError.ts
Download https://deno.land/x/oak/middleware.ts
Download https://deno.land/x/oak/request.ts
Download https://deno.land/x/oak/response.ts
Download https://deno.land/x/oak/router.ts
Download https://deno.land/x/oak/send.ts
Download https://deno.land/x/oak/types.ts
Download https://deno.land/x/oak/deps.ts
Download https://deno.land/x/oak/keyStack.ts
Download https://deno.land/x/oak/tssCompare.ts
Download https://deno.land/std@v1.0.0-rc1/http/server.ts
Download https://deno.land/std@v1.0.0-rc1/http/http_status.ts
Download https://deno.land/std@v1.0.0-rc1/http/cookie.ts
Download https://deno.land/std@v1.0.0-rc1/path/mod.ts
Download https://deno.land/std@v1.0.0-rc1/util/sha256.ts
Download https://deno.land/std@v1.0.0-rc1/testing/asserts.ts
Download https://deno.land/x/media_types@v2.0.0/mod.ts
Download https://deno.land/std@v1.0.0-rc1/encoding/utf8.ts
Download https://deno.land/std@v1.0.0-rc1/io/bufio.ts
Download https://deno.land/std@v1.0.0-rc1/util/async.ts
Download https://deno.land/std@v1.0.0-rc1/http/io.ts
Download https://deno.land/std@v1.0.0-rc1/io/util.ts
Download https://deno.land/std@v1.0.0-rc1/path/win32.ts
Download https://deno.land/std@v1.0.0-rc1/path/posix.ts
Download https://deno.land/std@v1.0.0-rc1/path/constants.ts
Download https://deno.land/std@v1.0.0-rc1/path/common.ts
Download https://deno.land/std@v1.0.0-rc1/path/constants.ts
Download https://deno.land/std@v1.0.0-rc1/path/interface.ts
Download https://deno.land/std@v1.0.0-rc1/path/glob.ts
Download https://deno.land/std@v1.0.0-rc1/path/globrex.ts
Download https://deno.land/std@v1.0.0-rc1/path/utils.ts
Download https://deno.land/std@v1.0.0-rc1/fmt/colors.ts
Download https://deno.land/std@v1.0.0-rc1/testing/diff.ts
Download https://deno.land/std@v1.0.0-rc1/textproto/mod.ts
Download https://deno.land/std@v1.0.0-rc1/bytes/mod.ts
Download https://deno.land/std@v1.0.0-rc1/datetime/mod.ts
Download https://deno.land/x/media_types@v2.0.0/db.ts
Download https://deno.land/x/media_types@v2.0.0/deps.ts
Download https://deno.land/std@v0.42.0/path/mod.ts
Download https://deno.land/std@v0.42.0/path/win32.ts
Download https://deno.land/std@v0.42.0/path/posix.ts
Download https://deno.land/std@v0.42.0/path/constants.ts
Download https://deno.land/std@v0.42.0/path/common.ts
Download https://deno.land/std@v0.42.0/path/constants.ts
Download https://deno.land/std@v0.42.0/path/interface.ts
Download https://deno.land/std@v0.42.0/path/glob.ts
Download https://deno.land/std@v0.42.0/path/globrex.ts
Download https://deno.land/std@v0.42.0/path/utils.ts
Download https://deno.land/std@v0.42.0/testing/asserts.ts
Download https://deno.land/std@v0.42.0/fmt/colors.ts
Download https://deno.land/std@v0.42.0/testing/diff.ts
Download https://deno.land/x/oak/encoding.ts
Download https://deno.land/x/oak/isMediaType.ts
Download https://deno.land/x/oak/mediaType.ts
Download https://deno.land/x/oak/mediaTyper.ts
Download https://deno.land/x/oak/util.ts
Download https://deno.land/x/oak/pathToRegExp.ts
error: Uncaught PermissionDenied: network access to "127.0.0.1:8000", run again with the --allow-net flag
    at unwrapResponse ($deno$/ops/dispatch_json.ts:43:11)
    at Object.sendSync ($deno$/ops/dispatch_json.ts:72:10)
    at Object.listen ($deno$/ops/net.ts:51:10)
    at listen ($deno$/net.ts:164:18)
    at Application.serve (server.ts:261:20)
    at Application.listen (application.ts:106:31)
    at server.ts:18:11
```

Let's add the missing permission `deno --allow-net server.ts`:
```bash
$ curl http://127.0.0.1:8000
Hello World!
```