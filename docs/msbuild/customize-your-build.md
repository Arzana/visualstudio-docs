---
title: "Customize your build | Microsoft Docs"
ms.custom: ""
ms.date: "06/14/2017"
ms.reviewer: ""
ms.suite: ""
ms.technology:
  - "vs-ide-sdk"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords:
  - "MSBuild, transforms"
  - "transforms [MSBuild]"
ms.assetid: d0bceb3b-14fb-455c-805a-63acefa4b3ed
caps.latest.revision: 13
author: "kempb"
ms.author: "kempb"
manager: "ghogen"
translation.priority.ht:
  - "cs-cz"
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "pl-pl"
  - "pt-br"
  - "ru-ru"
  - "tr-tr"
  - "zh-cn"
  - "zh-tw"
---
# Customize your build
In versions of MSBuild prior to version 15, if you wanted to provide a new, custom property to projects in your solution, you had to manually add a reference to that property to every project file in the solution. Or, you had to define the property in a .props file and then explicitly import the .props file in every project in the solution, among other things.

However, now you can add a new property to every project in one step by defining it in a single file called Directory.Build.props at the root of your repo. When MSBuild runs, Microsoft.Common.props searches your directory structure for the Directory.Build.props file (and Microsoft.Common.targets looks for Directory.Build.targets). If it finds one, it imports the property. Directory.Build.props is a user-defined file that provides customizations to projects under a directory.

## Directory.Build.props example
For example, if you wanted to enable all of your projects to access the new Roslyn **/deterministic** feature (which is exposed in the Roslyn CoreCompile target by the property `$(Deterministic)`), you could do the following.

1. Create a new file in the root of your repo called Directory.Build.props.
2. Add the following XML to the file.

  ```xml
  <Project>
    <PropertyGroup>
      <Deterministic>true</Deterministic>
    </PropertyGroup>
  </Project>
  ```
3. Run MSBuild. Your project’s existing imports of Microsoft.Common.props and Microsoft.Common.targets find the file and import it.

## Search scope
When searching for a Directory.Build.props file, MSBuild walks the directory structure upwards from your project location ($(MSBuildProjectFullPath)), stopping after it locates a Directory.Build.props file. For example, if your $(MSBuildProjectFullPath) was c:\users\username\code\test\case1, MSBuild would start searching there and then search the directory structure upward until it located a Directory.Build.props file, as in the following directory structure.

```
c:\users\username\code\test\case1
c:\users\username\code\test
c:\users\username\code
c:\users\username
c:\users
c:\
```
The location of the solution file is irrelevant to Directory.Build.props.

## Import order

Directory.Build.props is imported very early in Microsoft.Common.props, so properties defined later are unavailable to it. So, avoid referring to properties that are not yet defined (and will thus evaluate to empty).

Directory.Build.targets is imported from Microsoft.Common.targets after importing .targets files from NuGet packages. So, it can be used to override properties and targets defined in most of the build logic, but at times it may be necessary to do customizations within the project file after the final import.

## See Also  
 [MSBuild Concepts](../msbuild/msbuild-concepts.md)   
 [MSBuild Reference](../msbuild/msbuild-reference.md)   