---
title: Avkoda X12 meddelanden – Azure Logic Apps | Microsoft Docs
description: Validera EDI och generera bekräftelser med X12 meddelande avkodaren i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: 4a19462f4f849602fd14fe1204f1c7e3c01e6ec4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701454"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Avkoda X12 meddelanden i Azure Logic Apps med Enterprise-Integrationspaket

Med avkodningen X12 meddelande-anslutningen kan kan du verifiera kuvert mot ett handelspartneravtal, verifiera EDI och partner-specifika egenskaper, dela utbyten till transaktioner uppsättningar eller bevara hela utbyten och generera bekräftelser för bearbetade transaktioner. Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integrationskonto för att använda anslutningstjänsten avkodningen X12 meddelande.
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto
* En [X12 avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt integrationskonto

## <a name="decode-x12-messages"></a>Avkoda X12 meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Avkoda X12 meddelande kopplingen har utlösare, så måste du lägga till en utlösare för att starta logikappen, som en begäransutlösare. Lägg till en utlösare i Logic App Designer, och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”x12” som filter. Välj **X12-avkoda X12 meddelande**.
   
    ![Sök efter ”x12”](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Om du inte tidigare skapade alla anslutningar till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integrationskontot som du vill ansluta till. 

    ![Ger information om kontot för anslutning för integrering](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Egenskaper med en asterisk krävs.

    | Egenskap  | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integrationskontot * |Ange ett namn för ditt integrationskonto. Se till att din app med integration-kontot och logik finns i samma Azure-plats. |

5.  När du är klar bör anslutningen likna det här exemplet. För att du har skapat anslutningen, Välj **skapa**.
   
    ![information om integrering av kontot för anslutning](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. När anslutningen har skapats, som visas i det här exemplet väljer du X12 platt filmeddelande att avkoda.

    ![anslutning till integrering skapas](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Exempel:

    ![Välj X12 meddelande för platt fil för avkodning](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Den faktiska meddelandeinnehåll eller nyttolast för meddelande-matris, bra eller dålig, är base64-kodad. Därför måste du ange ett uttryck som bearbetar det här innehållet.
   > Här är ett exempel som bearbetar innehållet i XML-format som du kan ange i kodvyn eller genom att använda Uttrycksverktyget i designern.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Innehåll exempel](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 avkoda information

X12 avkodningen connector utför dessa uppgifter:

* Verifierar kuvert mot handel partneravtalet
* Verifierar EDI- och egenskaper för partner
  * Strukturella EDI-validering och utökade schemavalidering
  * Validering av strukturen för kuvert utbytet.
  * Schemavalideringen kuvertets mot kontrollschemat.
  * Schemavalideringen transaktionsuppsättningen dataelement mot meddelande-schemat.
  * EDI-validering utförs på transaktionsuppsättningen dataelement 
* Verifierar att kontrollnummer för set-utbytet, grupp och transaktionen inte är dubbletter
  * Kontrollerar interchange-kontrollnummer mot tidigare mottagna utbyten.
  * Kontrollerar gruppkontrollnummer mot andra gruppen kontrollnummer i utbytet.
  * Kontrollerar transaktionen angetts kontrollnummer mot andra transaktion set kontrollnummer i gruppen.
* Delar upp interchange i transaktionsuppsättningar eller bevarar hela utbytet:
  * Dela upp Interchange i transaktionsuppsättningar – inaktivera transaktionsuppsättningar vid fel: Delar upp interchange i transaktion anger och Parsar varje transaktionsuppsättning. 
  X12 avkodningen åtgärdens utdata bara dessa transaktion anger som inte kan valideras till `badMessages`, och övriga transaktioner som anger att utdata `goodMessages`.
  * Dela upp Interchange i transaktionsuppsättningar – inaktivera interchange vid fel: Delar upp interchange i transaktion anger och Parsar varje transaktionsuppsättning. 
  Om en eller flera transaktion anger i utbytet verifieringen, X12 avkodningen åtgärdens utdata alla transaktioner som anger i det utbytet till `badMessages`.
  * Bevara Interchange – inaktivera transaktionsuppsättningar vid fel: Bevara utbytet och bearbeta hela gruppbaserade utbytet. 
  X12 avkodningen åtgärdens utdata bara dessa transaktion anger som inte kan valideras till `badMessages`, och övriga transaktioner som anger att utdata `goodMessages`.
  * Bevara Interchange – inaktivera interchange vid fel: Bevara utbytet och bearbeta hela gruppbaserade utbytet. 
  Om en eller flera transaktion anger i utbytet verifieringen, X12 avkodningen åtgärdens utdata alla transaktioner som anger i det utbytet till `badMessages`. 
* Genererar en teknisk och/eller funktionella bekräftelse (om konfigurerad).
  * En teknisk bekräftelse genererar till följd av huvud-verifiering. Teknisk bekräftelsen rapporterar status för bearbetning av ett interchange rubrik och trailer av adress-mottagare.
  * En funktionell bekräftelse genererar ett resultat av brödtext verifiering. Funktionella bekräftelsen rapporterar varje fel vid bearbetning av mottagna dokumentet

## <a name="view-the-swagger"></a>Visa swagger
Se den [swagger information](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

