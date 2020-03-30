---
title: X12-meddelanden för B2B-integrering
description: Exchange X12-meddelanden i EDI-format för B2B-företagsintegrering i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651482"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-meddelanden för B2B-företagsintegrering i Azure Logic Apps med Enterprise Integration Pack

Innan du kan utbyta X12-meddelanden för Azure Logic Apps måste du skapa ett X12-avtal och lagra det avtalet i ditt integrationskonto. Här är stegen för hur du skapar ett X12-avtal.

> [!NOTE]
> Den här sidan täcker X12-funktionerna för Azure Logic Apps. Mer information finns i [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett [integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration
* Minst två [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som definieras i ditt integrationskonto och konfigureras med X12-identifieraren under **Affärsidentiteter**    
* Ett [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du kan ladda upp till ditt integrationskonto

När du [har skapat ett integrationskonto,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) [lagt till partner](logic-apps-enterprise-integration-partners.md)och har ett [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du vill använda kan du skapa ett X12-avtal genom att följa dessa steg.

## <a name="create-an-x12-agreement"></a>Skapa ett X12-avtal

1. Logga in på [Azure-portalen](https://portal.azure.com "Azure Portal"). 

2. Välj **Alla tjänster**på Azure-menyn. 
   I sökrutan anger du "integration" och väljer sedan **Integrationskonton**.  

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Om **Alla tjänster** inte visas kanske du måste expandera menyn först. Högst upp på den komprimerade menyn väljer du **Visa-menyn**.

3. Under **Integrationskonton**väljer du det integrationskonto där du vill lägga till avtalet.

   ![Välj integrationskonto där avtalet ska skapas](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Välj **Översikt**och välj sedan panelen **Avtal.** 
   Om du inte har en avtalspanel lägger du till panelen först. 

   ![Välj panel "Avtal"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Under **Avtal**väljer du **Lägg till**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Under **Lägg till**anger du ett **namn** för ditt avtal. 
   För avtalstypen väljer du **X12**. 
   Välj **värdpartner,** **värdidentitet,** **gästpartner**och **gästidentitet** för ditt avtal. 
   Mer egenskapsinformation finns i tabellen i det här steget.

    ![Ange information om avtalet](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Egenskap | Beskrivning |
    | --- | --- |
    | Namn |Avtalets namn |
    | Typ av avtal | Bör vara X12 |
    | Värdpartner |Ett avtal kräver både en värd och gästpartner. Värdpartnern representerar den organisation som konfigurerar avtalet. |
    | Värdidentitet |En identifierare för värdpartnern |
    | Gästpartner |Ett avtal kräver både en värd och gästpartner. Gästpartnern representerar organisationen som gör affärer med värdpartnern. |
    | Gästidentitet |En identifierare för gästpartnern |
    | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |  

   > [!NOTE]
   > Lösningen av X12-avtalet beror på att matcha avsändarens kvalificerare och identifierare och mottagarens kvalificerare och identifierare som definieras i partner- och inkommande meddelandet. Om dessa värden ändras för din partner uppdaterar du även avtalet.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur avtalet hanterar mottagna meddelanden

Nu när du har angett avtalsegenskaperna kan du konfigurera hur det här avtalet identifierar och hanterar inkommande meddelanden som tas emot från din partner via det här avtalet.

1.  Under **Lägg till**väljer du Ta emot **inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig. Om du vill ha egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Inleveransinställningarna** är ordnade i följande avsnitt: Identifierare, Bekräftelse, scheman, kuvert, kontrollnummer, valideringar och interna inställningar.

2. När du är klar måste du spara inställningarna genom att välja **OK**.

Nu är ditt avtal klart att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange egenskaper för identifierare](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Egenskap | Beskrivning |
| --- | --- |
| ISA1 (Behörighetskvalificerare) |Välj värdet för kvalificerare för auktorisering i listrutan. |
| ISA2 (PÅ ANDRA) |Valfri. Ange värdet för auktoriseringsinformation. Om värdet som du angav för ISA1 är annat än 00 anger du minst ett alfanumeriskt tecken och högst 10. |
| ISA3 (Säkerhetskvalificerare) |Välj värdet För säkerhetskvalificerare i listrutan. |
| ISA4 (PÅ ANDRA) |Valfri. Ange värdet för säkerhetsinformation. Om värdet som du angav för ISA3 är annat än 00 anger du minst ett alfanumeriskt tecken och högst 10. |

### <a name="acknowledgment"></a>Bekräftelse

![Ange bekräftelseegenskaper](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Egenskap | Beskrivning |
| --- | --- |
| TA1 förväntas |Returnerar en teknisk bekräftelse till utbytesavsändaren |
| Fa förväntas |Returnerar en funktionell bekräftelse till utväxlingsavsändaren. Välj sedan om du vill ha 997- eller 999-bekräftelser, baserat på schemaversionen |
| Inkludera AK2/IK2-loop |Möjliggör generering av AK2-loopar i funktionella bekräftelser för godkända transaktionsuppsättningar |

### <a name="schemas"></a>Scheman

Välj ett schema för varje transaktionstyp (ST1) och Avsändningsprogram (GS2). Den mottagningspipeline demonterar det inkommande meddelandet genom att matcha värdena för ST1 och GS2 i det inkommande meddelandet med de värden som du anger här och schemat för det inkommande meddelandet med det schema som du anger här.

![Välj schema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Version |Välj X12-versionen |
| Transaktionstyp (ST01) |Välj transaktionstyp |
| Avsändare ansökan (GS02) |Markera avsändningsprogrammet |
| Schema |Markera den schemafil som du vill använda. Scheman läggs till i ditt integrationskonto. |

> [!NOTE]
> Konfigurera det [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) som krävs som överförs till [ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsaren i en transaktionsuppsättning: välj Standardidentifierare eller repetitionsavgränsare](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Egenskap | Beskrivning |
| --- | --- |
| ISA11-användning |Anger den avgränsare som ska användas i en transaktionsuppsättning: <p>Välj **Standardidentifierare** om du vill använda en punkt (.) för decimalnotation i stället för decimalnotationen för det inkommande dokumentet i EDI-mottagningspipelinen. <p>Välj **Repetitionsavgränsare** om du vill ange avgränsaren för upprepade förekomster av ett enkelt dataelement eller en upprepad datastruktur. Till exempel används vanligtvis karaten (^) som upprepningsavgränsare. För HIPAA-scheman kan du bara använda karaten. |

### <a name="control-numbers"></a>Kontrollnummer

![Välj hur du ska hantera kontrollnummerdubbletter](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Tillåt dubbletter av utväxlingskontrollnummer |Blockera dubbla utbyten. Kontrollerar nummer för utbyteskontroll (ISA13) för det mottagna utbyteskontrollnumret. Om en matchning identifieras bearbetas inte utbytet. Du kan ange antalet dagar för att utföra checken genom att ange ett värde för *Sök efter duplicera ISA13 var (dagar)*. |
| Tillåt inte gruppkontrollnummerdubbletter |Blockera utbyten med dubbla gruppkontrollnummer. |
| Tillåt inte transaktionsuppsättning kontrollnummer dubbletter |Blockera korsningar med dubbla transaktionsuppsättningskontrollnummer. |

### <a name="validation"></a>Validering

![Ange valideringsegenskaper för mottagna meddelanden](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

När du har slutfört varje valideringsrad läggs en annan automatiskt till. Om du inte anger några regler används raden "Standard" i valideringen.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj meddelandetypen EDI. |
| EDI-validering |Utför EDI-validering på datatyper som definieras av schemats EDI-egenskaper, längdbegränsningar, tomma dataelement och efterföljande avgränsare. |
| Utökad validering |Om datatypen inte är EDI är valideringen på dataelementkravet och tillåten upprepning, uppräkning och dataelementlängdsvalidering (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande nolltecken och blanksteg. Ta inte bort dessa tecken. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande nolltecken och blanksteg. |
| Efterföljande avgränsare |Generera efterföljande avgränsare. <p>Välj **Tillåts inte** att förbjuda efterföljande avgränsare och avgränsare i det mottagna utbytet. Om utbytet har efterföljande avgränsare och avgränsare förklaras utbytet inte giltigt. <p>Välj **Valfritt** om du vill acceptera utbyten med eller utan efterföljande avgränsare och avgränsare. <p>Välj **Obligatoriskt** när utbytet måste ha efterföljande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Interna inställningar

![Välj interna inställningar](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Konvertera implicit decimalformat "Nn" till ett basvärde på 10 numeriska värden |Konverterar ett EDI-nummer som anges med formatet "Nn" till ett bas-10-numeriskt värde |
| Skapa tomma XML-taggar om efterföljande avgränsare tillåts |Markera den här kryssrutan om du vill att utväxlingsavsändaren ska innehålla tomma XML-taggar för efterföljande avgränsare. |
| Dela utbyte som transaktionsuppsättningar - pausa transaktionsuppsättningar på fel|Tolkar varje transaktion som anges i ett utbyte till ett separat XML-dokument genom att använda lämpligt kuvert på transaktionsuppsättningen. Avbryter endast de transaktioner där valideringen misslyckas. |
| Dela utbyte som transaktionsuppsättningar - avbryta utbyte vid fel|Tolkar varje transaktion som anges i ett utbyte till ett separat XML-dokument genom att använda lämpligt kuvert. Avbryter hela utbytet när en eller flera transaktionsuppsättningar i valideringen av utbytes misslyckas. | 
| Bevara utbyte - pausa transaktionsuppsättningar vid fel |Lämnar utbytet intakt, skapar ett XML-dokument för hela batched utbyte. Avbryter endast transaktionsuppsättningar som misslyckas validering, samtidigt som du fortsätter att bearbeta alla andra transaktionsuppsättningar. |
| Bevara utväxling - avbryta utbyte vid fel |Lämnar utbytet intakt, skapar ett XML-dokument för hela batched utbyte. Avbryter hela utbytet när en eller flera transaktionsuppsättningar i valideringen av utbytes misslyckas. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur det här avtalet identifierar och hanterar utgående meddelanden som du skickar till din partner via det här avtalet.

1.  Under **Lägg till**väljer du Skicka **inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med din partner som utbyter meddelanden med dig. Om du vill ha egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Sändningsinställningarna** är ordnade i följande avsnitt: Identifierare, Bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontrollnummer och validering.

2. När du är klar måste du spara inställningarna genom att välja **OK**.

Nu är ditt avtal klart att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange egenskaper för identifierare](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Egenskap | Beskrivning |
| --- | --- |
| Kvalificerare för auktorisering (ISA1) |Välj värdet för kvalificerare för auktorisering i listrutan. |
| ISA2 (PÅ ANDRA) |Ange värdet för auktoriseringsinformation. Om det här värdet är annat än 00 anger du minst ett alfanumeriskt tecken och högst 10. |
| Säkerhet kvalificerare (ISA3) |Välj värdet För säkerhetskvalificerare i listrutan. |
| ISA4 (PÅ ANDRA) |Ange värdet för säkerhetsinformation. Om det här värdet är annat än 00 anger du minst ett alfanumeriskt värde för textrutan Value (ISA4) och högst 10. |

### <a name="acknowledgment"></a>Bekräftelse

![Ange bekräftelseegenskaper](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap | Beskrivning |
| --- | --- |
| TA1 förväntas |Returnera en teknisk bekräftelse (TA1) till utbytesavsändaren. Den här inställningen anger att den värdpartner som skickar meddelandet begär en bekräftelse från gästpartnern i avtalet. Dessa bekräftelser förväntas av värdpartnern baserat på avtalets mottagningsinställningar. |
| Fa förväntas |Returnera en funktionell bekräftelse (FA) till utväxlingsavsändaren. Välj om du vill ha 997- eller 999-bekräftelsen, baserat på de schemaversioner du arbetar med. Dessa bekräftelser förväntas av värdpartnern baserat på avtalets mottagningsinställningar. |
| FA-version |Välj fa-versionen |

### <a name="schemas"></a>Scheman

![Välj schema som ska användas](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap | Beskrivning |
| --- | --- |
| Version |Välj X12-versionen |
| Transaktionstyp (ST01) |Välj transaktionstyp |
| Schemat |Välj det schema som ska användas. Scheman finns i ditt integrationskonto. Om du väljer schema först konfigureras version och transaktionstyp automatiskt  |

> [!NOTE]
> Konfigurera det [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) som krävs som överförs till [ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsaren i en transaktionsuppsättning: välj Standardidentifierare eller repetitionsavgränsare](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Egenskap | Beskrivning |
| --- | --- |
| ISA11-användning |Anger den avgränsare som ska användas i en transaktionsuppsättning: <p>Välj **Standardidentifierare** om du vill använda en punkt (.) för decimalnotation i stället för decimalnotationen för det inkommande dokumentet i EDI-mottagningspipelinen. <p>Välj **Repetitionsavgränsare** om du vill ange avgränsaren för upprepade förekomster av ett enkelt dataelement eller en upprepad datastruktur. Till exempel används vanligtvis karaten (^) som upprepningsavgränsare. För HIPAA-scheman kan du bara använda karaten. |

### <a name="control-numbers"></a>Kontrollnummer

![Ange egenskaper för kontrollnummer](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Kontrollversionsnummer (ISA12) |Välj version av X12-standarden |
| Användningsindikator (ISA15) |Välj kontexten för ett utbyte.  Värdena är information, produktionsdata eller testdata |
| Schema |Genererar GS- och ST-segmenten för ett X12-kodat utbyte som skickas till Send Pipeline |
| GS1 (på andra sätt) |Valfritt väljer du ett värde för funktionskoden i listrutan |
| GS2 (2) |Valfri, programavsändare |
| GS3 (3) |Valfri, programmottagare |
| GS4 (på andra sätt) |Valfritt väljer du CCYYMMDD eller YYMMDD |
| GS5 |Valfritt väljer du HHMM, HHMMSS eller HHMMSSdd |
| GS7 (2000) |Valfritt väljer du ett värde för ansvarig myndighet i listrutan |
| GS8 (på andra sätt) |Valfri, version av dokumentet |
| Nummer för utbyteskontroll (ISA13) |Obligatoriskt anger du ett värdeintervall för utbyteskontrollnumret. Ange ett numeriskt värde med minst 1 och högst 99999999999 |
| Gruppkontrollnummer (GS06) |Obligatoriskt anger du ett talintervall för gruppkontrollnumret. Ange ett numeriskt värde med minst 1 och högst 99999999999 |
| Kontrollnummer för transaktionsuppsättning (ST02) |Obligatoriskt anger du ett nummerintervall för transaktionsuppsättningskontrollnumret. Ange ett intervall med numeriska värden med minst 1 och högst 9999999999 |
| Prefix |Valfritt, som anges för intervallet för transaktionsuppsättningskontrollnummer som används i bekräftelsen. Ange ett numeriskt värde för de två mittfälten och ett alfanumeriskt värde (om så önskas) för prefix- och suffixfälten. De mellersta fälten krävs och innehåller minimi- och maximivärdena för kontrollnumret |
| Suffix |Valfritt, som anges för intervallet för transaktionsuppsättningskontrollnummer som används i en bekräftelse. Ange ett numeriskt värde för de två mittfälten och ett alfanumeriskt värde (om så önskas) för prefix- och suffixfälten. De mellersta fälten krävs och innehåller minimi- och maximivärdena för kontrollnumret |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Förutom teckenuppsättningen kan du ange en annan uppsättning avgränsare för varje meddelandetyp. Om en teckenuppsättning inte har angetts för ett visst meddelandeschema används standardteckenuppsättningen.

![Ange avgränsare för meddelandetyper](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Teckenuppsättning som ska användas |Om du vill validera egenskaperna väljer du X12-teckenuppsättningen. Alternativen är Basic, Extended och UTF8. |
| Schema |Välj ett schema i listrutan. När du har slutfört varje rad läggs en ny rad automatiskt till. För det valda schemat väljer du den avgränsare som du vill använda, baserat på separatorbeskrivningarna nedan. |
| Indatatyp |Välj en indatatyp i listrutan. |
| Komponentavgränsare |Om du vill separera sammansatta dataelement anger du ett enda tecken. |
| Avgränsare för dataelement |Om du vill separera enkla dataelement i sammansatta dataelement anger du ett enda tecken. |
| Ersättningstecken |Ange ett ersättningstecken som används för att ersätta alla avgränsare i nyttolastdata när du genererar det utgående X12-meddelandet. |
| Segment Terminator |Om du vill ange slutet på ett EDI-segment anger du ett enda tecken. |
| Suffix |Markera det tecken som används med segmentidentifieraren. Om du anger ett suffix kan segmentets terminatordataelement vara tomt. Om segmentsluten lämnas tom måste du ange ett suffix. |

> [!TIP]
> Om du vill ange särskilda teckenvärden redigerar du avtalet som JSON och anger ASCII-värdet för specialtecknet.

### <a name="validation"></a>Validering

![Ange valideringsegenskaper för att skicka meddelanden](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

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

    ![Välj panel "Avtal"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel åtgärder och begränsningar som beskrivs i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)