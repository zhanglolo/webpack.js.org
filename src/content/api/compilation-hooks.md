---
title: Compilation Hooks
group: Plugins
sort: 10
contributors:
  - byzyk
  - madhavarshney
  - misterdev
  - wizardofhogwarts
  - EugeneHlushko
  - chenxsan
---

The `Compilation` module is used by the `Compiler` to create new compilations
(or builds). A `compilation` instance has access to all modules and their
dependencies (most of which are circular references). It is the literal
compilation of all the modules in the dependency graph of an application.
During the compilation phase, modules are loaded, sealed, optimized, chunked,
hashed and restored.

The `Compilation` class also extends `Tapable` and provides the following
lifecycle hooks. They can be tapped the same way as compiler hooks:

``` js
compilation.hooks.someHook.tap(/* ... */);
```

As with the `compiler`, `tapAsync` and `tapPromise` may also be available
depending on the type of hook.


### `buildModule` {#build-module}

`SyncHook`

Triggered before a module build has started, can be used to modify the module.

- Callback Parameters: `module`


```js
compilation.hooks.buildModule.tap('SourceMapDevToolModuleOptionsPlugin',
  module => {
    module.useSourceMap = true;
  }
);
```


### `rebuildModule` {#rebuild-module}

`SyncHook`

Fired before rebuilding a module.

- Callback Parameters: `module`


### `failedModule` {#failed-module}

`SyncHook`

Run when a module build has failed.

- Callback Parameters: `module` `error`


### `succeedModule` {#succeed-module}

`SyncHook`

Executed when a module has been built successfully.

- Callback Parameters: `module`


### `finishModules` {#finish-modules}

`AsyncSeriesHook`

Called when all modules have been built without errors.

- Callback Parameters: `modules`


### `finishRebuildingModule` {#finish-rebuilding-module}

`SyncHook`

Executed when a module has been rebuilt, in case of both success or with errors.

- Callback Parameters: `module`


### `seal` {#seal}

`SyncHook`

Fired when the compilation stops accepting new modules.


### `unseal` {#unseal}

`SyncHook`

Fired when a compilation begins accepting new modules.


### `optimizeDependencies` {#optimize-dependencies}

`SyncBailHook`

Fired at the beginning of dependency optimization.

- Callback Parameters: `modules`


### `afterOptimizeDependencies` {#after-optimize-dependencies}

`SyncHook`

Fired after the dependency optimization.

- Callback Parameters: `modules`


### `optimize` {#optimize}

`SyncHook`

Triggered at the beginning of the optimization phase.


### `optimizeModules` {#optimize-modules}

`SyncBailHook`

Called at the beginning of the module optimization phase. A plugin can tap into this hook to perform optimizations on modules.

- Callback Parameters: `modules`


### `afterOptimizeModules` {#after-optimize-modules}

`SyncHook`

Called after modules optimization has completed.

- Callback Parameters: `modules`


### `optimizeChunks` {#optimize-chunks}

`SyncBailHook`

Called at the beginning of the chunk optimization phase. A plugin can tap into this hook to perform optimizations on chunks.

- Callback Parameters: `chunks`


### `afterOptimizeChunks` {#after-optimize-chunks}

`SyncHook`

Fired after chunk optimization has completed.

- Callback Parameters: `chunks`


### `optimizeTree` {#optimize-tree}

`AsyncSeriesHook`

Called before optimizing the dependency tree. A plugin can tap into this hook to perform a dependency tree optimization.

- Callback Parameters: `chunks` `modules`


### `afterOptimizeTree` {#after-optimize-tree}

`SyncHook`

Called after the dependency tree optimization has completed with success.

- Callback Parameters: `chunks` `modules`


### `optimizeChunkModules` {#optimize-chunk-modules}

`SyncBailHook`

Called after the tree optimization, at the beginning of the chunk modules optimization. A plugin can tap into this hook to perform optimizations of chunk modules.

- Callback Parameters: `chunks` `modules`


### `afterOptimizeChunkModules` {#after-optimize-chunk-modules}

`SyncHook`

Called after the chunkmodules optimization has completed successfully.

- Callback Parameters: `chunks` `modules`


### `shouldRecord` {#should-record}

`SyncBailHook`

Called to determine whether or not to store records. Returning anything `!== false` will prevent every other "record" hook from being executed ([`record`](#record), [`recordModules`](#recordmodules), [`recordChunks`](#recordchunks) and [`recordHash`](#recordhash)).


### `reviveModules` {#revive-modules}

`SyncHook`

Restore module information from records.

- Callback Parameters: `modules` `records`


### `beforeModuleIds` {#before-module-ids}

`SyncHook`

Executed before assigning an `id` to each module.

- Callback Parameters: `modules`


### `moduleIds` {#module-ids}

`SyncHook`

Called to assign an `id` to each module.

- Callback Parameters: `modules`


### `optimizeModuleIds` {#optimize-module-ids}

`SyncHook`

Called at the beginning of the modules `id` optimization.

- Callback Parameters: `modules`


### `afterOptimizeModuleIds` {#after-optimize-module-ids}

`SyncHook`

Called when the modules `id` optimization phase has completed.

- Callback Parameters: `modules`


### `reviveChunks` {#revive-chunks}

`SyncHook`

Restore chunk information from records.

- Callback Parameters: `chunks` `records`


### `beforeChunkIds` {#before-chunk-ids}

`SyncHook`

Executed before assigning an `id` to each chunk.

- Callback Parameters: `chunks`


### `optimizeChunkIds` {#optimize-chunk-ids}

`SyncHook`

Called at the beginning of the chunks `id` optimization phase.

- Callback Parameters: `chunks`


### `afterOptimizeChunkIds` {#after-optimize-chunk-ids}

`SyncHook`

Triggered after chunk `id` optimization has finished.

- Callback Parameters: `chunks`


### `recordModules` {#record-modules}

`SyncHook`

Store module info to the records. This is triggered if [`shouldRecord`](#shouldrecord) returns a truthy value.

- Callback Parameters: `modules` `records`


### `recordChunks` {#record-chunks}

`SyncHook`

Store chunk info to the records. This is only triggered if [`shouldRecord`](#shouldrecord) returns a truthy value.

- Callback Parameters: `chunks` `records`


### `beforeHash` {#before-hash}

`SyncHook`

Called before the compilation is hashed.


### `afterHash` {#after-hash}

`SyncHook`

Called after the compilation is hashed.


### `recordHash` {#record-hash}

`SyncHook`

Store information about record hash to the `records`. This is only triggered if [`shouldRecord`](#shouldrecord) returns a truthy value.

- Callback Parameters: `records`


### `record` {#record}

`SyncHook`

Store information about the `compilation` to the `records`. This is only triggered if [`shouldRecord`](#shouldrecord) returns a truthy value.

- Callback Parameters: `compilation` `records`


### `beforeModuleAssets` {#before-module-assets}

`SyncHook`

Executed before module assets creation.


### `additionalChunkAssets` {#additional-chunk-assets}

`SyncHook`

W> `additionalChunkAssets` is deprecated (use the [Compilation.hook.processAssets](#processassets) instead and use one of the Compilation.PROCESS_ASSETS_STAGE_* as a stage option)

Create additional assets for the chunks.

- Callback Parameters: `chunks`


### `shouldGenerateChunkAssets` {#should-generate-chunk-assets}

`SyncBailHook`

Called to determine whether or not generate chunks assets. Returning anything `!== false` will allow chunk assets generation.


### `beforeChunkAssets` {#before-chunk-assets}

`SyncHook`

Executed before creating the chunks assets.



### `additionalAssets` {#additional-assets}

`AsyncSeriesHook`

Create additional assets for the compilation. This hook can be used to download
an image, for example:

``` js
compilation.hooks.additionalAssets.tapAsync('MyPlugin', callback => {
  download('https://img.shields.io/npm/v/webpack.svg', function(resp) {
    if(resp.status === 200) {
      compilation.assets['webpack-version.svg'] = toAsset(resp);
      callback();
    } else {
      callback(new Error('[webpack-example-plugin] Unable to download the image'));
    }
  });
});
```

### `optimizeChunkAssets` {#optimize-chunk-assets}

`AsyncSeriesHook`

W> `optimizeChunkAssets` is deprecated (use the [Compilation.hook.processAssets](#processassets) instead and use one of the Compilation.PROCESS_ASSETS_STAGE_* as a stage option)

Optimize any chunk assets. The assets are stored in `compilation.assets`. A
`Chunk` has a property `files` which points to all files created by a chunk.
Any additional chunk assets are stored in `compilation.additionalChunkAssets`.

- Callback Parameters: `chunks`

Here's an example that simply adds a banner to each chunk.

``` js
compilation.hooks
  .optimizeChunkAssets
  .tapAsync('MyPlugin', (chunks, callback) => {
    chunks.forEach(chunk => {
      chunk.files.forEach(file => {
        compilation.assets[file] = new ConcatSource(
          '\/**Sweet Banner**\/',
          '\n',
          compilation.assets[file]
        );
      });
    });

    callback();
  });
```


### `afterOptimizeChunkAssets` {#after-optimize-chunk-assets}

`SyncHook`

W> `afterOptimizeChunkAssets` is deprecated (use the [Compilation.hook.processAssets](#processassets) instead and use one of the Compilation.PROCESS_ASSETS_STAGE_* as a stage option)

The chunk assets have been optimized.

- Callback Parameters: `chunks`

Here's an example plugin from [@boopathi](https://github.com/boopathi) that outputs exactly what went into each chunk.

``` js
compilation.hooks.afterOptimizeChunkAssets.tap('MyPlugin', chunks => {
  chunks.forEach(chunk => {
    console.log({
      id: chunk.id,
      name: chunk.name,
      includes: chunk.getModules().map(module => module.request)
    });
  });
});
```



### `optimizeAssets` {#optimize-assets}

`AsyncSeriesHook`

Optimize all assets stored in `compilation.assets`.

- Callback Parameters: `assets`


### `afterOptimizeAssets` {#after-optimize-assets}

`SyncHook`

The assets have been optimized.

- Callback Parameters: `assets`


### `processAssets` {#process-assets}

`AsyncSeriesHook`

Asset processing.

- Callback Parameters: `assets`

Here's an example:

```js
compilation.hooks.processAssets.tap(
  {
    name: 'MyPlugin',
    stage: Compilation.PROCESS_ASSETS_STAGE_ADDITIONS,
  },
  (assets) => {
    // code here
  }
);
```

There're many stages to use:

- `PROCESS_ASSETS_STAGE_ADDITIONAL` - Add additional assets to the compilation.
- `PROCESS_ASSETS_STAGE_PRE_PROCESS` - Basic preprocessing of the assets.
- `PROCESS_ASSETS_STAGE_DERIVED` - Derive new assets from the existing assets.
- `PROCESS_ASSETS_STAGE_ADDITIONS` - Add additional sections to the existing assets e.g. banner or initialization code.
- `PROCESS_ASSETS_STAGE_OPTIMIZE` - Optimize existing assets in a general way.
- `PROCESS_ASSETS_STAGE_OPTIMIZE_COUNT` - Optimize the count of existing assets, e.g. by merging them.
- `PROCESS_ASSETS_STAGE_OPTIMIZE_COMPATIBILITY` - Optimize the compatibility of existing assets, e.g. add polyfills or vendor prefixes.
- `PROCESS_ASSETS_STAGE_OPTIMIZE_SIZE` - Optimize the size of existing assets, e.g. by minimizing or omitting whitespace.
- `PROCESS_ASSETS_STAGE_SUMMARIZE` - Summarize the list of existing assets.
- `PROCESS_ASSETS_STAGE_DEV_TOOLING` - Add development tooling to the assets, e.g. by extracting a source map.
- `PROCESS_ASSETS_STAGE_OPTIMIZE_TRANSFER` - Optimize the transfer of existing assets, e.g. by preparing a compressed (gzip) file as separate asset.
- `PROCESS_ASSETS_STAGE_ANALYSE` - Analyze the existing assets.
- `PROCESS_ASSETS_STAGE_REPORT` - Creating assets for the reporting purposes.

### `afterProcessAssets` {#after-process-assets}

`SyncHook`

Called after the [`processAssets`](#processassets) hook had finished without error.

### `needAdditionalSeal` {#need-additional-seal}

`SyncBailHook`

Called to determine if the compilation needs to be unsealed to include other files.


### `afterSeal` {#after-seal}

`AsyncSeriesHook`

Executed right after `needAdditionalSeal`.


### `chunkHash` {#chunk-hash}

`SyncHook`

Triggered to emit the hash for each chunk.

- Callback Parameters: `chunk` `chunkHash`


### `moduleAsset` {#module-asset}

`SyncHook`

Called when an asset from a module was added to the compilation.

- Callback Parameters: `module` `filename`


### `chunkAsset` {#chunk-asset}

`SyncHook`

Triggered when an asset from a chunk was added to the compilation.

- Callback Parameters: `chunk` `filename`


### `assetPath` {#asset-path}

`SyncWaterfallHook`

Called to determine the path of an asset.

- Callback Parameters: `path` `options`


### `needAdditionalPass` {#need-additional-pass}

`SyncBailHook`

Called to determine if an asset needs to be processed further after being emitted.


### `childCompiler` {#child-compiler}

`SyncHook`

Executed after setting up a child compiler.

- Callback Parameters: `childCompiler` `compilerName` `compilerIndex`


### `normalModuleLoader` {#normal-module-loader}

Since webpack v5 `normalModuleLoader` hook was removed. Now to access the loader use `NormalModule.getCompilationHooks(compilation).loader` instead.
