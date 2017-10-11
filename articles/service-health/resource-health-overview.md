---
title: "Översikt av Azure Resource health | Microsoft Docs"
description: "Översikt över Azure Resource health"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 040d58a81a9b41fe660e4276d698bf884f90bb6c
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2017
---
# <a name="azure-resource-health-overview"></a>Översikt över Azure-resurs-hälsa
 
Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.

Medan [Azure Status](https://status.azure.com) informerar om tjänsten problem som påverkar en bred uppsättning Azure-kunder, resurshälsa ger dig en anpassad instrumentpanel för hälsotillståndet för dina resurser. Resurshälsa visar alltid resurserna var inte tillgängliga i förflutna på grund av problem med Azure-tjänsten. Detta gör det enkelt för dig att förstå om ett SLA har överskridits. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>Vad som anses en resurs och hur fungerar resurshälsa bestämmer om en resurs är felfri?
En resurs är en instans av en resurstyp som erbjuds av en Azure-tjänst via Azure Resource Manager, till exempel: en virtuell dator, ett webbprogram eller en SQL-databas.

Resurshälsa är beroende av signaler sänds av olika Azure-tjänster för att bedöma om en resurs är felfri eller inte. Om en resurs är i feltillstånd, analyserar resurshälsa ytterligare information för att fastställa orsaken till problemet. Den visar även de åtgärder som Microsoft tar för att åtgärda problemet eller vilka åtgärder du kan vidta för att åtgärda orsaken till problemet. 

Läs den fullständiga listan över resurstyper och kontroll [Azure resource health](resource-health-checks-resource-types.md) för mer information om hur hälsa utvärderas.

## <a name="health-status-provided-by-resource-health"></a>Hälsostatus som tillhandahålls av resurshälsa
Hälsotillståndet för en resurs är en av följande status:

### <a name="available"></a>Tillgänglig
Tjänsten har inte identifierat eventuella händelser som påverkar hälsotillståndet för resursen. I fall där resursen har återställts från oplanerade driftsavbrott under de senaste 24 timmarna visas den **har nyligen återställts** meddelande.

![Resource health tillgänglig virtuell dator](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Inte tillgänglig
Tjänsten har upptäckt en pågående plattform eller icke-plattform händelse som påverkar hälsotillståndet för resursen.

#### <a name="platform-events"></a>Plattform händelser
Dessa händelser har utlösts av flera komponenter i Azure-infrastrukturen och inkluderar både schemalagda åtgärder som planerat underhåll och oväntat incidenter som en oplanerad värden omstart.

Resurshälsa innehåller ytterligare information om händelsen, återställningsprocessen och kan du kontakta supporten, även om du inte har en aktiv Microsoft supportavtal.

![Resource health tillgänglig virtuell dator på grund av plattformshändelsen](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Icke-plattform händelser
Dessa händelser som utlöses av åtgärder som vidtas av användare, till exempel stoppar en virtuell dator eller når det maximala antalet anslutningar till ett Redis-Cache.

![Resource health tillgänglig virtuella på grund av icke-plattformshändelsen](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Okänd
Den här hälsostatus anger att resurshälsa inte har tagit emot information om den här resursen för mer än 10 minuter. Denna status inte är en slutgiltig angivelse av resursens tillstånd, men det är ett viktigt datapunkten i felsökningen:
* Om resursen körs som förväntat status för resursen uppdateras till tillgänglig efter några minuter.
* Om du har problem med resursen som kan okänd hälsostatus föreslå resursen påverkas av en händelse i plattformen.

![Resource health okänd virtuell dator](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>Rapportera felaktig status
Om du tror aktuella hälsostatus är felaktig för när som helst, du kan berätta för oss genom att klicka på **Rapportera felaktig hälsostatus**. I fall där du påverkas av ett problem med Azure kan rekommenderar vi att du kontakta support från resursbladet för hälsotillstånd. 

![Resurshälsa rapporten felaktig Status](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Historisk Information
Du kan komma åt upp till 14 dagar historiska hälsa data genom att klicka på **Visa historik över** i resursbladet för hälsotillstånd. 

![Resource health rapporthistorik](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Komma igång
Öppna resursen hälsan för en resurs
1.  Logga in på Azure-portalen.
2.  Gå till din resurs.
3.  I menyn resursen finns i den vänstra sidan klickar du på **Resource health**.

![Öppna Resource health från resursbladet](./media/resource-health-overview/from-resource-blade.png)

Du kan också komma åt resurshälsa genom att klicka på **fler tjänster**, och skriva **resurshälsa** i filterrutan att öppna den **hjälp + stöd** bladet. Klicka slutligen på [ **resurshälsa**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Öppna Resource health från flera service](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Nästa steg

Checka ut dessa resurser om du vill veta mer om resurshälsa:
-  [Hälsa och resurstyper kontrollerar i Azure resurshälsa](resource-health-checks-resource-types.md)
-  [Vanliga frågor och svar om Azure-resurs hälsotillstånd](resource-health-faq.md)




