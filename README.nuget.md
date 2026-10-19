# Smartstore EmbeddedSass.Net

Smartstore's fork of [EmbeddedSass.Net](https://github.com/gumbarros/EmbeddedSass.Net), an asynchronous .NET host for the Embedded Sass Protocol.

## Why this fork exists

We needed to ship a fix for a callback ID reuse race without waiting for an upstream release.
Dart Sass may reuse a callback ID immediately after receiving a response, while the host's asynchronous send is still completing.
The original host could reject this valid callback because the previous ID was still registered as active, causing compilation to fail.
This fork releases the ID before sending the response and ensures that completion of an earlier send cannot remove a newer callback using the same ID.
Regression tests cover custom function callbacks and importer callbacks.

## Packages and installation

- **Smartstore.EmbeddedSass.Net** provides the .NET host for an externally supplied Dart Sass compiler.
- **Smartstore.EmbeddedSass.Net.Compiler** includes the host dependency and bundled Dart Sass executables for Windows, macOS, and Linux on x64 and ARM64.

For the bundled compiler, reference:

```xml
<PackageReference Include="Smartstore.EmbeddedSass.Net.Compiler" Version="1.2.1" />
```

```csharp
using EmbeddedSass;
using EmbeddedSass.Compiler;

var options = new SassCompilerOptions().UseBundledDartSass();
await using var compiler = new SassCompiler(options);
var result = await compiler.CompileStringAsync("a { color: red; }");
Console.WriteLine(result.Css);
```

For an externally managed Dart Sass compiler, reference `Smartstore.EmbeddedSass.Net` instead and configure `SassCompilerOptions.CompilerPath`.

The packages retain the original assembly names and namespaces. Replace the corresponding upstream package references; do not install both distributions together.
Only the two packages listed above are distributed by this fork.

## Source and license

- [Smartstore fork and documentation](https://github.com/muratcakir/EmbeddedSass.Net)
- [Original project](https://github.com/gumbarros/EmbeddedSass.Net)

The original MIT license and copyright notices are preserved. Bundled Dart Sass retains its own license and third-party notices, included in the compiler package.
