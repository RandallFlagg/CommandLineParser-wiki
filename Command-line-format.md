You surely have some experience with command line programs. Such programs usually allow the user to set various arguments to alter programs behaviour. The format of the arguments slightly differs from application to application but there are some usual rules that even the `CommandLineParser` library follows. 

There are generally two types of arguments:
 1. *Explicit arguments* that are usually tightly connected to the programs algorithm, function and behavior
 2. *Additional arguments* that are used to pass input data, filenames etc. Value of these arguments can for example contain filenames to process.

## Explicit arguments

The main purpose of CommandLineParser library is to handle explicit arguments. Explicit arguments of the application must be defined by the user of the library. 

There are two basic types of explicit arguments - switch arguments (toggles) and arguments with value. If an argument has a value, the value must be the next word after the argument on the command line. 

Consider a console application *Finder* that prints a set of words as it output and the interface of the application supports removing the duplicate entries. You may want to introduce "distinct" argument for the application. You define an ordinary argument "distinct" with short name d. Then user of the application run the application like this: 
```
Finder.exe -d
```
or
```
Finder.exe --distinct
```

Explicit arguments can have short names (single character) and long names (one word). As you can see, if the argument is used with short name (resp. long name), it must be prefixed with single `-` character (resp. `--` prefix). 

For value arguments the format stays the same and the argument is followed by the value. Consider another argument for *Finder* - how deep it should search the subdirectories. This value would be an integer and would be used in this format (short name of the argument is `s` and long name `subdirectories`): 
```
Finder.exe -s 3
```
or
```
Finder.exe --subdirectories 3
```

For value arguments, someone might prefer separating the value with an equals sign `=` instead of a space. Property `CommandLineParser.AcceptEqualSignSyntaxForValueArguments` controls this behavior. 
```
Finder.exe --subdirectories=3
```
Explicit arguments can be combined and used regardless of order. 

## Additional arguments

Additional arguments provide means to give an application additional input. The values are distinguished by the parser and stored in separate collection. 

Additional arguments are recognized by not having neither `-` nor `--` prefix. First argument without the prefix is considered additional argument and all subsequent arguments as well. 

Fore example on command line 
```
Finder.exe -s 3 --distinct directory1 directory2 directory3
```

Arguments `directory1` `directory2` and `directory3` are additional arguments. The library also gives you an option to specify how many additional arguments are expected, what type should they have etc. See `CommandLineParser.AdditionalArgumentsSettings`.

## Alternative formats 

Some authors prefer a different style. For example, instead prefixing arguments with `-` or `--`, a slash character `/` might be used. This can be controlled by setting CommandLineParser.AcceptSlash and CommandLineParser.AcceptHyphen properties. 

You can also allow grouping of switches (via CommandLineParser.AllowShortSwitchGrouping), eg. instead of 

```
Finder.exe -d -x
```
The user can write 
```
Finder.exe -dx
``` 

You can also specify whether argument names should be case sensitive (CommandLineParser.IgnoreCase)

## Parser settings & properties 

 * `Arguments` - list of arguments accepted by the parser. This can be populated directly by subclasses of `Argument` or it can be obtained by examining an object decorated with `ArgumentAttributes` (see [Attributes and declarative syntax](https://github.com/j-maly/CommandLineParser/wiki/Attributes-&--declarative-syntax))
* `Certifications` - list of argument certifications, see [Combinations of arguments](https://github.com/j-maly/CommandLineParser/wiki/Combinations-of-arguments)) 
* `ShowUsageHeader` - text printed at the beginning of argument usage description displayed to the user of the application 
* `ShowUsageFooter` - text printed at the end of argument usage description displayed to the user of the application 
* `ShowUsageCommands` - if the user supplies one of these values as a command line argument, show usage information will be displayed. Contains `help`, `/?` and `--help` by default. 
* `ShowUsageOnEmptyCommandline` - when set to true, show usage information is printed when the user does not supply any arguments 
* `CheckMandatoryArguments` - when set to true (default) and a mandatory argument is missing on the command line, an exception is thrown
* `CheckArgumentCertifications` - when set to true (default), all `Certifications` must pass during parsing, otherwise an exception is thrown
* `AllowShortSwitchGrouping` - when set to true (default), multiple switch arguments can be collapsed together, e.g `-a -b -c` can be shortened to `-abc`
* `AcceptSlash` - argument start with `/` character, e.g. `/distinct` (default is `true`)
* `AcceptHyphen` - argument start with `-`/`--` character, e.g. `--distinct` (default is `true`)
* `IgnoreCase` - argument names are not case sensitive (default is `false`)
* `AcceptEqualSignSyntaxForValueArguments` - when set to true value arguments are separated by `=` sign instead of a space, e.g. the user should write --level=3 instead of --level 3  (default is `false`)
* `ParsingSucceeded` - will contain `true`/`false` after parsing is finished

Public parser methods: 

* `ParseCommandLine` - starts parsing (arguments must be set up beforehand)
* `ExtractArgumentAttributes` - use this to read ArgumentAttribute declaration from a class (see [Attributes and declarative syntax](https://github.com/j-maly/CommandLineParser/wiki/Attributes-&--declarative-syntax))
* `PrintUsage` - prints usage information to an output writer
* `ShowUsage` - prints usage information to console
* `ShowParsedArguments` - prints information about the parsed arguments (call this after `ParseCommandLine`)
* `FillDescFromResource` - use this to populate argument descriptions from a resource file, see ([Localization, resources](https://github.com/j-maly/CommandLineParser/wiki/Localization,-resources))


