---
title: Koda X12 meddelanden – Azure Logic Apps | Microsoft Docs
description: Verifiera EDI och konvertera XML-kodade meddelanden med X12 meddelande encoder i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 871d6d2b2019372bd258f8909ed0feeeddac4af7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106526"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Koda X12 meddelanden i Azure Logic Apps med Enterprise-Integrationspaket

Med koda X12 meddelande-anslutningen kan du verifiera EDI och partner-specifika egenskaper, konvertera XML-kodade meddelanden till EDI transaktionsuppsättningar i utbytet och begära en teknisk bekräftelse eller den funktionella bekräftelse.
Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integrationskonto för att använda anslutningstjänsten koda X12 meddelande.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto
* En [X12 avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrationskonto

## <a name="encode-x12-messages"></a>Koda X12 meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Koda X12 meddelande kopplingen har utlösare, så måste du lägga till en utlösare för att starta logikappen, som en begäransutlösare. Lägg till en utlösare i Logic App Designer, och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”x12” som filter. Välj antingen **X12-koda till X12 meddelande med avtalsnamn** eller **X12-koda till X12 meddelande med identiteter**.
   
    ![Sök efter ”x12”](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Om du inte tidigare skapade alla anslutningar till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integrationskontot som du vill ansluta till. 
   
    ![anslutning till integrering](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Egenskaper med en asterisk krävs.

    | Egenskap  | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integrationskontot * |Ange ett namn för ditt integrationskonto. Se till att din app med integration-kontot och logik finns i samma Azure-plats. |

5.  När du är klar bör anslutningen likna det här exemplet. För att du har skapat anslutningen, Välj **skapa**.

    ![anslutning till integrering skapas](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Anslutningen har skapats.

    ![information om integrering av kontot för anslutning](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Koda X12 meddelanden med avtalsnamn

Om du väljer att koda X12 meddelanden med avtalsnamn, öppna den **namnet på X12 avtal** lista anger eller väljer din befintliga X12 avtal. Ange XML-meddelande att koda.

![Ange X12 avtalets namn och XML-meddelande att koda](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Koda X12 meddelanden med identiteter

Om du väljer att koda X12 meddelanden efter identiteter, ange avsändaridentifierare, avsändarkvalificerare, mottagaridentifierare och mottagarkvalificerare som konfigurerats i din X12 avtal. Välj XML-meddelande att koda.
   
![Ange identiteter för avsändaren och mottagaren, Välj XML-meddelande att koda](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 koda information

X12 koda connector utför dessa uppgifter:

* Avtalsmatchningen genom att matcha kontextegenskaperna för avsändaren och mottagaren.
* Serialiserar EDI-utbytet, konvertera XML-kodade meddelanden till EDI transaktionsuppsättningar i utbytet.
* Gäller transaktion set rubrik och trailer segment
* Genererar ett interchange-kontrollnummer, en gruppkontrollnummer och en transaktionsuppsättningens kontrollnummer för varje utgående utbytet
* Ersätter avgränsare i nyttolasten
* Verifierar EDI- och egenskaper för partner
  * Schemavalideringen av transaktionsuppsättningen dataelement mot meddelandet schemat
  * EDI-validering utförs på transaktionsuppsättningen dataelement.
  * Utökad validering utförs på transaktionsuppsättningen dataelement
* Begär en teknisk och/eller funktionella bekräftelse (om konfigurerad).
  * En teknisk bekräftelse genererar till följd av huvud-verifiering. Teknisk bekräftelsen rapporterar status för bearbetning av ett interchange rubrik och trailer av adress-mottagare
  * En funktionell bekräftelse genererar ett resultat av brödtext verifiering. Funktionella bekräftelsen rapporterar varje fel vid bearbetning av mottagna dokumentet

## <a name="view-the-swagger"></a>Visa swagger
Se den [swagger information](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

