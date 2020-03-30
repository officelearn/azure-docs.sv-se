---
title: Avkoda X12-meddelanden
description: Validera EDI och generera bekräftelser med X12-meddelandeavkodare i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792366"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Avkoda X12-meddelanden i Azure Logic Apps med Enterprise Integration Pack

Med meddelandekopplingen Avkoda X12 kan du validera kuvertet mot ett handelspartneravtal, validera EDI- och partnerspecifika egenskaper, dela upp korsningar i transaktionsuppsättningar eller bevara hela korsningar och generera bekräftelser för bearbetade transaktioner. Om du vill använda den här kopplingen måste du lägga till kopplingen i en befintlig utlösare i logikappen.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; du kan skapa ett [gratis konto](https://azure.microsoft.com/free)
* Ett [integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrationskonto för att kunna använda meddelandekopplingen av koda X12.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrationskonto
* Ett [X12-avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrationskonto

## <a name="decode-x12-messages"></a>Avkoda X12-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Meddelandekopplingen av kodning av X12 har inga utlösare, så du måste lägga till en utlösare för att starta logikappen, till exempel en begärandeutlösare. Lägg till en utlösare i Logic App Designer och lägg sedan till en åtgärd i logikappen.

3.  Skriv "x12" för filtret i sökrutan. Välj **X12 - Avkoda X12 meddelande**.
   
    ![Sök efter "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Om du inte tidigare har skapat några anslutningar till ditt integrationskonto uppmanas du att skapa anslutningen nu. Namnge anslutningen och välj det integrationskonto som du vill ansluta. 

    ![Ange anslutningsinformation för integrationskonto](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange vilket namn som helst för din anslutning. |
    | Integrationskonto * |Ange ett namn för ditt integrationskonto. Kontrollera att ditt integrationskonto och logikapp finns på samma Azure-plats. |

5.  När du är klar bör anslutningsinformationen se ut ungefär som det här exemplet. Om du vill slutföra skapandet av anslutningen väljer du **Skapa**.
   
    ![anslutningsinformation för integrationskonto](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. När anslutningen har skapats, som visas i det här exemplet, väljer du det X12-plattfilsmeddelande som ska avkodas.

    ![anslutning till integrationskonto har skapats](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Ett exempel:

    ![Välj ett fast X12-filmeddelande för avkodning](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Det faktiska meddelandeinnehållet eller nyttolasten för meddelandematrisen, bra eller dåligt, är base64 kodad. Därför måste du ange ett uttryck som bearbetar det här innehållet.
   > Här är ett exempel som bearbetar innehållet som XML som du kan ange i kodvyn eller med hjälp av uttrycksverktyget i designern.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exempel på innehåll](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Information om X12 avkoda

X12-avkodningsanslutningen utför följande uppgifter:

* Validerar kuvertet mot handelspartneravtal
* Validerar EDI- och partnerspecifika egenskaper
  * EDI strukturell validering och utökad schemavalidering
  * Validering av utbyteshöljets struktur.
  * Schemavalidering av kuvertet mot kontrollschemat.
  * Schemavalidering av transaktionsuppsättningsdataelementen mot meddelandeschemat.
  * EDI-validering utförd på dataelement för transaktionsuppsättning 
* Verifierar att kontrollnumren för utbyte, grupp och transaktionsuppsättning inte är dubbletter
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
* Genererar en teknisk och/eller funktionell bekräftelse (om den är konfigurerad).
  * En teknisk bekräftelse genererar som ett resultat av rubrikvalidering. Den tekniska bekräftelsen rapporterar status för behandlingen av en utväxlingshuvud och släpvagn av adressmottagaren.
  * En funktionell bekräftelse genererar som ett resultat av kroppsvalidering. Den funktionella bekräftelsen rapporterar varje fel som påträffades vid bearbetning av det mottagna dokumentet

## <a name="view-the-swagger"></a>Visa swagger
Se [swagger detaljer](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack") 

