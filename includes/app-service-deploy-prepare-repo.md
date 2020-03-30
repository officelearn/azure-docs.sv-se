---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836789"
---
## <a name="prepare-your-repository"></a>Förbereda databasen

Om du vill hämta automatiska versioner från Azure App Service Kudu build server kontrollerar du att databasroten har rätt filer i projektet.

| Körmiljö | Rotkatalogfiler |
|-|-|
| ASP.NET (endast Windows) | _*.sln_, _*.csproj_eller _default.aspx_ |
| ASP.NET Core | _*.sln_ eller _*.csproj_ |
| PHP | _Index.php_ |
| Ruby (endast Linux) | _Gemfile (gemfile)_ |
| Node.js | _server.js,_ _app.js_eller _package.json_ med ett startskript |
| Python | .py , _requirements.txt_eller _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_eller _iisstart.htm_ |
| Webbjobb | _\<job_name>/kör. tillägget \<>_ under _\_AppData/jobb/kontinuerligt_ för kontinuerliga WebJobs eller _Appdata/jobb/utlöses\__ för utlösta WebJobs. Mer information finns i [Dokumentationen för Kudu WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funktioner | Se [Kontinuerlig distribution för Azure-funktioner](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Om du vill anpassa distributionen inkluderar du en *DISTRIBUTIONSFIL* i databasroten. Mer information finns i [Anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och anpassat [distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Om du utvecklar dig i Visual Studio låter du [Visual Studio skapa en databas åt dig](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projektet är omedelbart redo att distribueras med hjälp av Git.
>

