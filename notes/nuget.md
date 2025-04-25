---
title: "using dotnet nuget"
date: 2025-04-25
created_at: 2025-04-25
updated_at: 2025-04-25
categories: [nuget]
tags: [csharp, nuget]
---

Here in the office we have nuget.org disabled as a source so to be able to use it we add it with the `--add-source` flag 

```sh
 dotnet tool install --global Clojure.Cljr --version 0.1.0-alpha6 --add-source https://api.nuget.org/v3/index.json
```
