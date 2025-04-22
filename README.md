<!--

@license Apache-2.0

Copyright (c) 2025 The Stdlib Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

-->


<details>
  <summary>
    About stdlib...
  </summary>
  <p>We believe in a future in which the web is a preferred environment for numerical computation. To help realize this future, we've built stdlib. stdlib is a standard library, with an emphasis on numerical and scientific computation, written in JavaScript (and C) for execution in browsers and in Node.js.</p>
  <p>The library is fully decomposable, being architected in such a way that you can swap out and mix and match APIs and functionality to cater to your exact preferences and use cases.</p>
  <p>When you use stdlib, you can be absolutely certain that you are using the most thorough, rigorous, well-written, studied, documented, tested, measured, and high-quality code out there.</p>
  <p>To join us in bringing numerical computing to the web, get started by checking us out on <a href="https://github.com/stdlib-js/stdlib">GitHub</a>, and please consider <a href="https://opencollective.com/stdlib">financially supporting stdlib</a>. We greatly appreciate your continued support!</p>
</details>

# unaryStrided1d

[![NPM version][npm-image]][npm-url] [![Build Status][test-image]][test-url] [![Coverage Status][coverage-image]][coverage-url] <!-- [![dependencies][dependencies-image]][dependencies-url] -->

> Apply a one-dimensional strided array function to a list of specified dimensions in an input ndarray and assign results to a provided output ndarray.

<section class="intro">

</section>

<!-- /.intro -->

<section class="installation">

## Installation

```bash
npm install @stdlib/ndarray-base-unary-strided1d
```

Alternatively,

-   To load the package in a website via a `script` tag without installation and bundlers, use the [ES Module][es-module] available on the [`esm`][esm-url] branch (see [README][esm-readme]).
-   If you are using Deno, visit the [`deno`][deno-url] branch (see [README][deno-readme] for usage intructions).
-   For use in Observable, or in browser/node environments, use the [Universal Module Definition (UMD)][umd] build available on the [`umd`][umd-url] branch (see [README][umd-readme]).

The [branches.md][branches-url] file summarizes the available branches and displays a diagram illustrating their relationships.

To view installation and usage instructions specific to each branch build, be sure to explicitly navigate to the respective README files on each branch, as linked to above.

</section>

<section class="usage">

## Usage

```javascript
var unaryStrided1d = require( '@stdlib/ndarray-base-unary-strided1d' );
```

#### unaryStrided1d( fcn, arrays, dims\[, options] )

Applies a one-dimensional strided array function to a list of specified dimensions in an input ndarray and assigns results to a provided output ndarray.

<!-- eslint-disable max-len -->

```javascript
var Float64Array = require( '@stdlib/array-float64' );
var ndarray2array = require( '@stdlib/ndarray-base-to-array' );
var getStride = require( '@stdlib/ndarray-base-stride' );
var getOffset = require( '@stdlib/ndarray-base-offset' );
var getData = require( '@stdlib/ndarray-base-data-buffer' );
var numelDimension = require( '@stdlib/ndarray-base-numel-dimension' );
var gcusum = require( '@stdlib/blas-ext-base-gcusum' ).ndarray;

function wrapper( arrays ) {
    var x = arrays[ 0 ];
    var y = arrays[ 1 ];
    var s = arrays[ 2 ];
    return gcusum( numelDimension( x, 0 ), getData( s )[ getOffset( s ) ], getData( x ), getStride( x, 0 ), getOffset( x ), getData( y ), getStride( y, 0 ), getOffset( y ) );
}

// Create data buffers:
var xbuf = new Float64Array( [ 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0, 10.0, 11.0, 12.0 ] );
var ybuf = new Float64Array( [ 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0 ] );

// Define the array shapes:
var xsh = [ 1, 3, 2, 2 ];
var ysh = [ 1, 3, 2, 2 ];

// Define the array strides:
var sx = [ 12, 4, 2, 1 ];
var sy = [ 12, 4, 2, 1 ];

// Define the index offsets:
var ox = 0;
var oy = 0;

// Create an input ndarray-like object:
var x = {
    'dtype': 'float64',
    'data': xbuf,
    'shape': xsh,
    'strides': sx,
    'offset': ox,
    'order': 'row-major'
};

// Create an ndarray-like object for the initial sum:
var initial = {
    'dtype': 'float64',
    'data': new Float64Array( [ 0.0 ] ),
    'shape': [ 1, 3 ],
    'strides': [ 0, 0 ],
    'offset': 0,
    'order': 'row-major'
};

// Create an output ndarray-like object:
var y = {
    'dtype': 'float64',
    'data': ybuf,
    'shape': ysh,
    'strides': sy,
    'offset': oy,
    'order': 'row-major'
};

// Apply strided function:
unaryStrided1d( wrapper, [ x, y, initial ], [ 2, 3 ] );

var arr = ndarray2array( y.data, y.shape, y.strides, y.offset, y.order );
// returns [ [ [ [ 1.0, 3.0 ], [ 6.0, 10.0 ] ], [ [ 5.0, 11.0 ], [ 18.0, 26.0 ] ], [ [ 9.0, 19.0 ], [ 30.0, 42.0 ] ] ] ]
```

The function accepts the following arguments:

-   **fcn**: function which will be applied to a one-dimensional input subarray and should update a one-dimensional output subarray with results.
-   **arrays**: array-like object containing one input ndarray and one output ndarray, followed by any additional ndarray arguments.
-   **dims**: list of dimensions to which to apply a strided array function.
-   **options**: function options which are passed through to `fcn` (_optional_).

Each provided ndarray should be an object with the following properties:

-   **dtype**: data type.
-   **data**: data buffer.
-   **shape**: dimensions.
-   **strides**: stride lengths.
-   **offset**: index offset.
-   **order**: specifies whether an ndarray is row-major (C-style) or column major (Fortran-style).

#### TODO: document factory method

</section>

<!-- /.usage -->

<section class="notes">

## Notes

-   Any additional ndarray arguments are expected to have the same dimensions as the loop dimensions of the input ndarray. When calling the strided array function, any additional ndarray arguments are provided as zero-dimensional ndarray-like objects.

-   The strided array function is expected to have the following signature:

    ```text
    fcn( arrays[, options] )
    ```

    where

    -   **arrays**: array containing a one-dimensional subarray of the input ndarray, a one-dimensional subarray of the output ndarray, and any additional ndarray arguments as zero-dimensional ndarrays.
    -   **options**: function options (_optional_).

-   For very high-dimensional ndarrays which are non-contiguous, one should consider copying the underlying data to contiguous memory before performing a reduction in order to achieve better performance.

</section>

<!-- /.notes -->

<section class="examples">

## Examples

<!-- eslint-disable max-len -->

<!-- eslint no-undef: "error" -->

```javascript
var discreteUniform = require( '@stdlib/random-array-discrete-uniform' );
var zeros = require( '@stdlib/array-base-zeros' );
var ndarray2array = require( '@stdlib/ndarray-base-to-array' );
var numelDimension = require( '@stdlib/ndarray-base-numel-dimension' );
var getData = require( '@stdlib/ndarray-base-data-buffer' );
var getStride = require( '@stdlib/ndarray-base-stride' );
var getOffset = require( '@stdlib/ndarray-base-offset' );
var gcusum = require( '@stdlib/blas-ext-base-gcusum' ).ndarray;
var unaryStrided1d = require( '@stdlib/ndarray-base-unary-strided1d' );

function wrapper( arrays ) {
    var x = arrays[ 0 ];
    var y = arrays[ 1 ];
    var s = arrays[ 2 ];
    return gcusum( numelDimension( x, 0 ), getData( s )[ getOffset( s ) ], getData( x ), getStride( x, 0 ), getOffset( x ), getData( y ), getStride( y, 0 ), getOffset( y ) );
}

var N = 10;
var x = {
    'dtype': 'generic',
    'data': discreteUniform( N, -5, 5, {
        'dtype': 'generic'
    }),
    'shape': [ 1, 5, 2 ],
    'strides': [ 10, 2, 1 ],
    'offset': 0,
    'order': 'row-major'
};
var initial = {
    'dtype': 'generic',
    'data': [ 0.0 ],
    'shape': [ 1, 2 ],
    'strides': [ 0, 0 ],
    'offset': 0,
    'order': 'row-major'
};
var y = {
    'dtype': 'generic',
    'data': zeros( N ),
    'shape': [ 1, 5, 2 ],
    'strides': [ 10, 2, 1 ],
    'offset': 0,
    'order': 'row-major'
};

unaryStrided1d( wrapper, [ x, y, initial ], [ 1 ] );

console.log( ndarray2array( x.data, x.shape, x.strides, x.offset, x.order ) );
console.log( ndarray2array( y.data, y.shape, y.strides, y.offset, y.order ) );
```

</section>

<!-- /.examples -->

<!-- Section for related `stdlib` packages. Do not manually edit this section, as it is automatically populated. -->

<section class="related">

</section>

<!-- /.related -->


<section class="main-repo" >

* * *

## Notice

This package is part of [stdlib][stdlib], a standard library for JavaScript and Node.js, with an emphasis on numerical and scientific computing. The library provides a collection of robust, high performance libraries for mathematics, statistics, streams, utilities, and more.

For more information on the project, filing bug reports and feature requests, and guidance on how to develop [stdlib][stdlib], see the main project [repository][stdlib].

#### Community

[![Chat][chat-image]][chat-url]

---

## License

See [LICENSE][stdlib-license].


## Copyright

Copyright &copy; 2016-2025. The Stdlib [Authors][stdlib-authors].

</section>

<!-- /.stdlib -->

<!-- Section for all links. Make sure to keep an empty line after the `section` element and another before the `/section` close. -->

<section class="links">

[npm-image]: http://img.shields.io/npm/v/@stdlib/ndarray-base-unary-strided1d.svg
[npm-url]: https://npmjs.org/package/@stdlib/ndarray-base-unary-strided1d

[test-image]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/actions/workflows/test.yml/badge.svg?branch=main
[test-url]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/actions/workflows/test.yml?query=branch:main

[coverage-image]: https://img.shields.io/codecov/c/github/stdlib-js/ndarray-base-unary-strided1d/main.svg
[coverage-url]: https://codecov.io/github/stdlib-js/ndarray-base-unary-strided1d?branch=main

<!--

[dependencies-image]: https://img.shields.io/david/stdlib-js/ndarray-base-unary-strided1d.svg
[dependencies-url]: https://david-dm.org/stdlib-js/ndarray-base-unary-strided1d/main

-->

[chat-image]: https://img.shields.io/gitter/room/stdlib-js/stdlib.svg
[chat-url]: https://app.gitter.im/#/room/#stdlib-js_stdlib:gitter.im

[stdlib]: https://github.com/stdlib-js/stdlib

[stdlib-authors]: https://github.com/stdlib-js/stdlib/graphs/contributors

[umd]: https://github.com/umdjs/umd
[es-module]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules

[deno-url]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/tree/deno
[deno-readme]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/blob/deno/README.md
[umd-url]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/tree/umd
[umd-readme]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/blob/umd/README.md
[esm-url]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/tree/esm
[esm-readme]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/blob/esm/README.md
[branches-url]: https://github.com/stdlib-js/ndarray-base-unary-strided1d/blob/main/branches.md

[stdlib-license]: https://raw.githubusercontent.com/stdlib-js/ndarray-base-unary-strided1d/main/LICENSE

</section>

<!-- /.links -->
