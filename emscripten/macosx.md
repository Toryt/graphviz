The intention of this story is to get done what [viz.js] does in a more controlled way.

autogen
=======

[viz.js] downloads a [graphviz] distrubution tarbal, patches it, and `make`s that.
The distribution tarbal is different from [the source in the repository][graphviz repo]: the release process
that generates the tarbal generates some additional files, and patches some other files.
This is described in the [build documentation](../doc/build.html) ( _Abbreviated Build Instructions_ ):
before we can do a build from the repository, we have to excute `./autogen.sh`.

This requires `libtool`, `automake`, and `autoconf`.

I am working on Mac OS X Yosemite 10.10.2, with the Developer Tools installed and up to date.

Extra Tools
-----------

On Mac OS X `automake` and `autoconf` are not installed by default, and the `libtool` we find turns
out not te be the `libtool` your are looking for:

    KOSTUNRIX:graphviz jand$ which libtool
    /usr/bin/libtool
    KOSTUNRIX:graphviz jand$ which automake
    KOSTUNRIX:graphviz jand$ which autoconf

### [Homebrew]

To get these development tools, I highly suggest you use [Homebrew]. If you haven't installed it yet,
[please do][Homebrew install], and then come back here.


### `automake` and `autoconf`

Next, I installed `automake`. This also installs `autoconf`:

    KOSTUNRIX:graphviz jand$ su jdadmin
    Password:
    KOSTUNRIX:graphviz jdadmin$ brew install automake
    ==> Installing automake dependency: autoconf
    ==> Downloading https://homebrew.bintray.com/bottles/autoconf-2.69.yosemite.bottle.1.tar.gz
    ######################################################################## 100.0%
    ==> Pouring autoconf-2.69.yosemite.bottle.1.tar.gz
    🍺  /usr/local/Cellar/autoconf/2.69: 70 files, 3.1M
    ==> Installing automake
    ==> Downloading https://homebrew.bintray.com/bottles/automake-1.15.yosemite.bottle.tar.gz
    ######################################################################## 100.0%
    ==> Pouring automake-1.15.yosemite.bottle.tar.gz
    🍺  /usr/local/Cellar/automake/1.15: 130 files, 3.2M
    KOSTUNRIX:graphviz jdadmin$ exit
    KOSTUNRIX:graphviz jand$ which automake
    /usr/local/bin/automake
    KOSTUNRIX:graphviz jand$ which autoconf
    /usr/local/bin/autoconf

Sadly, when we now run `./autogen.sh`, it fails -- that's how I found out that one `libtool` ain't the other:

    KOSTUNRIX:graphviz jand$ ./autogen.sh
    autoreconf: Entering directory `.'
    autoreconf: configure.ac: not using Gettext
    autoreconf: running: aclocal --force -I m4
    autoreconf: configure.ac: tracing
    autoreconf: configure.ac: not using Libtool
    autoreconf: running: /usr/local/Cellar/autoconf/2.69/bin/autoconf --force
    configure.ac:33: error: possibly undefined macro: AC_SUBST
          If this token and others are legitimate, please use m4_pattern_allow.
          See the Autoconf documentation.
    configure.ac:58: error: possibly undefined macro: AC_DEFINE_UNQUOTED
    configure.ac:238: error: possibly undefined macro: AC_ENABLE_STATIC
    configure.ac:241: error: possibly undefined macro: AC_DISABLE_STATIC
    configure.ac:249: error: possibly undefined macro: AC_ENABLE_SHARED
    configure.ac:259: error: possibly undefined macro: AC_DISABLE_SHARED
    configure.ac:272: error: possibly undefined macro: AC_PROG_LIBTOOL
    configure.ac:278: error: possibly undefined macro: AC_CHECK_PROG
    configure.ac:462: error: possibly undefined macro: AC_CHECK_FUNCS
    configure.ac:471: error: possibly undefined macro: AC_CHECK_HEADER
    configure.ac:475: error: possibly undefined macro: AC_MSG_WARN
    autoreconf: /usr/local/Cellar/autoconf/2.69/bin/autoconf failed with exit status: 1
    KOSTUNRIX:graphviz jand$ which libtool
    /usr/bin/libtool
    KOSTUNRIX:graphviz jand$ libtool --version
    error: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/libtool: unknown option character `-' in: --version
    Usage: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/libtool -static [-] file [...] [-filelist listfile[,dirname]] [-arch_only arch] [-sacLT] [-no_warning_for_no_symbols]
    Usage: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/libtool -dynamic [-] file [...] [-filelist listfile[,dirname]] [-arch_only arch] [-o output] [-install_name name] [-compatibility_version #] [-current_version #] [-seg1addr 0x#] [-segs_read_only_addr 0x#] [-segs_read_write_addr 0x#] [-seg_addr_table <filename>] [-seg_addr_table_filename <file_system_path>] [-all_load] [-noall_load]

Note that the output says `not using Libtool`.

### libtool

After some searching, I found out that this is a different `libtool` than expected. [Homebrew] to the rescue:

    KOSTUNRIX:graphviz jand$ su jdadmin
    Password:
    KOSTUNRIX:graphviz jdadmin$ brew install libtool
    ==> Downloading https://homebrew.bintray.com/bottles/libtool-2.4.6.yosemite.bottle.tar.gz
    ######################################################################## 100.0%
    ==> Pouring libtool-2.4.6.yosemite.bottle.tar.gz
    ==> Caveats
    In order to prevent conflicts with Apple's own libtool we have prepended a "g"
    so, you have instead: glibtool and glibtoolize.
    ==> Summary
    🍺  /usr/local/Cellar/libtool/2.4.6: 69 files, 3.8M
    KOSTUNRIX:graphviz jdadmin$ exit

Note that we now have installed `glibtool`, not `libtool`. But no problem:

    KOSTUNRIX:graphviz jand$ ./autogen.sh
    autoreconf: Entering directory `.'
    autoreconf: configure.ac: not using Gettext
    autoreconf: running: aclocal --force -I m4
    autoreconf: configure.ac: tracing
    autoreconf: configure.ac: adding subdirectory libltdl to autoreconf
    autoreconf: Entering directory `libltdl'
    autoreconf: running: aclocal --force -I ../m4
    autoreconf: running: glibtoolize --copy --force --ltdl
    glibtoolize: putting auxiliary files in AC_CONFIG_AUX_DIR, '../config'.
    glibtoolize: copying file '../config/compile'
    glibtoolize: copying file '../config/config.guess'
    glibtoolize: copying file '../config/config.sub'
    glibtoolize: copying file '../config/depcomp'
    ...

The [graphviz] build proces is intelligent, and uses `glibtool`. However, after while we get:

    ...
    glibtoolize: copying file 'libltdl/ltdl.c'
    glibtoolize: copying file 'libltdl/ltdl.h'
    glibtoolize: copying file 'libltdl/slist.c'
    configure.ac:33: error: possibly undefined macro: AC_SUBST
          If this token and others are legitimate, please use m4_pattern_allow.
          See the Autoconf documentation.
    configure.ac:58: error: possibly undefined macro: AC_DEFINE_UNQUOTED
    configure.ac:278: error: possibly undefined macro: AC_CHECK_PROG
    configure.ac:462: error: possibly undefined macro: AC_CHECK_FUNCS
    configure.ac:471: error: possibly undefined macro: AC_CHECK_HEADER
    configure.ac:475: error: possibly undefined macro: AC_MSG_WARN
    autoreconf: /usr/local/Cellar/autoconf/2.69/bin/autoconf failed with exit status: 1

Something's wrong.

### pkgconfig

It took some searching, and I don't know why, but if you also install `pkgconfig`
we get joy:

    KOSTUNRIX:graphviz jand$ su jdadmin
    Password:
    KOSTUNRIX:graphviz jdadmin$ brew install pkgconfig
    ==> Downloading https://homebrew.bintray.com/bottles/pkg-config-0.28.yosemite.bottle.2.tar.gz
    ######################################################################## 100.0%
    ==> Pouring pkg-config-0.28.yosemite.bottle.2.tar.gz
    🍺  /usr/local/Cellar/pkg-config/0.28: 10 files, 612K
    KOSTUNRIX:graphviz jdadmin$ exit
    KOSTUNRIX:graphviz jand$ ./autogen.sh
    autoreconf: Entering directory `.'
    autoreconf: configure.ac: not using Gettext
    autoreconf: running: aclocal --force -I m4
    autoreconf: configure.ac: tracing
    autoreconf: configure.ac: adding subdirectory libltdl to autoreconf
    autoreconf: Entering directory `libltdl'
    autoreconf: running: aclocal --force -I ../m4
    autoreconf: running: glibtoolize --copy --force --ltdl
    glibtoolize: putting auxiliary files in AC_CONFIG_AUX_DIR, '../config'.
    glibtoolize: copying file '../config/compile'
    glibtoolize: copying file '../config/config.guess'
    glibtoolize: copying file '../config/config.sub'

    ...

    checking for strlcat... (cached) yes
    checking for strlcpy... (cached) yes
    configure: updating cache ../config.cache
    checking that generated files are newer than configure... done
    configure: creating ./config.status
    config.status: creating Makefile
    config.status: creating config.h
    config.status: config.h is unchanged
    config.status: executing depfiles commands
    config.status: executing libtool commands

    ----------------------------------------------------------------

    graphviz-2.36.0 will be compiled with the following:

    options:
      cgraph:
      digcola:       Yes
      expat:         Yes
      fontconfig:    No (missing fontconfig-config)
      freetype:      No (missing freetype-config)
      glut:          No (missing GL/glut.h)
      ann:           No (ANN library not available)
      gts:           No (gts library not available)
      ipsepcola:     No (disabled by default - C++ portability issues)
      ltdl:          Yes
      ortho:         Yes
      sfdp:          Yes
      shared:        Yes
      static:        No (disabled by default)
      qt:            No (qmake not found)
      x:             No (disabled or unavailable)

    commands:
      dot:           Yes (always enabled)
      neato:         Yes (always enabled)
      fdp:           Yes (always enabled)
      circo:         Yes (always enabled)
      twopi:         Yes (always enabled)
      gvpr:          Yes (always enabled)
      gvmap:         Yes (always enabled)
      lefty:         No (missing Xaw headers)
      smyrna:        No (disabled by default - experimental)
      gvedit:        No (qmake not found)

    plugin libraries:
      dot_layout:    Yes (always enabled)
      neato_layout:  Yes (always enabled)
      core:          Yes (always enabled)
      devil:         No (missing library)
      gd:            No (gd headers not found)
      gdiplus:       No (disabled by default - Windows only)
      gdk:
      gdk_pixbuf:    No (gdk_pixbuf library not available)
      ghostscript:   No (missing Xrender)
      glitz:         No (disabled by default - incomplete)
      gtk:           No (gtk library not available)
      lasi:          No (missing pangocairo support)
      ming:          No (disabled by default - incomplete)
      pangocairo:    No (pangocairo library not available)
      poppler:       No (poppler library not available)
      quartz:        No (disabled by default - Mac only)
      rsvg:          No (rsvg library not available)
      visio:         No (disabled by default - experimental)
      webp:          No (disabled by default - experimental)
      xlib:          No (disabled or unavailable)

    language extensions:
      gv_sharp:      No (swig not available)
      gv_go:         No (disabled by default - experimental)
      gv_guile:      No (swig not available)
      gv_io:         No (disabled by default - no swig support yet)
      gv_java:       No (swig not available)
      gv_lua:        No (swig not available)
      gv_ocaml:      No (swig not available)
      gv_perl:       No (swig not available)
      gv_php:        No (swig not available)
      gv_python:     No (swig not available)
      gv_python23:   No (disabled by default - for multiversion installs)
      gv_python24:   No (disabled by default - for multiversion installs)
      gv_python25:   No (disabled by default - for multiversion installs)
      gv_python26:   No (disabled by default - for multiversion installs)
      gv_python27:   No (disabled by default - for multiversion installs)
      gv_R:          No (swig not available)
      gv_ruby:       No (swig not available)
      gv_tcl:        No (tcl not available)

      tcldot:        No (tcl not available)
      tclpathplan:   No (tcl not available)
      gdtclft:       No (tcl not available)
      tkspline:      No (tk not available)

The output ends with a report of all the different options we are now setup for.
Maybe someday JavaScript will be in this list too ... :-).


[graphviz]: http://www.graphviz.org
[graphviz repo]: https://github.com/ellson/graphviz
[viz.js]: https://github.com/mdaines/viz.js/
[Homebrew]: http://brew.sh
[Homebrew install]: https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Installation.md#installation
