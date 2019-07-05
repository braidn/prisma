# Code generation & Node.js setup

## TLDR

Photon JS is generated into `node_modules/@generated` by default. While this approach has a number of [benefits](#), it is also unconventional and can be a source confusion for developers new to Photon JS.

Using `node_modules/@generated` as the default `output` for Photon JS is still experimental. Please share your feedback and tell us whether you think this is a good idea or any other thoughts you have on this topic by joining the [dicussion on GitHub](https://github.com/prisma/prisma2/issues/83).

## Overview

`prisma2 generate` uses the [generators](https://github.com/prisma/prisma2/blob/master/docs/prisma-schema-file.md#generators-optional) specified in the [Prisma schema file](../prisma-schema-file.md) and generates the respective packages on the respective output path(s).

The default Photon JS generator can be specified as follows in your schema file:

```prisma
generator photonjs {
  provider = "photonjs"
}
```

Note that this is equivalent to specifying the default `output` path:

```prisma
generator photonjs {
  provider = "photonjs"
  output   = "node_modules/@generated/photon"
}
```

When running `prisma2 generate` for either of these schema files, the `photon` package will be located in:

```
node_modules/@generated/photon
```

## Why is Photon JS generated into `node_modules/@generated` by default?

### Photon JS should be viewed as an npm package

Node.js libraries are typically installed as npm dependencies using `npm install`. The respective packages are then located inside the [`node_modules`](https://docs.npmjs.com/files/folders#node-modules) directory from where they can be easily imported into application code.

Because Photon JS is a custom API for _your_ specific database setup, it can't follow that model. It needs to be generated locally instead of being installed from a central repository like npm. However, the mental model for Photon JS should still be that of an npm dependency.

### Easy imports

By generating Photon JS into `node_modules/@generated`, you can easily import it into your code:

```js
import Photon from '@generated/photon'
```

or

```js
const Photon = require('@generated/photon')
```

### Keeping the query engine binary out of version control by default

Photon JS is based on a query engine that's running as a binary alongside your application. This binary is downloaded when `prisma2 generate` is invoked initially and stored in the `output` path (right next to the generated Photon API).

By generating Photon JS into `node_modules`, the query engine is kept out of version control by default (since `node_modules` is typically ignored for version control). If it was not generated into `node_modules`, then developers would need to explicitly ignore it, e.g. for Git they'd need to add the `output` path to `.gitignore`.