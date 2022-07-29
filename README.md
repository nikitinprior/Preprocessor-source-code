# Preprocessor-source-code
Mark Ogden version

In reconstructing the C code, the main challenge for me was:

1. adding to the Hi-Tech C compiler v3.09 preprocessor the ability to handle C++-style comments;
2. compatibility with the original preprocessor;
3. Maintaining an acceptable size of the new executable file to be able to work in the CP/M operating system.

I managed to meet these requirements, but in an effort to make the preprocessor program as similar to the original as possible, I kept the errors of the original version in the new preprocessor, although Joerg Schilling corrected them in his update.

Mark Ogden independently of me did the preprocessor program adaptation for Hi-Tech C compiler v3.09. His approach is different from mine. 

As a result, there is now another version of the preprocessor compatible with Hi-Tech C compiler v3.09 for CP/M. This preprocessor can be used in a cross compiler for Linux, MacOS or Windows, as well as in the CP/M operating system.

To compile a full version of the preprocessor that can be used in a cross-compiler it is necessary to execute the command

gcc -O2 -ocpp_new3 cpp.c cpy.c getargs.c fname.c

To create a compact version to run on CP/M, you must use the "#define SMALL" directive. In this case, an executable file will be created that corresponds in its capabilities to the original version of the CPP.COM preprocessor from Hi-Tech C compiler v3.09.

Command

zc3 -O -DSMALL -CPM -o cpp_new3 cpp.c cpy.c getargs.c fname.c

creates an executable file cpp_new3.com for CP/M.

I use a full version of the Mark Ogden preprocessor in the cross-compiler.

Mark accompanied his code with the following description:

The code is largely based on Joerg Schilling’s updates on John Reiser’s original code.

I have done a little more with cpp, including

1.	Replicating the Hi-Tech behaviour of comments within #defines. i.e. discarding them
2.	Removal of pre comment white space as per Hi-Tech behaviour
3.	Fixing the lexer tobinary function
4.	Adding support for unix/windows filenames and also fixing the handling of CP/M names
5.	Retaining only the z80 predefined value
6.	Conditional compilation to build a small aka CP/M build vs. a more extensive cross compiler version that supports a number of additional features
7.	Using INCDIR80 environment variable to set the system include dir on none CP/M systems
8.	Adding #pragma, but just issuing a warning
9.	Adding #error to stop immediately
10.	As you did adding #asm and #endasm
11.	Replacing a couple of functions with standard C versions, which most compilers have builtins for.
12.	Fixing ‘\\’ handling as when emitted was left as a single \, which would leave later passes to handle incorrectly.


The help command is

-help                     (single -)

The full list of commands is

        -C      Pass all comments.
        
        -H      Print the path names of include files on standard error.
        
        -M      Generate a list of dependencies and write them to the output.
        
        -p      Warn of extra tokens in directives.
        
        -P      Do not include line control information in the preprocessor output.
        
        -R      Allow recursive macros.
        
        -noinclude Ignore standard system include path.
        
        -undef  Remove all initially predefined macros.
        
        -Dname  Defines name as 1.
        
        -Dname=var Defines name as val.
        
        -Idirectory Adds directory to the search path.
        
        -Uname  Remove an initial definition of name.
        
        -Ydirectory Uses directory instead of the standard system include directory.
 
Plus additionally

-help and -version
 
The differences between the SMALL and full version are The full version uses different code for buffers and the symbol table to allow programs with larger number of #defines. It also supports larger definitions

Additional command line options

-noinclude will not use the INCDIR80 path

-undef     will remove the z80 predefined

-p         warns of extra tokens in directives

-H         shows the include files on standard error (see below why this is useful)

-M         generates make dependencies for makefile usage

-Y         allows you to specify a system include directory other than through INCDIR80

-U         Remove an initial definition of name (only really applicable for predefined z80)

-help      show help screen

-version   show version info

Of these -noinclude, -p, -U are not overly useful and -undef could be implemented by using #undef z80 in your code Note -Y is different to -I due to the search order for <includes> The use of a single '–' for the longer options is as per the original source, it would be trivial to convert to use '–' which would be more consistent with current usage.
 
You might try using -H to see which file it cannot find.
For example if I try

cpp -H cpp.c >/dev/null

with INCDIR80 set to /mnt/d/local/lib/cpm/include80/

It shows

./cpp.h
        
/mnt/d/local/lib/cpm/include80/ctype.h
        
/mnt/d/local/lib/cpm/include80/limits.h
        
/mnt/d/local/lib/cpm/include80/stdarg.h
        
/mnt/d/local/lib/cpm/include80/stdio.h
        
/mnt/d/local/lib/cpm/include80/stdlib.h
        
/mnt/d/local/lib/cpm/include80/string.h
        
cpp.c: line 94: Can't find include file fcntl.h
        
cpp.c: line 95: Can't find include file unistd.h
        
./version.h
 
Here the two missing files are because I didn’t use -DCPM to indicate that I am doing a build using CP/M include files

Running

cpp -H -DCPM cpp.c >/dev/null

Gives

./cpp.h
        
/mnt/d/local/lib/cpm/include80/ctype.h
        
/mnt/d/local/lib/cpm/include80/limits.h
        
/mnt/d/local/lib/cpm/include80/stdarg.h
        
/mnt/d/local/lib/cpm/include80/stdio.h
        
/mnt/d/local/lib/cpm/include80/stdlib.h
        
/mnt/d/local/lib/cpm/include80/string.h
        
/mnt/d/local/lib/cpm/include80/unixio.h
        
./version.h
        
 
Note no missing files

