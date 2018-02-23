---
title: Introduktion till App Service i Linux | Microsoft Docs
description: "Läs om Azure App Service i Linux."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: c06270c75ae16ba516e71a5dde9db4a7277d7e38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introduktion till Azure App Service i Linux

[Web App](../app-service-web-overview.md) är en helt hanterad beräkningsplattform som är optimerad för hantering av webbplatser och webbappar. Kunderna kan använda App Service i Linux för att agera intern värd för webbappar i Linux för de programstackar som stöds. Följande avsnitt innehåller en lista med de programstackar som stöds för närvarande.

## <a name="languages"></a>Språk

App Service i Linux stöder ett antal inbyggda avbildningar i syfte att öka utvecklarnas produktivitet. Om det saknas stöd för den körmiljö som ditt program behöver för att fungera tillhandahåller vi anvisningar för hur du [skapar en egen Docker-avbildning](tutorial-custom-docker-image.md) som distribueras till Web App for Containers.

| Språk | Versioner som stöds |
|---|---|
| Node.js | 4.4, 4.5, 6.2, 6.6, 6.9-6.11, 8.0, 8.1 |
| PHP | 5.6, 7.0 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |

## <a name="deployments"></a>Distributioner

* FTP
* Lokal Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Mellanlagringsmiljöer
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) och DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konsol, publicering och felsökning

* Miljöer
* Distributioner
* Basic-konsol
* SSH

## <a name="scaling"></a>Skalning

* Kunderna kan skala upp och ned webbapparna genom att byta prisnivå för sin [App Service-plan](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Platser

Kontrollera [instrumentpanelen för Azure-status](https://azure.microsoft.com/status).

## <a name="limitations"></a>Begränsningar

Azure-portalen visar endast de funktioner som för närvarande fungerar tillsammans med Web App for Containers. Allt fler funktioner kommer att bli synliga i portalen i takt med att vi aktiverar dem.

Vissa funktioner – till exempel Virtual Network-integration, Azure Active Directory/autentiseringsmetoder från tredje part och Kudu-platstillägg – är inte tillgängliga ännu. När funktionerna blir tillgängliga kommer vi att uppdatera vår dokumentation och bloggen så att de återspeglar ändringarna.

App Service i Linux fungerar endast om du har valt någon av App Service-planerna [Basic eller Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) utan [kostnadsfritt eller delat](https://azure.microsoft.com/pricing/details/app-service/plans/) alternativ. Följande utgör också viktiga begränsningar för App Service i Linux:

* Det går inte att skapa Web App for Containers i en App Service-plan som redan agerar värd för webbappar av andra typer än Linux.
* Om du skapar Web App for Containers i en resursgrupp som innehåller webbappar av andra typer än Linux måste du skapa en App Service-plan i en annan resursgrupp än den där du redan har en befintlig App Service-plan.

## <a name="troubleshooting"></a>Felsökning

När det inte går att starta appen eller om du vill kontrollera loggen i din app kan du öppna Docker-loggarna i katalogen LogFiles. Katalogen är åtkomlig via SCM-webbplatsen eller via FTP.
Om du vill logga `stdout` och `stderr` från din behållare måste du aktivera **Loggning av dockerbehållare** under **Diagnostikloggar**.

![Aktivera loggning][2]

![Använda Kudu för att visa Docker-loggar][1]

Du kommer åt SCM-webbplatsen från **Avancerade verktyg** i menyn **Utvecklingsverktyg**.

## <a name="next-steps"></a>Nästa steg

Se följande länkar för att få hjälp med att komma igång med App Service i Linux. Du kan publicera frågor och funderingar i [vårt forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Så här använder du en anpassad Docker-avbildning för Web App for Containers](quickstart-custom-docker-image.md)
* [Använda .NET Core i Azure App Service i Linux](quickstart-dotnetcore.md)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](app-service-linux-faq.md)
* [SSH-stöd för Azure App Service i Linux](app-service-linux-ssh-support.md)
* [Konfigurera mellanlagringsmiljöer i Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution i Docker Hub med Web App for Containers](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
