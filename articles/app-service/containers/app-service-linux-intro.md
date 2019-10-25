---
title: Introduktion till App Service i Linux – Azure | Microsoft Docs
description: Läs om Azure App Service i Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1bbcd5e4f8c6a429def84ad77d7dd93fa11b7324
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819680"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introduktion till Azure App Service i Linux

[Azure App Service](../overview.md) är en helt hanterad beräkningsplattform som är optimerad för hantering av webbplatser och webbappar. Kunderna kan använda App Service i Linux för att agera intern värd för webbappar i Linux för de programstackar som stöds.

## <a name="languages"></a>Språk

App Service i Linux stöder ett antal inbyggda avbildningar i syfte att öka utvecklarnas produktivitet. Språken inkluderar: Node. js, Java (JRE 8 & JRE 11), PHP, python, .NET Core och Ruby. Kör [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) för att visa de senaste språken och versioner som stöds. Om det saknas stöd för den körmiljö som ditt program behöver för att fungera tillhandahåller vi anvisningar för hur du [skapar en egen Docker-avbildning](tutorial-custom-docker-image.md) som distribueras till Web App for Containers.

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

* Kunderna kan skala upp och ned webbapparna genom att byta prisnivå för sin [App Service-plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Platser

Kontrollera [instrumentpanelen för Azure-status](https://azure.microsoft.com/status).

## <a name="limitations"></a>Begränsningar

Azure-portalen visar endast de funktioner som för närvarande fungerar tillsammans med Web App for Containers. Allt fler funktioner kommer att bli synliga i portalen i takt med att vi aktiverar dem.

App Service på Linux stöds bara med [kostnads fria, Basic-, standard-och Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service-planer och har inte någon [delad](https://azure.microsoft.com/pricing/details/app-service/plans/) nivå. Det går inte att skapa en Linux-webbapp i en App Service plan som redan är värd för Web Apps som inte är Linux.  

Baserat på en aktuell begränsning för samma resurs grupp kan du inte blanda Windows-och Linux-appar i samma region.

## <a name="troubleshooting"></a>Felsöka

När det inte går att starta appen eller om du vill kontrollera loggen i din app kan du öppna Docker-loggarna i katalogen LogFiles. Katalogen är åtkomlig via SCM-webbplatsen eller via FTP. Om du vill logga `stdout` och `stderr` från din behållare måste du aktivera **Docker-behållar loggning** under **App Service loggar**. Inställningen utförs direkt. App Service identifierar ändringen och startar om behållaren automatiskt.

Du kommer åt SCM-webbplatsen från **Avancerade verktyg** i menyn **Utvecklingsverktyg**.

![Använda Kudu för att visa Docker-loggar][1]

## <a name="next-steps"></a>Nästa steg

I följande artiklar hjälper vi dig att komma igång med App Service i Linux med webappar som skrivits på flera olika språk:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Appar med flera containrar](quickstart-multi-container.md)

Mer information om App Service på Linux finns i:

* [Vanliga frågor och svar om App Service för Linux](app-service-linux-faq.md)
* [SSH-stöd för App Service i Linux](app-service-linux-ssh-support.md)
* [Konfigurera mellanlagringsmiljöer i App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution i Docker-hubb](app-service-linux-ci-cd.md)

Du kan publicera frågor och funderingar i [vårt forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
