---
title: "Introduktion till App Service på Linux | Microsoft Docs"
description: "Läs mer om Azure App Service på Linux."
keywords: "Azure apptjänst, linux, oss"
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
ms.openlocfilehash: 89cb7dc488da42724f212d13f8550064ff8b9188
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introduktion till Azure App Service på Linux

[Webbapp](../app-service-web-overview.md) är en helt hanterad beräkningsplattform som är optimerad för hantering av webbplatser och webbprogram. Kunder kan använda Apptjänst i Linux värden webbappar internt på Linux för stackar för program som stöds. Följande avsnitt listar program stackar som stöds för närvarande.

## <a name="languages"></a>Språk

Apptjänst i Linux stöder ett antal inbyggda avbildningar för att öka utvecklarproduktivitet. Om körningen som krävs för ditt program inte stöds i de inbyggda bilderna, det finns anvisningar om hur du [skapa en egen avbildning med Docker](tutorial-custom-docker-image.md) att distribuera till webbprogrammet för behållare.

| Språk | Versioner som stöds |
|---|---|
| Node.js | 4.4, 4.5, 6.2, 6.6, 6.9-6.11, 8.0, 8.1 |
| PHP | 5.6, 7.0 |
| .NET Core | 1.0, 1.1 |
| Ruby | 2.3 |

## <a name="deployments"></a>Distributioner

* FTP
* Lokal Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Mellanlagringsmiljöer
* [Azure-behållaren registret](https://docs.microsoft.com/azure/container-registry/container-registry-intro) och DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konsolen för publicering och felsökning

* Miljöer
* Distributioner
* Basic-konsolen
* SSH

## <a name="scaling"></a>Skalning

* Kunder kan skala webbprogram uppåt och nedåt genom att ändra nivån av deras [App Service-plan](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Platser

Kontrollera den [Azure Status instrumentpanelen](https://azure.microsoft.com/status).

## <a name="limitations"></a>Begränsningar

Azure-portalen visar funktioner som för närvarande stöds för webbprogrammet för behållare. Som vi aktivera flera funktioner som visas de på portalen.

Vissa funktioner, till exempel virtuell nätverksintegration, Azure Active Directory/autentiseringsmetoder från tredje part eller Kudu webbplatstillägg är inte tillgängliga ännu. När dessa funktioner är tillgängliga, kommer vi uppdatera våra dokumentationen och bloggen om ändringarna.

Apptjänst i Linux stöds endast med [Basic och Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) app service-planer och inte har en [kostnadsfri eller delad](https://azure.microsoft.com/pricing/details/app-service/plans/) nivå. Följande är också viktigt begränsningar för App Service på Linux:

* Du kan inte skapa webbprogrammet för behållare i en apptjänstplan redan värd för icke - Linux Web Apps.
* När du skapar webbprogrammet för behållare i en resursgrupp som innehåller icke - Linux Web Apps, måste du skapa en apptjänstplan i en annan region än befintlig programtjänstplan.

## <a name="troubleshooting"></a>Felsökning

När det inte går att starta programmet eller du vill kontrollera loggning från din app, kan du kontrollera Docker loggar i katalogen loggfiler. Du kan komma åt den här katalogen via webbplatsen SCM eller via FTP.
Loggen i `stdout` och `stderr` från din behållare, måste du aktivera **Dockerbehållare loggning** under **diagnostik loggar**.

![Aktivera loggning][2]

![Använda Kudu för att visa Docker-loggar][1]

Du kan komma åt webbplatsen SCM från **avancerade verktyg** i den **utvecklingsverktyg** menyn.

## <a name="next-steps"></a>Nästa steg

Se följande länkar för att komma igång med Apptjänst i Linux. Du kan publicera frågor och funderingar på [vårt forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Hur du använder en anpassad Docker-avbildning för webbprogrammet för behållare](quickstart-custom-docker-image.md)
* [Med .NET Core i Azure App Service på Linux](quickstart-dotnetcore.md)
* [Med Ruby i Azure App Service på Linux](quickstart-ruby.md)
* [Azure App Service Webbapp för behållare vanliga frågor och svar](app-service-linux-faq.md)
* [SSH-stöd för Azure App Service på Linux](app-service-linux-ssh-support.md)
* [Skapa mellanlagringsmiljöer i Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker-hubb kontinuerlig distribution med webbprogrammet för behållare](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
