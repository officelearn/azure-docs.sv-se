---
title: Avkoda X12 meddelanden - Azure Logic Apps | Microsoft Docs
description: "Validera EDI och generera bekräftelser med X12 meddelande avkodarens i Enterprise-Integrationspaket för Logikappar i Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bc2e5c2b351fb87cb763459a9e24368a422ada1b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Avkoda X12 meddelanden för Azure Logikappar med Enterprise-Integrationspaket

Med avkoda X12 meddelande-anslutningen kan du validera kuvertet mot ett handelspartneravtal, verifiera EDI och partner-specifika egenskaper, dela externa utbyten i transaktioner uppsättningar eller bevara hela externa utbyten och generera bekräftelser för bearbetade transaktioner. Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integration konto att använda anslutningstjänsten avkoda X12 meddelande.
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering
* En [X12 avtal](logic-apps-enterprise-integration-x12.md) som redan har definierats i ditt konto för integrering

## <a name="decode-x12-messages"></a>Avkoda X12 meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Avkoda X12 meddelande kopplingen har utlösare, så måste du lägga till en utlösare för att starta logikappen som en utlösare för begäran. Lägg till en utlösare i logik App Designer och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”x12” för filtret. Välj **X12-avkoda X12 meddelandet**.
   
    ![Sök efter ”x12”](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Om du inte tidigare skapade alla anslutningar till ditt konto integration, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integration kontot som du vill ansluta till. 

    ![Ange integration kontoinformation för anslutning](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integration konto * |Ange ett namn för ditt konto för integrering. Se till att appen integration konto och logik finns i samma Azure-plats. |

5.  När du är klar bör din anslutningsinformation likna exemplet. Slutför din anslutning väljer **skapa**.
   
    ![Integration kontoinformation för anslutning](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Efter anslutningen skapas, som visas i det här exemplet väljer du X12 flat fil meddelandet för att avkoda.

    ![Integration konto anslutning som skapats](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Exempel:

    ![Välj X12 flat fil meddelande för avkodning av](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Den faktiska meddelandeinnehåll eller nyttolasten för meddelande matrisen bra eller felaktig, är base64-kodad. Därför måste du ange ett uttryck som bearbetar det här innehållet.
   > Här är ett exempel som bearbetar innehållet i XML-format som du kan ange i kodvy eller genom att använda uttrycksbyggare i designern.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Innehåll exempel](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 avkoda information

X12 avkoda connector utför dessa uppgifter:

* Verifierar kuvertet mot handel partneravtalet
* Verifierar EDI och partner-specifika egenskaper
  * Strukturella EDI-validering och utökade schemavalidering
  * Validering av strukturen för datautbyte kuvertet.
  * Schemavalideringen Envelope mot kontroll schemat.
  * Schemavalideringen av transaktionen set dataelement mot meddelandet schemat.
  * EDI-verifiering utförs på transaktion set dataelement 
* Verifierar att interchange, grupp och transaktionen set kontrollen talen som inte är dubbletter
  * Kontrollerar antalet interchange kontroll mot tidigare mottagna externa utbyten.
  * Kontrollerar kontrollen gruppnumret mot andra grupp kontrollen talen i utbyte.
  * Kontrollerar transaktionen ange kontroll många mot andra transaktion set kontrollen talen i den gruppen.
* Delar upp interchange i transaktionen uppsättningar eller bevarar hela interchange:
  * Dela Interchange som transaktionen uppsättningar - inaktivera transaktion anger vid fel: delningar interchange i transaktion anger och Parsar varje transaktion uppsättning. 
  X12 avkoda åtgärd matar ut bara dessa transaktion anger som inte kan valideras till `badMessages`, och utdata återstående transaktioner anger till `goodMessages`.
  * Dela Interchange som transaktionen uppsättningar - inaktivera utbyte vid fel: delningar interchange i transaktion anger och Parsar varje transaktion uppsättning. 
  Om en eller flera transaktion anger i utbyte inte verifiering, X12 avkoda åtgärd matar ut alla transaktionen anger i den interchange till `badMessages`.
  * Bevara Interchange – inaktivera transaktion anger vid fel: bevara utbyte och bearbeta hela gruppbaserad utbyte. 
  X12 avkoda åtgärd matar ut bara dessa transaktion anger som inte kan valideras till `badMessages`, och utdata återstående transaktioner anger till `goodMessages`.
  * Bevara Interchange – inaktivera utbyte vid fel: bevara utbyte och bearbeta hela gruppbaserad utbyte. 
  Om en eller flera transaktion anger i utbyte inte verifiering, X12 avkoda åtgärd matar ut alla transaktionen anger i den interchange till `badMessages`. 
* Genererar en tekniska och/eller funktionella bekräftelse (om konfigurerad).
  * En teknisk bekräftelse genererar på grund av huvud-verifiering. Tekniska bekräftelsen rapporterar status för bearbetning av ett utbyte huvudet och trailern av adress-mottagare.
  * En funktionell bekräftelse genererar på grund av brödtext validering. Funktionella bekräftelsen rapporterar varje fel uppstod under bearbetning av Mottaget dokument

## <a name="view-the-swagger"></a>Visa swagger
Finns det [swagger information](/connectors/x12/). 

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

