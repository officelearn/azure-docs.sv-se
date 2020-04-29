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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836789"
---
## <a name="prepare-your-repository"></a>Förbered din lagrings plats

Om du vill hämta automatiska versioner från Azure App Service kudu build Server kontrollerar du att din lagrings plats rot har rätt filer i projektet.

| Körmiljö | Rot Katalog-filer |
|-|-|
| ASP.NET (endast Windows) | _*. SLN_, _*. CSPROJ_eller _default. aspx_ |
| ASP.NET Core | _*. SLN_ eller _*. CSPROJ_ |
| PHP | _index. php_ |
| Ruby (endast Linux) | _Gemfile_ |
| Node.js | _Server. js_, _app. js_eller _Package. JSON_ med ett start skript |
| Python | . py, _Requirements. txt_eller _runtime. txt_ _ \*_ |
| HTML | _default. htm_, _standard. html_, _default. asp_, _index. htm_, _index. html_eller _iisstart. htm_ |
| Webbjobb | _\<job_name>/Run. tillägg \<>_ under _AppData\_/jobb/kontinuerligt_ för kontinuerliga webbjobb eller _AppData\_/jobb/utlöses_ för utlösta WebJobs. Mer information finns i [kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Se [kontinuerlig distribution för Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Om du vill anpassa distributionen inkluderar du en *. distributions* fil i lagrings platsens rot. Mer information finns i [Anpassa distributioner](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) och [anpassat distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Om du utvecklar i Visual Studio [skapar du en lagrings plats för Visual Studio](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projektet är omedelbart klart att distribueras med hjälp av git.
>

