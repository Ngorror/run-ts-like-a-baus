How to run and debug typescript with node in XXI century

## The requirements

Here is your `package.json` and the minimum version of node is 18.11.0 so you can use the new [--watch](https://nodejs.org/dist/latest-v18.x/docs/api/cli.html#--watch) feature.

I just took the latest `ts-node` and `typescript`. I will leave to you to experiment with earlier versions

```json
{
  "author": "ngorror",
  "dependencies": {},
  "description": "My interpretation of how to run TypeScript in XXI century :)",
  "devDependencies": {
    "ts-node": "^10.9.1",
    "typescript": "^4.8.4"
  },
  "engines": {
    "node": ">=18.11.0",
    "npm": ">=8.19.2"
  },
  "homepage": "https://github.com/Ngorror/run-ts-like-a-baus",
  "keywords": ["debug", "typescript", "watch"],
  "license": "ISC",
  "main": "index.js",
  "name": "run-ts-like-a-baus",
  "scripts": {
    "build": "rm -f -R build && tsc -p tsconfig.build.json",  
    "start": "node --no-warnings --loader ts-node/esm ./src/index.ts",
    "start:production": "node build/index.js",
    "start:transpile-only": "node --no-warnings --loader ts-node/esm/transpile-only ./src/index.ts",
    "watch": "node --no-warnings --watch --loader ts-node/esm ./src/index.ts"
  },
  "type": "module",
  "version": "1.0.0"
}
```

## Configure your TS with the latest and greatest ESNext

All credits for this config goes to [@benawad](https://twitter.com/benawad). I've just pumped the ES to the Next level (pun intended)

```json
{
  "compilerOptions": {
    "allowSyntheticDefaultImports": true,
    "baseUrl": ".",
    "emitDecoratorMetadata": true,
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "lib": ["DOM", "ESNext"],
    "module": "ESNext",
    "moduleResolution": "NodeNext",
    "noFallthroughCasesInSwitch": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "outDir": "./dist",
    "removeComments": true,
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "sourceMap": true,
    "strictFunctionTypes": true,
    "strictNullChecks": true,
    "target": "ESNext"
  },
  "exclude": ["node_modules"],
  "include": ["./src/**/*.ts"]
}
```

## Configure your package.json `script: { start }` command

The inspiration to spend time rediscovering the weel/hot water/toilet paper came from [@wesbos](https://twitter.com/Ngorror/status/1580956521290035200)

```properties
node --no-warnings --watch --loader ts-node/esm ./src/index.ts
```

## How to debug in `vscode`?

Here is the right configuration of your `.vscode/launch.json` file

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug TypeScript",
      "program": "${workspaceFolder}/src/index.ts",
      "request": "launch",
      "runtimeArgs": ["--loader", "ts-node/esm"],
      // if you are fan of nvm you might need to specify the following runtimeExecutable path
      // to get the value on linux, run `which node` in your bash terminal
      "runtimeExecutable": "/home/user/.nvm/versions/node/v18.11.0/bin/node",
      "skipFiles": ["<node_internals>/**"],
      "type": "node"
    }
  ]
}
```

## Huston we have a problem !

`ERR_MODULE_NOT_FOUND, file extensions in imports using ES modules`

You need to cheat by adding `.js` extension in the `import` clause for all your `.ts` files. Yep, it sounds weird but it works :)

Check the [following link](https://github.com/TypeStrong/ts-node/discussions/1781) for more details.

You don't even need to add `// @ts-ignore` like it was suggested in the link above.

```ts
import { MyVeryNiceTS } from "./this_is_a_real_typescript_file.js";
```
