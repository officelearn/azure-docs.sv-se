---
title: Kör kod på standardbehållare för Linux
description: Azure App Service kan köra din kod på färdiga Linux-behållare. Ta reda på hur du kan köra dina Linux-webbprogram på Azure.
keywords: azure app service, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 27f085543869b1a77db9c97ca2e7ae7d3d3b7b88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046418"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introduktion till Azure App Service i Linux

[Azure App Service](../overview.md) är en helt hanterad beräkningsplattform som är optimerad för hantering av webbplatser och webbappar. Kunderna kan använda App Service i Linux för att agera intern värd för webbappar i Linux för de programstackar som stöds.

## <a name="languages"></a>Språk

App Service i Linux stöder ett antal inbyggda avbildningar i syfte att öka utvecklarnas produktivitet. Språk inkluderar: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core och Ruby. Kör [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) för att visa de senaste språken och versioner som stöds. Om det saknas stöd för den körmiljö som ditt program behöver för att fungera tillhandahåller vi anvisningar för hur du [skapar en egen Docker-avbildning](tutorial-custom-docker-image.md) som distribueras till Web App for Containers.

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

App service på Linux stöds endast med [kostnadsfria, grundläggande, standard- och](https://azure.microsoft.com/pricing/details/app-service/plans/) premiumapptjänstplaner och har ingen [delad](https://azure.microsoft.com/pricing/details/app-service/plans/) nivå. Du kan inte skapa en Linux Web App i en App Service-plan som redan är värd för icke-Linux Web Apps.  

Baserat på en aktuell begränsning kan du för samma resursgrupp inte blanda Windows- och Linux-appar i samma region.

## <a name="troubleshooting"></a>Felsökning

> [!NOTE]
> Det finns nya integrerade loggningsfunktioner med [Azure Monitoring (förhandsversion).](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) 
>
>

När det inte går att starta appen eller om du vill kontrollera loggen i din app kan du öppna Docker-loggarna i katalogen LogFiles. Katalogen är åtkomlig via SCM-webbplatsen eller via FTP. Om du `stdout` `stderr` vill logga och från behållaren måste du aktivera **programloggning** under **App Service Loggar**. Inställningen utförs direkt. App Service identifierar ändringen och startar om behållaren automatiskt.

Du kommer åt SCM-webbplatsen från **Avancerade verktyg** i menyn **Utvecklingsverktyg**.

![Använda Kudu för att visa Docker-loggar][1]

## <a name="next-steps"></a>Nästa steg

I följande artiklar hjälper vi dig att komma igång med App Service i Linux med webappar som skrivits på flera olika språk:

* [.NET Core](quickstart-dotnetcore.md)
* [Php](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Kör](quickstart-docker-go.md)
* [Appar med flera containrar](quickstart-multi-container.md)

Mer information om App Service på Linux finns i:

* [Vanliga frågor och svar om App Service för Linux](app-service-linux-faq.md)
* [SSH-stöd för App Service i Linux](app-service-linux-ssh-support.md)
* [Konfigurera mellanlagringsmiljöer i App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution i Docker-hubb](app-service-linux-ci-cd.md)

Du kan publicera frågor och funderingar i [vårt forum](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
