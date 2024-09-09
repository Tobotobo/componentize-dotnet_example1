# componentize-dotnet_example1

## 概要
componentize-dotnet の Getting started を試す。

.NETでWebAssemblyの最新仕様「WASI Preview 2」対応コンポーネントを作れる「componentize-dotnet」、Bytecode Allianceがオープンソースでリリース  
https://www.publickey1.jp/blog/24/netwebassemblywasi_preview_2componentize-dotnetbytecode_alliance.html  

componentize-dotnet  
https://github.com/bytecodealliance/componentize-dotnet/tree/main  

## 環境
```
> dotnet --info
.NET SDK:
 Version:           8.0.401   
 Commit:            811edcc344
 Workload version:  8.0.400-manifests.56cd0383
 MSBuild version:   17.11.4+37eb419ad

ランタイム環境:
 OS Name:     Windows
 OS Version:  10.0.19045
 OS Platform: Windows
 RID:         win-x64
```

```
> .\tools\wasmtime-v24.0.0-x86_64-windows\wasmtime.exe --version
wasmtime-cli 24.0.0 (6fc3d274c 2024-08-20)
```

## 実行結果
```
> .\tools\wasmtime-v24.0.0-x86_64-windows\wasmtime.exe bin\Debug\net8.0\wasi-wasm\native\componentize-dotnet_example1.wasm
Hello, World!
```

## 詳細

### プロジェクト作成
```
dotnet new console
dotnet new nugetconfig
```

### `nuget.config` の `<clear />` の後ろに以下を追加
```
    <add key="dotnet-experimental" value="https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet-experimental/nuget/v3/index.json" />
    <add key="nuget" value="https://api.nuget.org/v3/index.json" />
```

### パッケージ追加
```
dotnet add package BytecodeAlliance.Componentize.DotNet.Wasm.SDK --prerelease
```

### `componentize-dotnet_example1.csproj` の `<PropertyGroup>` に以下を追加
```
    <RuntimeIdentifier>wasi-wasm</RuntimeIdentifier>
    <UseAppHost>false</UseAppHost>
    <PublishTrimmed>true</PublishTrimmed>
    <InvariantGlobalization>true</InvariantGlobalization>
```

### ビルドして wasm ファイル作成
```
dotnet build
```
※約 500 MB の wasi-sdk をダウンロードするので注意

実行ログ
```
> dotnet build
  復元対象のプロジェクトを決定しています...
  D:\Projects\componentize-dotnet_example1\componentize-dotnet_example1.csproj を復元しました (9.42 秒)。
CSC : warning CS8784: ジェネレーター 'VtableIndexStubGenerator' を初期化できませんでした。出力には寄与しません。結果として、コンパイル エラーが発生する可能性があります。例外の型: 'TypeLoadException'。メッセージ: 'Could not load type 'Microsoft.Intero
p.MarshallingGeneratorFactoryKey`1' from assembly 'Microsoft.Interop.SourceGeneration, Version=9.0.10.42901, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'.'。 [D:\Projects\componentize-dotnet_example1\componentize-dotnet_example1.csproj]
CSC : warning CS8784: ジェネレーター 'LibraryImportGenerator' を初期化できませんでした。出力には寄与しません。結果として、コンパイル エラーが発生する可能性があります。例外の型: 'TypeLoadException'。メッセージ: 'Could not load type 'Microsoft.Interop.
MarshallingGeneratorFactoryKey`1' from assembly 'Microsoft.Interop.SourceGeneration, Version=9.0.10.42901, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'.'。 [D:\Projects\componentize-dotnet_example1\componentize-dotnet_example1.csproj]
  componentize-dotnet_example1 -> D:\Projects\componentize-dotnet_example1\bin\Debug\net8.0\wasi-wasm\componentize-dotnet_example1.dll
  Generating native code
  LLVM compilation to IR finished in 0.77 seconds
  LLVM generation of bitcode finished in 0.12 seconds
  Object writing finished in 0.09 seconds, allocated 29.59 MB
  "https://github.com/WebAssembly/wasi-sdk/releases/download/wasi-sdk-24/wasi-sdk-24.0-x86_64-windows.tar.gz" から "C:\Users\XXXXX\AppData\Local\Temp\ckz5ognb.tf0\wasi-sdk-24.
  0-x86_64-windows.tar.gz" にダウンロードしています (495,098,073 バイト)。
  Extracting C:\Users\XXXXX\AppData\Local\Temp\ckz5ognb.tf0\wasi-sdk-24.0-x86_64-windows.tar.gz to C:\Users\XXXXX\.wasi-sdk\wasi-sdk-24.0...
  Emit on build componentize-dotnet_example1 

ビルドに成功しました。

CSC : warning CS8784: ジェネレーター 'VtableIndexStubGenerator' を初期化できませんでした。出力には寄与しません。結果として、コンパイル エラーが発生する可能性があります。例外の型: 'TypeLoadException'。メッセージ: 'Could not load type 'Microsoft.Intero
p.MarshallingGeneratorFactoryKey`1' from assembly 'Microsoft.Interop.SourceGeneration, Version=9.0.10.42901, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'.'。 [D:\Projects\componentize-dotnet_example1\componentize-dotnet_example1.csproj]
CSC : warning CS8784: ジェネレーター 'LibraryImportGenerator' を初期化できませんでした。出力には寄与しません。結果として、コンパイル エラーが発生する可能性があります。例外の型: 'TypeLoadException'。メッセージ: 'Could not load type 'Microsoft.Interop.
MarshallingGeneratorFactoryKey`1' from assembly 'Microsoft.Interop.SourceGeneration, Version=9.0.10.42901, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'.'。 [D:\Projects\componentize-dotnet_example1\componentize-dotnet_example1.csproj]
    2 個の警告
    0 エラー

経過時間 00:01:08.35
```

`bin\Debug\net8.0\wasi-wasm\native` に `componentize-dotnet_example1.wasm` が生成される

### wasmtime で実行

wasmtime-v24.0.0-x86_64-windows をダウンロード
```
Invoke-WebRequest -Uri "https://github.com/bytecodealliance/wasmtime/releases/download/v24.0.0/wasmtime-v24.0.0-x86_64-windows.zip" -OutFile "$pwd\wasmtime.zip"; Expand-Archive -Path "$pwd\wasmtime.zip" -DestinationPath "$pwd\tools"; Remove-Item "$pwd\wasmtime.zip"
```

実行
```
.\tools\wasmtime-v24.0.0-x86_64-windows\wasmtime.exe bin\Debug\net8.0\wasi-wasm\native\componentize-dotnet_example1.wasm
```

実行結果
```
> .\tools\wasmtime-v24.0.0-x86_64-windows\wasmtime.exe bin\Debug\net8.0\wasi-wasm\native\componentize-dotnet_example1.wasm
Hello, World!
```

