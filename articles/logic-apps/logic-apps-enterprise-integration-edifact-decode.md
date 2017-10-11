---
title: Avkoda meddelanden med EDIFACT - Azure Logic Apps | Microsoft Docs
description: "Validera EDI och generera bekräftelser med EDIFACT meddelandet avkodaren i Enterprise-Integrationspaket för Logikappar i Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: e3787b48037360bf6066ddce2bacba6842213b2d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Avkoda EDIFACT-meddelanden för Azure Logikappar med Enterprise-Integrationspaket

Med meddelandet avkoda EDIFACT-anslutningen kan du verifiera EDI och partner-specifika egenskaper, dela externa utbyten i transaktioner uppsättningar eller bevara hela externa utbyten och generera bekräftelser för bearbetade transaktioner. Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integration konto att använda anslutningstjänsten EDIFACT avkoda meddelandet. 
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering
* En [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt konto för integrering

## <a name="decode-edifact-messages"></a>Avkoda EDIFACT-meddelanden

1. [Skapa en logikapp](logic-apps-create-a-logic-app.md).

2. Avkoda EDIFACT meddelandet kopplingen har utlösare, så måste du lägga till en utlösare för att starta logikappen som en utlösare för begäran. Lägg till en utlösare i logik App Designer och sedan lägga till en åtgärd i din logikapp.

3. I sökrutan anger du ”EDIFACT” som filter. Välj **avkoda meddelandet EDIFACT**.
   
    ![Sök EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Om du inte tidigare skapade alla anslutningar till ditt konto integration, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integration kontot som du vill ansluta till.
   
    ![Skapa integration konto](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Egenskaper med en asterisk krävs.

    | Egenskap | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integration konto * |Ange ett namn för ditt konto för integrering. Se till att appen integration konto och logik finns i samma Azure-plats. |

4. När du är klar att anslutningen är klar, Välj **skapa**. Information om anslutningen bör likna exemplet:

    ![Integration kontoinformation](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. När anslutningen har skapats, som visas i det här exemplet, Välj EDIFACT flat fil meddelandet att avkoda.

    ![Integration konto anslutning som skapats](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Exempel:

    ![Välj EDIFACT flat fil meddelande för avkodning av](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT avkodarens information

Avkoda EDIFACT-kopplingen utför dessa uppgifter: 

* Verifierar kuvertet mot handel partneravtalet.
* Löser avtalet genom att matcha avsändaren kvalificerare och identifierare och mottagaren kvalificerare och identifierare.
* Delar en interchange i flera transaktioner när utbyte har fler än en transaktion baserat på avtalet ta emot konfigurationen av prenumerationsinställningar.
* Disassemblerar utbyte.
* Verifierar EDI och partner-specifika egenskaper, inklusive:
  * Validering av interchange kuvert struktur
  * Schemavalideringen Envelope mot kontroll schemat
  * Schemavalideringen av transaktionen set dataelement mot meddelandet schemat
  * EDI-verifiering utförs på transaktion set dataelement
* Verifierar att interchange, grupp och transaktionen set kontrollen talen som inte är dubbletter (om konfigurerad) 
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
* Genererar en Technical (kontroll) och/eller funktionella bekräftelse (om konfigurerad).
  * En teknisk bekräftelse eller CONTRL ACK rapporterar resultatet av en syntaktiska kontroll av fullständig mottagna utbyte.
  * En funktionell bekräftelse om godkänna eller avvisa en mottagna utbyte eller en grupp

## <a name="view-swagger-file"></a>Visa Swagger-fil
Swagger-information för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

