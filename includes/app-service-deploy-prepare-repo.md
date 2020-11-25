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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004370"
---
## <a name="prepare-your-repository"></a>Förbered din lagrings plats

Om du vill hämta automatiska versioner från Azure App Service kudu build Server kontrollerar du att din lagrings plats rot har rätt filer i projektet.

| Körning | Rot Katalog-filer |
|-|-|
| ASP.NET (endast Windows) | _*. SLN_, _*. CSPROJ_ eller _default. aspx_ |
| ASP.NET Core | _*. SLN_ eller _*. CSPROJ_ |
| PHP | _index. php_ |
| Ruby (endast Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ eller _package.jspå_ med ett start skript |
| Python | _\* . py_, _requirements.txt_ eller _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default. asp_, _index.htm_, _index.html_ eller _iisstart.htm_ |
| WebJobs | _\<job_name>/Run.\<extension>_ under _AppData \_ /jobb/kontinuerligt_ för kontinuerliga Webbjobb eller _AppData/ \_ jobb/utlöses_ för utlösta WebJobs. Mer information finns i [kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funktioner | Se [kontinuerlig distribution för Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Om du vill anpassa distributionen inkluderar du en *. distributions* fil i lagrings platsens rot. Mer information finns i [Anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Om du utvecklar i Visual Studio [skapar du en lagrings plats för Visual Studio](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projektet är omedelbart klart att distribueras med hjälp av git.
>

