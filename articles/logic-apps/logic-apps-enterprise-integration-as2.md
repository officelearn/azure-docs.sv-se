---
title: AS2-meddelanden för B2B enterprise-integration – Azure Logic Apps
description: Utbyta AS2-meddelanden i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760255"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Utbyta AS2-meddelanden för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket

Du kan använda AS2-anslutningstjänsten, som innehåller utlösare och åtgärder för att hantera AS2-kommunikation om du vill arbeta med AS2-meddelanden i Azure Logic Apps. Du kan till exempel använda åtgärderna för att upprätta säkerhet och tillförlitlighet vid överföring av meddelanden:

* [**Koda till AS2-meddelande** åtgärd](#encode) för att tillhandahålla kryptering, digital signering och bekräftelser via meddelande Disposition meddelanden (MDN), vilket hjälper att stöd för icke-repudiation. Den här åtgärden gäller AS2/HTTP-huvuden och utför dessa uppgifter när de konfigurerade:

  * Loggar utgående meddelanden.
  * Krypterar utgående meddelanden.
  * Komprimerar meddelandet.
  * Överför filnamnet i MIME-huvud.

* [**Avkoda AS2-meddelande** åtgärd](#decode) för att tillhandahålla dekryptering digital signering och bekräftelser via meddelande Disposition meddelanden (MDN). Den här åtgärden utförs till exempel följande: 

  * Bearbetar AS2/HTTP-huvuden.
  * Synkroniserar mottagna mdn måste specificeras med de ursprungliga utgående meddelandena.
  * Uppdaterar och korrelerar poster i icke-repudiation-databasen.
  * Skriver poster för AS2-statusrapportering.
  * Utdata nyttolast innehållet som base64-kodad.
  * Anger om mdn måste specificeras krävs. Baserat på AS2-avtal, som anger om mdn måste specificeras ska vara synkron eller asynkron.
  * Genererar synkron eller asynkron mdn måste specificeras som baseras på AS2-avtal.
  * Anger den token för korrelation och egenskaperna för mdn måste specificeras.

  Den här åtgärden utför även dessa uppgifter när de konfigurerade:

  * Verifierar signaturen.
  * Dekrypterar meddelanden.
  * Dekomprimerar meddelandet. 
  * Kontrollera och Tillåt inte dubbletter av meddelande-ID: T.

Den här artikeln visar hur du lägger till AS2-kodning och avkodning åtgärder i en befintlig logic app.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logikapp från där du vill använda AS2-koppling och en utlösare som startar logikappens arbetsflöde. AS2-koppling ger endast åtgärder, inte utlösare. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som har som är associerade med din Azure-prenumeration och länkats till logikappen som du planerar att använda AS2-koppling. Båda logic app och integrering kontot måste finnas i samma plats eller Azure-region.

* Minst två [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) att du redan har definierat i ditt integrationskonto med hjälp av kvalificeraren för AS2-identitet.

* Innan du kan använda AS2-koppling, måste du skapa en AS2 [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md) mellan din affärspartner och store avtalet i ditt integrationskonto.

* Om du använder [Azure Key Vault](../key-vault/key-vault-overview.md) för certifikathantering, kontrollera att dina nycklar i valvet tillåter den **Encrypt** och **dekryptera** åtgärder. Annars misslyckas kodningen och avkoda åtgärder.

  I Azure-portalen går du till ditt nyckelvalv kan visa din valvnyckel **tillåtna åtgärder**, och kontrollera att den **Encrypt** och **dekryptera** operations har valts.

  ![Kontrollera key vault-åtgärder](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Koda AS2-meddelanden

1. Om du inte redan har gjort i den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. Lägg till en ny åtgärd i logikappen i designern. 

1. Under **Välj en åtgärd** och sökningen väljer **alla**. Ange ”koda as2” i sökrutan och välj den här åtgärden: **Koda till AS2-meddelande**.

   ![Välj ”koda till AS2-meddelande”](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Om du inte har en befintlig anslutning till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning, Välj integrationskontot som du vill ansluta och välj **skapa**.

   ![Skapa anslutning till integrationskontot](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Nu ska du ange information för de här egenskaperna:

   | Egenskap  | Beskrivning |
   |----------|-------------|
   | **AS2-från** | Identifieraren för avsändaren som anges av AS2-avtal |
   | **AS2-To** | Identifierare för mottagaren meddelandet som anges av AS2-avtal |
   | **body** | Meddelandenyttolast |
   |||

   Exempel:

   ![Egenskaper för kodning](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Avkoda AS2-meddelanden

1. Om du inte redan har gjort i den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. Lägg till en ny åtgärd i logikappen i designern. 

1. Under **Välj en åtgärd** och sökningen väljer **alla**. Ange ”avkoda as2” i sökrutan och välj den här åtgärden: **Avkoda AS2-meddelande**

   ![Välj ”avkoda AS2-meddelandet”](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Om du inte har en befintlig anslutning till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning, Välj integrationskontot som du vill ansluta och välj **skapa**.

   ![Skapa anslutning till integrationskontot](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. För **brödtext** och **rubriker**, väljer du dessa värden från föregående utlösaren eller åtgärden utdata.

   Anta exempelvis att logikappen tar emot meddelanden via en begäransutlösare. Du kan välja utdata från den utlösaren.

   ![Välj brödtext och rubriker från utdata för begäran](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Exempel

Om du vill prova att distribuera ett fullt fungerande logic app och exempel AS2-scenario, se den [AS2 mall för logikapp och scenario](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/as2/).

## <a name="next-steps"></a>Nästa steg

Läs mer om den [Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md)
