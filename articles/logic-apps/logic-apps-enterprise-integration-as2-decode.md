---
title: Avkoda AS2-meddelanden – Azure Logic Apps | Microsoft Docs
description: Avkoda som meddelanden med Azure Logic Apps och Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 08/08/2018
ms.openlocfilehash: ca297e1b4a007db3020b4369132b190608484738
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001634"
---
# <a name="decode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Avkoda AS2-meddelanden med Azure Logic Apps och Enterprise-Integrationspaket 

Använd avkoda AS2-meddelande-koppling för att upprätta säkerhet och tillförlitlighet vid överföring av meddelanden. Den här anslutningstjänsten tillhandahåller digital signering, dekryptering och bekräftelser via meddelande Disposition meddelanden (MDN).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integrationskonto för att använda anslutningstjänsten för avkoda AS2-meddelande.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto
* En [AS2-avtal](logic-apps-enterprise-integration-as2.md) som redan har definierats i ditt integrationskonto

## <a name="decode-as2-messages"></a>Avkoda AS2-meddelanden

1. [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Avkoda AS2-meddelande-koppling har utlösare, så måste du lägga till en utlösare för att starta logikappen, som en begäransutlösare. Lägg till en utlösare i Logic App Designer, och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”AS2” som filter. Välj **AS2 - avkoda AS2-meddelandet**.
   
    ![Sök efter ”AS2”](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Om du inte tidigare skapade alla anslutningar till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integrationskontot som du vill ansluta till.
   
    ![Skapa integration anslutning](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integrationskontot * |Ange ett namn för ditt integrationskonto. Se till att din app med integration-kontot och logik finns i samma Azure-plats. |

5.  När du är klar bör anslutningen likna det här exemplet. För att du har skapat anslutningen, Välj **skapa**.

    ![anslutningsinformationen för integrering](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. När anslutningen har skapats, som visas i det här exemplet, Välj **brödtext** och **rubriker** från utdata för begäran.
   
    ![integrering av anslutning som skapats](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Exempel:

    ![Välj brödtext och rubriker från utdata för begäran](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 


## <a name="as2-decoder-details"></a>AS2-avkodaren information

Avkoda AS2-koppling utför dessa uppgifter: 

* Bearbetar AS2/HTTP-huvuden
* Verifierar signaturen (om konfigurerad)
* Dekrypterar meddelanden (om konfigurerad)
* Dekomprimerar meddelandet (om konfigurerad)
* Kontrollera och Tillåt inte dubbletter av meddelande-ID (om konfigurerad)
* Synkroniserar en mottagna MDN med det ursprungliga utgående meddelandet
* Uppdaterar och kopplar poster i oavvislighet databasen
* Skriver poster för AS2-statusrapportering
* Innehållet i utdata-nyttolasten är base64-kodad
* Avgör om en MDN krävs, och om MDN ska vara synkron eller asynkron baserat på konfigurationen i AS2-avtal
* Genererar en synkron eller asynkron MDN (baserat på avtal konfigurationer)
* Anger den token för korrelation och egenskaperna för MDN


  > [!NOTE]
  > Om du använder Azure Key Vault för certifikathantering, se till att du konfigurerar nycklar för att tillåta den **dekryptera** igen.
  > I annat fall misslyckas AS2-avkodning.
  >
  > ![Keyvault dekrypterar](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Testa det här exemplet

Om du vill prova att distribuera ett fullt fungerande logic app och exempel AS2-scenario, se den [AS2 mall för logikapp och scenario](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visa swagger
Se den [swagger information](/connectors/as2/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md) 

