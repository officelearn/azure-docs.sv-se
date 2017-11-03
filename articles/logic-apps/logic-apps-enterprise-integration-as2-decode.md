---
title: Avkoda AS2 - meddelanden i Azure Logic Apps | Microsoft Docs
description: "Hur du använder AS2-avkodarens i Enterprise-Integrationspaket för Logikappar i Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a7920b2509fe368c6f7d55e17fe0bf0020c4562c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Avkoda AS2-meddelanden för Azure Logikappar med Enterprise-Integrationspaket 

Använd avkoda AS2 meddelandet kopplingen för att upprätta säkerheten och pålitligheten vid överföring av meddelanden. Den här anslutningen ger digital signering dekryptering och bekräftelser via meddelandet Disposition meddelanden (MDN).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integration konto att använda anslutningstjänsten AS2 avkoda meddelandet.
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering
* En [AS2-avtal](logic-apps-enterprise-integration-as2.md) som redan har definierats i ditt konto för integrering

## <a name="decode-as2-messages"></a>Avkoda AS2-meddelanden

1. [Skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md).

2. Avkoda AS2 meddelande-koppling har utlösare, så måste du lägga till en utlösare för att starta logikappen som en utlösare för begäran. Lägg till en utlösare i logik App Designer och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”AS2” för filtret. Välj **AS2 - avkoda AS2-meddelandet**.
   
    ![Sök efter ”AS2”](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Om du inte tidigare skapade alla anslutningar till ditt konto integration, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integration kontot som du vill ansluta till.
   
    ![Skapa integration anslutning](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integration konto * |Ange ett namn för ditt konto för integrering. Se till att appen integration konto och logik finns i samma Azure-plats. |

5.  När du är klar bör din anslutningsinformation likna exemplet. Slutför din anslutning väljer **skapa**.

    ![Integration anslutningsinformation](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. När anslutningen har skapats, som visas i det här exemplet, Välj **brödtext** och **huvuden** från utdata för begäran.
   
    ![Integration anslutning som har skapats](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Exempel:

    ![Välj brödtext och rubriker från utdata för begäran](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>AS2 avkodarens information

Avkoda AS2-koppling utför dessa uppgifter: 

* Bearbetar AS2/HTTP-huvuden
* Verifierar signaturen (om konfigurerad)
* Dekrypterar meddelanden (om konfigurerad)
* Dekomprimerar meddelandet (om konfigurerad)
* Synkroniserar en mottagna MDN med ursprungliga utgående meddelande
* Uppdateringar och korrelerar poster i oavvislighet databasen
* Skriver poster för AS2 statusrapportering
* Innehållet i utdata-nyttolasten är base64-kodade
* Anger om en MDN krävs, och om MDN ska synkron eller asynkron baserat på konfigurationen i AS2-avtal
* Genererar en synkron eller asynkron MDN (baserat på avtal konfigurationer)
* Ställer in token Korrelations och egenskaper på MDN

## <a name="try-this-sample"></a>Försök med det här exemplet

Om du vill prova att distribuera ett fullt fungerande logik appen och exempel AS2 scenario, finns det [AS2 logik appmallen och scenario](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Visa swagger
Finns det [swagger information](/connectors/as2/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md) 

