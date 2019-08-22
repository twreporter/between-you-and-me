# How to develop npm packages confortably


## Case

Given 2 repos:

```bash
/twreporter-react
  /node_modules
    /@twreporter
      /redux # scoped package @twreporter/redux
    /redux
  /src
    /server.js
```

```bash
/twreporter-npm-packages
  /packages
    /redux # the source code of @twreporter/redux
      /node_modules
      /lib
        /create-store.js
```

We want to develop `@twreporter/redux` and test it being used in `twreporter-react` at local.


## Different Ways to Do This

### Symlink

Use `npm link` or `yarn link` to symlink the files of a module into the consumers `node_modules` folder.

This is the most common suggested way.

But it has some problems:

1. Not like packages published with `npm publish` will only contain the files we specified. When compiling the files in `@twreporter/redux`, the compiler like Babel may take the closest config file (`.babelrc`) relative to the target.

2. The `require` invoked in the `@twreporter/redux` will search the Node modules in `/twreporter-npm-packages/packages/redux/node_modules`, not `twreporter-react/node_modules`.


### Pack the Package into Zipped File

As mentioned in [this PR comment](https://github.com/twreporter/twreporter-react/pull/1320#issuecomment-523714489), we can use `npm pack` or `yarn pack` to pack the package. 

Ref: 

- [`yarn pack`](https://yarnpkg.com/lang/en/docs/cli/pack/)
- [`npm pack`](https://docs.npmjs.com/cli/pack.html)


### Third Party Management Tools

As mentions in [this PR comment](https://github.com/twreporter/twreporter-react/pull/1320#issuecomment-523373005), we can use package like `yalc` to simulate a local npm registry.

Ref:

- [Yalc](https://github.com/whitecolor/yalc)
