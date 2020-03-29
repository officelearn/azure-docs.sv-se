---
title: Avkoda EDIFACT-meddelanden
description: Validera EDI och generera bekräftelser med EDIFACT-meddelandeavkodaren för Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790660"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Avkoda EDIFACT-meddelanden för Azure Logic Apps med Enterprise Integration Pack

Med avkodnings-EDIFACT-meddelandekopplingen kan du validera EDI- och partnerspecifika egenskaper, dela upp korsningar i transaktionsuppsättningar eller bevara hela korsningar och generera bekräftelser för bearbetade transaktioner. Om du vill använda den här kopplingen måste du lägga till kopplingen i en befintlig utlösare i logikappen.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; du kan skapa ett [gratis konto](https://azure.microsoft.com/free)
* Ett [integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrationskonto för att kunna använda avkodnings-EDIFACT-meddelandeanslutningen. 
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrationskonto
* Ett [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt integrationskonto

## <a name="decode-edifact-messages"></a>Avkoda EDIFACT-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Meddelandekopplingen av avkoda EDIFACT har inga utlösare, så du måste lägga till en utlösare för att starta logikappen, till exempel en begärandeutlösare. Lägg till en utlösare i Logic App Designer och lägg sedan till en åtgärd i logikappen.

3. Skriv "EDIFACT" som filter i sökrutan. Välj **Avkoda EDIFACT-meddelande**.
   
    ![sök EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Om du inte tidigare har skapat några anslutningar till ditt integrationskonto uppmanas du att skapa anslutningen nu. Namnge anslutningen och välj det integrationskonto som du vill ansluta.
   
    ![skapa integrationskonto](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange vilket namn som helst för din anslutning. |
    | Integrationskonto * |Ange ett namn för ditt integrationskonto. Kontrollera att ditt integrationskonto och logikapp finns på samma Azure-plats. |

4. När du är klar med att skapa anslutningen väljer du **Skapa**. Anslutningsinformationen ska se ut ungefär som i det här exemplet:

    ![information om integrationskonto](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. När anslutningen har skapats, som visas i det här exemplet, väljer du det EDIFACT-plattfilsmeddelande som ska avkodas.

    ![anslutning till integrationskonto har skapats](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Ett exempel:

    ![Välj EDIFACT-platt filmeddelande för avkodning](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT-information om avkodare

Avkoda EDIFACT-kopplingen utför dessa uppgifter: 

* Validerar kuvertet mot handelspartneravtal.
* Löser avtalet genom att matcha avsändarens kvalificerare & identifierare och mottagare kvalificera & identifierare.
* Delar upp ett utbyte i flera transaktioner när utbytet har mer än en transaktion baserat på avtalets konfiguration av mottagningsinställningar.
* Demonterar utbytet.
* Validerar EDI och partnerspecifika egenskaper, inklusive:
  * Validering av utbyteskuvertets struktur
  * Schemavalidering av kuvertet mot kontrollschemat
  * Schemavalidering av transaktionsuppsättningsdataelementen mot meddelandeschemat
  * EDI-validering utförd på dataelement för transaktionsuppsättning
* Verifierar att kontrollnumren för utbyte, grupp och transaktionsuppsättning inte är dubbletter (om de är konfigurerade) 
  * Kontrollerar utbyteskontrollnumret mot tidigare mottagna byten. 
  * Kontrollerar gruppkontrollnumret mot andra gruppkontrollnummer i utbytet. 
  * Kontrollerar transaktionsuppsättningens kontrollnummer mot andra transaktionsuppsättningskontrollnummer i den gruppen.
* Delar upp utbytet i transaktionsuppsättningar eller bevarar hela utbytet:
  * Dela utbyte som transaktionsuppsättningar - pausa transaktionsuppsättningar på fel: Delar utbyte i transaktionsuppsättningar och tolkar varje transaktionsuppsättning. 
  Åtgärden X12 Avkoda utdata utdata endast `badMessages`de transaktionsuppsättningar som `goodMessages`misslyckas med validering till och utdata de återstående transaktionerna anges till .
  * Dela utbyte som transaktionsuppsättningar - avbryta utbyte vid fel: Delar utbyte i transaktionsuppsättningar och tolkar varje transaktionsuppsättning. 
  Om en eller flera transaktionsuppsättningar i utbytesvalideringen misslyckas, utdataar `badMessages`åtgärden X12 Avkod alla transaktionsuppsättningar i utbytet till .
  * Bevara utväxling - pausa transaktionsuppsättningar på fel: Bevara utbytet och bearbeta hela batched utbyte. 
  Åtgärden X12 Avkoda utdata utdata endast `badMessages`de transaktionsuppsättningar som `goodMessages`misslyckas med validering till och utdata de återstående transaktionerna anges till .
  * Bevara utväxling - pausa utbyte vid fel: Bevara utbytet och bearbeta hela batched utbyte. 
  Om en eller flera transaktionsuppsättningar i utbytesvalideringen misslyckas, utdataar `badMessages`åtgärden X12 Avkod alla transaktionsuppsättningar i utbytet till .
* Genererar en teknisk (kontroll) och/eller funktionell bekräftelse (om den är konfigurerad).
  * En teknisk bekräftelse eller CONTRL ACK rapporterar resultaten av en syntaktisk kontroll av det fullständiga mottagna utbytet.
  * En funktionell bekräftelse bekräftar acceptera eller avvisa ett mottaget utbyte eller en grupp

## <a name="view-swagger-file"></a>Visa Swagger-fil
Information om hur du visar Swagger-informationen för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack") 

