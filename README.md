# human-error [![CircleCI](https://circleci.com/gh/franciscop/human-error.svg?style=shield)](https://circleci.com/gh/franciscop/human-error)

Allows you to write errors so people can understand:

| [![Show an error in the code](img/meta-run.png)](img/meta-run.png)  | [![Show an error in the code](img/meta-jest.png)](img/meta-jest.png)
|:---:|:---:|
| An error while running `node app.js` | Same error while testing with `jest` |



## Getting started

Install it with NPM:

```bash
npm install human-error
```

Then create your error codes:

```js
// myfun-errors.js
const errors = require('human-error')({
  // [optional] a url where the error is explained in-depth
  url: name => `http://example.com/error/${name}`
});

// "NAMESPACE" can be whatever you want (or nothing at all)
errors['NAMESPACE.missingcallback'] = () => `
  myFun() expects a callback to be passed but nothing was passed.
`;

errors['NAMESPACE.invalidcallback'] = ({ type }) => `
  myFun() expects the argument to be a callback function.
  ${type ? `"${type}" was passed instead.` : ''}
`;

module.exports = errors;
```

Then use them in your code:

```js
// myfun.js
const errors = require('./myfun-errors');

module.exports = (cb) => {
  if (!cb) {
    throw errors('NAMESPACE.missingcallback');
  }
  if (!(cb instanceof Function)) {
    throw errors('NAMESPACE.invalidcallback', { type: typeof cb });
  }
  cb('Cool library!');
};
```


## Options

- `url` [false]: if there's an url to show more info. It can be a string in which case it will just be printed or a function that build the error such as `(key) => 'https://example.com/errors#' + key` so you can show the appropriate support url.
- `extra` [{}]: an object that defaults to all of the options in the namespace. Useful for example for status code errors (`status: 500`) etc.
- `width` [80]: the minimum width of the row. It cannot cut strings since links shouldn't be cut.
- `plain` [false]: avoid generating a table and using plain-text only, in case some things break.
