The library uses exceptions to report errors. Therefore it may be useful to translate the messages into desired language. All messages that could possibly get to the user of the application that uses the library are stored in `Messages` resource file and can be edited with the resource editor. 

Parsing of the value of `ValueArgument` and its descendants may be affected by local and geographical settings at the user computer. You can specify `ValueArgument.CultureInfo` for `ValueArgument` to make sure that argument is parsed in a desired way. 

Each argument has an `Argument.FullDescription` property that can be used to give more information abut the argument to the user. 

Because values of these strings can be quite long, it is useful tu store them separately in resource files. Assigning the value of a property from a resource file is no problem when arguments are defined programatically. 

However, when you use declarative syntax, the resources cannot be accessed directly, this is simply not possible to compile (`CommandlineArguments` is the resource class): 

```csharp
[DirectoryArgument('o', "outputDir", Description = "Output directory", FullDescription = CommandlineArguments.ARG_OUTPUTDIR)]
public DirectoryInfo OutputDir { get; set; }
```

Nevertheless, workaround is provided. Use this declaration instead:

```csharp
[DirectoryArgument('o', "outputDir", Description = "Output directory", FullDescription = "ARG_OUTPUTDIR")]
public DirectoryInfo OutputDir { get; set; }
```

When initializing the parser, use these calls: 

```csharp
parser.ExtractArgumentAttributes(this);
parser.FillDescFromResource(new CommandlineArguments());
```

You will have to manually edit the `CommandlineArguments.Designer.cs` code-behind file and add the `IResource` interface to the resource class and implement the only required property like this: 

```csharp
internal class CommandlineArguments : IResource  
{
    // 
    //...
    // 

    ResourceManager IResource.ResourceManager
    {    
        get { return ResourceManager; }
    }
}
```