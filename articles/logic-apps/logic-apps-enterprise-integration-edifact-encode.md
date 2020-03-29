---
title: Koda EDIFACT-meddelanden
description: Validera EDI och generera XML med EDIFACT-meddelandekodare för Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 257cbd0b1a68ddd2b16235e6f8dec5d5b0eb10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790655"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Koda EDIFACT-meddelanden för Azure Logic Apps med Enterprise Integration Pack

Med meddelandekopplingen Koda EDIFACT kan du validera EDI- och partnerspecifika egenskaper, generera ett XML-dokument för varje transaktionsuppsättning och begära en teknisk bekräftelse, funktionell bekräftelse eller båda.
Om du vill använda den här kopplingen måste du lägga till kopplingen i en befintlig utlösare i logikappen.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; du kan skapa ett [gratis konto](https://azure.microsoft.com/free)
* Ett [integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrationskonto för att kunna använda meddelandekopplingen Koda EDIFACT. 
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrationskonto
* Ett [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt integrationskonto

## <a name="encode-edifact-messages"></a>Koda EDIFACT-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Meddelandekopplingen Koda EDIFACT har inga utlösare, så du måste lägga till en utlösare för att starta logikappen, till exempel en begärandeutlösare. Lägg till en utlösare i Logic App Designer och lägg sedan till en åtgärd i logikappen.

3.  Skriv "EDIFACT" som filter i sökrutan. Välj antingen **Koda EDIFACT-meddelande efter avtalsnamn** eller **Koda till EDIFACT-meddelande av identiteter**.
   
    ![sök EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Om du inte tidigare har skapat några anslutningar till ditt integrationskonto uppmanas du att skapa anslutningen nu. Namnge anslutningen och välj det integrationskonto som du vill ansluta.

    ![skapa anslutning till integrationskonto](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange vilket namn som helst för din anslutning. |
    | Integrationskonto * |Ange ett namn för ditt integrationskonto. Kontrollera att ditt integrationskonto och logikapp finns på samma Azure-plats. |

5.  När du är klar bör anslutningsinformationen se ut ungefär som det här exemplet. Om du vill slutföra skapandet av anslutningen väljer du **Skapa**.

    ![anslutningsinformation för integrationskonto](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Anslutningen har nu skapats.

    ![anslutning till integrationskonto har skapats](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Koda EDIFACT-meddelande efter avtalsnamn

Om du väljer att koda EDIFACT-meddelanden efter avtalsnamn öppnar du listan Namn på **EDIFACT-avtal,** anger eller väljer ditt EDIFACT-avtalsnamn. Ange det XML-meddelande som ska kodas.

![Ange EDIFACT-avtalsnamn och XML-meddelande som ska kodas](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Koda EDIFACT-meddelande efter identiteter

Om du väljer att koda EDIFACT-meddelanden efter identitet anger du avsändarens identifierare, avsändare kvalificerare, mottagareidentifierare och mottagare kvalificerare som konfigurerats i ditt EDIFACT-avtal. Markera det XML-meddelande som ska kodas.

![Ange identiteter för avsändare och mottagare, välj XML-meddelande som ska kodas](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT Koda detaljer

Koda EDIFACT-anslutningen utför dessa uppgifter: 

* Lösa avtalet genom att matcha avsändarens kvalificerare &-kvalifieraren och mottagarens kvalificerare och identifierare
* Serialiserar EDI-utbytet och konverterar XML-kodade meddelanden till EDI-transaktionsuppsättningar i utbytet.
* Tillämpar segment för transaktionsuppsättningshuvud och trailer
* Genererar ett utbyteskontrollnummer, ett gruppkontrollnummer och ett transaktionsuppsättningskontrollnummer för varje utgående utbyte
* Ersätter avgränsare i nyttolastdata
* Validerar EDI- och partnerspecifika egenskaper
  * Schemavalidering av transaktionsuppsättningsdataelementen mot meddelandeschemat.
  * EDI-validering som utförs på dataelement för transaktionsuppsättningar.
  * Utökad validering utförd på dataelement för transaktionsuppsättning
* Genererar ett XML-dokument för varje transaktionsuppsättning.
* Begär en teknisk och/eller funktionell bekräftelse (om den är konfigurerad).
  * Som en teknisk bekräftelse anger CONTRL-meddelandet mottagandet av ett utbyte.
  * Som en funktionell bekräftelse anger CONTRL-meddelandet godkännande eller avvisande av det mottagna utbytet, gruppen eller meddelandet, med en lista över fel eller funktioner som inte stöds

## <a name="view-swagger-file"></a>Visa Swagger-fil
Information om hur du visar Swagger-informationen för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack") 

