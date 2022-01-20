[![Build and Release](https://github.com/OlegRa/System.DateTimeOnly/actions/workflows/release.yml/badge.svg)](https://github.com/OlegRa/System.DateTimeOnly/actions/workflows/release.yml)
[![Codacy Badge](https://app.codacy.com/project/badge/Grade/37aac9b569e347d591f1648ff2793092)](https://www.codacy.com/gh/OlegRa/System.DateTimeOnly/dashboard?utm_source=github.com&amp)
[![Codacy Badge](https://app.codacy.com/project/badge/Coverage/37aac9b569e347d591f1648ff2793092)](https://www.codacy.com/gh/OlegRa/System.DateTimeOnly/dashboard?utm_source=github.com)
[![Nuget](https://img.shields.io/nuget/v/Portable.System.DateTimeOnly?logo=NuGet)](https://www.nuget.org/packages/Portable.System.DateTimeOnly)
[![Nuget](https://img.shields.io/nuget/dt/Portable.System.DateTimeOnly?logo=NuGet)](https://www.nuget.org/stats/packages/Portable.System.DateTimeOnly?groupby=Version)

# ![Logo](./Logo.png) Portable.System.DateTimeOnly

The .NET 6 introduced two new data types `System.DateOnly` and `System.TimeOnly` that can be used in various scenarios. If you want to use the same types in the .NET versions below to .NET 6 you can use this library in form of the NuGet package. You can also use it in your own NuGet package if you want to provide an API that uses these types and make it compatible with all currently supported .NET versions at the same time.

## Parsing Performance

The original version of this package used the simplified string parsing approach - it just reuses the default `System.DateTime` parsing logic as is. It works well in most use cases but parsing behavior was not matched 100% to the .NET 6 `DateOnly` and `TimeOnly` data types. Their raise the `System.Format` exception in case if the input string contains the time part for the `System.DateOnly` or the date part in the case of `System.TimeOnly` types.
Unfortunately, this behavior in .NET 6 implementation depends heavily on an internal parser that provides a lot more information about parsing results than a public version of parsing API. Porting this parser required porting a lot of culture-specific code and looks unpractical for such a simple package. So another approach selected for solving this issue - double parsing using `System.DateTime.TryParseExact` with proper formatting strings.
This approach, of course, has an obvious disadvantage - in some cases, we have to parse the same string twice. If performance is critical for you and you are OK with the _incorrect_ parsing results in some rare cases you can restore the original behavior using the application context switch named `Portable.System.DateTimeOnly.UseFastParsingLogic`. Check the `FastStrictParsingLogicTests` for more examples for this issue.
