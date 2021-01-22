# github-releases-validator

![tests](https://github.com/ptomulik/github-releases-validator/workflows/Tests/badge.svg?branch=master)
![build](https://github.com/ptomulik/github-releases-validator/workflows/Build/badge.svg?branch=master)
![code](https://github.com/ptomulik/github-releases-validator/workflows/Code%20Quality/badge.svg?branch=master)

GitHub REST API implements [list releases][list-releases] query that returns an array of releases published by given repository. The [github-releases-validator][github-releases-validator] is a node.js module which validates JSON data against a schema describing [list releases][list-releases] result. The schema used is extracted from [github/rest-api-description][rest-api-description].

## Usage

Install the npm module

```console
user@pc:$ npm install @ptomulik/github-release-validator
```

Use it in your code

```js
const validate = require('@ptomulik/github-release-validator')
const data = [{}]

if (!validate(data)) {
  console.log(validate.errors)
}
```


## LICENSE

Copyright (c) 2021 by Paweł Tomulik <ptomulik@meil.pw.edu.pl>

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[list-releases]: https://docs.github.com/en/rest/reference/repos#list-releases
[github-releases-validator]: https://github.com/ptomulik/github-releases-validator
[rest-api-description]: https://github.com/github/rest-api-description
