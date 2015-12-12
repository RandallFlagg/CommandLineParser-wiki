`CommandLineParser` library allows you to define arguments of your own (via deriving from `Argument` class), but there are several argument types already included and ready to use. 

## SwitchArgument

You can use SwitchArgument to define an argument with true/false logic. Switch argument has its default value - the initial value and value that argument holds when the user does not use the argument on the command line. Each time the user uses the argument, the value is flipped. Suppose "Finder" application that has "distinct" switch argument with default value false. This usage: 
```
Finder.exe --distinct
```
flips the value of distinct argument to true. Note that the switch argument is not followed by value therefore this: `Finder.exe --distinct false` would result in something that the user probably didn't want - default value of "distinct" argument (false) would be flipped to true and the word false would be interpreted as an additional argument not related to "distinct" argument. To define a bool argument followed by value, use ValueArgument<bool> instead. 

## ValueArgument

You can use `ValueArgument` to define an argument with value (of any type). User can then type the argument and its value on the command line. Suppose "Finder" application that has "subdirectories" argument with integer value. You would define it as `ValueArgument<int>` and then it could be used like this: 

```
Finder.exe --subdirectories 3
```
`ValueArgument` class takes care of parsing the value and converting it to the right type. For built-in C# types (bool, int, double, string, char etc.) the static .NET function Parse is used. If you want to use some other type (your class or struct for example) as arguments value, you have to specify a conversion routine that converts string on the command line to your type. 

## EnumeratedValueArgument

`EnumeratedValueArgument` is similar to `ValueArgument`, only its values must belong to a user defined set of possible values. Remark - if you want to map the set of values to your `enum` type, you can use ValueArgument<TValue> where `TValue` is your `enum` type instead. 

## BoundedValueArgument

`BoundedValueArgument` is similar to `ValueArgument` and `EnumeratedValueArgument`. You can use any comparable type as `TValue` (type implementing `IComparable`) and define `minimum` and `maximum` allowed value of the argument. 

## FileArgument

`FileArgument` can be used to define input and output files for the program. The value of the argument is of type `FileInfo` and via property `FileArgument.FileMustExist` you can declare whether the file must already exist (useful for input files) or not. 

## DirectoryArgument

`DirectoryArgument` can be used to specify directory in the file system. The value of the argument is of type `DirectoryInfo` and via property `DirectoryArgumet.DirectoryMustExist` you can declare whether the file must already exist or not. 
