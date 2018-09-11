---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c212bda3b59037f99139e02ee6adc63b0084cbe5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305622"
---
## <a name="prepare-your-repository"></a>Förbereda din lagringsplats

För att få automatiska versioner från App Service Kudu build-servern kan du se till att Lagringsplatsens rot har rätt filer i projektet.

| Körmiljö | Root directory filer |
|-|-|
| ASP.NET (endast Windows) | _*.SLN_, _*.csproj_, eller _default.aspx_ |
| ASP.NET Core | _*.SLN_ eller _*.csproj_ |
| PHP | _index.php_ |
| Ruby (endast Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, eller _package.json_ med ett skript för start |
| Python (endast Windows) | _\*.PY_, _requirements.txt_, eller _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, eller  _iisstart.htm_ |
| Webbjobb | _\<job_name > / run. \<tillägget >_ under _App\_Data/jobb/continuous_ (för kontinuerliga WebJobs) eller _App\_Data/jobb/triggered_ (för utlöses WebJobs). Mer information finns i [Kudu WebJobs-dokumentation](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Functions | Se [kontinuerlig distribution för Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Om du vill anpassa distributionen omfattar en _.deployment_ filen i Lagringsplatsens rot. Mer information finns i [anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och [anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Om du utvecklar i Visual Studio kan du låta [Visual Studio skapar en lagringsplats du](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projektet är klar att distribueras med hjälp av Git.
>
>

