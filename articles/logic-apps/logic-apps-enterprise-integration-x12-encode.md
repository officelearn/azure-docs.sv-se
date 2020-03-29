---
title: Koda X12-meddelanden
description: Validera EDI och konvertera XML-kodade meddelanden med X12-meddelandekodare i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792360"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Koda X12-meddelanden i Azure Logic Apps med Enterprise Integration Pack

Med meddelandekopplingen Koda X12 kan du validera EDI- och partnerspecifika egenskaper, konvertera XML-kodade meddelanden till EDI-transaktionsuppsättningar i utbytet och begära en teknisk bekräftelse, funktionell bekräftelse eller båda.
Om du vill använda den här kopplingen måste du lägga till kopplingen i en befintlig utlösare i logikappen.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; du kan skapa ett [gratis konto](https://azure.microsoft.com/free)
* Ett [integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrationskonto för att kunna använda meddelandekopplingen Koda X12.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrationskonto
* Ett [X12-avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrationskonto

## <a name="encode-x12-messages"></a>Koda X12-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Meddelandekopplingen Koda X12 har inga utlösare, så du måste lägga till en utlösare för att starta logikappen, till exempel en begärandeutlösare. Lägg till en utlösare i Logic App Designer och lägg sedan till en åtgärd i logikappen.

3.  Skriv "x12" för filtret i sökrutan. Välj antingen **X12 - Koda till X12-meddelande efter avtalsnamn** eller **X12 - Koda till X12-meddelande av identiteter**.
   
    ![Sök efter "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Om du inte tidigare har skapat några anslutningar till ditt integrationskonto uppmanas du att skapa anslutningen nu. Namnge anslutningen och välj det integrationskonto som du vill ansluta. 
   
    ![anslutning till integrationskonto](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange vilket namn som helst för din anslutning. |
    | Integrationskonto * |Ange ett namn för ditt integrationskonto. Kontrollera att ditt integrationskonto och logikapp finns på samma Azure-plats. |

5.  När du är klar bör anslutningsinformationen se ut ungefär som det här exemplet. Om du vill slutföra skapandet av anslutningen väljer du **Skapa**.

    ![anslutning till integrationskonto har skapats](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Anslutningen har nu skapats.

    ![anslutningsinformation för integrationskonto](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Koda X12-meddelanden efter avtalsnamn

Om du väljer att koda X12-meddelanden efter avtalsnamn öppnar du listan Namn på **X12-avtal,** anger eller väljer ditt befintliga X12-avtal. Ange det XML-meddelande som ska kodas.

![Ange X12-avtalsnamn och XML-meddelande som ska kodas](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Koda X12-meddelanden efter identiteter

Om du väljer att koda X12-meddelanden efter identitet anger du avsändarens identifierare, avsändare kvalificeraren, mottagarens identifierare och mottagare kvalificeraren som konfigurerats i ditt X12-avtal. Markera det XML-meddelande som ska kodas.
   
![Ange identiteter för avsändare och mottagare, välj XML-meddelande som ska kodas](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Information om X12 koda

X12-kodkontakten utför följande uppgifter:

* Avtalslösning genom att matcha kontextegenskaper för avsändare och mottagare.
* Serialiserar EDI-utbytet och konverterar XML-kodade meddelanden till EDI-transaktionsuppsättningar i utbytet.
* Tillämpar segment för transaktionsuppsättningshuvud och trailer
* Genererar ett utbyteskontrollnummer, ett gruppkontrollnummer och ett transaktionsuppsättningskontrollnummer för varje utgående utbyte
* Ersätter avgränsare i nyttolastdata
* Validerar EDI- och partnerspecifika egenskaper
  * Schemavalidering av transaktionsuppsättningsdataelementen mot meddelandeschemat
  * EDI-validering som utförs på dataelement för transaktionsuppsättningar.
  * Utökad validering utförd på dataelement för transaktionsuppsättning
* Begär en teknisk och/eller funktionell bekräftelse (om den är konfigurerad).
  * En teknisk bekräftelse genererar som ett resultat av rubrikvalidering. Den tekniska bekräftelsen rapporterar status för behandlingen av en utväxlingshuvud och släpvagn av adressmottagaren
  * En funktionell bekräftelse genererar som ett resultat av kroppsvalidering. Den funktionella bekräftelsen rapporterar varje fel som påträffades vid bearbetning av det mottagna dokumentet

## <a name="view-the-swagger"></a>Visa swagger
Se [swagger detaljer](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack") 

