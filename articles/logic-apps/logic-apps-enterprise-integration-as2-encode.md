---
title: Koda AS2 - meddelanden i Azure Logic Apps | Microsoft Docs
description: "Hur du använder AS2-kodaren i Enterprise-Integrationspaket för Logikappar i Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 7889bf9e4e02143b6bb4c797531afa54f8647ce5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Koda AS2-meddelanden för Azure Logikappar med Enterprise-Integrationspaket

Använd koda AS2 meddelandet kopplingen för att upprätta säkerheten och pålitligheten vid överföring av meddelanden. Den här anslutningen ger digital signering, kryptering och bekräftelser via meddelandet Disposition meddelanden (MDN), vilket leder också till stöd för icke-Repudiation.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integration konto att använda anslutningstjänsten AS2 koda meddelandet.
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering
* En [AS2-avtal](logic-apps-enterprise-integration-as2.md) som redan har definierats i ditt konto för integrering

## <a name="encode-as2-messages"></a>Koda AS2-meddelanden

1. [Skapa en logikapp](logic-apps-create-a-logic-app.md).

2. Koda AS2 meddelande-koppling har utlösare, så måste du lägga till en utlösare för att starta logikappen som en utlösare för begäran. Lägg till en utlösare i logik App Designer och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”AS2” för filtret. Välj **AS2 - koda AS2-meddelandet**.
   
    ![Sök efter ”AS2”](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Om du inte tidigare skapade alla anslutningar till ditt konto integration, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integration kontot som du vill ansluta till. 
   
    ![Skapa anslutning till kontot på integrering](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integration konto * |Ange ett namn för ditt konto för integrering. Se till att appen integration konto och logik finns i samma Azure-plats. |

5.  När du är klar bör din anslutningsinformation likna exemplet. Slutför din anslutning väljer **skapa**.
   
    ![Integration anslutningsinformation](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. När anslutningen har skapats, som visas i det här exemplet, ange information för **AS2-från**, **AS2-att identifierare** som konfigurerats i ditt avtal och **brödtext**, vilket är nyttolast meddelande.
   
    ![Ange obligatoriska fält](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2-kodaren information

Koda AS2-koppling utför dessa uppgifter: 

* Gäller AS2/HTTP-huvuden
* Loggar utgående meddelanden (om konfigurerad)
* Krypterar utgående meddelanden (om konfigurerad)
* Komprimerar meddelandet (om konfigurerad)

## <a name="try-this-sample"></a>Försök med det här exemplet

Om du vill prova att distribuera ett fullt fungerande logik appen och exempel AS2 scenario, finns det [AS2 logik appmallen och scenario](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visa swagger
Finns det [swagger information](/connectors/as2/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

