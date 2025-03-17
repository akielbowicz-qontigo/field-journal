---
title: "Configuring EMACS for CSharp development"
date: 2025-03-14
created_at: 2025-03-14
updated_at: 2025-03-17
categories: [config]
tags: [csharp, emacs]
---

So finally get some time to try to setup [Spacemacs C# layer](https://www.spacemacs.org/layers/+lang/csharp/README.html) on the Windows machine

I decided to configure it with lsp and had the following issues:

To check the issues I had to look into the logs `Ctrl-tab` find the `csharp-*logs` in the buffers ... Well, I'm still learning to move arround in spacemacs

First I got an error that it coudn't find [csharp-ls](https://github.com/emacs-lsp/lsp-mode/issues/4365)
that was fixed by `dotnet tool install csharp-ls --global --add-source https://api.nuget.org/v3/index.json`. 

Then I had an issue that a powershell script [lsp-roslyn-stdpipe.ps1](https://github.com/emacs-lsp/lsp-mode/issues/4557) did not exist. That was fixed by copying that file from the repo
into the path that was detailed in the error message.
