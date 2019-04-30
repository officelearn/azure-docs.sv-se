---
title: Koda EDIFACT-meddelanden – Azure Logic Apps | Microsoft Docs
description: Validera EDI och generera XML med EDIFACT meddelandekodaren för Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.date: 01/27/2017
ms.openlocfilehash: 7396aee56acdf2476ed1bb3cc5a9909349662dc7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097818"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Koda EDIFACT-meddelanden för Azure Logic Apps med Enterprise-Integrationspaket

Med koda EDIFACT-meddelandet anslutningen kan du verifiera EDI och partner-specifika egenskaper, generera ett XML-dokument för varje transaktion och begära en teknisk bekräftelse eller den funktionella bekräftelse.
Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integrationskonto för att använda anslutningstjänsten för koda EDIFACT-meddelandet. 
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto
* En [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt integrationskonto

## <a name="encode-edifact-messages"></a>Koda EDIFACT-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Koda EDIFACT-meddelande-anslutningsapp har utlösare, så måste du lägga till en utlösare för att starta logikappen, som en begäransutlösare. Lägg till en utlösare i Logic App Designer, och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”EDIFACT” som filter. Välj antingen **koda EDIFACT-meddelande med avtalsnamn** eller **koda till EDIFACT-meddelande med identiteter**.
   
    ![Sök EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Om du inte tidigare skapade alla anslutningar till ditt integrationskonto, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integrationskontot som du vill ansluta till.

    ![Skapa anslutning till integrering](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Egenskaper med en asterisk krävs.

    | Egenskap  | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integrationskontot * |Ange ett namn för ditt integrationskonto. Se till att din app med integration-kontot och logik finns i samma Azure-plats. |

5.  När du är klar bör anslutningen likna det här exemplet. För att du har skapat anslutningen, Välj **skapa**.

    ![information om integrering av kontot för anslutning](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Anslutningen har skapats.

    ![anslutning till integrering skapas](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Koda EDIFACT-meddelande med avtalsnamn

Om du väljer att koda EDIFACT-meddelanden med avtalsnamn, öppna den **namn av EDIFACT-avtal** lista anger eller väljer namnet på din EDIFACT-avtal. Ange XML-meddelande att koda.

![Ange namn för EDIFACT-avtal och XML-meddelande att koda](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Koda EDIFACT-meddelande med identiteter

Om du väljer att koda EDIFACT-meddelanden med identiteter, ange avsändaridentifierare, avsändarkvalificerare, mottagaridentifierare och mottagarkvalificerare som konfigurerats i EDIFACT-avtal. Välj XML-meddelande att koda.

![Ange identiteter för avsändaren och mottagaren, Välj XML-meddelande att koda](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT-kodning information

Koda EDIFACT-anslutningsapp utför dessa uppgifter: 

* Lös avtalet genom att matcha avsändarkvalificerare & identifierare och mottagarkvalificerare och identifierare
* Serialiserar EDI-utbytet, konvertera XML-kodade meddelanden till EDI transaktionsuppsättningar i utbytet.
* Gäller transaktion set rubrik och trailer segment
* Genererar ett interchange-kontrollnummer, en gruppkontrollnummer och en transaktionsuppsättningens kontrollnummer för varje utgående utbytet
* Ersätter avgränsare i nyttolasten
* Verifierar EDI- och egenskaper för partner
  * Schemavalideringen transaktionsuppsättningen dataelement mot meddelande-schemat.
  * EDI-validering utförs på transaktionsuppsättningen dataelement.
  * Utökad validering utförs på transaktionsuppsättningen dataelement
* Genererar ett XML-dokument för varje transaktion.
* Begär en teknisk och/eller funktionella bekräftelse (om konfigurerad).
  * Som en teknisk bekräftelse anger CONTRL meddelandet mottagande av ett utbyte.
  * Som en funktionell bekräftelse anger CONTRL meddelandet godkännande eller avvisande i mottagna utbytet, gruppen eller meddelande med en lista över fel eller funktioner som inte stöds

## <a name="view-swagger-file"></a>Visa Swagger-fil
Swagger-information för EDIFACT-anslutningen finns [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

