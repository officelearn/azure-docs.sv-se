---
title: Hälsoöversikt över Azure Resource
description: Översikt över Azure Resource Health
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159259"
---
# <a name="resource-health-overview"></a>Översikt över resurshälsa
 
Azure Resource Health hjälper dig att diagnostisera och få support för serviceproblem som påverkar dina Azure-resurser. Den rapporterar om den nuvarande och tidigare hälsa dina resurser.

[Azure-statusrapporter](https://status.azure.com) om serviceproblem som påverkar en bred uppsättning Azure-kunder. Resource Health ger dig en personlig instrumentpanel för hälsotillståndet för dina resurser. Resurshälsa visar alla gånger som dina resurser inte har varit tillgängliga på grund av Azure-tjänstproblem. Dessa data gör det enkelt för dig att se om ett serviceavtal har överträtts.

## <a name="resource-definition-and-health-assessment"></a>Resursdefinition och hälsobedömning

En *resurs* är en specifik instans av en Azure-tjänst, till exempel en virtuell dator, webbapp eller SQL-databas. Resurshälsan är beroende av signaler från olika Azure-tjänster för att bedöma om en resurs är felfri. Om en resurs är felfritt analyserar Resource Health ytterligare information för att fastställa orsaken till problemet. Den rapporterar också om åtgärder som Microsoft vidtar för att åtgärda problemet och identifierar saker som du kan göra för att åtgärda det.

Mer information om hur hälsotillstånd bedöms finns i listan över resurstyper och hälsokontroller på [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Hälsostatus

Hälsotillståndet för en resurs visas som en av följande statusar.

### <a name="available"></a>Tillgängligt

*Tillgänglig* innebär att inga händelser har identifierats som påverkar resursens hälsotillstånd. I de fall där resursen har återställts från oplanerade driftstopp under de senaste 24 timmarna visas meddelandet "Nyligen löst".

![Status för *Tillgänglig* för en virtuell dator som har meddelandet "Nyligen löst"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Inte tillgänglig

*Otillgänglig* innebär att tjänsten upptäckte en pågående plattform eller icke-plattformshändelse som påverkar resursens hälsa.

#### <a name="platform-events"></a>Plattformsevenemang

Plattformshändelser utlöses av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade incidenter (till exempel en oplanerad värdomstart eller försämrad värdmaskinvara som förutspås misslyckas efter ett angivet tidsfönster).

Resource Health innehåller ytterligare information om händelsen och återställningsprocessen. Du kan också kontakta Microsoft Support även om du inte har ett aktivt supportavtal.

![Status för *Ej tillgänglig* för en virtuell dator på grund av en plattformshändelse](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Evenemang som inte är plattformar

Icke-plattformshändelser utlöses av användaråtgärder. Exempel på detta är att stoppa en virtuell dator eller nå det maximala antalet anslutningar till Azure Cache för Redis.

![Status för "Ej tillgänglig" för en virtuell dator på grund av en händelse som inte är plattform](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Okänt

*Okänd* innebär att Resource Health inte har fått information om resursen på mer än 10 minuter. Även om den här statusen inte är en slutgiltig indikation på resursens tillstånd är den en viktig datapunkt för felsökning.

Om resursen körs som förväntat ändras resursens status till *Tillgänglig* efter några minuter.

Om du får problem med resursen kan den *okända* hälsostatusen innebära att en händelse på plattformen påverkar resursen.

![Status för *Okänd* för en virtuell dator](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degraderad

*Degraderad* innebär att din resurs upptäckte en förlust i prestanda, även om den fortfarande är tillgänglig för användning.

Olika resurser har sina egna kriterier för när de rapporterar att de är försämrade.

![Status för *Försämrad* för en virtuell dator](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Rapportera en felaktig status

Om du tror att den aktuella hälsostatusen är felaktig kan du meddela oss genom att välja **Rapportera felaktig hälsostatus**. I de fall ett Azure-problem påverkar dig rekommenderar vi att du kontaktar support från Resurshälsa.

![Formulär för att skicka information om en felaktig status](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Information om historik

Du kan komma åt upp till 30 dagars historia i avsnittet **Hälsohistorik** i Resurshälsa.

![Lista över Resource Health-händelser under de senaste två veckorna](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Komma igång

Så här öppnar du Resurshälsa för en resurs:

1. Logga in på Azure Portal.
2. Bläddra till resursen.
3. Välj **Resurshälsa**på resursmenyn i den vänstra rutan .

![Öppna resurshälsa från resursvyn](./media/resource-health-overview/from-resource-blade.png)

Du kan också komma åt Resurshälsa genom att välja **Alla tjänster** och skriva **resurshälsa** i filtertextrutan. Välj [Resurshälsa](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)i **supportfönstret Hjälp +** .

![Öppna Resurshälsa från "Alla tjänster"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Nästa steg

Läs de här referenserna om du vill veta mer om Resurshälsa:
-  [Resurstyper och hälsokontroller i Azure Resource Health](resource-health-checks-resource-types.md)
-  [Vanliga frågor och svar om Azure Resource Health](resource-health-faq.md)
