# @rolldown/plugin-babel declarations with skipLibCheck: false

A minimal reproduction: `@rolldown/plugin-babel` 0.3.0's published declarations fail to type-check when a project checks library declarations (`"skipLibCheck": false`), on both Babel 7 and Babel 8.

Each folder imports the plugin and type-checks one file with TypeScript 6.0.3:

```sh
cd babel7   # or babel8
npm install
npm run typecheck
```

## Babel 7 (`@babel/core` 7.29.7 + `@types/babel__core` 7.20.5)

```
node_modules/@rolldown/plugin-babel/dist/babelCompat-ag_0o5ff.d.mts(10,28): error TS2694: Namespace '…/@types/babel__core/index' has no exported member 'InputOptions'.
node_modules/@rolldown/plugin-babel/dist/babelCompat-ag_0o5ff.d.mts(11,26): error TS2694: Namespace '…/@types/babel__core/index' has no exported member 'PresetItem'.
node_modules/@rolldown/plugin-babel/dist/babelCompat-ag_0o5ff.d.mts(12,26): error TS2694: Namespace '…/@types/babel__core/index' has no exported member 'FileResult'.
```

## Babel 8 (`@babel/core` 8.0.10)

```
node_modules/@rolldown/plugin-babel/dist/babelCompat-ag_0o5ff.d.mts(13,31): error TS2694: Namespace '…/@babel/core/lib/index' has no exported member 'TransformOptions'.
node_modules/@rolldown/plugin-babel/dist/babelCompat-ag_0o5ff.d.mts(14,30): error TS2724: '…/@babel/core/lib/index' has no exported member named 'BabelFileResult'. Did you mean 'FileResult'?
```

The Babel 8 folder also installs `@types/convert-source-map`. Without it, Babel 8's own declarations report an unrelated `TS7016` for `convert-source-map`.

## Cause

`packages/babel/src/babelCompat.ts` puts `// @ts-ignore` above each alias of a name that exists in only one Babel major. The emitted `.d.mts` drops those comments.
