---
title: Avkoda EDIFACT-meddelanden – Azure Logic Apps | Microsoft Docs
description: Validera EDI och generera bekräftelser med avkodaren för EDIFACT-meddelande för Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.date: 01/27/2017
ms.openlocfilehash: b101922d15a3f90c29eff51c223d2ea7dc30ddf2
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125360"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Avkoda EDIFACT-meddelanden för Azure Logic Apps med Enterprise-Integrationspaketet

Med avkoda EDIFACT-meddelandet anslutningen kan du verifiera EDI och partner-specifika egenskaper, dela utbyten till transaktioner uppsättningar eller bevara hela utbyten och generera bekräftelser för bearbetade transaktioner. Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integrationskonto för att använda anslutningstjänsten för avkoda EDIFACT-meddelandet. 
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto
* En [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt integrationskonto

## <a name="decode-edifact-messages"></a>Avkoda EDIFACT-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Avkoda EDIFACT-meddelande-anslutningsapp har utlösare, så måste du lägga till en utlösare för att starta logikappen, som en begäransutlösare. Lägg till en utlösare i Logic App Designer, och sedan lägga till en åtgärd i din logikapp.

3. I sökrutan anger du ”EDIFACT” som filter. Välj **avkoda EDIFACT-meddelandet**.
   
    ![Sök EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Om du inte tidigare skapade alla anslutningar till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integrationskontot som du vill ansluta till.
   
    ![Skapa integrationskontot](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Egenskaper med en asterisk krävs.

    | Egenskap  | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integrationskontot * |Ange ett namn för ditt integrationskonto. Se till att din app med integration-kontot och logik finns i samma Azure-plats. |

4. När du är klar att skapa anslutningen, väljer **skapa**. Anslutningen bör likna det här exemplet:

    ![information om kontot för integrering](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. När anslutningen har skapats, som visas i det här exemplet, Välj EDIFACT platt filmeddelande att avkoda.

    ![anslutning till integrering skapas](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Exempel:

    ![Välj EDIFACT platt filmeddelande för avkodning](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT-avkodaren information

Avkoda EDIFACT-anslutningsapp utför dessa uppgifter: 

* Verifierar kuvert mot handel partneravtal.
* Löser avtalet genom att matcha avsändarkvalificerare och identifierare och mottagarkvalificerare och identifierare.
* Delar upp en utbytet i flera transaktioner när utbytet har mer än en transaktion baserat på avtalet får Inställningskonfiguration.
* Disassemblerar utbytet.
* Verifierar EDI och partner-specifika egenskaper, inklusive:
  * Verifiering av utbytet kuvert-struktur
  * Schemavalideringen kuvertets mot kontrollschemat
  * Schemavalideringen transaktionsuppsättningen dataelement mot meddelande schemat
  * EDI-validering utförs på transaktionsuppsättningen dataelement
* Verifierar att kontrollnummer för set-utbytet, grupp och transaktionen inte är dubbletter (om konfigurerad) 
  * Kontrollerar interchange-kontrollnummer mot tidigare mottagna utbyten. 
  * Kontrollerar gruppkontrollnummer mot andra gruppen kontrollnummer i utbytet. 
  * Kontrollerar transaktionen angetts kontrollnummer mot andra transaktion set kontrollnummer i gruppen.
* Delar upp interchange i transaktionsuppsättningar eller bevarar hela utbytet:
  * Dela upp Interchange i transaktionsuppsättningar – inaktivera transaktionsuppsättningar vid fel: delar upp interchange i transaktion anger och Parsar varje transaktionsuppsättning. 
  X12 avkodningen åtgärdens utdata bara dessa transaktion anger som inte kan valideras till `badMessages`, och övriga transaktioner som anger att utdata `goodMessages`.
  * Dela upp Interchange i transaktionsuppsättningar – inaktivera interchange vid fel: delar upp interchange i transaktion anger och Parsar varje transaktionsuppsättning. 
  Om en eller flera transaktion anger i utbytet verifieringen, X12 avkodningen åtgärdens utdata alla transaktioner som anger i det utbytet till `badMessages`.
  * Bevara Interchange – inaktivera transaktionsuppsättningar vid fel: bevara utbytet och bearbeta hela gruppbaserade utbytet. 
  X12 avkodningen åtgärdens utdata bara dessa transaktion anger som inte kan valideras till `badMessages`, och övriga transaktioner som anger att utdata `goodMessages`.
  * Bevara Interchange – inaktivera interchange vid fel: bevara utbytet och bearbeta hela gruppbaserade utbytet. 
  Om en eller flera transaktion anger i utbytet verifieringen, X12 avkodningen åtgärdens utdata alla transaktioner som anger i det utbytet till `badMessages`.
* Genererar en Technical (kontroll) och/eller funktionella bekräftelse (om konfigurerad).
  * En teknisk bekräftelse eller CONTRL ACK rapporterar resultaten från en syntaktiska kontroll av fullständig mottagna utbytet.
  * En funktionell bekräftelse om godkänna eller avvisa en mottagna utbytet eller en grupp

## <a name="view-swagger-file"></a>Visa Swagger-fil
Swagger-information för EDIFACT-anslutningen finns [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

