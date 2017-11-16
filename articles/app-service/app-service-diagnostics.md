---
title: "Azure Apptjänst diagnostik översikt | Microsoft Docs"
description: "Lär dig hur du kan felsöka problem med ditt webbprogram med App Service-diagnostik."
keywords: "App service, azure app service, diagnostik, support, webbprogram, felsökning, självhjälp"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: f027e7fbc5866a85e7f55460192a1c99a71e368e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="azure-app-service-diagnostics-overview"></a>Översikt av Azure App Service-diagnostik 

När du kör ett webbprogram som du vill att förbereda för eventuella problem som kan uppstå i 500 fel till dina användare säger som webbplatsen är igång. Apptjänst diagnostics är en intelligent och interaktiva upplevelse för att felsöka ditt webbprogram med krävs ingen konfiguration. När du stöter på problem med ditt webbprogram, peka Apptjänst diagnostik reda på vad är fel att guida dig till rätt information för att snabbt och enkelt kan felsöka och lösa problemet. 
 
Även om det här upplevelsen är användbart när du har problem med ditt webbprogram under de senaste 24 timmarna, blir alla diagnostiska diagram tillgängliga för dig att analysera hela tiden. Ytterligare verktyg för felsökning och länkar till användbar dokumentation och forum finns på den högra kolumnen.

![Startsida](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Hälsotillstånd checkup

Om du inte vet vad är fel med ditt webbprogram eller inte vet var att starta felsökning av problem med din är hälsa checkup ett bra ställe att börja. Hälsotillstånd checkup analyserar dina webbprogram för att ge dig en snabb interaktiva översikt som leder dig vad är felfri och fel, anger du var du undersöka problemet. Dess intelligent och interaktiva gränssnittet får du vägledning genom felsökningen.  

![Hälsotillstånd checkup](./media/app-service-diagnostics/HealthCheckup2.png)

Om ett problem har upptäckts med ett specifikt problemkategori under de senaste 24 timmarna, kan du visa fullständig diagnostisk rapport och diagnostik för Apptjänst kan du uppmanas att visa fler felsökningsråd och nästa steg för en mer interaktiva upplevelse.

![Felsökning och nästa steg](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Panelen genvägar

Om du vet exakt vilken typ av du letar efter information om felsökning, leder panelen genvägar dig direkt till den fullständiga Diagnostikrapporten problemkategori som du är intresserad av. Jämfört med hälsa checkup panelen genvägar är mer direkt, men mindre interaktiv sätt att komma åt din diagnostiska mått.  

![Panelen genvägar](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnostisk rapport

Om du vill ha mer information när du har kört en [hälsa checkup](#health-checkup) eller du klickade på något av de [panelen genvägar](#tile-shortcuts), fullständig diagnostikrapport visa relevanta systemstabilitetsindex mått från de senaste 24 timmarna. Om din app får driftavbrott, representeras med en orange stapel under tidslinjen. Du kan välja något av stillestånd få analyserade Anmärkningar om avbrottstiden och förslag på lösningar. 

![Diagnostisk rapport](./media/app-service-diagnostics/DiagnosticReport5.png)

## <a name="open-app-service-diagnostics"></a>Öppna App Service-diagnostik

För att komma åt Apptjänst diagnostik, navigera till din Apptjänst-webbapp i den [Azure-portalen](https://portal.azure.com). 

I det vänstra navigeringsfönstret klickar du på **diagnostisera och lösa problem**.