---
title: Översikt över Azure Resource Health | Microsoft Docs
description: Översikt över Azure Resource Health
services: Resource health
documentationcenter: ''
author: shawntabrizi
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: shawn.tabrizi
ms.openlocfilehash: 99e996f182aac774f2e2565d87fd0debaba1b2d1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
ms.locfileid: "30263130"
---
# <a name="azure-resource-health-overview"></a>Översikt över Azure Resource Health
 
Azure Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure-tjänsten påverkar dina resurser. Det informerar dig om aktuella och tidigare hälsotillståndet för dina resurser. Och ger teknisk support för att hjälpa dig att minimera problem.

Medan [Azure Status](https://status.azure.com) meddelar dig om problem med tjänster som påverkar en bred uppsättning Azure-kunder, Resource Health ger du en anpassad instrumentpanel för hälsotillståndet för dina resurser. Resource Health visar alltid resurserna var inte tillgängliga i förflutna på grund av problem med Azure-tjänsten. Sedan är det enkelt för dig att förstå om ett SLA har överskridits. 

## <a name="resource-definition-and-health-assessment"></a>Resursen definitions- och utvärdering
En resurs är en specifik instans en Azure-tjänst: till exempel en virtuell dator, ett webbprogram eller en SQL-databas.

Resource Health beroende signaler sänds av olika Azure-tjänster för att bedöma om en resurs är felfri eller inte. Om en resurs är i feltillstånd, analyserar Resource Health ytterligare information för att fastställa orsaken till problemet. Du identifierar även åtgärder som Microsoft tar för att åtgärda problemet eller åtgärder som du kan vidta för att åtgärda orsaken till problemet. 

För ytterligare information om hur hälsa används för att utvärdera, granska en fullständig lista över resurstyper och kontroll [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Hälsotillstånd
Hälsotillståndet för en resurs visas som en av följande status.

### <a name="available"></a>Tillgänglig
Statusen **tillgänglig** innebär att tjänsten inte har identifierats eventuella händelser som påverkar hälsotillståndet för resursen. I fall där resursen har återställts från oplanerade driftsavbrott under de senaste 24 timmarna som du ser den **nyligen lösta** meddelande.

![Status för ”tillgänglig” för en virtuell dator med ett meddelande om ”nyligen löst”](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Ej tillgänglig
Statusen **ej tillgänglig** innebär att tjänsten har upptäckt en pågående plattform eller icke-plattform händelse som påverkar hälsotillståndet för resursen.

#### <a name="platform-events"></a>Plattform händelser
Plattform händelser har utlösts av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade händelser (till exempel en oplanerad värden omstart).

Resource Health innehåller ytterligare information om händelsen och återställningsprocessen. Du kan också kontakta support även om du inte har en aktiv Microsoft supportavtal.

![Status för ”ej tillgänglig” för en virtuell dator på grund av en plattformshändelse](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Icke-plattform händelser
Icke-plattform händelser som utlöses av användarnas åtgärder. Exempel stoppar en virtuell dator eller når det maximala antalet anslutningar till ett Redis-cache.

![Status för ”ej tillgänglig” för en virtuell dator på grund av en plattformshändelse](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Okänt
Hälsostatus för **okänd** anger att Resource Health inte har tagit emot information om den här resursen för mer än 10 minuter. Denna status inte är en slutgiltig indikation på tillståndet för resursen, är men ett viktigt datapunkten i felsökningen.

Om resursen körs som förväntat, ändrar status för resursen till **tillgänglig** efter några minuter.

Om du har problem med resursen, den **okänd** hälsostatus kan vara indikationer på att en händelse i plattformen påverkar resursen.

![Status för ”okänt” för en virtuell dator](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degraderad
Hälsostatus för **degraderad** anger att resursen har identifierat en förlust i prestanda, även om det är fortfarande tillgänglig för användning.
Olika resurser har sina egna kriterier för när de anger att en resurs har degraderats.

![Status för ”degraderad” för en virtuell dator](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Rapportering felaktig status
Om du tror att den aktuella hälsostatus är felaktig, du kan berätta för oss genom att välja **Rapportera felaktig hälsostatus**. I fall där en Azure problemet påverkar dig bör du kontakta support från Resource Health. 

![Kryssrutan för att skicka information om felaktig status](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Historisk information
Du kan komma åt upp till 14 dagar hälsohistorik i den **hälsohistorik** delen av Resource Health. 

![Lista över Resource Health händelser under de senaste två veckorna](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Komma igång
Öppna Resource Health för en resurs:
1.  Logga in på Azure Portal.
2.  Bläddra till resursen.
3.  Välj på menyn resurs i den vänstra rutan **Resource health**.

![Öppna Resource Health från vyn](./media/resource-health-overview/from-resource-blade.png)

Du kan också komma åt Resource Health genom att välja **alla tjänster** och skriva **resurshälsa** i filter-textrutan. I den **hjälp + support** väljer [Resource health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Öppna Resource Health från ”alla tjänster”](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Nästa steg

Checka ut dessa resurser om du vill veta mer om Resource Health:
-  [Hälsa och resurstyper kontrollerar i Azure Resource Health](resource-health-checks-resource-types.md)
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)




