---
title: Koda EDIFACT-meddelanden
description: Verifiera EDI och generera XML med EDIFACT Message Encoder för Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106533"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Koda EDIFACT-meddelanden för Azure Logic Apps med Enterprise-integrationspaket

Med koda EDIFACT Message Connector kan du verifiera egenskaper för EDI och partner, generera ett XML-dokument för varje transaktions uppsättning och begära en teknisk bekräftelse, funktions bekräftelse eller både och.
Om du vill använda den här anslutningen måste du lägga till anslutningen till en befintlig utlösare i din Logic app.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [integrations konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrations konto för att kunna använda kodningen EDIFACT Message Connector. 
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrations konto
* Ett [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt integrations konto

## <a name="encode-edifact-messages"></a>Koda EDIFACT-meddelanden

> [!IMPORTANT]
> EDIFACT-anslutningen har endast stöd för UTF-8-tecken.
> Om utdata innehåller oväntade tecken, kontrol lera att EDIFACT-meddelanden använder UTF-8-teckenuppsättningen.

1. [Skapa en Logic-app](quickstart-create-first-logic-app-workflow.md).

2. EDIFACT Message Connector har inte utlösare, så du måste lägga till en utlösare för att starta din Logic app, t. ex. en begäran-utlösare. Lägg till en utlösare i Logic Apps designer och Lägg sedan till en åtgärd i din Logic app.

3.  I rutan Sök anger du "EDIFACT" som filter. Välj antingen **koda EDIFACT-meddelande efter avtals namn** eller **koda till EDIFACT-meddelande med identiteter**.
   
    ![Sök EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Om du inte tidigare skapade några anslutningar till ditt integrations konto uppmanas du att skapa anslutningen nu. Ge anslutningen ett namn och välj det integrations konto som du vill ansluta.

    ![Skapa integrations konto anslutning](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Egenskaper med en asterisk måste anges.

    | Egenskap | Information |
    | --- | --- |
    | Anslutnings namn * |Ange ett namn för anslutningen. |
    | Integrations konto * |Ange ett namn för ditt integrations konto. Se till att ditt integrations konto och din Logic app finns på samma Azure-plats. |

5.  När du är klar bör din anslutnings information se ut ungefär som i det här exemplet. Klicka på **skapa**om du vill slutföra skapandet av anslutningen.

    ![anslutnings information för integrations konto](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Nu skapas din anslutning.

    ![integrerings konto anslutningen har skapats](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Koda EDIFACT-meddelande efter avtals namn

Om du har valt att koda EDIFACT-meddelanden efter avtals namn öppnar du listan **namn på EDIFACT** -avtal, anger eller väljer ditt EDIFACT-avtals namn. Ange det XML-meddelande som ska kodas.

![Ange EDIFACT avtals namn och XML-meddelande som ska kodas](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Koda EDIFACT-meddelande med identiteter

Om du väljer att koda EDIFACT-meddelanden efter identiteter anger du avsändar-ID, avsändarens kvalificerare, mottagar identifierare och mottagarens kvalificerare enligt konfigurationen i ditt EDIFACT-avtal. Välj det XML-meddelande som ska kodas.

![Ange identiteter för avsändare och mottagare, Välj XML-meddelande för att koda](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Information om EDIFACT-kod

EDIFACT-anslutningsprogrammet för kodning utför följande uppgifter: 

* Lös avtalet genom att matcha avsändarens kvalificerare & identifierare och mottagarens kvalificerare och identifierare
* Serialiserar EDI Interchange och konverterar XML-kodade meddelanden till EDI-transaktions uppsättningar i Interchange.
* Använder transaktions uppsättnings huvud och trailer-segment
* Genererar ett utbytes kontroll nummer, ett grupp kontroll nummer och ett kontroll nummer för transaktions uppsättning för varje utgående utbyte
* Ersätter avgränsare i nytto Last data
* Validerar EDI-och partner-/regionsspecifika egenskaper
  * Schema validering av transaktions uppsättningens data element mot meddelande schemat.
  * EDI-verifiering utförs på transaktions uppsättnings data element.
  * Utökad verifiering utförs på transaktions uppsättnings data element
* Genererar ett XML-dokument för varje transaktions uppsättning.
* Begär en teknisk och/eller funktionell bekräftelse (om den är konfigurerad).
  * Som en teknisk bekräftelse anger CONTRL-meddelandet mottagandet av ett utbyte.
  * Som en funktionell bekräftelse anger CONTRL-meddelandet godkännande eller avvisande av mottagen utbyte, grupp eller meddelande, med en lista över fel eller funktioner som inte stöds

## <a name="view-swagger-file"></a>Visa Swagger-fil
Information om hur du visar Swagger-information för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket") 

