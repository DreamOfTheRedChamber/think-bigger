- [Project deepdive: Auth library migration](#project-deepdive-auth-library-migration)
  - [Highlight slides: Overview slides](#highlight-slides-overview-slides)
  - [Project context](#project-context)
  - [Motivation](#motivation)
  - [TODO](#todo)

# Project deepdive: Auth library migration

## [Highlight slides: Overview slides](https://docs.google.com/presentation/d/1MpL-0gpj26kJaeYDu2-QH0z3wNSU0a58tI9oKb9dLUw/edit?usp=sharing)

## Project context
* Average RPS: 30K / s
* Peak RPS: 60K / s
* Distributed token cache hit rate: 80%

## Motivation
* Migrate from [ADAL.Net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) to [MSAL.Net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)

![](../../.gitbook/assets/adalNetVsMsalNet.png)

![](../../.gitbook/assets/adalNetVsMsalNetAPI.png)

## TODO
* On behalf of: https://curity.io/resources/learn/on-behalf-of-flow/
