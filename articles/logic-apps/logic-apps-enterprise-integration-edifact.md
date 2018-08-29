---
title: EDIFACT-meddelanden för B2B enterprise-integration – Azure Logic Apps | Microsoft Docs
description: Utbyta EDIFACT-meddelanden i EDI-formatet för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.date: 07/26/2016
ms.openlocfilehash: 2da672e1f55af1e38ae0a3fa90b7ecb10d2f17c7
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128385"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Utbyta EDIFACT-meddelanden för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket

Innan du kan utbyta EDIFACT-meddelanden för Azure Logic Apps, måste du skapa ett EDIFACT-avtal och lagra det avtalet i ditt integrationskonto. Här följer stegen för hur du skapar ett EDIFACT-avtal.

> [!NOTE]
> Den här sidan beskriver EDIFACT-funktioner för Azure Logic Apps. Mer information finns i [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration  
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan är definierade i ditt integrationskonto

> [!NOTE]
> När du skapar ett avtal måste innehållet i de meddelanden som du får eller skicka till och från partnern matcha avtalstypen.

När du [skapar ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [lägga till partner](logic-apps-enterprise-integration-partners.md), du kan skapa ett EDIFACT-avtal genom att följa dessa steg.

## <a name="create-an-edifact-agreement"></a>Skapa ett EDIFACT-avtal 

1. Logga in på [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. Välj på Azure-huvudmenyn **alla tjänster**. Ange ”-integration” i sökrutan och välj sedan **integrationskonton**.

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Om **alla tjänster** inte visas kan du behöva expanderar du menyn först. Högst upp på menyn minimerade, Välj **visa textetiketter**.

3. Under **Integrationskonton**, Välj integrationskontot där du vill skapa avtalet.

   ![Välj var du vill skapa avtalet för integrationskontot](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Välj **avtal**. Om du inte har ett avtal panel, Lägg till panelen först.   

   ![Välj panelen ”avtal”](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. På sidan avtal väljer **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Under **Lägg till**, ange en **namn** för ditt avtal. För **avtalstyp**väljer **EDIFACT**. Välj den **värdpartner**, **Värdidentiteten**, **Gästpartner**, och **Gästidentitet** för ditt avtal.

   ![Ange avtalsinformation](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Egenskap  | Beskrivning |
   | --- | --- |
   | Namn |Avtalets namn |
   | Avtalstyp | Bör vara EDIFACT |
   | Värdpartner |Ett avtal måste både en värdens och gästens partner. Den mottagande partnern representerar organisationen som konfigurerar avtalet. |
   | Värd-identitet |En identifierare för den mottagande partnern |
   | Gästpartner |Ett avtal måste både en värdens och gästens partner. Gästpartner representerar den organisation som gör affärer med den mottagande partnern. |
   | Gästidentitet |En identifierare för gästpartner |
   | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
   | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal hanterar fått meddelanden

Nu när du har ställt in egenskaperna avtal kan konfigurera du hur detta avtal identifierar och hanterar inkommande meddelanden som tas emot från din partner via detta avtal.

1. Under **Lägg till**väljer **ta emot inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

   **Ta emot inställningarna** är uppdelad i följande avsnitt: identifierare, bekräftelse, scheman, kontrollnummer, verifiering och interna inställningar.

   ![Konfigurera ”Mottagningsinställningarna”](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap  | Beskrivning |
| --- | --- |
| UNB6.1 (referenslösenord för mottagare) |Ange ett alfanumeriskt värde mellan 1 och 14 tecken. |
| UNB6.2 (referenskvalificerare för mottagare) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |

### <a name="acknowledgments"></a>Bekräftelser

| Egenskap  | Beskrivning |
| --- | --- |
| Inleverans av meddelande (CONTRL) |Markera kryssrutan för att returnera en teknisk bekräftelse (CONTRL) till utbytet avsändaren. Bekräftelsen skickas till utbytet avsändaren baserat på de skicka inställningar för avtalet. |
| Bekräftelse (CONTRL) |Välj den här kryssrutan om du vill returnera en funktionell bekräftelse (CONTRL) till utbytet avsändaren bekräftelsen skickas till utbytet avsändaren baserat på de skicka inställningar för avtalet. |

### <a name="schemas"></a>Scheman

| Egenskap  | Beskrivning |
| --- | --- |
| UNH2.1 (TYP) |Välj en uppsättning transaktionstyp. |
| UNH2.2 (VERSION) |Ange det meddelande-versionsnumret. (Minimum, ett tecken, maximum, tre tecken). |
| UNH2.3 (UTGÅVA) |Ange det meddelande-versionsnumret. (Minimum, ett tecken, maximum, tre tecken). |
| UNH2.5 (ASSOCIERADE TILLDELADE KOD) |Ange koden som är tilldelade. (Högst sex tecken. Måste vara alfanumeriskt). |
| UNH2.1 (APP-AVSÄNDAR-ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. |
| UNH2.2 (APP-AVSÄNDARKODSKVALIFICERARE) |Ange ett alfanumeriskt värde med högst fyra tecken. |
| SCHEMAT |Välj det tidigare överförda schema som du vill använda från ditt associerade integrationskontot. |

### <a name="control-numbers"></a>Kontrollnummer
| Egenskap  | Beskrivning |
| --- | --- |
| Tillåt inte dubbletter av Interchange-kontrollnummer |Välj den här egenskapen för att blockera duplicerade utbyten. Om åtgärden avkoda EDIFACT kontrollerar att interchange-kontrollnummer (UNB5) för mottagna utbytet inte matchar ett tidigare bearbetade interchange-kontrollnummer. Om en matchning hittas, bearbetas inte utbytet. |
| Leta efter UNB5-dubletter varje (dagar) |Om du väljer att inte tillåta dubbla interchange-kontrollnummer, du kan ange hur många dagar när du ska utföra kontrollen genom att ge lämpligt värde för den här inställningen. |
| Tillåt inte dubletter av gruppkontrollnummer |Välj den här egenskapen för att blockera utbyten med dubblettgruppens kontrollnummer (UNG5). |
| Tillåt inte dubletter av transaktionsuppsättningsnummer |Välj den här egenskapen för att blockera utbyten med kontrollnummer för dubbletttransaktionsuppsättning set-kontrollnummer (UNH1). |
| Kontrollnummer för EDIFACT-bekräftelse |Om du vill ange referensnummer för transaktionen set för användning i en bekräftelse, ange ett värde för prefixet, en mängd referensnummer och ett suffix. |

### <a name="validations"></a>Valideringar

När du har slutfört raderna verifiering, läggs en annan automatiskt. Om du inte anger några regler använder verifiering raden ”standard”.

| Egenskap  | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ av EDI-meddelande. |
| EDI-validering |Utföra EDI-validering för-datatyper som definieras av schemat EDI egenskaper, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte är EDI, verifiering på kravet på elementet tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt ledande/avslutande nollor |Behålla alla ytterligare inledande eller avslutande noll och utrymme tecken. Inte ta bort dessa tecken. |
| Trimma ledande/avslutande nollor |Ta bort inledande eller avslutande noll och blanksteg. |
| Princip för avslutande avgränsare |Generera avslutande avgränsare. <p>Välj **otillåtna** att förbjuda avslutande avgränsare och avgränsare i mottagna utbytet. Om utbytet har avslutande avgränsare och avgränsare, deklareras utbytet inte giltig. <p>Välj **valfritt** att acceptera utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatorisk** när mottagna utbyte måste ha avslutande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Interna inställningar

| Egenskap  | Beskrivning |
| --- | --- |
| Skapa tomma XML-taggar om avslutande avgränsare är tillåtna |Välj den här kryssrutan ska utbytet avsändaren innehåller tomma XML-taggar för avslutande avgränsare. |
| Dela upp interchange i transaktionsuppsättningar – inaktivera transaktionsuppsättningar vid fel|Tolkar varje transaktion som angetts i ett utbyte i ett separat XML-dokument genom att tillämpa lämpliga kuvert till transaktionsuppsättningen med. Inaktivera endast transaktionsuppsättningar som inte kan valideras. |
| Dela upp interchange i transaktionsuppsättningar – inaktivera interchange vid fel|Tolkar varje transaktion som angetts i ett utbyte i ett separat XML-dokument genom att tillämpa lämpliga intervall. Inaktivera hela interchange när en eller flera transaktionsuppsättningar i utbytet verifieringen. | 
| Bevara Interchange – inaktivera transaktionsuppsättningar vid fel |Utbytet förblir intakta, skapar ett XML-dokument för hela gruppbaserade utbytet. Inaktivera endast transaktionsuppsättningar som inte kan valideras när du fortsätter att bearbeta alla andra transaktionsuppsättningar. |
| Bevara interchange – inaktivera interchange vid fel |Utbytet förblir intakta, skapar ett XML-dokument för hela gruppbaserade utbytet. Inaktivera hela interchange när en eller flera transaktionsuppsättningar i utbytet verifieringen. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur detta avtal identifierar och hanterar utgående meddelanden som du skickar till dina partner via detta avtal.

1.  Under **Lägg till**väljer **skicka inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med din partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Skicka inställningar** är uppdelad i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontrollnummer och verifieringar.

    ![Konfigurera ”skicka inställningar”](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap  | Beskrivning |
| --- | --- |
| UNB1.2 (syntaxversion) |Välj ett värde mellan **1** och **4**. |
| UNB2.3 (omvänd routingadress för avsändare) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB3.3 (omvänd routingadress för mottagare) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB6.1 (referenslösenord för mottagare) |Ange ett alfanumeriskt värde med minst en och högst 14 tecken. |
| UNB6.2 (referenskvalificerare för mottagare) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |
| UNB7 (programreferens-ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken |

### <a name="acknowledgment"></a>Bekräftelse
| Egenskap  | Beskrivning |
| --- | --- |
| Inleverans av meddelande (CONTRL) |Välj den här kryssrutan om den värdbaserade partnern förväntas ta emot en teknisk bekräftelse (CONTRL). Den här inställningen anger att den värdbaserade partner som skickar meddelandet, begär en bekräftelse från gästpartner. |
| Bekräftelse (CONTRL) |Välj den här kryssrutan om den värdbaserade partnern förväntas ta emot en funktionell bekräftelse (CONTRL). Den här inställningen anger att den värdbaserade partner som skickar meddelandet, begär en bekräftelse från gästpartner. |
| Skapa SG1/SG4-slinga för godkända transaktionsuppsättningar |Om du väljer att begära en funktionell bekräftelse, markera kryssrutan för att tvinga generering av SG1/SG4 loopar i funktionella CONTRL bekräftelser för godkända transaktionsuppsättningar. |

### <a name="schemas"></a>Scheman
| Egenskap  | Beskrivning |
| --- | --- |
| UNH2.1 (TYP) |Välj en uppsättning transaktionstyp. |
| UNH2.2 (VERSION) |Ange det meddelande-versionsnumret. |
| UNH2.3 (UTGÅVA) |Ange det meddelande-versionsnumret. |
| SCHEMAT |Välj scheman som ska användas. Scheman finns i ditt integrationskonto. För att komma åt dina scheman, länka din integrationskontot till logikappen. |

### <a name="envelopes"></a>Kuvert
| Egenskap  | Beskrivning |
| --- | --- |
| UNB8 (prioritetskod för bearbetning) |Ange en alfabetisk värde som inte är mer än ett tecken. |
| UNB10 (kommunikationsavtal) |Ange ett alfanumeriskt värde med minst ett tecken och högst 40 tecken. |
| UNB11 (testindikator) |Markera kryssrutan för att ange att utbytet genereras är testdata |
| Tillämpa UNA-segment (tjänststrängråd) |Markera kryssrutan för att generera en UNA-segmentet för datautbyte som ska skickas. |
| Tillämpa UNG-segment (funktionsgrupprubrik) |Markera kryssrutan för att skapa gruppering segment i rubriken till funktionsgruppen meddelanden som skickats till gästpartner. Följande värden används för att skapa UNG-segment: <p>För **UNG1**, ange ett alfanumeriskt värde med minst ett tecken och högst sex tecken. <p>För **unh2.1**, ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **unh2.2**, ange ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG3.1**, ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **UNG3.2**, ange ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG6**, ange ett alfanumeriskt värde med minst en och högst tre tecken. <p>För **UNG7.1**, ange ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **UNG7.2**, ange ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **UNG7.3**, ange ett alfanumeriskt värde med minst 1 tecken och högst 6 tecken. <p>För **UNG8**, ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Förutom de teckenuppsättningar kan du ange en annan uppsättning avgränsare som ska användas för varje meddelandetyp. Om en teckenuppsättning inte har angetts för ett visst meddelande-schema, används den standardinställda teckenuppsättningen.

| Egenskap  | Beskrivning |
| --- | --- |
| UNB1.1 (systemidentifierare) |Välj EDIFACT-teckenuppsättningen som ska användas för utgående utbytet. |
| Schema |Välj ett schema från den nedrullningsbara listan. En ny rad läggs automatiskt när du har slutfört varje rad. Välj uppsättningen avgränsare som du vill använda, baserat på avgränsare beskrivningarna nedan för det aktuella schemat. |
| Indatatyp |Välj en typ av användarindata från den nedrullningsbara listan. |
| Komponentavgränsare |Ange ett enskilt tecken för att avgränsa sammansatta dataelement. |
| Dataelement-avgränsare |Ange ett enskilt tecken för att avgränsa enkla element i sammansatt dataelement. |
| Segmentsterminator |Ange ett enskilt tecken för att ange ett EDI-segment är slut. |
| Suffix |Välj det tecken som används med det segment-ID: t. Om du anger ett suffix kan dataelementet segment Begränsare vara tom. Om segmentterminator lämnas tomt, måste du ange ett suffix. |

### <a name="control-numbers"></a>Kontrollnummer
| Egenskap  | Beskrivning |
| --- | --- |
| UNB5 (Interchange-kontrollnummer) |Ange ett prefix, ett intervall med värden för interchange-kontrollnummer och ett suffix. Dessa värden används för att generera en utgående utbytet. Prefix och suffix är valfria, medan kontrollnummer krävs. Kontrollnummer ökas för varje ny message; prefix och suffix desamma. |
| UNG5 (gruppkontrollnummer) |Ange ett prefix, ett intervall med värden för interchange-kontrollnummer och ett suffix. Dessa värden används för att generera gruppkontrollnummer. Prefix och suffix är valfria, medan kontrollnummer krävs. Kontrollnummer ökas för varje nytt meddelande tills det största värdet har uppnåtts; prefix och suffix desamma. |
| UNH1 (referensnummer för meddelanderubrik) |Ange ett prefix, ett intervall med värden för interchange-kontrollnummer och ett suffix. Dessa värden används för att generera referensnummer i meddelandet. Prefix och suffix är valfria, medan referensnumret måste anges. Referensnummer ökas för varje ny message; prefix och suffix desamma. |

### <a name="validations"></a>Valideringar

När du har slutfört raderna verifiering, läggs en annan automatiskt. Om du inte anger några regler använder verifiering raden ”standard”.

| Egenskap  | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ av EDI-meddelande. |
| EDI-validering |Utföra EDI-validering för-datatyper som definieras av EDI-egenskaperna för schemat, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte är EDI, verifiering på kravet på elementet tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt ledande/avslutande nollor |Behålla alla ytterligare inledande eller avslutande noll och utrymme tecken. Inte ta bort dessa tecken. |
| Trimma ledande/avslutande nollor |Ta bort inledande eller avslutande noll tecken. |
| Princip för avslutande avgränsare |Generera avslutande avgränsare. <p>Välj **otillåtna** att förbjuda avslutande avgränsare och avgränsare i skickade utbytet. Om utbytet har avslutande avgränsare och avgränsare, deklareras utbytet inte giltig. <p>Välj **valfritt** att skicka utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatorisk** om skickade utbyte måste ha avslutande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta ditt skapade avtal

1.  När du är klar med att alla dina avtal egenskaper på den **Lägg till** väljer **OK** har skapat ditt avtal och gå tillbaka till ditt integrationskonto.

    Ditt nyligen tillagda avtal nu visas i din **avtal** lista.

2.  Du kan också visa dina avtal i din integrering Kontoöversikt. På din integration meny väljer **översikt**och välj sedan den **avtal** panelen. 

    ![Välj panelen ”avtal”](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Visa Swagger-fil
Swagger-information för EDIFACT-anslutningen finns [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Läs mer
* [Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  

