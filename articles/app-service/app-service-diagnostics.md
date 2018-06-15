---
title: Azure Apptjänst diagnostik översikt | Microsoft Docs
description: Lär dig hur du kan felsöka problem med ditt webbprogram med App Service-diagnostik.
keywords: App service, azure app service, diagnostik, support, webbprogram, felsökning, självhjälp
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
ms.openlocfilehash: 50e0e9f5edc18aac42ee80e232f70e09736124bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33761895"
---
# <a name="azure-app-service-diagnostics-overview"></a>Översikt av Azure App Service-diagnostik 

När du kör ett webbprogram som du vill att förbereda för eventuella problem som kan uppstå i 500 fel till dina användare säger som webbplatsen är igång. Apptjänst diagnostics är en intelligent och interaktiva upplevelse för att felsöka ditt webbprogram med krävs ingen konfiguration. När du stöter på problem med ditt webbprogram, peka Apptjänst diagnostik reda på vad är fel att guida dig till rätt information för att snabbt och enkelt kan felsöka och lösa problemet. 
 
Även om det här upplevelsen är användbart när du har problem med ditt webbprogram under de senaste 24 timmarna, blir alla diagnostiska diagram tillgängliga för dig att analysera hela tiden. Ytterligare verktyg för felsökning och länkar till användbar dokumentation och forum finns på den högra kolumnen.

Diagnostik för Apptjänst fungerar för inte bara din app i Windows, utan också appar på [Linux-behållare](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro), [Apptjänstmiljö](https://docs.microsoft.com/en-us/azure/app-service/environment/intro), och [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Öppna App Service-diagnostik

För att komma åt Apptjänst diagnostik, navigera till din Apptjänst-app eller Apptjänstmiljö i den [Azure-portalen](https://portal.azure.com). I det vänstra navigeringsfönstret klickar du på **diagnostisera och lösa problem**. 

Navigera till funktionen appen och i det övre navigeringsfältet för Azure Functions, klickar du på **plattformsfunktioner** och välj **diagnostisera och lösa problem** från den **övervakning**avsnitt. 

![Startsida](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Hälsotillstånd checkup

Om du inte vet vad är fel med ditt webbprogram eller inte vet var att starta felsökning av problem med din är hälsa checkup ett bra ställe att börja. Hälsotillstånd checkup analyserar dina webbprogram för att ge dig en snabb interaktiva översikt som leder dig vad är felfri och fel, anger du var du undersöka problemet. Dess intelligent och interaktiva gränssnittet får du vägledning genom felsökningen.  

![Hälsotillstånd checkup](./media/app-service-diagnostics/HealthCheckup2.png)

Om ett problem har upptäckts med ett specifikt problemkategori under de senaste 24 timmarna, kan du visa fullständig diagnostisk rapport och diagnostik för Apptjänst kan du uppmanas att visa fler felsökningsråd och nästa steg för en mer interaktiva upplevelse.

![Felsökning och nästa steg](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Panelen genvägar

Om du vet exakt vilken typ av du letar efter information om felsökning, leder panelen genvägar dig direkt till den fullständiga Diagnostikrapporten problemkategori som du är intresserad av. Jämfört med hälsa checkup panelen genvägar är mer direkt, men mindre interaktiv sätt att komma åt din diagnostiska mått. Som en del av panelen genvägar det är också där du hittar **diagnosverktyg** som är mer avancerade verktyg som hjälper dig att undersöka problem relaterade till kod programproblem, långsamt arbete, anslutningssträngar med mera. 

![Panelen genvägar](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnostisk rapport

Om du vill ha mer information när du har kört en [hälsa checkup](#health-checkup) eller du klickade på något av de [panelen genvägar](#tile-shortcuts), fullständig diagnostikrapport visa relevanta systemstabilitetsindex mått från de senaste 24 timmarna. Om din app får driftavbrott, representeras med en orange stapel under tidslinjen. Du kan välja något av fälten orange att välja avbrottstiden Se Anmärkningar om den driftstopp och föreslagna felsökningsstegen. 

![Diagnostisk rapport](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Undersöker programproblem kod

Eftersom många app problem relaterade till problem i programkoden, diagnostik för Apptjänst kan integreras med [Programinsikter](https://azure.microsoft.com/services/application-insights/) om du vill markera undantag och beroende problem att korrelera valda avbrott. Application Insights behöver aktiveras separat. 

Om du vill visa Application Insights-undantag och beroenden, Välj den **Web App ned** eller **Web App långsam** panelen genvägar. 

![Application Insights](./media/app-service-diagnostics/AppInsights6.png)

