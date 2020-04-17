---
title: Skicka och ta emot AS2-meddelanden för B2B
description: Exchange AS2-meddelanden för B2B-företagsintegrationsscenarier med hjälp av Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0e7c34e42d0ab68a5dab9718075f02a85322ce6c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458836"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange AS2-meddelanden för B2B-företagsintegrering i Azure Logic Apps med Enterprise Integration Pack

> [!IMPORTANT]
> Den ursprungliga AS2-kontakten är inaktuell, så se till att du använder **AS2 -kontakten (v2)** i stället. Den här versionen innehåller samma funktioner som den ursprungliga versionen, är inbyggd i Logic Apps-körningen och ger betydande prestandaförbättringar när det gäller dataflöde och meddelandestorlek. Den inbyggda v2-kopplingen kräver inte heller att du skapar en anslutning till ditt integrationskonto. I stället, som beskrivs i förutsättningarna, se till att du länkar ditt integrationskonto till logikappen där du planerar att använda kopplingen.

Om du vill arbeta med AS2-meddelanden i Azure Logic Apps kan du använda AS2-kopplingen, som tillhandahåller utlösare och åtgärder för att hantera AS2-kommunikation. Om du till exempel vill skapa säkerhet och tillförlitlighet vid överföring av meddelanden kan du använda följande åtgärder:

* [ **AS2 Koda** åtgärd](#encode) för att tillhandahålla kryptering, digital signering och bekräftelser via Message Disposition Notifications (MDN), som hjälper till att stödja icke-avståndstagande. Den här åtgärden använder till exempel AS2/HTTP-huvuden och utför dessa uppgifter när de konfigureras:

  * Signerar utgående meddelanden.
  * Krypterar utgående meddelanden.
  * Komprimerar meddelandet.
  * Överför filnamnet i MIME-huvudet.

* [ **AS2 Avkoda** åtgärd](#decode) för att tillhandahålla dekryptering, digital signering och bekräftelser via Message Disposition Notifications (MDN). Den här åtgärden utför till exempel följande uppgifter:

  * Bearbetar AS2/HTTP-huvuden.
  * Stämma av mottagna MDN med de ursprungliga utgående meddelandena.
  * Uppdaterar och korrelerar poster i databasen som inte är avstöjd.
  * Skriver poster för AS2-statusrapportering.
  * Matar in nyttolastinnehåll som base64-kodat.
  * Avgör om MDN krävs. Baserat på AS2-avtalet avgör om MDN ska vara synkrona eller asynkrona.
  * Genererar synkrona eller asynkrona MDN baserat på AS2-avtalet.
  * Anger korrelationstoken och egenskaper på MDN.Sets the correlation tokens and properties on MDNs.

  Den här åtgärden utför även dessa uppgifter när de konfigureras:

  * Verifierar signaturen.
  * Dekrypterar meddelandena.
  * Dekomprimerar meddelandet.
  * Kontrollera och tillåt inte meddelande-ID-dubbletter.

Den här artikeln visar hur du lägger till AS2-kodnings- och avkodningsåtgärder i en befintlig logikapp.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du vill använda AS2-kopplingen och en utlösare som startar logikappens arbetsflöde. AS2-kopplingen innehåller endast åtgärder, inte utlösare. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är kopplat till din Azure-prenumeration och som är länkat till logikappen där du planerar att använda AS2-anslutningen. Både logikappen och integrationskontot måste finnas på samma plats eller Azure-region.

* Minst två [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har definierat i ditt integrationskonto med hjälp av AS2-identitetskvalen.

* Innan du kan använda AS2-kopplingen måste du skapa ett [AS2-avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md) mellan dina handelspartner och lagra avtalet i ditt integrationskonto.

* Om du använder [Azure Key Vault](../key-vault/general/overview.md) för certifikathantering kontrollerar du att dina valvnycklar tillåter åtgärderna **Kryptera** och **dekryptera.** Annars misslyckas kodnings- och avkodningsåtgärderna.

  I Azure-portalen går du till nyckeln i nyckelvalvet, granskar nyckelns **tillåtna åtgärder**och bekräftar att **åtgärderna Kryptera** och **dekryptera** har valts, till exempel:

  ![Kontrollera valvnyckeloperationer](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Koda AS2-meddelanden

1. Om du inte redan har öppnat logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Lägg till en ny åtgärd i logikappen i designern.

1. Under **Välj en åtgärd** och sökrutan väljer du **Alla**. I sökrutan anger du "as2-kod" och ser till att du väljer åtgärden AS2 (v2) : **AS2 Koda**

   ![Välj "AS2-kod"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Nu ger information för dessa egenskaper:

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Meddelande som ska kodas** | Nyttolasten för meddelandet |
   | **AS2 från** | Identifieraren för meddelandeavsändaren enligt ditt AS2-avtal |
   | **AS2 till** | Identifieraren för meddelandemottagaren enligt ditt AS2-avtal |
   |||

   Ett exempel:

   ![Egenskaper för meddelandekodning](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Om du får problem när du skickar signerade eller krypterade meddelanden kan du prova olika SHA256-algoritmformat. AS2-specifikationen innehåller ingen information om SHA256-format, så varje leverantör använder sin egen implementering eller sitt eget format.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Avkoda AS2-meddelanden

1. Om du inte redan har öppnat logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Lägg till en ny åtgärd i logikappen i designern.

1. Under **Välj en åtgärd** och sökrutan väljer du **Alla**. I sökrutan anger du "as2 decode" och ser till att du väljer åtgärden AS2 (v2) : **AS2 Avkoda**

   ![Välj "AS2 Avkoda"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Om **egenskaperna Meddelande ska koda** och **meddelanderubrikerna** väljer du dessa värden från tidigare utlösar- eller åtgärdsutdata.

   Anta till exempel att logikappen tar emot meddelanden via en begärandeutlösare. Du kan välja utdata från den utlösaren.

   ![Välj brödtext och rubriker från begärandeutdata](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Exempel

Information om hur du provar att distribuera en fullt fungerande logikapp och exempel på AS2-scenario finns i [AS2-logikappmallen och scenariot](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel åtgärder och begränsningar som beskrivs i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/as2/). 

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ursprungliga ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
