---
title: Koda AS2-meddelanden – Azure Logic Apps | Microsoft Docs
description: Koda som meddelanden med Azure Logic Apps och Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.date: 08/08/2018
ms.openlocfilehash: 6bb19199929a004ee5668a3a6e057a69c24dd752
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122721"
---
# <a name="encode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Koda AS2-meddelanden med Azure Logic Apps och Enterprise-Integrationspaket

För att upprätta säkerhet och tillförlitlighet vid överföring av meddelanden, använder du koda AS2-meddelande-koppling. Den här anslutningstjänsten tillhandahåller digital signering, kryptering och bekräftelser via meddelande Disposition meddelanden (MDN), vilket leder också till stöd för Non-Repudiation.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integrationskonto för att använda anslutningstjänsten för koda AS2-meddelande.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto
* En [AS2-avtal](logic-apps-enterprise-integration-as2.md) som redan har definierats i ditt integrationskonto

## <a name="encode-as2-messages"></a>Koda AS2-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Koda AS2-meddelande-koppling har utlösare, så måste du lägga till en utlösare för att starta logikappen, som en begäransutlösare. Lägg till en utlösare i Logic App Designer, och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”AS2” som filter. Välj **AS2 - koda AS2-meddelandet**.
   
    ![Sök efter ”AS2”](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Om du inte tidigare skapade alla anslutningar till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integrationskontot som du vill ansluta till. 
   
    ![Skapa anslutning till integrationskontot](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Egenskaper med en asterisk krävs.

    | Egenskap  | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integrationskontot * |Ange ett namn för ditt integrationskonto. Se till att din app med integration-kontot och logik finns i samma Azure-plats. |

5.  När du är klar bör anslutningen likna det här exemplet. För att du har skapat anslutningen, Välj **skapa**.
   
    ![anslutningsinformationen för integrering](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. När anslutningen har skapats, som visas i det här exemplet, ange information för **AS2-från**, **AS2-att identifierare** som konfigurerats i ditt avtal och **brödtext**, vilket är meddelandenyttolast.
   
    ![Ange obligatoriska fält](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Information om AS2-kodare

Koda AS2-koppling utför dessa uppgifter: 

* Gäller AS2/HTTP-huvuden
* Loggar utgående meddelanden (om konfigurerad)
* Krypterar utgående meddelanden (om konfigurerad)
* Komprimerar meddelandet (om konfigurerad)
* Överföra filnamn i MIME-huvud (om konfigurerad)


  > [!NOTE]
  > Om du använder Azure Key Vault för certifikathantering, se till att du konfigurerar nycklar för att tillåta den **Encrypt** igen.
  > I annat fall misslyckas AS2-kodning.
  >
  > ![Keyvault dekrypterar](media/logic-apps-enterprise-integration-as2-encode/keyvault1.png)

## <a name="try-this-sample"></a>Testa det här exemplet

Om du vill prova att distribuera ett fullt fungerande logic app och exempel AS2-scenario, se den [AS2 mall för logikapp och scenario](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visa swagger
Se den [swagger information](/connectors/as2/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

