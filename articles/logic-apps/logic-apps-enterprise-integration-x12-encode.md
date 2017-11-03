---
title: Koda X12 meddelanden - Azure Logic Apps | Microsoft Docs
description: "Validera EDI och konvertera XML-kodade meddelanden med X12 meddelande kodare i Enterprise-Integrationspaket för Logikappar i Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 29d19364b9a98e351c95f13e68a2e63b9f6439f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Koda X12 meddelanden för Azure Logikappar med Enterprise-Integrationspaket

Med koda X12 meddelande-anslutningen kan du verifiera EDI och partner-specifika egenskaper, konvertera XML-kodade meddelanden till uppsättningar för EDI-transaktion i utbyte och begär en teknisk bekräftelse eller den funktionella bekräftelse.
Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integration konto att använda anslutningstjänsten koda X12 meddelande.
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering
* En [X12 avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt konto för integrering

## <a name="encode-x12-messages"></a>Koda X12 meddelanden

1. [Skapa en logikapp](logic-apps-create-a-logic-app.md).

2. Koda X12 meddelande kopplingen har utlösare, så måste du lägga till en utlösare för att starta logikappen som en utlösare för begäran. Lägg till en utlösare i logik App Designer och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”x12” för filtret. Välj antingen **X12-koda till X12 meddelandet genom avtalsnamn** eller **X12-koda till X12 meddelandet genom identiteter**.
   
    ![Sök efter ”x12”](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Om du inte tidigare skapade alla anslutningar till ditt konto integration, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integration kontot som du vill ansluta till. 
   
    ![Integration konto anslutning](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integration konto * |Ange ett namn för ditt konto för integrering. Se till att appen integration konto och logik finns i samma Azure-plats. |

5.  När du är klar bör din anslutningsinformation likna exemplet. Slutför din anslutning väljer **skapa**.

    ![Integration konto anslutning som skapats](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Anslutningen har skapats.

    ![Integration kontoinformation för anslutning](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Koda X12 meddelanden efter avtalsnamn

Om du väljer att koda X12 meddelanden efter avtalsnamn, öppna den **namnet på X12 avtal** listan, ange eller välj en befintlig X12 avtal. Ange den XML-meddelanden om att koda.

![Ange X12 avtalets namn och XML-meddelande att koda](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Koda X12 meddelanden med identiteter

Om du väljer att koda X12 meddelanden med identiteter, ange ingen avsändaridentifierare, kvalificerare avsändaren, mottagaren identifierare och mottagaren kvalificeraren som konfigurerats i din X12 avtal. Välj XML-meddelandet för att koda.
   
![Ange identiteter för avsändare och mottagare, Välj XML-meddelande att koda](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 koda information

X12 koda connector utför dessa uppgifter:

* Avtalet lösning genom att matcha avsändare och mottagare kontextegenskaper.
* Serialiserar EDI-utbyte, konvertera XML-kodade meddelanden till uppsättningar för EDI-transaktion i utbyte.
* Gäller transaktion set-huvudet och trailern segment
* Genererar ett utbyte kontrollen Antal, gruppnumret kontroll och en uppsättning kontrollen transaktionsnumret för varje utgående utbyte
* Ersätter avgränsare i nyttolasten
* Verifierar EDI och partner-specifika egenskaper
  * Schemavalideringen av transaktionen set dataelement mot meddelandet Schema
  * EDI-verifiering utförs på dataelement för transaktionen uppsättningen.
  * Utökad verifiering utförs på transaktion set dataelement
* Begär en tekniska och/eller funktionella bekräftelse (om konfigurerad).
  * En teknisk bekräftelse genererar på grund av huvud-verifiering. Tekniska bekräftelsen rapporterar status för bearbetning av ett utbyte huvudet och trailern av adress-mottagare
  * En funktionell bekräftelse genererar på grund av brödtext validering. Funktionella bekräftelsen rapporterar varje fel uppstod under bearbetning av Mottaget dokument

## <a name="view-the-swagger"></a>Visa swagger
Finns det [swagger information](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

