---
title: "X12 meddelanden för B2B enterprise integration - Azure Logic Apps | Microsoft Docs"
description: "Exchange X12 meddelanden EDI-format för B2B enterprise integrering med Azure Logikappar"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bfad01d8c14cdd972ebe8e4038f226ffe0da93b1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Exchange X12 meddelanden för enterprise-integrering med logic apps

Innan du kan utbyta X12 meddelanden för Logikappar i Azure måste du skapa en X12 avtalet och lagra avtalet i ditt konto för integrering. Här följer stegen för hur du skapar en X12 avtal.

> [!NOTE]
> Den här sidan omfattar X12 funktioner för Azure Logic Apps. Mer information finns i [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* En [integrering konto](../logic-apps/logic-apps-enterprise-integration-accounts.md) som redan har definierat och som är associerade med din Azure-prenumeration
* Minst två [partners](../logic-apps/logic-apps-enterprise-integration-partners.md) som definieras i ditt konto för integrering och konfigurerats med X12 identifierare under **Business identiteter**    
* En nödvändig [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) för överföring till din [integrering konto](../logic-apps/logic-apps-enterprise-integration-accounts.md)

När du [skapa ett konto för integrering](../logic-apps/logic-apps-enterprise-integration-accounts.md), [lägga till partners](logic-apps-enterprise-integration-partners.md), och har en [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du vill använda kan du skapa en X12 avtal genom att följa dessa steg.

## <a name="create-an-x12-agreement"></a>Skapa en X12 avtal

1.  Logga in på [Azure Portal](http://portal.azure.com "Azure Portal"). I den vänstra menyn, Välj **fler tjänster**. 

    > [!TIP]
    > Om du inte ser **fler tjänster**, du kan behöva expandera menyn först. Längst upp i den komprimerade menyn, Välj **menyn Visa**.

    ![På vänster-menyn väljer du ”fler tjänster”](./media/logic-apps-enterprise-integration-x12/account-1.png)

2.  I sökrutan skriver du ”integration” som filter. Välj i resultatlistan **Integrationskonton**.  

    ![Filtrera efter ”integration”, Välj ”Integrationskonton”](./media/logic-apps-enterprise-integration-x12/account-2.png)

3. I den **Integrationskonton** bladet som öppnas väljer du det integration konto där du vill lägga till avtalet.
Om du inte ser några integrationskonton [skapa en första](../logic-apps/logic-apps-enterprise-integration-accounts.md "om integrationskonton").

    ![Välj integration konto var du vill skapa avtalet](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Välj **översikt**och välj den **avtal** panelen. Om du inte har ett avtal sida vid sida, lägga till panelen först. 

    ![Välj ikonen ”avtal”](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. I bladet avtal som öppnas väljer du **Lägg till**.

    ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. Under **Lägg till**, ange en **namn** för ditt avtal. Avtalstyp, Välj **X12**. Välj den **värden Partner**, **Värdidentiteten**, **gäst Partner**, och **gäst identitet** för ditt avtal. Mer information för egenskapen finns i tabellen i det här steget.

    ![Ange avtalsuppgifter](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Egenskap | Beskrivning |
    | --- | --- |
    | Namn |Avtalets namn |
    | Avtalstyp | Ska vara X12 |
    | Värd-Partner |Ett avtal måste både värden och gästen partner. Den mottagande partnern representerar den organisation som konfigurerar avtalet. |
    | Värdidentiteten |En identifierare för värdpartnern som |
    | Gästen Partner |Ett avtal måste både värden och gästen partner. Gästen partnern representerar den organisation som har att göra affärer med den mottagande partnern. |
    | Gästen identitet |En identifierare för gäst-partner |
    | Ta emot inställningarna |Dessa egenskaper gäller för alla meddelanden som tagits emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |  

  > [!NOTE]
  > Lösning av X12 avtal beror på matchar avsändaren kvalificeraren och identifierare, och mottagare kvalificerare och identifieraren som definierats i partner och inkommande meddelande. Ändrar värdena för din partner, uppdatera avtalet för.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal handtag mottagna meddelanden

Nu när du har angett egenskaperna avtal, kan du konfigurera hur detta avtal identifierar och hanterar inkommande meddelanden som tagits emot från din partner via det här avtalet.

1.  Under **Lägg till**väljer **tar emot inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Ta emot inställningarna** är indelad i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, kontroll siffror, verifieringar och interna inställningar.

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange ID-egenskaper](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Egenskap | Beskrivning |
| --- | --- |
| ISA1 (auktorisering kvalificerare) |Välj auktorisering kvalificerarvärdet från den nedrullningsbara listan. |
| ISA2 |Valfri. Ange värdet för auktorisering information. Om värdet för ISA1 är än 00 ange minst ett alfanumeriskt tecken och högst 10. |
| ISA3 (Security kvalificerare) |Välj kvalificerare säkerhetsvärde från den nedrullningsbara listan. |
| ISA4 |Valfri. Ange information säkerhetsvärde. Om värdet för ISA3 är än 00 ange minst ett alfanumeriskt tecken och högst 10. |

### <a name="acknowledgment"></a>Bekräftelse

![Ange egenskaper för bekräftelse](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Egenskap | Beskrivning |
| --- | --- |
| TA1 förväntades |Returnerar en teknisk bekräftelse till interchange avsändaren |
| MI förväntades |Returnerar en funktionell bekräftelse till interchange avsändaren. Ange om du vill bekräftelser 997 eller 999 baserat på schemaversionen |
| Inkludera AK2/IK2 Loop |Aktiverar generering av AK2 loopar i funktionella bekräftelser för godkända transaktionen uppsättningar |

### <a name="schemas"></a>Scheman

Välj ett schema för varje transaktionstyp (ST1) och avsändaren program (GS2). Ta emot pipeline Disassemblerar det inkommande meddelandet genom att matcha värdena för ST1 och GS2 i det inkommande meddelandet med de värden som du anger här och schemat för det inkommande meddelandet med schemat du anger här.

![Välj schema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Version |Välj X12 version |
| Transaktionstyp (ST01) |Välj transaktionstyp |
| Avsändaren program (GS02) |Välj programmet för sändaren |
| Schema |Välj den schemafilen som du vill använda. Scheman läggs till ditt konto för integrering. |

> [!NOTE]
> Konfigurera de nödvändiga [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som överförs till ditt [integrering konto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsaren i en transaktion: Välj Standard identifierare eller upprepning avgränsare](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Egenskap | Beskrivning |
| --- | --- |
| ISA11 användning |Anger avgränsare som ska användas i en transaktion: <p>Välj **Standard identifierare** för att använda en punkt (.) för decimalform, i stället för decimalform inkommande dokument i EDI får pipeline. <p>Välj **upprepning avgränsare** att ange avgränsaren för upprepade förekomster av en enkel dataelement eller en upprepad datastruktur. Till exempel används vanligtvis cirkumflex (^) som avgränsare för upprepning. Du kan bara använda cirkumflex för HIPAA scheman. |

### <a name="control-numbers"></a>Kontrollen siffror

![Välj hur du vill hantera kontrollen nummer dubbletter](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Tillåt inte Interchange kontrollen antal dubbletter |Blockera dubbla externa utbyten. Kontrollerar interchange kontrollnumret (ISA13) för det mottagna interchange kontrollnummer. Om en matchning hittas bearbeta inte ta emot pipeline utbyte. Du kan ange antalet dagar för att utföra kontrollen genom att ge ett värde för *Sök efter duplicerade ISA13 varje (dagar)*. |
| Tillåt inte grupp kontrollen nummer dubbletter |Blockera interchanges med dubbla grupp kontrollen nummer. |
| Tillåt inte transaktionen set kontrollen nummer dubbletter |Blockera interchanges med dubbla transaktion set kontrollen nummer. |

### <a name="validations"></a>Verifieringar

![Ange egenskaper för verifiering för mottagna meddelanden](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

När du slutför varje validering rad läggs en annan automatiskt. Om du inte anger några regler använder verifieringen raden ”standard”.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ för EDI-meddelande. |
| EDI-validering |Validerar EDI-datatyper som definieras av schemat EDI egenskaper, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte EDI, validering på kravet element tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt inledande/efterföljande nollor |Behåll ytterligare inledande eller avslutande noll och utrymme tecken. Ta inte bort dessa tecken. |
| Ta bort inledande/efterföljande nollor |Ta bort inledande eller efterföljande noll och blanksteg. |
| Avslutande avgränsare princip |Generera avslutande avgränsare. <p>Välj **inte tillåtet** förhindra avslutande avgränsare och avgränsare i mottagna utbyte. Om utbyte har avslutande avgränsare och avgränsare, deklareras utbyte inte giltig. <p>Välj **valfritt** att acceptera externa utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatoriska** när utbyte måste ha avslutande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Internt inställningar

![Välj inställningar för internt](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Konvertera underförstådda decimalformat ”Nn” till en bas 10 numeriskt värde |Konverterar ett EDI-nummer som har angetts med formatet ”Nn” till ett numeriskt värde bas 10 |
| Skapa tomma XML-taggar om avslutande avgränsare tillåts |Välj den här kryssrutan om du vill interchange avsändaren är tom XML-taggar för avslutande avgränsare. |
| Dela Interchange som transaktionen uppsättningar - inaktivera transaktion anger vid fel|Parsar varje transaktion som anges i ett utbyte i ett annat XML-dokument genom att använda lämpliga kuvertet uppsättningen transaktion. Pausar enbart transaktioner där valideringen misslyckades. |
| Dela Interchange som transaktionen uppsättningar - inaktivera utbyte vid fel|Parsar varje transaktion som anges i ett utbyte i ett annat XML-dokument genom att tillämpa lämpliga kuvertet. Pausar hela interchange när en eller flera uppsättningar av transaktionen i utbyte inte kan valideras. | 
| Bevara Interchange – inaktivera transaktion anger vid fel |Lämnar utbyte intakta, skapar ett XML-dokument för hela gruppbaserad datautbyte. Pausar bara som inte kan verifieras när fortsätter att bearbeta alla andra transaktion anger mängderna transaktion. |
| Bevara Interchange – inaktivera utbyte vid fel |Lämnar utbyte intakta, skapar ett XML-dokument för hela gruppbaserad datautbyte. Pausar hela interchange när en eller flera uppsättningar av transaktionen i utbyte inte kan valideras. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur detta avtal identifierar och hanterar utgående meddelanden som du skickar till din partner via det här avtalet.

1.  Under **Lägg till**väljer **skicka inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med din partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Skicka inställningar** är indelad i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontroll siffror och validering.

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange ID-egenskaper](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Egenskap | Beskrivning |
| --- | --- |
| Auktorisering kvalificerare (ISA1) |Välj auktorisering kvalificerarvärdet från den nedrullningsbara listan. |
| ISA2 |Ange värdet för auktorisering information. Om det här värdet är än 00, ange minst ett alfanumeriskt tecken och högst 10. |
| Säkerhet kvalificerare (ISA3) |Välj kvalificerare säkerhetsvärde från den nedrullningsbara listan. |
| ISA4 |Ange information säkerhetsvärde. Om det här värdet är än 00 för textrutan värde (ISA4) ange minst ett alfanumeriskt värde och högst 10. |

### <a name="acknowledgment"></a>Bekräftelse

![Ange egenskaper för bekräftelse](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap | Beskrivning |
| --- | --- |
| TA1 förväntades |Returnera en teknisk bekräftelse (TA1) till interchange avsändaren. Den här inställningen anger att den mottagande partnern som skickar meddelandet begär en bekräftelse från gästen partner i avtalet. Dessa bekräftelser förväntas av partner-värden baserat på inställningarna som visas i avtalet. |
| MI förväntades |Returnera en funktionell bekräftelse (MI) interchange avsändaren. Välj om du vill bekräftelser 997 eller 999 baserat på schema-versioner som du arbetar med. Dessa bekräftelser förväntas av partner-värden baserat på inställningarna som visas i avtalet. |
| MI Version |Välj MI-version |

### <a name="schemas"></a>Scheman

![Välj schema du ska använda](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap | Beskrivning |
| --- | --- |
| Version |Välj X12 version |
| Transaktionstyp (ST01) |Välj transaktionstyp |
| SCHEMAT |Välj scheman som ska användas. Scheman finns i ditt konto för integrering. Om du väljer schemat först konfigurerar den automatiskt version och transaktionen typ  |

> [!NOTE]
> Konfigurera de nödvändiga [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som överförs till ditt [integrering konto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsaren i en transaktion: Välj Standard identifierare eller upprepning avgränsare](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Egenskap | Beskrivning |
| --- | --- |
| ISA11 användning |Anger avgränsare som ska användas i en transaktion: <p>Välj **Standard identifierare** för att använda en punkt (.) för decimalform, i stället för decimalform inkommande dokument i EDI får pipeline. <p>Välj **upprepning avgränsare** att ange avgränsaren för upprepade förekomster av en enkel dataelement eller en upprepad datastruktur. Till exempel används vanligtvis cirkumflex (^) som avgränsare för upprepning. Du kan bara använda cirkumflex för HIPAA scheman. |

### <a name="control-numbers"></a>Kontrollen siffror

![Ange egenskaper för kontrollen tal](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Versionsnumret för kontrollen (ISA12) |Välj versionen av X12 standard |
| Användning-indikatorn (ISA15) |Välj kontexten för ett utbyte.  Värdena är information produktionsdata, eller testa data |
| Schema |Genererar GS och ST segment för X12-kodade utbyte som skickas till skicka Pipeline |
| GS1 |Valfritt, väljer du ett värde för funktionell kod från den nedrullningsbara listrutan |
| GS2 |Valfritt program avsändaren |
| GS3 |Valfritt program mottagare |
| GS4 |Valfritt, Välj CCYYMMDD eller ååmmdd |
| GS5 |Valfritt, Välj HHMM, HHMMSS eller HHMMSSdd |
| GS7 |Valfritt, väljer du ett värde för ansvarar agency från den nedrullningsbara listrutan |
| GS8 |Valfritt, versionen av dokumentet |
| Interchange kontrollnumret (ISA13) |Krävs, ange ett värdeintervall för datautbyte kontroll. Ange ett numeriskt värde med minst 1 och högst 999999999 |
| Gruppnummer kontrollen (GS06) |Krävs, ange ett intervall med nummer för gruppnummer för kontrollen. Ange ett numeriskt värde med minst 1 och högst 999999999 |
| Transaktionsnumret Set kontrollen (ST02) |Krävs, ange ett intervall med nummer för ange Transaktionskontroll numret. Ange ett intervall med numeriska värden med minst 1 och högst 999999999 |
| Prefix |Valfritt, avsedda för intervallet för transaktionen set kontrollen nummer som används i bekräftelse. Ange ett numeriskt värde för mellersta två fält och ett alfanumeriskt värde (om du vill) för fälten prefix och suffix. De mellersta är obligatoriska och innehålla minsta och största värdena för kontrollen |
| Suffix |Valfritt, avsedda för intervallet för transaktionen set kontrollen nummer som används i en bekräftelse. Ange ett numeriskt värde för mellersta två fält och ett alfanumeriskt värde (om du vill) för fälten prefix och suffix. De mellersta är obligatoriska och innehålla minsta och största värdena för kontrollen |

### <a name="character-sets-and-separators"></a>Tecknet uppsättningar och avgränsare

Annat än teckenuppsättningen, kan du ange en annan uppsättning avgränsare för varje meddelandetyp. Om en teckenuppsättning har inte angetts för ett visst meddelande-schema, används den standardinställda teckenuppsättningen.

![Ange avgränsare för meddelandetyper](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Egenskap | Beskrivning |
| --- | --- |
| Tecken som ska användas |Att verifiera egenskaper, Välj X12 teckenuppsättningen. Alternativen är grundläggande och utökad UTF8. |
| Schema |Välj ett schema från den nedrullningsbara listan. När du har slutfört varje rad läggs automatiskt en ny rad. Välj uppsättningen avgränsare som du vill använda, baserat på följande avgränsare beskrivningar för det aktuella schemat. |
| Indatatypen |Välj en typ av inkommande från den nedrullningsbara listan. |
| Komponenten avgränsare |Ange ett enskilt tecken för att avgränsa sammansatta dataelement. |
| Data elementet avgränsare |Ange ett enskilt tecken för att avgränsa enkla dataelement i sammansatta dataelement. |
| Ersättning tecken |Ange ett annat tecken som används för att ersätta alla tecken i nyttolasten vid generering av utgående X12 meddelande. |
| Segmentera Begränsare |Ange ett enskilt tecken för att visa slutet på ett EDI-segment. |
| Suffix |Välj det tecken som används med segment-ID. Om du har angett ett suffix kan dataelementet segment Begränsare vara tom. Om segment Begränsare lämnas tomt, måste du ange ett suffix. |

> [!TIP]
> Om du vill ange värden för specialtecken, redigera avtalet som JSON och ange ASCII-värdet för specialtecknet.

### <a name="validation"></a>Validering

![Ange egenskaper för verifiering för att skicka meddelanden](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

När du slutför varje validering rad läggs en annan automatiskt. Om du inte anger några regler använder verifieringen raden ”standard”.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ för EDI-meddelande. |
| EDI-validering |Validerar EDI-datatyper som definieras av schemat EDI egenskaper, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte EDI, validering på kravet element tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt inledande/efterföljande nollor |Behåll ytterligare inledande eller avslutande noll och utrymme tecken. Ta inte bort dessa tecken. |
| Ta bort inledande/efterföljande nollor |Ta bort inledande eller avslutande noll tecken. |
| Avslutande avgränsare princip |Generera avslutande avgränsare. <p>Välj **inte tillåtet** förhindra avslutande avgränsare och avgränsare i skickade utbyte. Om utbyte har avslutande avgränsare och avgränsare, deklareras utbyte inte giltig. <p>Välj **valfritt** att skicka externa utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatoriska** om skickade interchange måste ha avslutande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta din skapade avtal

1.  När du är klar med inställningen alla dina avtal egenskaper på den **Lägg till** bladet välj **OK** har skapat ditt avtal och gå tillbaka till ditt kontoblad för integrering.

    Ditt nya avtal nu visas i din **avtal** lista.

2.  Du kan också visa dina avtal i ditt Kontoöversikt för integrering. Välj på ditt kontoblad integration **översikt**och välj den **avtal** panelen.

    ![Välj ”avtal” panelen om du vill visa alla avtal](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Visa swagger
Finns det [swagger information](/connectors/x12/). 

## <a name="learn-more"></a>Läs mer
* [Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  

