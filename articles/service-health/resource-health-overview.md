---
title: Översikt över Azure Resource Health | Microsoft Docs
description: Översikt över Azure Resource Health
services: Resource health
documentationcenter: ''
author: stephbaron
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 11/16/2018
ms.author: stbaron
ms.openlocfilehash: 918ff674e57a14f685c7f9ef2aaaca5ca064976e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012852"
---
# <a name="azure-resource-health-overview"></a>Översikt över Azure Resource Health
 
Azure Resource Health hjälper dig att diagnostisera och få support när ett Azure-tjänst-problem påverkar dina resurser. Informerar dig om nya och gamla hälsotillståndet för dina resurser. Och du får teknisk support för att hjälpa dig att lösa problem.

Medan [Azure-Status](https://status.azure.com) meddelar dig om tjänstproblem med som påverkar en bred uppsättning Azure-kunder, Resource Health ger dig en anpassad instrumentpanel för hälsotillståndet för dina resurser. Resource Health visar hela tiden resurserna inte var tillgänglig tidigare på grund av problem med Azure-tjänsten. Sedan är det enkelt för dig att förstå om serviceavtal (SLA) har överskridits. 

## <a name="resource-definition-and-health-assessment"></a>Resursen definitions- och utvärdering
En resurs är en specifik instans av en Azure-tjänst: till exempel en virtuell dator, en webbapp eller en SQL-databas.

Resource Health förlitar sig på signaler som orsakats av olika Azure-tjänster för att bedöma huruvida en resurs är felfri eller inte. Om en resurs är felaktiga, analyserar Resource Health ytterligare information för att fastställa orsaken till problemet. Den identifierar också åtgärder som Microsoft tar för att åtgärda problemet eller åtgärder som du kan vidta för att åtgärda orsaken till problemet. 

För ytterligare information om hur hälsotillstånd bedöms, Läs den fullständiga listan över resurstyper och hälsokontroller i [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Hälsostatus
Hälsotillståndet för en resurs visas som en av följande statusar.

### <a name="available"></a>Tillgängligt
Statusen **tillgänglig** innebär att tjänsten inte har identifierats eventuella händelser som påverkar hälsotillståndet för resursen. I fall där resursen har återställts från oplanerade driftstopp under de senaste 24 timmarna, visas den **nyligen löst** meddelande.

![Status för ”tillgänglig” för en virtuell dator med ett meddelande om ”nyligen löst”](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Ej tillgänglig
Statusen **ej tillgänglig** innebär att tjänsten har identifierat en pågående eller icke-plattformen händelse som påverkar hälsotillståndet för resursen.

#### <a name="platform-events"></a>Plattformshändelser
Plattformshändelser har utlösts av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade händelser (till exempel en omstart för oplanerad värden).

Resource Health finns ytterligare information om händelsen och återställningsprocessen. Du kan också kontakta support även om du inte har en aktiv Microsoft supportavtal.

![Status för ”ej tillgänglig” för en virtuell dator på grund av en plattformshändelse](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Icke-plattformshändelser
Icke-plattformshändelser utlöses av användarnas åtgärder. Exempel stoppa en virtuell dator eller når det maximala antalet anslutningar till en Azure Cache för Redis.

![Status för ”ej tillgänglig” för en virtuell dator på grund av en plattformshändelse](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Okänt
Hälsostatus **okänd** indikerar att Resource Health inte har fått information om den här resursen för mer än 10 minuter. Denna status inte är en slutgiltig indikation på resursens tillstånd, är det en viktig datapunkt i felsökningen.

Om resursen körs som förväntat, ändras status för resursen till **tillgänglig** efter ett par minuter.

Om du har problem med resursen, den **okänd** hälsostatus kan det vara att en händelse i plattformen påverkar resursen.

![Status för ”okänt” för en virtuell dator](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degraderad
Hälsostatus **degraderad** anger att din resurs har identifierat en förlust i prestanda, även om det är fortfarande tillgängliga för användning.
Olika resurser har sina egna kriterier för när de anger att en resurs har degraderats.

![Status för ”degraderad” för en virtuell dator](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Rapportering felaktig status
Om du tror att den aktuella hälsostatus är felaktigt, kan du berätta för oss genom att välja **Rapportera felaktig hälsostatus**. Vi uppmanar dig att kontakta support från Resource Health i fall där ett Azure-problem påverkar dig. 

![Kryssrutan för att skicka information om felaktig status](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Historisk information
Du kan komma åt upp till 14 dagar efter hälsohistorik i den **hälsohistorik** avsnittet i Resource Health. 

![Lista över Resource Health-händelser under de senaste två veckorna](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Komma igång
Så här öppnar Resource Health för en resurs:
1.  Logga in på Azure Portal.
2.  Bläddra till resursen.
3.  Resurs-menyn i det vänstra fönstret, Välj **resurshälsa**.

![Öppna Resource Health från vyn](./media/resource-health-overview/from-resource-blade.png)

Du kan även använda Resource Health genom att välja **alla tjänster** och skriva **resurshälsa** i textrutan filter. I den **hjälp + support** väljer [resurshälsa](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Öppna Resource Health från ”alla tjänster”](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Nästa steg

Kolla in de här resurserna och lär dig mer om Resource Health:
-  [Resurstyper och hälsokontroller i Azure Resource Health](resource-health-checks-resource-types.md)
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)




