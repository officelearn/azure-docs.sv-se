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
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850822"
---
## <a name="prepare-your-repository"></a>Förbered din databas

Om du vill få automatisk versioner av App Service Kudu build-server måste du se till att Lagringsplatsens rot har rätt filer i projektet.

| Körmiljö | Rot-directory-filer |
|-|-|
| ASP.NET (endast Windows) | _*.SLN_, _*.csproj_, eller _default.aspx_ |
| ASP.NET Core | _*.SLN_ eller _*.csproj_ |
| PHP | _index.php_ |
| Ruby (endast Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, eller _package.json_ med ett start-skript |
| Python (endast Windows) | _\*.PY_, _requirements.txt_, eller _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, eller  _iisstart.htm_ |
| Webbjobb | _\<job_name > / Kör. \<tillägg >_ under _App\_Data/jobb/kontinuerlig_ (för kontinuerliga Webbjobb) eller _App\_Data/jobb/utlöst_ (för utlöses WebJobs). Mer information finns i [Kudu WebJobs-dokumentation](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Functions | Se [kontinuerlig distribution för Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Om du vill anpassa distributionen omfattar en _.deployment_ filen i Lagringsplatsens rot. Mer information finns i [anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och [anpassat distributionsskriptet](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Om du utvecklar i Visual Studio kan du låta [Visual Studio skapar en lagringsplats för](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio). Projektet är klar att distribueras med hjälp av Git.
>
>

