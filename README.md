This is a fork of the official [graphviz] project, intended to work on
necessary changes to make it possible to [emscripten] [graphviz], i.e.,
compile it with `emcc`. The original README is [here](README-graphviz.md).

[graphviz] is made available under the [Eclipse Public License
v1.0][EPL]. This is a "derivative work", and thus released under [EPL]
too. The source code is maintained in a [GitHub repository][graphviz repo].

[emscripten] is a "compiler" that "compiles" C code into JavaScript.
[emscripten] is released under the [MIT License].

- Go to the documentation on [building with emscripten](emscripten/index.md).





Why the changes
===============

The work done here is based in [viz.js] by [Mike Daines], in the
context of [graphviz-emscriptened]. In the original project, the source
distribution of version 2.36.0 is downloaded, and patched, before it is
put through `emcc`.

Version 2.36.0 is not the latest version of [graphviz], and the patches
cannot be applied on the current version at the time of writing (2.38.0)
immediately.

This project is intended as a better way to manage this process.

_Frankly, at this time I have no idea what the patches do, and why they
are needed_ , but I assume they are necessary.

By forking [graphviz], and maintaining the changes here, we might be
able to more easily forward port the changes to more recent and future
versions. If this would result in a good and stable way of handling
this, it might be possible to suggest to [graphviz] to incorporate
support for `emcc` in the main project. This would make everything a lot
more simple and maintainable. Seeing how small the patches are, this
might be feasible.





Starting Point
==============

In the original [viz.js] project, all that needs to be done to [graphviz]
is done outside the [graphviz] project structure.

There are 2 steps that are taken:

- patching the [graphviz] code
- building (`make`) the JavaScript distributable

The first step might better be dealed with by having the variant code
inside pragmas in the original code, as is common practice in projects
like [graphviz], for different platforms, processors, compilers, etc.

The latter step just calls `emcc` as compiler, with specific `emcc`
options, and adds a little JavaScript to the build process. This might
better be dealt with by adding a JavaScript build target to the original
`Makefiles`.

The starting point does not cover the full range [graphviz] offers. Only
text-based output is supported. In practice, this means SVG output.
Binary formats, like PNG, are not supported.

[viz.js] does not include automated testing. There are only some
examples of the final product working. The final working product is
build with a lot of warnings and alarming messages. Maybe this can be
ameliorated too.





Humble steps
============

Humble Targets
--------------

This work is approached in humble steps, with small targets.

1. Patches
  1. first, try to get the patches inside the code for version 2.36.0
  2. next, try to forward port the patches to 2.38.0, and possibly the `HEAD` of
     `master`
  3. finally, try to incorporate the patches into the main code using pragmas
2. Make: try to get the way [viz.js] builds its result incorporated in the orginal
   build process

During this process, it probably will be necessary to understand the
test code, and activate it too.

For my immediate need, attaining target 1.1 is sufficient, but does not have any
direct benefit, apart from maintainability. The immediate goal is to attain target 1.2.

Humble Scope
------------

My working environment is Mac OS X, and I have neither the skill set (my C-and-Make
days are over a decade in the past), nor the intention, to make this build work on
all possible platforms. If I get it to work on Mac OS X, my goal is reached. I will
document this specific process. I will not test the effects on other platforms, but
feedback is welcome!





Why would you want to compile [graphviz] to JavaScript in the first place?
==========================================================================

[graphviz], the rendering of graphs, is a rather resource intensive process. So,
why would we want to do this through a slow JavaScript engine? The original code is
optimized to work as efficient as possible on different processors and platforms.
We are throwing this away. The resulting JavaScript file is a 25MB whopper.
Who wants that?

The reasons to do this are

- the popularity of the JavaScript platform,
- the fact that it is the only way to do stuff in a browser, and
- the huge difference in deployment ease-of-use versus a C-based (or Java based, etc.)
  solution on a server.

A pure JavaScript solution can be deployed "with a push" on different PAAS-solutions.
A C-based solutions, like [webdot] requires full control of a server OS, and has
important and complex configuration requirements, like any CGI-solution, with respect
to security.

Furthermore, I strongly believe in the future of JavaScript, because of economic
reasons. Every developer will (need to) know JavaScript, because it is the only language
supported by browsers. This will lead to the application of JavaScript in all other
contexts, regardless of the performance impact and other disadvantages. The cost of
training a developer is much higher than the cost of throwing more hardware at the
problem. This will lead in the near future to even more investment in ameliorating
the disadvantages of JavaScript.





Help
====

Other people might be interested to extend this scope, or help reach to more advanced
targets. Please do. Pull requests are welcomed, and if someone is interested in taking
over the entire project, I would welcome that.





[graphviz-emscripten]: https://github.com/Toryt/graphviz-emscripten
[EPL]: LICENSE
[MIT License]: http://opensource.org/licenses/MIT
[graphviz]: http://www.graphviz.org
[graphviz repo]: https://github.com/ellson/graphviz
[webdot]: https://github.com/ellson/webdot/
[zlib]: http://www.zlib.net
[zlib/libpng license]: http://www.zlib.net/zlib_license.html
[expat]: http://www.jclark.com/xml/expat.html
[viz.js]: https://github.com/mdaines/viz.js/
[liviz.js]: https://github.com/gyuque/livizjs
[Ueyama Satoshi]: https://github.com/gyuque
[Mike Daines]: https://github.com/mdaines
[node.js]: https://nodejs.org
[io.js]: https://iojs.org/en/index.html
[emscripten]: http://kripken.github.io/emscripten-site/
