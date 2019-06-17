---
title: Priser och fakturering – Azure Logic Apps | Microsoft Docs
description: Läs om hur priser och fakturering fungerar för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 776f79d7f32cf23943ecab4133e055993d30c7cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075065"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prismodellen för Azure Logic Apps

[Med Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att skapa och köra automatiserade integration arbetsflöden som kan skala i molnet. Den här artikeln beskrivs hur fakturering och priser fungerar för Azure Logic Apps. Mer specifik prisinformation finns i [Azure Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prismodellen för förbrukning

För nya logic apps som körs i den offentliga eller ”global” Azure Logic Apps-tjänsten, betalar du bara för det du använder. Dessa logikappar använder en förbrukningsbaserad plan och prismodell. I logic app-definition är varje steg en åtgärd. Till exempel åtgärderna är bland annat:

* Utlösare, som är särskilda åtgärder. Alla logikappar kräver en utlösare som det första steget.
* ”Inbyggd” eller inbyggda åtgärder som HTTP, anrop till Azure Functions och API Management och så vidare
* Anrop till kopplingar, till exempel Outlook 365, Dropbox och så vidare
* Kontrollera steg i flödet, till exempel slingor, villkorssatser, och så vidare

Med Azure Logic Apps-mätare alla åtgärder som körs i din logikapp. Mer information om hur fakturering fungerar för [utlösare](#triggers) och [åtgärder](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Fast prismodellen

En [ *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) är ett privat, isolerad och dedikerad sätt för dig att skapa och köra logikappar som kan komma åt resurser i Azure-nätverk. För nya logic apps som körs i en ISE kan du betalar en [fast månadspris](https://azure.microsoft.com/pricing/details/logic-apps) för inbyggda åtgärder och utlösare och för anslutningsappar av standardtyp.

Din ISE innehåller också en kostnadsfria anslutningstjänsten, som innehåller så många *anslutningar* som du vill ha. Användningen av ytterligare Enterprise-anslutningar debiteras baserat på den [Enterprise-pris för resursförbrukning](https://azure.microsoft.com/pricing/details/logic-apps). Endast Allmänt debiteras tillgängliga Enterprise-anslutningar enligt priserna för Enterprise-förbrukning. Förhandsversion av Enterprise-anslutningsappar debiteras enligt den [standardanslutningsprogram rate](https://azure.microsoft.com/pricing/details/logic-apps).

> [!NOTE]
> Inom en ISE, inbyggda utlösare och åtgärder visas den **Core** etikettera och kör i samma ISE som dina logic apps. Standard- och Enterprise-anslutningsappar som visar den **ISE** etikett som körs i samma ISE som dina logic apps. Kopplingar som inte visar etiketten ISE kör i tjänsten för global Logic Apps.

Basenheten ISE har fast kapacitet, så om du behöver större dataflöde, kan du [lägga till fler skalningsenheter](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), antingen under skapandet eller efteråt. Logikappar som körs i en ISE inte drar på dig kostnader för kvarhållning av data.

Mer specifik prisinformation finns i [Azure Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Anslutningar

Azure Logic Apps-anslutningsprogram att logic app åtkomst till appar, tjänster och system i molnet eller lokalt genom att tillhandahålla [utlösare](#triggers), [åtgärder](#actions), eller båda. Kopplingar klassificeras som Standard eller Enterprise. En översikt om dessa anslutningsappar finns i [Anslutningsappar för Azure Logic Apps](../connectors/apis-list.md). Följande avsnitt innehåller mer information om hur faktureringen för utlösare och åtgärder fungerar.

<a name="triggers"></a>

## <a name="triggers"></a>Utlösare

Utlösare är särskilda åtgärder för att skapar en logikappinstans när en viss händelse inträffar. Utlösare fungerar på olika sätt, vilket påverkar hur logikappen mäts. Här är de olika typerna av utlösare som finns i Azure Logic Apps:

* **Avsökningen utlösaren**: Den här utlösaren kontrollerar regelbundet en slutpunkt för meddelanden som uppfyller kriterierna för att skapa en logikappinstans och starta arbetsflödet. Även när inga logikappinstans skapas, Logic Apps-mätare varje avsökning-begäran som en körning. Om du vill ange avsökningsintervallet för att konfigurera utlösaren via Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-utlösaren**: Den här utlösaren väntar på att en klient att skicka en begäran till en viss slutpunkt. Varje begäran som skickas till webhook-slutpunkt räknas som en åtgärdskörning. Till exempel begäran och HTTP-Webhook-utlösaren är båda webhook-utlösare.

* **Upprepningsutlösare**: Den här utlösaren skapar en logikappinstans som baseras på upprepningsintervallet som du har konfigurerat i utlösaren. Du kan till exempel lägger upp en återkommande utlösare som körs var tredje dag eller enligt ett mer komplext schema.

<a name="actions"></a>

## <a name="actions"></a>Åtgärder

Med Azure Logic Apps-mätare ”inbyggd” åtgärder, till exempel HTTP, som inbyggda åtgärder. Till exempel inbyggda åtgärder omfattar HTTP-anrop, anrop från Azure Functions eller API Management, och styra flödet steg, till exempel villkor, slingor, och switch-satser. Varje åtgärd har sina egna åtgärdstyp. T.ex, åtgärder som anropar [kopplingar](https://docs.microsoft.com/connectors) har typen ”ApiConnection”. Dessa anslutningar är klassificerade som Standard eller Enterprise-anslutningsappar, som mäts baserat på deras respektive [priser](https://azure.microsoft.com/pricing/details/logic-apps). Enterprise-anslutningsappar i *förhandsversion* debiteras som Standard kopplingar.

Med Azure Logic Apps-mätare alla lyckade och misslyckade åtgärder som körningar. Logic Apps kan dock inte mäter dessa åtgärder:

* Åtgärder som hämta hoppades över på grund av motsvarar hittills ouppfyllda villkor
* Åtgärder som inte kör eftersom logikappen har stoppats innan du avslutar

För åtgärder som körs i slingor, räknas varje åtgärd för varje cykel uppdaterad i Azure Logic Apps. Anta exempelvis att du har en ”för var och en” loop som bearbetar en lista. Logic Apps taxor en åtgärd i den loopen genom att multiplicera antalet lista över objekt med antalet åtgärder i loopen och lägger till den åtgärd som startar loopen. Därför beräkningen för en lista över 10-objekt är (10 * 1) + 1, vilket resulterar i 11 körningar.

## <a name="disabled-logic-apps"></a>Inaktiverad logikappar

Inaktiverad logic apps inte debiteras eftersom de inte kan skapa nya instanser medan de är inaktiverad.
När du inaktiverar en logikapp, kan alla instanser som körs för närvarande ta lite tid innan de helt slutar.

## <a name="integration-accounts"></a>Integrationskonton

Fast prismodellen gäller [integrationskonton](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utvecklar och testar den [B2B- och EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) funktioner i Azure Logic Apps utan extra kostnad.
Du kan ha ett integrationskonto i varje Azure-region. Varje integrationskontot kan lagra upp till specifika [mängder artefakter](../logic-apps/logic-apps-limits-and-config.md), som innehåller samarbetspartner, avtal, kartor, scheman, sammansättningar, certifikat, batchkonfigurationer och så vidare.

Med Azure Logic Apps erbjuder Free, Basic och Standard-integrationskonton. Nivåerna Basic och Standard stöds av den Logic Apps servicenivåavtal (SLA), medan den kostnadsfria nivån stöds inte av något serviceavtal och har en gräns på dataflöde och användning.

Att välja mellan en kostnadsfri, Basic eller Standard-integrationskonto:

* **Kostnadsfria**: När du vill prova undersökande scenarier, inte produktionsscenarier.

* **Basic**: När du vill att endast meddelandehantering eller för att fungera som en partner för småföretag har som en handel partner-relation med en större företag entitet.

* **Standard**: När du har mer avancerade B2B-relationer och ökat antal entiteter som du måste hantera.

Mer specifik prisinformation finns i [priser för Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Datakvarhållning

Förutom logikappar att köra i en integreringstjänstmiljö (ISE), alla indata och utdata som lagras i din logikapp körningshistorik faktureras baserat på en logikapp [kör kvarhållningsperiod](logic-apps-limits-and-config.md#run-duration-retention-limits). Logikappar som körs i en ISE inte drar på dig kostnader för kvarhållning av data. Mer specifik prisinformation finns i [priser för Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

För att hjälpa dig att övervaka lagringsanvändningen för din logikapp, kan du:

* Visa antalet lagringsenheter i GB som logikappen använder varje månad.
* Visa storlekar för indata och utdata för en specifik åtgärd i din logikapp körningshistorik.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Visa förbrukning av lagringsutrymme för logic app

1. Hitta och öppna logikappen i Azure-portalen.

1. Din logikapp-menyn, under **övervakning**väljer **mått**.

1. I den högra rutan under **diagramrubrik**, från den **mått** väljer **fakturering användning för Storage förbrukning körningar**.

   Det här måttet ger antalet förbrukning lagringsenheter i GB per månad som är faktureras.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Visa indata och utdata storlekar

1. Hitta och öppna logikappen i Azure-portalen.

1. På menyn för din logikapp, Välj **översikt**.

1. I den högra rutan under **Körningshistorik**, Välj det kör som har indata och utdata du vill kontrollera.

1. Under **logikappskörningen**, Välj **körningsinformation**.

1. I den **information om logikappskörningen** fönstret i tabellen åtgärder, som visar status och varaktighet för varje åtgärd, Välj den åtgärd du vill visa.

1. I den **logikappens** fönstret, hitta storlekar för den åtgärden indata och utdata visas under respektive **indatalänkar** och **utdatalänkar**.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure Logic Apps](logic-apps-overview.md)
* [Skapa din första logiska app](quickstart-create-first-logic-app-workflow.md)
