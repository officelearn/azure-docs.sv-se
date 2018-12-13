---
title: Översikt över diagnostik – Azure App Service | Microsoft Docs
description: Lär dig hur du kan felsöka problem med ditt webbprogram med App Service-diagnostik.
keywords: App service, azure app service, diagnostik, support, webbapp, felsökning, självhjälp
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: a8b256f43d8e4103404ab4276431ceb06d9ed36a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255150"
---
# <a name="azure-app-service-diagnostics-overview"></a>Översikt av Azure App Service-diagnostik 

När du kör ett webbprogram, som du vill att förbereda för eventuella problem som kan uppstå i 500-fel till användarna om att webbplatsen inte fungerar. App Service-diagnostik är en intelligent och interaktiv upplevelse för att felsöka ditt webbprogram med krävs ingen konfiguration. När du stöter på problem med ditt webbprogram, påpeka App Service-diagnostik fel att guida dig till rätt information för att enkelt och snabbt felsöka och lösa problemet. 
 
Även om detta är användbart när du har problem med din webbapp under de senaste 24 timmarna, blir alla diagnostiska diagram tillgänglig för dig att analysera hela tiden. Ytterligare felsökningsverktyg och länkar till användbar dokumentation och forum finns på den högra kolumnen.

App Service-diagnostik passar inte bara din app på Windows, utan också appar på [Linux/behållare](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro), och [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Öppna App Service-diagnostik

Om du vill få åtkomst till App Service-diagnostik, navigera till din App Service-app eller en App Service Environment i den [Azure-portalen](https://portal.azure.com). I det vänstra navigeringsfönstret klickar du på **diagnostisera och lösa problem**. 

Azure Functions går du till din funktionsapp och i det övre navigeringsfältet, klickar du på **plattformsfunktioner** och välj **diagnostisera och lösa problem** från den **övervakning**avsnittet. 

![Startsida](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Hälsokontroll

Om du inte vet vad är fel med ditt webbprogram eller vet inte var du ska börja felsöka ditt problem med är hälsokontroll ett bra ställe att börja. Hälsokontroll analyserar dina webbprogram för att ge dig en snabba, interaktiva översikten som pekar på vad som är felfri och fel, om var du vill undersöka problemet. Dess intelligenta och interaktiva gränssnittet får du vägledning genom felsökningen.  

![Hälsokontroll](./media/app-service-diagnostics/HealthCheckup2.png)

Om ett problem har identifierats med ett specifikt problem-kategori under de senaste 24 timmarna, du kan visa den fullständiga Diagnostikrapporten och App Service-diagnostik kan bli ombedd att visa mer felsökningstips och nästa steg för en mer interaktiv upplevelse.

![Felsökning och nästa steg](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Panelen genvägar

Om du vet exakt vilken typ av information som du letar efter om felsökning tar genvägarna panelen dig direkt till den fullständiga Diagnostikrapporten problem-kategori som du är intresserad av. Jämfört med hälsokontroll, sida vid sida-genvägar är mer direkt, men mindre interaktiva sätt att komma åt din diagnostikmått. Som en del av panelen genvägar, det är också där du hittar **diagnostikverktyg** som är mer avancerade verktyg som hjälper dig att undersöka problem som rör kod programfel, långsamhet, anslutningssträngar med mera. 

![Panelen genvägar](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnostikrapport

Om du vill ha mer information efter att ha kört en [hälsokontroll](#health-checkup) eller du klickade på en av de [panelen genvägar](#tile-shortcuts), fullständig diagnostikrapport visa relevanta systemstabilitetsindex mått från de senaste 24 timmarna. Om din app får driftavbrott kan representeras det av en orange stapel under tidslinjen. Du kan välja någon av staplarna orange att välja driftstopp för finns i observationer om den driftstopp och föreslagna felsökningsstegen. 

![Diagnostikrapport](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Undersöker programproblem kod

Eftersom många app-problemen är relaterade till problem i programkoden, App Service-diagnostik kan integreras med [Application Insights](https://azure.microsoft.com/services/application-insights/) att markera undantag och beroendeproblem att korrelera med den valda stilleståndstiden. Application Insights har aktiveras separat. 

Om du vill visa Application Insights-undantag och beroenden, Välj den **Webbappen har slutat fungera** eller **Web App långsam** panelen genvägar. 

![Application insights](./media/app-service-diagnostics/AppInsights6.png)

