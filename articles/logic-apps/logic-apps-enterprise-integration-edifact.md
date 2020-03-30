---
title: EDIFACT-meddelanden för B2B-integrering
description: Exchange EDIFACT-meddelanden i EDI-format för B2B-företagsintegrering i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/26/2016
ms.openlocfilehash: 3ada12a0cde122fb78815a1d3241d8acb9da2580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651465"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange EDIFACT-meddelanden för B2B-företagsintegrering i Azure Logic Apps med Enterprise Integration Pack

Innan du kan utbyta EDIFACT-meddelanden mot Azure Logic Apps måste du skapa ett EDIFACT-avtal och lagra det avtalet i ditt integrationskonto. Här är stegen för hur du skapar ett EDIFACT-avtal.

> [!NOTE]
> Den här sidan täcker EDIFACT-funktionerna för Azure Logic Apps. Mer information finns i [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett [integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration  
* Minst två [partner](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt integrationskonto

> [!NOTE]
> När du skapar ett avtal måste innehållet i de meddelanden som du får eller skickar till och från partnern matcha avtalstypen.

När du [har skapat ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [lagt till partner](logic-apps-enterprise-integration-partners.md)kan du skapa ett EDIFACT-avtal genom att följa dessa steg.

## <a name="create-an-edifact-agreement"></a>Skapa ett EDIFACT-avtal 

1. Logga in på [Azure-portalen](https://portal.azure.com "Azure Portal"). 

2. På huvudmenyn i Azure väljer du **Alla tjänster**. I sökrutan anger du "integration" och väljer sedan **Integrationskonton**.

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Om **Alla tjänster** inte visas kanske du måste expandera menyn först. Högst upp på den komprimerade menyn väljer du **Visa textetiketter**.

3. Under **Integrationskonton**väljer du det integrationskonto där du vill skapa avtalet.

   ![Välj integrationskonto där avtalet ska skapas](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Välj **avtal**. Om du inte har en avtalspanel lägger du till panelen först.   

   ![Välj panel "Avtal"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. På sidan Avtal väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Under **Lägg till**anger du ett **namn** för ditt avtal. För **avtalstyp**väljer du **EDIFACT**. Välj **värdpartner,** **värdidentitet,** **gästpartner**och **gästidentitet** för ditt avtal.

   ![Ange information om avtalet](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Egenskap | Beskrivning |
   | --- | --- |
   | Namn |Avtalets namn |
   | Typ av avtal | Bör vara EDIFACT |
   | Värdpartner |Ett avtal kräver både en värd och gästpartner. Värdpartnern representerar den organisation som konfigurerar avtalet. |
   | Värdidentitet |En identifierare för värdpartnern |
   | Gästpartner |Ett avtal kräver både en värd och gästpartner. Gästpartnern representerar organisationen som gör affärer med värdpartnern. |
   | Gästidentitet |En identifierare för gästpartnern |
   | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
   | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur avtalet hanterar mottagna meddelanden

Nu när du har angett avtalsegenskaperna kan du konfigurera hur det här avtalet identifierar och hanterar inkommande meddelanden som tas emot från din partner via det här avtalet.

1. Under **Lägg till**väljer du Ta emot **inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig. Om du vill ha egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

   **Inleveransinställningarna** är ordnade i följande avsnitt: Identifierare, Bekräftelse, scheman, kontrollnummer, validering och interna inställningar.

   ![Konfigurera "Mottagningsinställningar"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. När du är klar måste du spara inställningarna genom att välja **OK**.

Nu är ditt avtal klart att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap | Beskrivning |
| --- | --- |
| UNB6.1 (Lösenord för mottagare) |Ange ett alfanumeriskt värde mellan 1 och 14 tecken. |
| UNB6.2 (Kvalificerare för mottagarens referens) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |

### <a name="acknowledgments"></a>Tack

| Egenskap | Beskrivning |
| --- | --- |
| Mottagande av meddelande (CONTRL) |Markera den här kryssrutan om du vill returnera en teknisk (CONTRL) bekräftelse till utväxlingsavsändaren. Bekräftelsen skickas till utbytesavsändaren baserat på skicka inställningar för avtalet. |
| Bekräftelse (CONTRL) |Markera den här kryssrutan om du vill returnera en funktionell (CONTRL) bekräftelse till utväxlingsavsändaren Bekräftelsen skickas till utväxlingsavsändaren baserat på skicka inställningar för avtalet. |

### <a name="schemas"></a>Scheman

| Egenskap | Beskrivning |
| --- | --- |
| UNH2.1 (TYP) |Välj en transaktionstyp. |
| UNH2.2 (VERSION) |Ange meddelandeversionsnumret. (Minimum, ett tecken; max tre tecken). |
| UNH2.3 (RELEASE) |Ange numret för meddelandeutgåvan. (Minimum, ett tecken; max tre tecken). |
| UNH2.5 (ASSOCIERAD TILLDELAD KOD) |Ange den tilldelade koden. (Max, sex tecken. Måste vara alfanumeriskt). |
| UNG2.1 (APP AVSÄNDARE ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. |
| UNG2.2 (KVALIFICERARE FÖR APPAVSÄNDARES KOD) |Ange ett alfanumeriskt värde med högst fyra tecken. |
| Schemat |Välj det tidigare uppladdade schema som du vill använda från ditt associerade integrationskonto. |

### <a name="control-numbers"></a>Kontrollnummer

| Egenskap | Beskrivning |
| --- | --- |
| Tillåt dubbletter av utväxlingskontrollnummer |Om du vill blockera dubbla korsningar markerar du den här egenskapen. Om du väljer det här alternativet kontrollerar EDIFACT-avkodningsåtgärden att utbyteskontrollnumret (UNB5) för det mottagna utbytet inte matchar ett tidigare bearbetat utbyteskontrollnummer. Om en matchning identifieras bearbetas inte utbytet. |
| Sök efter duplicera UNB5 var (dagar) |Om du väljer att inte tillåta dubbla nummer för utbyteskontroll kan du ange hur många dagar du ska utföra kontrollen genom att ange lämpligt värde för den här inställningen. |
| Tillåt inte gruppkontrollnummerdubbletter |Om du vill blockera utbyten med dubbla gruppkontrollnummer (UNG5) markerar du den här egenskapen. |
| Tillåt inte transaktionsuppsättning kontrollnummer dubbletter |Om du vill blockera korsningar med dubbla transaktionsuppsättningskontrollnummer (UNH1) markerar du den här egenskapen. |
| Kontrollnummer för EDIFACT-bekräftelse |Om du vill ange referensnumren för transaktionsuppsättningen som ska användas i en bekräftelse anger du ett värde för prefixet, ett referensnummerintervall och ett suffix. |

### <a name="validation"></a>Validering

När du har slutfört varje valideringsrad läggs en annan automatiskt till. Om du inte anger några regler används raden "Standard" i valideringen.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj meddelandetypen EDI. |
| EDI-validering |Utför EDI-validering på datatyper som definieras av schemats EDI-egenskaper, längdbegränsningar, tomma dataelement och efterföljande avgränsare. |
| Utökad validering |Om datatypen inte är EDI är valideringen på dataelementkravet och tillåten upprepning, uppräkning och dataelementlängdsvalidering (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande nolltecken och blanksteg. Ta inte bort dessa tecken. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande nolltecken och blanksteg. |
| Efterföljande avgränsare |Generera efterföljande avgränsare. <p>Välj **Tillåts inte** att förbjuda efterföljande avgränsare och avgränsare i det mottagna utbytet. Om utbytet har efterföljande avgränsare och avgränsare förklaras utbytet inte giltigt. <p>Välj **Valfritt** om du vill acceptera utbyten med eller utan efterföljande avgränsare och avgränsare. <p>Välj **Obligatoriskt** när det mottagna utbytet måste ha efterföljande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Interna inställningar

| Egenskap | Beskrivning |
| --- | --- |
| Skapa tomma XML-taggar om efterföljande avgränsare tillåts |Markera den här kryssrutan om du vill att utväxlingsavsändaren ska innehålla tomma XML-taggar för efterföljande avgränsare. |
| Dela utbyte som transaktionsuppsättningar - pausa transaktionsuppsättningar på fel|Tolkar varje transaktion som anges i ett utbyte till ett separat XML-dokument genom att använda lämpligt kuvert på transaktionsuppsättningen. Avbryt endast transaktionsuppsättningarna som misslyckas med valideringen. |
| Dela utbyte som transaktionsuppsättningar - avbryta utbyte vid fel|Tolkar varje transaktion som anges i ett utbyte till ett separat XML-dokument genom att använda lämpligt kuvert. Avbryt hela utbytet när en eller flera transaktionsuppsättningar i valideringen av utbytet misslyckas. | 
| Bevara utbyte - pausa transaktionsuppsättningar vid fel |Lämnar utbytet intakt, skapar ett XML-dokument för hela batched utbyte. Avbryt endast transaktionsuppsättningarna som misslyckas med valideringen, samtidigt som du fortsätter att bearbeta alla andra transaktionsuppsättningar. |
| Bevara utväxling - avbryta utbyte vid fel |Lämnar utbytet intakt, skapar ett XML-dokument för hela batched utbyte. Avbryt hela utbytet när en eller flera transaktionsuppsättningar i valideringen av utbytet misslyckas. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur det här avtalet identifierar och hanterar utgående meddelanden som du skickar till dina partner via det här avtalet.

1.  Under **Lägg till**väljer du Skicka **inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med din partner som utbyter meddelanden med dig. Om du vill ha egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Sändningsinställningarna** är ordnade i följande avsnitt: Identifierare, Bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontrollnummer och valideringar.

    ![Konfigurera "Skicka inställningar"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. När du är klar måste du spara inställningarna genom att välja **OK**.

Nu är ditt avtal klart att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap | Beskrivning |
| --- | --- |
| UNB1.2 (Syntaxversion) |Välj ett värde mellan **1** och **4**. |
| UNB2.3 (Omvänd routningsadress för avsändare) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB3.3 (Omvänd routningsadress för mottagare) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB6.1 (Lösenord för mottagare) |Ange ett alfanumeriskt värde med minst ett och högst 14 tecken. |
| UNB6.2 (Kvalificerare för mottagarens referens) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |
| UNB7 (Referens-ID för program) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken |

### <a name="acknowledgment"></a>Bekräftelse

| Egenskap | Beskrivning |
| --- | --- |
| Mottagande av meddelande (CONTRL) |Markera den här kryssrutan om den värdbaserade partnern förväntar sig att få en teknisk (CONTRL) bekräftelse. Den här inställningen anger att den värdbaserade partnern, som skickar meddelandet, begär en bekräftelse från gästpartnern. |
| Bekräftelse (CONTRL) |Markera den här kryssrutan om den värdbaserade partnern förväntar sig att få en funktionell (CONTRL) bekräftelse. Den här inställningen anger att den värdbaserade partnern, som skickar meddelandet, begär en bekräftelse från gästpartnern. |
| Generera SG1/SG4-loop för godkända transaktionsuppsättningar |Om du väljer att begära en funktionell bekräftelse markerar du den här kryssrutan för att tvinga fram generering av SG1/SG4-loopar i funktionella CONTRL-bekräftelser för godkända transaktionsuppsättningar. |

### <a name="schemas"></a>Scheman

| Egenskap | Beskrivning |
| --- | --- |
| UNH2.1 (TYP) |Välj en transaktionstyp. |
| UNH2.2 (VERSION) |Ange meddelandeversionsnumret. |
| UNH2.3 (RELEASE) |Ange numret för meddelandeutgåvan. |
| Schemat |Välj det schema som ska användas. Scheman finns i ditt integrationskonto. Om du vill komma åt dina scheman länkar du först ditt integrationskonto till logikappen. |

### <a name="envelopes"></a>Kuvert

| Egenskap | Beskrivning |
| --- | --- |
| UNB8 (Prioritetskod för bearbetning) |Ange ett alfabetiskt värde som inte är mer än ett tecken långt. |
| UNB10 (kommunikationsavtal) |Ange ett alfanumeriskt värde med minst ett tecken och högst 40 tecken. |
| UNB11 (testindikator) |Markera den här kryssrutan om du vill ange att det genererade utbytet är testdata |
| Tillämpa UNA-segment (Service String Advice) |Markera den här kryssrutan om du vill generera ett UNA-segment för utbytet som ska skickas. |
| Använda UNG-segment (funktionsgrupphuvud) |Markera den här kryssrutan om du vill skapa grupperingssegment i det funktionella grupphuvudet i meddelandena som skickas till gästpartnern. Följande värden används för att skapa UNG-segmenten: <p>För **UNG1**anger du ett alfanumeriskt värde med minst ett tecken och högst sex tecken. <p>För **UNG2.1**anger du ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **UNG2.2**anger du ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG3.1**anger du ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **UNG3.2**anger du ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG6**anger du ett alfanumeriskt värde med minst ett och högst tre tecken. <p>För **UNG7.1**anger du ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **UNG7.2**anger du ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **UNG7.3**anger du ett alfanumeriskt värde med minst 1 tecken och högst 6 tecken. <p>För **UNG8**anger du ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Förutom teckenuppsättningen kan du ange en annan uppsättning avgränsare som ska användas för varje meddelandetyp. Om en teckenuppsättning inte har angetts för ett visst meddelandeschema används standardteckenuppsättningen.

| Egenskap | Beskrivning |
| --- | --- |
| UNB1.1 (systemidentifierare) |Välj den EDIFACT-teckenuppsättning som ska användas på det utgående utbytet. |
| Schema |Välj ett schema i listrutan. När du har slutfört varje rad läggs en ny rad automatiskt till. För det valda schemat väljer du den avgränsare som du vill använda, baserat på separatorbeskrivningarna nedan. |
| Indatatyp |Välj en indatatyp i listrutan. |
| Komponentavgränsare |Om du vill separera sammansatta dataelement anger du ett enda tecken. |
| Avgränsare för dataelement |Om du vill separera enkla dataelement i sammansatta dataelement anger du ett enda tecken. |
| Segment Terminator |Om du vill ange slutet på ett EDI-segment anger du ett enda tecken. |
| Suffix |Markera det tecken som används med segmentidentifieraren. Om du anger ett suffix kan segmentets terminatordataelement vara tomt. Om segmentsluten lämnas tom måste du ange ett suffix. |

### <a name="control-numbers"></a>Kontrollnummer

| Egenskap | Beskrivning |
| --- | --- |
| UNB5 (nummer för utbyteskontroll) |Ange ett prefix, ett värdeintervall för utväxlingskontrollnumret och ett suffix. Dessa värden används för att generera ett utgående utbyte. Prefixet och suffixet är valfria, medan kontrollnumret krävs. Kontrollnumret ökas för varje nytt meddelande. prefixet och suffixet förblir desamma. |
| UNG5 (Gruppkontrollnummer) |Ange ett prefix, ett värdeintervall för utväxlingskontrollnumret och ett suffix. Dessa värden används för att generera gruppkontrollnumret. Prefixet och suffixet är valfria, medan kontrollnumret krävs. Kontrollnumret ökas för varje nytt meddelande tills det maximala värdet har uppnåtts. prefixet och suffixet förblir desamma. |
| UNH1 (referensnummer för meddelandehuvudet) |Ange ett prefix, ett värdeintervall för utväxlingskontrollnumret och ett suffix. Dessa värden används för att generera referensnumret för meddelanderubriken. Prefixet och suffixet är valfria, medan referensnumret krävs. Referensnumret ökas för varje nytt meddelande. prefixet och suffixet förblir desamma. |

### <a name="validation"></a>Validering

När du har slutfört varje valideringsrad läggs en annan automatiskt till. Om du inte anger några regler används raden "Standard" i valideringen.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj meddelandetypen EDI. |
| EDI-validering |Utför EDI-validering på datatyper som definieras av schemats EDI-egenskaper, längdbegränsningar, tomma dataelement och efterföljande avgränsare. |
| Utökad validering |Om datatypen inte är EDI är valideringen på dataelementkravet och tillåten upprepning, uppräkning och dataelementlängdsvalidering (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande nolltecken och blanksteg. Ta inte bort dessa tecken. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande nolltecken. |
| Efterföljande avgränsare |Generera efterföljande avgränsare. <p>Välj **Tillåts inte** att förbjuda efterföljande avgränsare och avgränsare i det skickade utbytet. Om utbytet har efterföljande avgränsare och avgränsare förklaras utbytet inte giltigt. <p>Välj **Valfritt** om du vill skicka korsningar med eller utan efterföljande avgränsare och avgränsare. <p>Välj **Obligatoriskt** om det skickade utbytet måste ha efterföljande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta ditt skapade avtal

1.  När du har ställt in alla avtalsegenskaper väljer du **OK** på sidan **Lägg** till för att slutföra avtalet och återgå till ditt integrationskonto.

    Ditt nyligen tillagda avtal visas nu i listan **Avtal.**

2.  Du kan också visa dina avtal i översikten över integrationskontot. På menyn för integrationskonto väljer du **Översikt**och väljer sedan panelen **Avtal.** 

    ![Välj panel "Avtal"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel åtgärder och begränsningar som beskrivs i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/edifact/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)