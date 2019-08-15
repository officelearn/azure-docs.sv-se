---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011999"
---
Klient biblioteket för [Microsoft Azure App-autentisering](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) för .net (för hands version) fören klar processen att förvärva och förnya en token från din kod. Klient biblioteket för app-autentisering hanterar autentiseringen automatiskt. Biblioteket använder utvecklarens autentiseringsuppgifter för att autentisera under lokal utveckling. Att använda autentiseringsuppgifter för utvecklare under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare. När lösningen senare distribueras till Azure växlar biblioteket automatiskt till att använda programautentiseringsuppgifter.

Om du vill använda app Authentication-biblioteket i ett Azure Storage-program installerar du det senaste för hands versions paketet från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), samt den senaste versionen av [Azure Storage vanliga klient bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) och [Azure Blob Storage klient biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Lägg till följande **using** -uttryck i koden:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
