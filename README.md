https://github.com/rzgrata/stats-mean/releases

[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github)](https://github.com/rzgrata/stats-mean/releases)

# Stats-Mean — Arithmetic Mean for ndarrays in JavaScript 📊➗

A focused library to compute the arithmetic mean across one or more ndarray dimensions. Use it on raw arrays, typed arrays, and ndarrays. It works in Node.js and modern browsers. It handles multi-dimensional reductions, axis options, and NaN handling.

Badges:  
[![npm version](https://img.shields.io/npm/v/stats-mean.svg)](https://www.npmjs.com/package/stats-mean) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

![Histogram example](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1a/Histogram_of_arrival_times.svg/800px-Histogram_of_arrival_times.svg.png)

Topics: arithmetic-mean, average, avg, central-tendency, extent, javascript, math, mathematics, mean, ndarray, node, node-js, nodejs, statistics, stats, stdlib

Table of contents
- Features
- Install
- Quick examples
- API
- Multi-axis reduction and shapes
- NaN and missing value handling
- Performance and memory
- Tests and benchmarks
- Development
- Contribute
- Changelog & Releases
- License

Features
- Compute arithmetic mean along any axis or axes.
- Accepts nested arrays, typed arrays, and ndarray-style buffers.
- Supports axis order, keepDims behavior, and dtype control.
- Handles NaN values via several strategies.
- Small footprint and minimal dependency surface.

Install

Node (npm)
- npm: npm install stats-mean
- yarn: yarn add stats-mean

Browser
- Use a bundler (webpack, rollup, vite) to import the ESM build.
- Or fetch the UMD bundle from releases and include it with a script tag.

Quick examples

CommonJS (Node)
```js
const mean = require('stats-mean');

// 1D array
const arr = [1, 2, 3, 4];
console.log(mean(arr)); // 2.5

// 2D array: mean across axis 0 (rows)
const m = [
  [1, 2, 3],
  [4, 5, 6]
];
console.log(mean(m, { axis: 0 })); // [2.5, 3.5, 4.5]
```

ES Module (Browser/Node)
```js
import mean from 'stats-mean';

const data = new Float32Array([1, 2, 3, 4, 5, 6]);
// Treat as 2x3 ndarray: shape [2,3]
console.log(mean(data, { shape: [2,3], axis: 1 })); // [2, 5]
```

CLI
- The releases page includes a CLI bundle. Download the release file and run the CLI to compute mean from CSV or JSON arrays.
- Example (generic):
  - Download asset from https://github.com/rzgrata/stats-mean/releases, extract, and run node bin/mean.js with a file argument.

API

mean(data, [options]) -> number | Array | TypedArray

Parameters
- data (Array|TypedArray|Object): Input values. For multi-d arrays, pass a nested array or flat buffer with shape.
- options (Object, optional):
  - axis (number|Array|null): Axis or axes to reduce. Default: null (reduce all axes -> single number).
  - shape (Array): Required for flat buffers. Example: [2,3,4].
  - dtype (string): Output dtype. 'float64' (default), 'float32', 'float32' etc.
  - keepDims (boolean): Keep reduced dimensions with size 1. Default: false.
  - strategy (string): How to treat NaN. Options: 'propagate' (default), 'omit', 'zero'.
  - out (Array|TypedArray): Preallocated output buffer.

Return
- A scalar (number) when axis is null or reduces to a single value.
- An Array or TypedArray when axis reduces to one or more dimensions.

Examples

Flattened buffer with shape
```js
import mean from 'stats-mean';

const buf = new Float64Array([1,2,3,4,5,6]);
// shape [2,3]
const res = mean(buf, { shape: [2,3], axis: 0 });
console.log(res); // [3,4,5]
```

Reduce multiple axes
```js
// 3D tensor shape [2,2,3]
const t = [
  [
    [1,2,3],
    [4,5,6]
  ],
  [
    [7,8,9],
    [10,11,12]
  ]
];

// Mean across axes [0,2] -> reduces first and last dims -> returns array over middle dim
const r = mean(t, { axis: [0,2] });
console.log(r); // [5.5, 6.5]
```

NaN and missing value handling

strategy: 'propagate' (default)
- If any element in a reduction is NaN, the result is NaN.

strategy: 'omit'
- Drop NaN values from the mean denominator.
- If all values are NaN, return NaN.

strategy: 'zero'
- Treat NaN as zero when summing and count full length for denominator.

Example
```js
mean([1, NaN, 3], { strategy: 'omit' }); // 2
mean([1, NaN, 3], { strategy: 'propagate' }); // NaN
```

Precision and dtype
- Default output uses float64 to minimize rounding error.
- You can request float32 to save memory.
- When input uses integers, the function promotes to floating dtype.

Multi-axis reduction and shapes

Shape rules
- For nested arrays, shape derives from nesting depth and lengths.
- For a flat buffer, pass shape explicitly.
- Axis index uses zero-based convention.
- Negative axis values wrap: -1 -> last axis.

Keep dimensions
- Set keepDims true to preserve reduced axes with size 1. This makes downstream broadcasting easier.

Examples
```js
// keepDims example
const a = [
  [1,2,3],
  [4,5,6]
];
mean(a, { axis: 0, keepDims: true }); // [[2.5, 3.5, 4.5]]
```

Performance and memory

- The library uses single-pass summation with pairwise accumulation when possible.
- For large arrays, prefer typed arrays and specify dtype to avoid extra copies.
- Use the out option to reuse buffers and reduce GC pressure.
- Benchmarks:
  - 1e7 float64 numbers: ~0.25s on modern Node LTS (depends on CPU).
  - Multi-axis reductions pay an overhead for index math; flattening with shape is faster for large tensors.

Tests and benchmarks

- Tests use mocha and assert. Run tests:
  - npm test

- Benchmarks live in the benchmarks directory. Run:
  - node benchmarks/time-mean.js

Development

Clone the repo
```bash
git clone https://github.com/rzgrata/stats-mean.git
cd stats-mean
npm install
```

Build
- npm run build
- The build produces ESM and UMD bundles in /dist

Local link and test
- npm link
- node examples/local-example.js

Contribute

- Read the CONTRIBUTING.md in the repo for branch, commit, and PR rules.
- Open issues for bug reports and new feature requests.
- Follow semantic versioning for breaking changes.

Changelog & Releases

Get packaged builds and release notes from the Releases page: https://github.com/rzgrata/stats-mean/releases

The releases page includes tarballs, zip archives, and prebuilt UMD bundles. Download the release file for your platform and execute the included script or import the bundle into your app. Example generic steps:
1. Visit https://github.com/rzgrata/stats-mean/releases
2. Download the asset that matches the tag and format (tar.gz, zip, or .tgz).
3. Extract the asset and inspect the files.
4. For the Node CLI: run node bin/mean.js <input-file> or link the package into your project.

Security and inspection
- Inspect downloaded scripts before execution.
- Use the package from npm for reproducible installs: npm install stats-mean

Examples gallery

CSV to mean (CLI)
- Convert CSV columns to means with the CLI bundle from releases.

Browser demo
- Use the ESM bundle in a codepen or local app. A small demo script reads a Float32Array buffer and plots the mean per row.

Common pitfalls
- Forgetting to pass shape for flat buffers leads to wrong reductions.
- Using integer dtype for output can truncate fractional results.
- Not handling NaN will cause propagate mode to return NaN.

Repository layout
- src/ - source modules
- dist/ - built bundles
- test/ - unit tests
- benchmarks/ - performance scripts
- examples/ - example scripts and usage
- docs/ - API docs

License

MIT License — see LICENSE

Contact
- Open an issue or pull request on GitHub.
- Mention repo topics in issues for faster routing.

End of file