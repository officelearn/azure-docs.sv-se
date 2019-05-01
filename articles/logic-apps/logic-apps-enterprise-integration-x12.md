---
title: X12 meddelanden för B2B enterprise-integration – Azure Logic Apps | Microsoft Docs
description: Exchange X12 meddelanden EDI-format för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: f06e213dbae31c9d7c4e212d605cc962aba71d2d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728753"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12 meddelanden för B2B enterprise-integration i Azure Logic Apps med Enterprise-Integrationspaket

Innan du kan utbyta X12 meddelanden för Azure Logic Apps, måste du skapa en X12 avtal och lagra det avtalet i ditt integrationskonto. Här följer stegen för hur du skapar en X12 avtal.

> [!NOTE]
> Den här sidan täcker X12 funktioner för Azure Logic Apps. Mer information finns i [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* En [integrationskontot](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierat och som är associerade med din Azure-prenumeration
* Minst två [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som definieras i ditt integrationskonto och konfigurerad med X12 identifierare under **Företagsidentiteter**    
* En nödvändig [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du kan överföra till ditt integrationskonto

När du [skapar ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [lägga till partner](logic-apps-enterprise-integration-partners.md), och har en [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du vill använda kan du skapa en X12 avtalet genom att följa dessa steg.

## <a name="create-an-x12-agreement"></a>Skapa en X12 avtal

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Välj den Azure-huvudmenyn **alla tjänster**. 
   Ange ”-integration” i sökrutan och välj sedan **integrationskonton**.  

   ![Hitta ditt integrationskonto](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Om **alla tjänster** inte visas kan du behöva expanderar du menyn först. Högst upp på menyn minimerade, Välj **menyn Visa**.

3. Under **Integrationskonton**, Välj integrationskontot där du vill lägga till avtalet.

   ![Välj var du vill skapa avtalet för integrationskontot](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Välj **översikt**och välj sedan den **avtal** panelen. 
   Om du inte har ett avtal panel, Lägg till panelen först. 

   ![Välj panelen ”avtal”](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Under **avtal**, Välj **Lägg till**.

   ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Under **Lägg till**, ange en **namn** för ditt avtal. 
   Avtalstyp, Välj **X12**. 
   Välj den **värdpartner**, **Värdidentiteten**, **Gästpartner**, och **Gästidentitet** för ditt avtal. 
   Mer information om egenskapen, finns i tabellen i det här steget.

    ![Ange avtalsinformation](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Egenskap  | Beskrivning |
    | --- | --- |
    | Namn |Avtalets namn |
    | Avtalstyp | Ska vara X12 |
    | Värdpartner |Ett avtal måste både en värdens och gästens partner. Den mottagande partnern representerar organisationen som konfigurerar avtalet. |
    | Värd-identitet |En identifierare för den mottagande partnern |
    | Gästpartner |Ett avtal måste både en värdens och gästens partner. Gästpartner representerar den organisation som gör affärer med den mottagande partnern. |
    | Gästidentitet |En identifierare för gästpartner |
    | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |  

   > [!NOTE]
   > Lösning av X12 avtal beror på matchande avsändarkvalificerare och identifierare och mottagarkvalificerare och identifieraren som definierats i partner och inkommande meddelande. Ändrar värdena för din partner, uppdatera avtalet också.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal hanterar fått meddelanden

Nu när du har ställt in egenskaperna avtal kan konfigurera du hur detta avtal identifierar och hanterar inkommande meddelanden som tas emot från din partner via detta avtal.

1.  Under **Lägg till**väljer **ta emot inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Ta emot inställningarna** är uppdelad i följande avsnitt: Identifierare, bekräftelse, scheman, kuvert, kontrollnummer, verifieringar och interna inställningar.

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange egenskaper för identifierare](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Egenskap  | Beskrivning |
| --- | --- |
| ISA1 (auktoriseringskvalificerare) |Välj kvalificerare auktoriseringsvärde från den nedrullningsbara listan. |
| ISA2 |Valfri. Ange information auktoriseringsvärde. Om värdet du angav för ISA1 är än 00, ange minst ett alfanumeriskt tecken och högst 10. |
| ISA3 (säkerhetskvalificerare) |Välj kvalificerare säkerhetsvärde från den nedrullningsbara listan. |
| ISA4 |Valfri. Ange information säkerhetsvärde. Om värdet du angav för ISA3 är än 00, ange minst ett alfanumeriskt tecken och högst 10. |

### <a name="acknowledgment"></a>Bekräftelse

![Ange egenskaper för bekräftelse](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| Förväntad TA1 |Returnerar en teknisk bekräftelse till utbytet avsändaren |
| Förväntad FA |Returnerar en funktionell bekräftelse till utbytet avsändaren. Ange om du vill att bekräftelser 997 eller 999 baserat på schemaversion |
| Inkludera AK2/IK2-slinga |Aktiverar generering av AK2 loopar i funktionella bekräftelser för godkända transaktionsuppsättningar |

### <a name="schemas"></a>Scheman

Välj ett schema för varje transaktionstyp (ST1) och avsändarprogram (GS2). Ta emot pipelinen Disassemblerar det inkommande meddelandet genom att matcha värdena för ST1 och GS2 i det inkommande meddelandet med de värden som du anger här och schemat för det inkommande meddelandet med schemat du anger här.

![Välj schema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| Version |Välj X12 version |
| Transaktionstyp (ST01) |Välj transaktionstyp |
| Avsändarprogram (GS02) |Välj sändningsprogrammet |
| Schema |Välj schemafilen som du vill använda. Scheman läggs till i ditt integrationskonto. |

> [!NOTE]
> Konfigurera de nödvändiga [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som har överförts till din [integrationskontot](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsaren i en transaktionsuppsättning: Välj standardidentifierare eller Upprepningsavgränsare](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Egenskap  | Beskrivning |
| --- | --- |
| ISA11-användning |Anger den avgränsningstecken som ska användas i en transaktion: <p>Välj **standardidentifierare** för att använda en punkt (.) för decimalform, i stället för decimalformen för inkommande dokument i EDI får pipeline. <p>Välj **Upprepningsavgränsare** Ange avgränsare för upprepade förekomster av en enkel dataelement eller en upprepad datastruktur. Till exempel används vanligtvis cirkumflex (^) som upprepningsavgränsare. Du kan bara använda cirkumflex för HIPAA scheman. |

### <a name="control-numbers"></a>Kontrollnummer

![Välj hur du hanterar dubletter av gruppkontrollnummer](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| Tillåt inte dubbletter av Interchange-kontrollnummer |Blockera duplicerade utbyten. Kontrollerar interchange-kontrollnummer (ISA13) för mottagna interchange-kontrollnummer. Om en matchning hittas bearbetar inte ta emot pipelinen utbytet. Du kan ange antalet dagar för att utföra kontrollen genom att ge ett värde för *leta efter dubletter av ISA13 varje (dagar)*. |
| Tillåt inte dubletter av gruppkontrollnummer |Blockera utbyten med dubblettgruppens kontrollnummer. |
| Tillåt inte dubletter av transaktionsuppsättningsnummer |Blockera utbyten med kontrollnummer för dubbletttransaktionsuppsättning set kontrollnummer. |

### <a name="validations"></a>Valideringar

![Ange egenskaper för verifiering för mottagna meddelanden](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

När du har slutfört raderna verifiering, läggs en annan automatiskt. Om du inte anger några regler använder verifiering raden ”standard”.

| Egenskap  | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ av EDI-meddelande. |
| EDI-validering |Utföra EDI-validering för-datatyper som definieras av schemat EDI egenskaper, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte är EDI, verifiering på kravet på elementet tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt ledande/avslutande nollor |Behålla alla ytterligare inledande eller avslutande noll och utrymme tecken. Inte ta bort dessa tecken. |
| Trimma ledande/avslutande nollor |Ta bort inledande eller avslutande noll och blanksteg. |
| Princip för avslutande avgränsare |Generera avslutande avgränsare. <p>Välj **otillåtna** att förbjuda avslutande avgränsare och avgränsare i mottagna utbytet. Om utbytet har avslutande avgränsare och avgränsare, deklareras utbytet inte giltig. <p>Välj **valfritt** att acceptera utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatorisk** när utbytet måste ha avslutande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Interna inställningar

![Välj interna inställningar](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| Konvertera underförstådda Decimalformatet ”Nn” till ett bas 10 numeriskt värde |Konverterar ett EDI-nummer som anges med formatet ”Nn” i en bas-10 numeriskt värde |
| Skapa tomma XML-taggar om avslutande avgränsare är tillåtna |Välj den här kryssrutan ska utbytet avsändaren innehåller tomma XML-taggar för avslutande avgränsare. |
| Dela upp interchange i transaktionsuppsättningar – inaktivera transaktionsuppsättningar vid fel|Tolkar varje transaktion som angetts i ett utbyte i ett separat XML-dokument genom att tillämpa lämpliga kuvert till transaktionsuppsättningen med. Pausar endast transaktioner där valideringen misslyckas. |
| Dela upp interchange i transaktionsuppsättningar – inaktivera interchange vid fel|Tolkar varje transaktion som angetts i ett utbyte i ett separat XML-dokument genom att tillämpa lämpliga intervall. Pausar hela utbytet när en eller flera transaktionsuppsättningar i utbytet verifieringen. | 
| Bevara Interchange – inaktivera transaktionsuppsättningar vid fel |Utbytet förblir intakta, skapar ett XML-dokument för hela gruppbaserade utbytet. Pausar de transaktionsuppsättningar som inte kan valideras när du fortsätter att bearbeta alla andra transaktionsuppsättningar. |
| Bevara interchange – inaktivera interchange vid fel |Utbytet förblir intakta, skapar ett XML-dokument för hela gruppbaserade utbytet. Pausar hela utbytet när en eller flera transaktionsuppsättningar i utbytet verifieringen. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur detta avtal identifierar och hanterar utgående meddelanden som du skickar till partnern via detta avtal.

1.  Under **Lägg till**väljer **skicka inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med din partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Skicka inställningar** är uppdelad i följande avsnitt: Identifierare, bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontrollnummer och verifiering.

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange egenskaper för identifierare](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Egenskap  | Beskrivning |
| --- | --- |
| Auktoriseringskvalificerare (ISA1) |Välj kvalificerare auktoriseringsvärde från den nedrullningsbara listan. |
| ISA2 |Ange information auktoriseringsvärde. Om det här värdet är än 00, anger du minst ett alfanumeriskt tecken och högst 10. |
| Säkerhetskvalificerare (ISA3) |Välj kvalificerare säkerhetsvärde från den nedrullningsbara listan. |
| ISA4 |Ange information säkerhetsvärde. Om det här värdet är än 00 för textrutan värde (ISA4) anger du minst ett alfanumeriskt värde och högst 10. |

### <a name="acknowledgment"></a>Bekräftelse

![Ange egenskaper för bekräftelse](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap  | Beskrivning |
| --- | --- |
| Förväntad TA1 |Returnera en teknisk bekräftelse (TA1) till utbytet avsändaren. Den här inställningen anger att värdpartner som skickar meddelandet begär en bekräftelse från gästpartner i avtalet. Dessa bekräftelser förväntas av värdpartner baserat på inställningarna som tar emot av avtalet. |
| Förväntad FA |Returnera en funktionell bekräftelse (FA) till utbytet avsändaren. Välj om du vill bekräftelser 997 eller 999 baserat på schema-versionen som du arbetar med. Dessa bekräftelser förväntas av värdpartner baserat på inställningarna som tar emot av avtalet. |
| FA-version |Välj FA-version |

### <a name="schemas"></a>Scheman

![Välj schema som ska användas](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap  | Beskrivning |
| --- | --- |
| Version |Välj X12 version |
| Transaktionstyp (ST01) |Välj transaktionstyp |
| SCHEMA |Välj scheman som ska användas. Scheman finns i ditt integrationskonto. Om du väljer schemat först konfigurerar den automatiskt version och transaktionen typ  |

> [!NOTE]
> Konfigurera de nödvändiga [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som har överförts till din [integrationskontot](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsaren i en transaktionsuppsättning: Välj standardidentifierare eller Upprepningsavgränsare](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| ISA11-användning |Anger den avgränsningstecken som ska användas i en transaktion: <p>Välj **standardidentifierare** för att använda en punkt (.) för decimalform, i stället för decimalformen för inkommande dokument i EDI får pipeline. <p>Välj **Upprepningsavgränsare** Ange avgränsare för upprepade förekomster av en enkel dataelement eller en upprepad datastruktur. Till exempel används vanligtvis cirkumflex (^) som upprepningsavgränsare. Du kan bara använda cirkumflex för HIPAA scheman. |

### <a name="control-numbers"></a>Kontrollnummer

![Ange egenskaper för kontrollnummer](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| Kontrollversionsnummer (ISA12) |Välj versionen av X12 standard |
| Användningsindikator (ISA15) |Välj kontexten för ett utbyte.  Värdena är information produktionsdata, eller testa data |
| Schema |Genererar GS och ST segment för en X12-kodad-utbytet som skickas till pipelinen skicka |
| GS1 |Valfritt, Välj ett värde för den funktionella koden från den nedrullningsbara listan |
| GS2 |Valfritt, program-avsändare |
| GS3 |Valfritt, program-mottagare |
| GS4 |Valfritt, Välj CCYYMMDD eller ååmmdd |
| GS5 |Valfritt, Välj HHMM, HHMMSS eller HHMMSSdd |
| GS7 |Valfritt, Välj ett värde för ansvarig myndighet från den nedrullningsbara listan |
| GS8 |Valfritt, versionen av dokumentet |
| Interchange-kontrollnummer (ISA13) |Krävs, ange ett värdeintervall för interchange-kontrollnummer. Ange ett numeriskt värde med minst 1 och högst 999999999 |
| Gruppkontrollnummer (GS06) |Krävs, ange ett intervall med tal för gruppkontrollnummer. Ange ett numeriskt värde med minst 1 och högst 999999999 |
| Transaktionsuppsättningens kontrollnummer (ST02) |Krävs, ange ett intervall med tal för kontrollnummer för att ange transaktionen. Ange ett intervall med numeriska värden med minst 1 och högst 999999999 |
| Prefix |Valfritt, avsedda för antalet transaktion set kontrollnummer som används i bekräftelse. Ange ett numeriskt värde för de mellersta två fält och ett alfanumeriskt värde (om det behövs) för prefix och suffix fält. De mellersta är obligatoriska och innehålla minsta och högsta värden för kontrollnummer |
| Suffix |Valfritt, avsedda för antalet transaktion set kontrollnummer som används i en bekräftelse. Ange ett numeriskt värde för de mellersta två fält och ett alfanumeriskt värde (om det behövs) för prefix och suffix fält. De mellersta är obligatoriska och innehålla minsta och högsta värden för kontrollnummer |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Förutom de teckenuppsättningar kan du ange en annan uppsättning avgränsare för varje meddelandetyp. Om en teckenuppsättning har inte angetts för ett visst meddelande-schema används den standardinställda teckenuppsättningen.

![Ange avgränsare för meddelandetyper](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Egenskap  | Beskrivning |
| --- | --- |
| Teckenuppsättningen som ska användas |Att verifiera egenskaper, Välj X12 teckenuppsättningen. Alternativen är grundläggande och utökad UTF8. |
| Schema |Välj ett schema från den nedrullningsbara listan. En ny rad läggs automatiskt när du har slutfört varje rad. Välj uppsättningen avgränsare som du vill använda, baserat på avgränsare beskrivningarna nedan för det aktuella schemat. |
| Indatatyp |Välj en typ av användarindata från den nedrullningsbara listan. |
| Komponentavgränsare |Ange ett enskilt tecken för att avgränsa sammansatta dataelement. |
| Dataelement-avgränsare |Ange ett enskilt tecken för att avgränsa enkla element i sammansatt dataelement. |
| Annat tecken |Ange ett annat tecken som används för att ersätta alla avgränsningstecknen i nyttolasten vid generering av utgående X12 meddelande. |
| Segmentsterminator |Ange ett enskilt tecken för att ange ett EDI-segment är slut. |
| Suffix |Välj det tecken som används med det segment-ID: t. Om du anger ett suffix kan dataelementet segment Begränsare vara tom. Om segmentterminator lämnas tomt, måste du ange ett suffix. |

> [!TIP]
> Redigera avtal som JSON för att ange specialtecken värden, och ange ASCII-värde för specialtecknet.

### <a name="validation"></a>Validering

![Ange egenskaper för verifiering för att skicka meddelanden](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

När du har slutfört raderna verifiering, läggs en annan automatiskt. Om du inte anger några regler använder verifiering raden ”standard”.

| Egenskap  | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ av EDI-meddelande. |
| EDI-validering |Utföra EDI-validering för-datatyper som definieras av schemat EDI egenskaper, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte är EDI, verifiering på kravet på elementet tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt ledande/avslutande nollor |Behålla alla ytterligare inledande eller avslutande noll och utrymme tecken. Inte ta bort dessa tecken. |
| Trimma ledande/avslutande nollor |Ta bort inledande eller avslutande noll tecken. |
| Princip för avslutande avgränsare |Generera avslutande avgränsare. <p>Välj **otillåtna** att förbjuda avslutande avgränsare och avgränsare i skickade utbytet. Om utbytet har avslutande avgränsare och avgränsare, deklareras utbytet inte giltig. <p>Välj **valfritt** att skicka utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatorisk** om skickade utbyte måste ha avslutande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta ditt skapade avtal

1.  När du är klar med att alla dina avtal egenskaper på den **Lägg till** väljer **OK** har skapat ditt avtal och gå tillbaka till ditt integrationskonto.

    Ditt nyligen tillagda avtal nu visas i din **avtal** lista.

2.  Du kan också visa dina avtal i din integrering Kontoöversikt. På din integration meny väljer **översikt**och välj sedan den **avtal** panelen.

    ![Välj panelen ”avtal”](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Visa swagger
Se den [swagger information](/connectors/x12/). 

## <a name="learn-more"></a>Läs mer
* [Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  

