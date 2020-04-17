---
title: Prissättning & faktureringsmodell
description: Översikt över hur pris- och faktureringsmodellen fungerar för Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: f47c7412bdd5ada1e50d1005b8e740e3f46ffd8d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536241"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Prismodell för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att skapa och köra automatiserade integrationsarbetsflöden som kan skalas i molnet. I den här artikeln beskrivs hur fakturering och prissättning fungerar för Azure Logic Apps. Prispriser finns i [Logic Apps Pricing](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modell för förbrukningspris

För nya logikappar som körs i den offentliga, "globala" Azure Logic Apps-tjänsten med flera innehavare, betalar du bara för det du använder. Dessa logikappar använder en förbrukningsbaserad plan och prismodell. I logikappen är varje steg en åtgärd och Azure Logic Apps mäter alla åtgärder som körs i logikappen.

Åtgärder omfattar till exempel:

* [Utlösare](#triggers), som är särskilda åtgärder. Alla logikappar kräver en utlösare som första steg.

* ["Inbyggda" eller inbyggda åtgärder](../connectors/apis-list.md#built-in) som HTTP, anrop till Azure-funktioner och API-hantering och så vidare

* Samtal till [hanterade anslutningsappar](../connectors/apis-list.md#managed-connectors) som Outlook 365, Dropbox och så vidare

* [Styra arbetsflödesåtgärder](../connectors/apis-list.md#control-workflow) som loopar, villkorssatser och så vidare

[Standardkopplingar debiteras](../connectors/apis-list.md#managed-connectors) till [standardanslutningspriset](https://azure.microsoft.com/pricing/details/logic-apps). Allmänt tillgängliga [Enterprise-anslutningsappar debiteras](../connectors/apis-list.md#managed-connectors) till [företagskopplingspriset,](https://azure.microsoft.com/pricing/details/logic-apps)medan offentliga förhandsversioner Av Företagskopplingar debiteras enligt [standardanslutningspriset](https://azure.microsoft.com/pricing/details/logic-apps).

Läs mer om hur fakturering fungerar på [triggers-](#triggers) och [åtgärdsnivå.](#actions) Eller, för information om gränser, se [Gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modell för fast prissättning

En [ *integrationstjänstmiljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) är ett isolerat sätt för dig att skapa och köra logikappar som kan komma åt resurser i ett virtuellt Azure-nätverk. Logikappar som körs i en ISE medför inte datalagringskostnader. När du skapar en ISE, och endast under skapandet, kan du välja en [ISE-nivå eller "SKU",](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)som har olika [priser:](https://azure.microsoft.com/pricing/details/logic-apps)

* **Premie** ISE: Den här SKU:s basenhet har fast kapacitet, men om du behöver mer dataflöde kan du [lägga till fler skalenheter](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) när ISE skapas eller efteråt. För ISE-gränser finns i [Gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Utvecklare** ISE: Den här SKU:n har ingen möjlighet att skala upp, inget servicenivåavtal (SLA) och inga publicerade gränser. Använd den här SKU:n endast för att experimentera, utveckla och testa, inte produktion eller prestandatestning.

För logikappar som du skapar och kör i en ISE betalar du ett [fast månadspris](https://azure.microsoft.com/pricing/details/logic-apps) för dessa funktioner:

* [Inbyggda](../connectors/apis-list.md#built-in) utlösare och åtgärder

  I en ISE visar inbyggda utlösare och åtgärder **core-etiketten** och körs i samma ISE som dina logikappar.

* [Standardkopplingar](../connectors/apis-list.md#managed-connectors) och [Enterprise-kopplingar,](../connectors/apis-list.md#enterprise-connectors) som gör att du kan ha så många Enterprise-anslutningar du vill

   Standard- och Enterprise-kopplingar som visar **ISE-etiketten** körs i samma ISE som dina logikappar. Kopplingar som inte visar ISE-etiketten körs i den offentliga, "globala" logic apps-tjänsten med flera innehavare. Fast månadspris gäller även för kopplingar som körs i tjänsten med flera innehavare när du använder dem med logikappar som körs i en ISE.

* Användning av [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) utan extra kostnad, baserat på din [ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

  * **Premie** ISE SKU: Ett integrationskonto [på en enda standardnivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Utvecklare** ISE SKU: Ett enda konto för integrering [på den kostnadsfria nivån](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Varje ISE SKU är begränsad till 5 totala integrationskonton. Mot en extra kostnad kan du ha fler integrationskonton, baserat på din ISE SKU:

  * **Premie** ISE SKU: Upp till fyra fler standardkonton. Inga gratis- eller basic-konton.

  * **Utvecklare** ISE SKU: Antingen upp till 4 fler standardkonton eller upp till 5 totalt standardkonton. Inga grundläggande konton.

  Mer information om begränsningar för integrationskonto finns i [Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Du kan läsa mer om [integrationskontonivåer och deras prismodell](#integration-accounts) senare i det här avsnittet.

<a name="connectors"></a>

## <a name="connectors"></a>Anslutningar

Azure Logic Apps-kopplingar hjälper din logikapp att komma åt appar, tjänster och system i molnet eller lokalt genom att tillhandahålla [utlösare,](#triggers) [åtgärder](#actions)eller båda. Kopplingar klassificeras som standard eller Enterprise. En översikt över dessa anslutningsappar finns i [Kopplingar för Azure Logic Apps](../connectors/apis-list.md). Om det inte finns några färdiga kopplingar tillgängliga för REST-API:er som du vill använda i logikapparna kan du skapa [anpassade kopplingar](https://docs.microsoft.com/connectors/custom-connectors), som bara är omslag runt dessa REST-API:er. Anpassade kopplingar faktureras som standardkopplingar. I följande avsnitt finns mer information om hur fakturering för utlösare och åtgärder fungerar.

<a name="triggers"></a>

## <a name="triggers"></a>Utlösare

Utlösare är särskilda åtgärder som skapar en logikappinstans när en viss händelse inträffar. Utlösare fungerar på olika sätt, vilket påverkar hur logikappen mäts. Här är de olika typer av utlösare som finns i Azure Logic Apps:

* **Avsökningsutlösare**: Den här utlösaren kontrollerar kontinuerligt en slutpunkt för meddelanden som uppfyller villkoren för att skapa en logikappinstans och starta arbetsflödet. Även när ingen logikappinstans skapas mäter Logic Apps varje avsökningsbegäran som en körning. Om du vill ange avsökningsintervallet ställer du in utlösaren via Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-utlösare**: Den här utlösaren väntar på att en klient ska skicka en begäran till en viss slutpunkt. Varje begäran som skickas till webhook-slutpunkten räknas som en åtgärdskörning. Utlösaren Begär och HTTP Webhook är till exempel båda webhook-utlösare.

* **Återkommande utlösare:** Den här utlösaren skapar en logikappinstans baserat på upprepningsintervallet som du ställer in i utlösaren. Du kan till exempel ställa in en återkommande utlösare som körs var tredje dag eller på ett mer komplext schema.

<a name="actions"></a>

## <a name="actions"></a>Åtgärder

Azure Logic Apps mäter "inbyggda" åtgärder, till exempel HTTP, som inbyggda åtgärder. Inbyggda åtgärder omfattar till exempel HTTP-anrop, anrop från Azure Functions eller API Management och kontrollflödessteg som villkor, loopar och switch-satser. Varje åtgärd har sin egen åtgärdstyp. Åtgärder som [anropar kopplingar](https://docs.microsoft.com/connectors) har till exempel typen "ApiConnection". Dessa kopplingar klassificeras som Standard- eller Enterprise-kopplingar, som mäts baserat på deras respektive [prissättning](https://azure.microsoft.com/pricing/details/logic-apps). Företagskopplingar i *förhandsversion* debiteras som standardkopplingar.

Azure Logic Apps mäter alla lyckade och misslyckade åtgärder som körningar. Logic Apps mäter dock inte dessa åtgärder:

* Åtgärder som hoppas över på grund av icke uppfyllda villkor
* Åtgärder som inte körs eftersom logikappen stoppades innan den slutade

För åtgärder som körs inuti loopar räknar Azure Logic Apps varje åtgärd för varje cykel i loopen. Anta till exempel att du har en "för varje" loop som bearbetar en lista. Logic Apps mäter en åtgärd i loopen genom att multiplicera antalet listobjekt med antalet åtgärder i loopen och lägger till åtgärden som startar loopen. Så är beräkningen för en 10-punktslista (10 * 1) + 1, vilket resulterar i 11 åtgärdskörningar.

## <a name="disabled-logic-apps"></a>Inaktiverade logikappar

Inaktiverade logikappar debiteras inte eftersom de inte kan skapa nya instanser när de är inaktiverade. När du har inaktiverat en logikapp kan det ta lite tid innan de stoppas helt.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrationskonton

En [fast prismodell](https://azure.microsoft.com/pricing/details/logic-apps) gäller för [integrationskonton](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utveckla och testa [funktionerna för B2B- och EDI- och](logic-apps-enterprise-integration-b2b.md) [XML-bearbetning](logic-apps-enterprise-integration-xml.md) i Azure Logic Apps utan extra kostnad. Varje Azure-prenumeration kan ha upp till en [specifik gräns för integrationskonton](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Varje integrationskonto kan lagra upp till specifik [gräns för artefakter](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), som inkluderar handelspartner, avtal, kartor, scheman, sammansättningar, certifikat, batchkonfigurationer och så vidare.

Azure Logic Apps erbjuder kostnadsfria, grundläggande och standardintegrationskonton. Basic- och Standard-nivåerna stöds av Service Service Service-Service-avtalet (Logic Apps), medan den kostnadsfria nivån inte stöds av ett serviceavtal och har begränsningar för dataflöde och användning. Med undantag för kostnadsfria nivåintegrationskonton kan du ha mer än ett integrationskonto i varje Azure-region. Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

Om du har en [ *integrationstjänstmiljö* (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)antingen [Premium eller Utvecklare,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)kan din ISE ha fem konton för totalt integration. Mer information om hur den fasta prismodellen fungerar för en ISE finns i föregående avsnitt om fast [prissättningsmodell](#fixed-pricing) i det här avsnittet. Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps).

Om du vill välja mellan ett kostnadsfrit, grundläggande eller standardintegrationskonto läser du dessa användningsfallsbeskrivningar:

* **Gratis:** För när du vill prova undersökande scenarier, inte produktionsscenarier

* **Grundläggande:** För när du bara vill hantera meddelanden eller fungera som en liten affärspartner som har en handelspartnersrelation med en större affärsenhet

* **Standard**: För när du har mer komplexa B2B-relationer och ökat antal entiteter som du måste hantera

<a name="data-retention"></a>

## <a name="data-retention"></a>Datakvarhållning

Förutom logikappar som körs i en integrationstjänstmiljö (ISE) faktureras alla indata och utdata som lagras i logikappens körningshistorik baserat på en logikapps [körningskvarhållningsperiod](logic-apps-limits-and-config.md#run-duration-retention-limits). Logikappar som körs i en ISE medför inte datalagringskostnader. Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps).

För att hjälpa dig att övervaka logikappens lagringsförbrukning kan du:

* Visa antalet lagringsenheter i GB som logikappen använder varje månad.
* Visa storlekarna för en viss åtgärds indata och utdata i logikappens körningshistorik.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Visa förbrukning för logikapplagring

1. Hitta och öppna logikappen i Azure-portalen.

1. Välj **Mått**under **Övervakning**på logikappens meny .

1. Välj **Faktureringsanvändning för körning av lagringsförbrukning**under **Diagramrubrik**i den högra rutan i listan **Tabellrubrik** .

   Det här måttet ger dig antalet lagringsförbrukningsenheter i GB per månad som faktureras.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Visa åtgärdsinmatnings- och utdatastorlekar

1. Hitta och öppna logikappen i Azure-portalen.

1. På logikappens meny väljer du **Översikt**.

1. I den högra rutan under **Historik för körs**väljer du den körning som har de in- och utdata som du vill kontrollera.

1. Under **Logikappkörning**väljer du **Kör information**.

1. I informationsfönstret **Logikapp kör information** i åtgärdstabellen, som visar varje åtgärds status och varaktighet, väljer du den åtgärd du vill visa.

1. I **åtgärdsfönstret Logikapp** hittar du storleken för åtgärdens indata respektive utdata under **länken Ingångar** och **Länken Utdata**.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure Logic Apps](logic-apps-overview.md)
* [Skapa din första logiska app](quickstart-create-first-logic-app-workflow.md)
