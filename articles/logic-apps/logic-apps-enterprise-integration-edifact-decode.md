---
title: Avkoda EDIFACT-meddelanden
description: Verifiera EDI och generera bekräftelser med EDIFACT-meddelande avkodaren för Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: c32b3ee5c4689e960834d543de1ca377e918751d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106295"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Avkoda EDIFACT-meddelanden för Azure Logic Apps med Enterprise-integrationspaket

Med avkoda EDIFACT Message Connector kan du verifiera EDI-och partner-/regionsspecifika egenskaper, dela ändringar i transaktioner eller bevara hela interändringar och generera bekräftelser för bearbetade transaktioner. Om du vill använda den här anslutningen måste du lägga till anslutningen till en befintlig utlösare i din Logic app.

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett Azure-konto; Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free)
* Ett [integrations konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration. Du måste ha ett integrations konto för att kunna använda meddelandet avkoda EDIFACT Message Connector. 
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrations konto
* Ett [EDIFACT-avtal](logic-apps-enterprise-integration-edifact.md) som redan har definierats i ditt integrations konto

## <a name="decode-edifact-messages"></a>Avkoda EDIFACT-meddelanden

> [!IMPORTANT]
> EDIFACT-anslutningen har endast stöd för UTF-8-tecken.
> Om utdata innehåller oväntade tecken, kontrol lera att EDIFACT-meddelanden använder UTF-8-teckenuppsättningen. 

1. [Skapa en Logic-app](quickstart-create-first-logic-app-workflow.md).

2. EDIFACT Message Connector har inte utlösare, så du måste lägga till en utlösare för att starta din Logic app, t. ex. en begäran-utlösare. Lägg till en utlösare i Logic Apps designer och Lägg sedan till en åtgärd i din Logic app.

3. I rutan Sök anger du "EDIFACT" som filter. Välj **avkoda EDIFACT-meddelande**.
   
    ![Sök EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Om du inte tidigare skapade några anslutningar till ditt integrations konto uppmanas du att skapa anslutningen nu. Ge anslutningen ett namn och välj det integrations konto som du vill ansluta.
   
    ![Skapa integrations konto](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Egenskaper med en asterisk måste anges.

    | Egenskap | Information |
    | --- | --- |
    | Anslutnings namn * |Ange ett namn för anslutningen. |
    | Integrations konto * |Ange ett namn för ditt integrations konto. Se till att ditt integrations konto och din Logic app finns på samma Azure-plats. |

4. När du är klar att slutföra skapandet av anslutningen väljer du **skapa**. Din anslutnings information bör se ut ungefär som i det här exemplet:

    ![information om integrations konto](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. När anslutningen har skapats, som du ser i det här exemplet, väljer du det EDIFACT platt fil meddelande som ska avkodas.

    ![integrerings konto anslutningen har skapats](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Exempel:

    ![Välj EDIFACT platt fil meddelande för avkodning](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Information om EDIFACT-avkodare

Avkodningen EDIFACT-koppling utför följande uppgifter: 

* Validerar kuvertet mot handels partner avtal.
* Löser avtalet genom att matcha avsändarens kvalificerare & identifierare och mottagarens kvalificerare &-ID.
* Delar upp ett utbyte i flera transaktioner när växlingen har mer än en transaktion baserat på avtalets konfiguration för mottagnings inställningar.
* Disassemblerar växlingen.
* Validerar EDI-och partner-/regionsspecifika egenskaper, inklusive:
  * Validering av strukturen för utbytes kuvertet
  * Schema validering av kuvertet mot kontroll schema
  * Schema validering av transaktions uppsättningens data element mot meddelande schemat
  * EDI-verifiering utförs på transaktions uppsättnings data element
* Kontrollerar att kontroll numren för växling, grupp och transaktions uppsättning inte är dubbletter (om de har kon figurer ATS) 
  * Kontrollerar växlings kontroll numret mot tidigare mottagna ändringar. 
  * Kontrollerar grupp kontroll numret mot andra grupp kontroll nummer i Interchange. 
  * Kontrollerar transaktions uppsättningens kontroll nummer mot andra kontroll nummer för transaktions uppsättningar i gruppen.
* Delar upp Interchange i transaktions uppsättningar eller bevarar hela utbytet:
  * Dela upp utbyte som transaktions uppsättningar – inaktivera transaktions uppsättningar vid fel: delar upp Interchange i transaktions uppsättningar och parsar varje transaktions uppsättning. 
  X12-avkodnings åtgärden matar bara ut de transaktions uppsättningar som `badMessages`inte kan verifieras till, och de återstående `goodMessages`transaktions uppsättningarna matas ut till.
  * Dela upp utbyte som transaktions uppsättningar – pausa utbyte vid fel: delar upp Interchange i transaktions uppsättningar och parsar varje transaktions uppsättning. 
  Om en eller flera transaktions uppsättningar i växlingen inte kan verifieras, kommer X12-avkodnings åtgärden att mata ut alla `badMessages`transaktions uppsättningar i som Interchange till.
  * Bevara Interchange – pausa transaktions uppsättningar vid fel: bevara Interchange och bearbeta hela det batchade utbytet. 
  X12-avkodnings åtgärden matar bara ut de transaktions uppsättningar som `badMessages`inte kan verifieras till, och de återstående `goodMessages`transaktions uppsättningarna matas ut till.
  * Bevara Interchange – pausa utbyte vid fel: bevara Interchange och bearbeta hela det batchade utbytet. 
  Om en eller flera transaktions uppsättningar i växlingen inte kan verifieras, kommer X12-avkodnings åtgärden att mata ut alla `badMessages`transaktions uppsättningar i som Interchange till.
* Genererar en teknisk (kontroll) och/eller funktions bekräftelse (om den är konfigurerad).
  * En teknisk bekräftelse eller CONTRL ACK rapporterar resultatet av en syntaktisk kontroll av fullständigt mottaget utbyte.
  * En funktionell bekräftelse bekräftar eller avvisar ett mottaget utbyte eller en grupp

## <a name="view-swagger-file"></a>Visa Swagger-fil
Information om hur du visar Swagger-information för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nästa steg
[Läs mer om Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket") 

