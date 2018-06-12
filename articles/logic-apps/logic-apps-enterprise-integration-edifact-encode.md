---
title: Koda meddelanden med EDIFACT - Azure Logic Apps | Microsoft Docs
description: Validera EDI och generera XML med EDIFACT meddelandekodare i Enterprise-Integrationspaket för Logikappar i Azure
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: eaad2561254d858af99f06e576d67c05838e1220
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299900"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Koda EDIFACT-meddelanden för Azure Logikappar med Enterprise-Integrationspaket

Med meddelandet koda EDIFACT-anslutningen kan du verifiera EDI och partner-specifika egenskaper, generera ett XML-dokument för varje transaktion och begär en teknisk bekräftelse eller den funktionella bekräftelse.
Om du vill använda denna anslutning måste du lägga till anslutningen till en befintlig utlösare i din logikapp.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [integrering konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration. Du måste ha ett integration konto att använda anslutningstjänsten EDIFACT koda meddelandet. 
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering
* En [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt konto för integrering

## <a name="encode-edifact-messages"></a>Koda EDIFACT-meddelanden

1. [Skapa en logikapp](quickstart-create-first-logic-app-workflow.md).

2. Koda EDIFACT meddelandet kopplingen har utlösare, så måste du lägga till en utlösare för att starta logikappen som en utlösare för begäran. Lägg till en utlösare i logik App Designer och sedan lägga till en åtgärd i din logikapp.

3.  I sökrutan anger du ”EDIFACT” som filter. Välj antingen **koda EDIFACT-meddelandet genom avtalsnamn** eller **koda på EDIFACT meddelandet identiteter**.
   
    ![Sök EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Om du inte tidigare skapade alla anslutningar till ditt konto integration, uppmanas du att skapa anslutningen nu. Namnge din anslutning och välj integration kontot som du vill ansluta till.

    ![Skapa integration konto anslutning](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Egenskaper med en asterisk krävs.

    | Egenskap  | Information |
    | --- | --- |
    | Anslutningsnamn * |Ange ett namn för anslutningen. |
    | Integration konto * |Ange ett namn för ditt konto för integrering. Se till att appen integration konto och logik finns i samma Azure-plats. |

5.  När du är klar bör din anslutningsinformation likna exemplet. Slutför din anslutning väljer **skapa**.

    ![Integration kontoinformation för anslutning](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Anslutningen har skapats.

    ![Integration konto anslutning som skapats](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Koda EDIFACT-meddelande med avtalsnamn

Om du vill koda EDIFACT-meddelanden med avtalsnamn, öppna den **namn EDIFACT-avtal** listan, ange eller välj namnet på din EDIFACT-avtal. Ange den XML-meddelanden om att koda.

![Ange EDIFACT avtalets namn och XML-meddelande att koda](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Koda EDIFACT-meddelande med identiteter

Om du vill koda EDIFACT-meddelanden med identiteter Ange ingen avsändaridentifierare, kvalificerare för avsändaren, mottagaren identifierare och mottagaren kvalificeraren som konfigurerats i EDIFACT-avtal. Välj XML-meddelandet för att koda.

![Ange identiteter för avsändare och mottagare, Välj XML-meddelande att koda](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT koda information

Koda EDIFACT-kopplingen utför dessa uppgifter: 

* Lös avtalet genom att matcha avsändaren kvalificerare & identifierare och mottagaren kvalificerare och identifierare
* Serialiserar EDI-utbyte, konvertera XML-kodade meddelanden till uppsättningar för EDI-transaktion i utbyte.
* Gäller transaktion set-huvudet och trailern segment
* Genererar ett utbyte kontrollen Antal, gruppnumret kontroll och en uppsättning kontrollen transaktionsnumret för varje utgående utbyte
* Ersätter avgränsare i nyttolasten
* Verifierar EDI och partner-specifika egenskaper
  * Schemavalideringen av transaktionen set dataelement mot meddelandet schemat.
  * EDI-verifiering utförs på dataelement för transaktionen uppsättningen.
  * Utökad verifiering utförs på transaktion set dataelement
* Genererar XML-dokument för varje transaktion.
* Begär en tekniska och/eller funktionella bekräftelse (om konfigurerad).
  * Som en teknisk bekräftelse anger CONTRL meddelandet har mottagit ett utbyte.
  * Som en funktionell bekräftelse anger CONTRL meddelandet godkännande eller underkännande mottagna interchange, grupp eller meddelandet med en lista över fel eller funktioner som inte stöds

## <a name="view-swagger-file"></a>Visa Swagger-fil
Swagger-information för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket") 

