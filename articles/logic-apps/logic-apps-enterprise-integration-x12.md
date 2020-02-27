---
title: X12-meddelanden för B2B-integrering
description: Exchange X12-meddelanden i EDI-format för B2B Enterprise-integration i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651482"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Exchange X12-meddelanden för B2B Enterprise-integration i Azure Logic Apps med Enterprise-integrationspaket

Innan du kan utbyta X12-meddelanden för Azure Logic Apps måste du skapa ett X12-avtal och lagra avtalet i integrations kontot. Här följer stegen för att skapa ett X12-avtal.

> [!NOTE]
> Den här sidan täcker X12-funktionerna för Azure Logic Apps. Mer information finns i [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt du behöver:

* Ett [integrations konto](logic-apps-enterprise-integration-create-integration-account.md) som redan har definierats och associerats med din Azure-prenumeration
* Minst två [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som har definierats i ditt integrations konto och kon figurer ATS med X12-ID under **affärs identiteter**    
* Ett nödvändigt [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du kan överföra till ditt integrations konto

När du har [skapat ett integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [lagt till partner](logic-apps-enterprise-integration-partners.md)och har ett [schema](../logic-apps/logic-apps-enterprise-integration-schemas.md) som du vill använda, kan du skapa ett X12-avtal genom att följa dessa steg.

## <a name="create-an-x12-agreement"></a>Skapa ett X12-avtal

1. Logga in på [Azure-portalen](https://portal.azure.com "Azure Portal"). 

2. Från huvud menyn i Azure väljer du **alla tjänster**. 
   I rutan Sök anger du "integration" och väljer sedan **integrations konton**.  

   ![Hitta ditt integrations konto](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Om **alla tjänster** inte visas kan du behöva expandera menyn först. Överst på menyn komprimerad väljer du **Visa meny**.

3. Under **integrations konton**väljer du det integrations konto där du vill lägga till avtalet.

   ![Välj integrations konto där du vill skapa avtalet](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Välj **Översikt**och välj sedan **avtals** panelen. 
   Om du inte har någon avtals panel måste du först lägga till panelen. 

   ![Välj avtals panelen](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Välj **Lägg till**under **avtal**.

   ![Välj "Lägg till"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Under **Lägg till**anger du ett **namn** för ditt avtal. 
   För avtals typen väljer du **X12**. 
   Välj **värd partner**, **värd identitet**, **gäst partner**och **gäst identitet** för ditt avtal. 
   Mer information om egenskaper finns i tabellen i det här steget.

    ![Ange avtals information](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Egenskap | Description |
    | --- | --- |
    | Name |Avtalets namn |
    | Avtals typ | Ska vara X12 |
    | Värd partner |Ett avtal måste både vara värd-och gäst partner. Värd partnern representerar den organisation som konfigurerar avtalet. |
    | Värd identitet |En identifierare för värd partnern |
    | Gäst partner |Ett avtal måste både vara värd-och gäst partner. Gäst partnern representerar den organisation som gör affärer med värd partnern. |
    | Gäst identitet |En identifierare för gäst partnern |
    | Ta emot inställningar |Dessa egenskaper gäller för alla meddelanden som tas emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |  

   > [!NOTE]
   > Lösning av X12-avtal beror på matchning av avsändarens kvalificerare och identifierare och mottagarens kvalificerare och identifierare som definierats i partnern och inkommande meddelande. Om dessa värden ändras för din partner uppdaterar du även avtalet.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal hanterar mottagna meddelanden

Nu när du har angett avtals egenskaperna kan du konfigurera hur det här avtalet ska identifiera och hantera inkommande meddelanden som tas emot från din partner genom detta avtal.

1.  Under **Lägg till**väljer du **ta emot inställningar**.
Konfigurera dessa egenskaper utifrån ditt avtal med den partner som utbyter meddelanden med dig. För egenskaps beskrivningar, se tabellerna i det här avsnittet.

    **Ta emot inställningar** är indelat i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, kontroll nummer, validering och interna inställningar.

2. När du är klar bör du spara inställningarna genom att välja **OK**.

Nu är ditt avtal redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange egenskaper för identifierare](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Egenskap | Description |
| --- | --- |
| ISA1 (kvalificering av autentisering) |Välj kvalificerat värde för auktorisering från den nedrullningsbara listan. |
| ISA2 |Valfri. Ange värde för autentiseringsinformation. Om värdet som du angav för ISA1 är ett annat än 00 anger du minst ett alfanumeriskt tecken och högst 10. |
| ISA3 (säkerhets kvalificerare) |Välj värdet för säkerhets kvalificeraren i list rutan. |
| ISA4 |Valfri. Ange värdet för säkerhets information. Om värdet som du angav för ISA3 är ett annat än 00 anger du minst ett alfanumeriskt tecken och högst 10. |

### <a name="acknowledgment"></a>Godkänd

![Ange bekräftelse egenskaper](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Egenskap | Description |
| --- | --- |
| TA1 förväntades |Returnerar en teknisk bekräftelse till Interchange Sender |
| Anl. förväntas |Returnerar en funktions bekräftelse till Interchange-avsändaren. Välj sedan om du vill ha bekräftelser för 997 eller 999 baserat på schema versionen |
| Inkludera AK2/IK2-slinga |Möjliggör generering av AK2-slingor i funktions bekräftelser för accepterade transaktions uppsättningar |

### <a name="schemas"></a>Scheman

Välj ett schema för varje transaktions typ (ST1) och avsändar program (GS2). Receive pipeline Disassemblerar det inkommande meddelandet genom att matcha värdena för ST1 och GS2 i det inkommande meddelandet med de värden som du anger här och schemat för det inkommande meddelandet med det schema som du anger här.

![Välj schema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Egenskap | Description |
| --- | --- |
| Version |Välj X12-versionen |
| Transaktions typ (ST01) |Välj transaktions typ |
| Avsändar program (GS02) |Välj avsändar programmet |
| Schema |Välj den schema fil som du vill använda. Scheman läggs till i ditt integrations konto. |

> [!NOTE]
> Konfigurera det nödvändiga [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som överförs till ditt [integrations konto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsare i en transaktions uppsättning: Välj standard identifierare eller upprepnings avgränsare](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Egenskap | Description |
| --- | --- |
| ISA11-användning |Anger avgränsaren som ska användas i en transaktions uppsättning: <p>Välj **Standard-ID** om du vill använda en punkt (.) för decimal notation i stället för decimal tecknet för det inkommande dokumentet i EDI-inpipeline. <p>Välj **upprepnings avgränsare** för att ange avgränsare för upprepade förekomster av ett enkelt data element eller en upprepad data struktur. Till exempel används vanligt vis cirkumflex (^) som upprepnings avgränsare. För HIPAA-scheman kan du bara använda cirkumflex. |

### <a name="control-numbers"></a>Kontroll nummer

![Välj hur du hanterar dubbletter av kontroll nummer](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Egenskap | Description |
| --- | --- |
| Tillåt inte dubbletter av utbytes kontroll nummer |Blockera dubbla ändringar. Kontrollerar utbytes kontroll numret (ISA13) för det mottagna utbytes kontroll numret. Om en matchning identifieras bearbetas inte utbytet av den mottagande pipelinen. Du kan ange antalet dagar för att utföra kontrollen genom att ange ett värde för att *söka efter dubbletter av ISA13 var (dagar)* . |
| Tillåt inte dubbletter av grupp kontroll nummer |Blockera ändringar med duplicerade grupp kontroll nummer. |
| Tillåt inte dubbletter av transaktions uppsättnings nummer |Blockera ändringar med dubbla uppsättningar av transaktions uppsättningar. |

### <a name="validation"></a>Validering

![Ange verifierings egenskaper för mottagna meddelanden](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

När du Slutför varje validerings rad läggs en ny automatiskt till. Om du inte anger några regler använder valideringen raden "standard".

| Egenskap | Description |
| --- | --- |
| Meddelandetyp |Välj typ av EDI-meddelande. |
| EDI-verifiering |Utföra EDI-verifiering på data typer som definieras av schemats EDI-egenskaper, längd begränsningar, tomma data element och avslutande avgränsare. |
| Utökad verifiering |Om data typen inte är EDI, är verifiering av data elementets krav och tillåten upprepning, uppräkningar och verifiering av data element längd (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande noll och blank stegs tecken. Ta inte bort de här tecknen. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande noll och blank stegs tecken. |
| Avslutande avgränsnings princip |Generera avslutande avgränsare. <p>Välj **tillåts inte** för att förhindra avslutande avgränsare och avgränsare i mottaget utbyte. Om Interchange har avslutande avgränsare och avgränsare, deklareras inte Interchange som giltig. <p>Välj **valfritt** om du vill acceptera ändringar med eller utan efterföljande avgränsare och avgränsare. <p>Välj **obligatoriskt** när Interchange måste ha avslutande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Interna inställningar

![Välj interna inställningar](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Egenskap | Description |
| --- | --- |
| Konvertera det underförstådda decimal formatet "NN" till ett numeriskt bas värde |Konverterar ett EDI-tal som anges med formatet "NN" till ett bas-10-numeriskt värde |
| Skapa tomma XML-taggar om avslutande avgränsare tillåts |Markera den här kryss rutan om du vill att Interchange Sender ska innehålla tomma XML-taggar för efterföljande avgränsare. |
| Dela upp utbyte som transaktions uppsättningar – inaktivera transaktions uppsättningar vid fel|Parsar varje transaktion som angetts i ett utbyte till ett separat XML-dokument genom att använda lämpligt kuvert i transaktions uppsättningen. Pausar endast transaktioner där valideringen Miss lyckas. |
| Dela upp utbyte som transaktions uppsättningar – pausa utbyte vid fel|Parsar varje transaktion som anges i ett utbyte till ett separat XML-dokument genom att använda rätt kuvert. Pausar hela utbytet när en eller flera transaktions uppsättningar i överförings testet inte verifieras. | 
| Bevara Interchange – pausa transaktions uppsättningar vid fel |Lämnar utväxlingen intakt, skapar ett XML-dokument för hela det batchade utbytet. Pausar bara de transaktions uppsättningar som inte kan verifieras och fortsätter att bearbeta alla andra transaktions uppsättningar. |
| Bevara Interchange – pausa utbyte vid fel |Lämnar utväxlingen intakt, skapar ett XML-dokument för hela det batchade utbytet. Pausar hela utbytet när en eller flera transaktions uppsättningar i överförings körningen inte verifieras. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur det här avtalet identifierar och hanterar utgående meddelanden som du skickar till din partner genom detta avtal.

1.  Under **Lägg till**väljer du **Skicka inställningar**.
Konfigurera dessa egenskaper utifrån ditt avtal med din partner som utbyter meddelanden med dig. För egenskaps beskrivningar, se tabellerna i det här avsnittet.

    **Sändnings inställningarna** är indelade i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontroll nummer och verifiering.

2. När du är klar bör du spara inställningarna genom att välja **OK**.

Nu är ditt avtal redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

![Ange egenskaper för identifierare](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Egenskap | Description |
| --- | --- |
| Kvalificerare för autentisering (ISA1) |Välj kvalificerat värde för auktorisering från den nedrullningsbara listan. |
| ISA2 |Ange värde för autentiseringsinformation. Om det här värdet är ett annat än 00 anger du minst ett alfanumeriskt tecken och högst 10. |
| Säkerhets kvalificerare (ISA3) |Välj värdet för säkerhets kvalificeraren i list rutan. |
| ISA4 |Ange värdet för säkerhets information. Om det här värdet är ett annat än 00, för text rutan värde (ISA4) anger du minst ett alfanumeriskt värde och högst 10. |

### <a name="acknowledgment"></a>Godkänd

![Ange bekräftelse egenskaper](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap | Description |
| --- | --- |
| TA1 förväntades |Returnera en teknisk bekräftelse (TA1) till Interchange-avsändaren. Den här inställningen anger att den värd partner som skickar meddelandet begär en bekräftelse från gäst partnern i avtalet. Dessa bekräftelser förväntas av värd partnern enligt avtalets mottagnings inställningar. |
| Anl. förväntas |Returnera en funktionell bekräftelse (FA) till Interchange-avsändaren. Välj om du vill ha bekräftelser för 997 eller 999, baserat på de schema versioner som du arbetar med. Dessa bekräftelser förväntas av värd partnern enligt avtalets mottagnings inställningar. |
| Anl. version |Välj anl-version |

### <a name="schemas"></a>Scheman

![Välj schema som ska användas](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Egenskap | Description |
| --- | --- |
| Version |Välj X12-versionen |
| Transaktions typ (ST01) |Välj transaktions typ |
| SCHEMA |Välj det schema som ska användas. Scheman finns i integrations kontot. Om du väljer schemat först konfigurerar den automatiskt version och transaktions typ  |

> [!NOTE]
> Konfigurera det nödvändiga [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) som överförs till ditt [integrations konto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kuvert

![Ange avgränsare i en transaktions uppsättning: Välj standard identifierare eller upprepnings avgränsare](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Egenskap | Description |
| --- | --- |
| ISA11-användning |Anger avgränsaren som ska användas i en transaktions uppsättning: <p>Välj **Standard-ID** om du vill använda en punkt (.) för decimal notation i stället för decimal tecknet för det inkommande dokumentet i EDI-inpipeline. <p>Välj **upprepnings avgränsare** för att ange avgränsare för upprepade förekomster av ett enkelt data element eller en upprepad data struktur. Till exempel används vanligt vis cirkumflex (^) som upprepnings avgränsare. För HIPAA-scheman kan du bara använda cirkumflex. |

### <a name="control-numbers"></a>Kontroll nummer

![Ange egenskaper för kontroll nummer](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Egenskap | Description |
| --- | --- |
| Kontroll versions nummer (ISA12) |Välj den version av X12-standarden |
| Användnings indikator (ISA15) |Välj kontext för ett utbyte.  Värdena är information, produktions data eller test data |
| Schema |Genererar GS-och ST-segmenten för ett X12-kodat utbyte som skickas till den skicka pipelinen |
| GS1 |Valfritt väljer du ett värde för funktions koden i list rutan |
| GS2 |Valfritt, program avsändare |
| GS3 |Valfri, programmottagare |
| GS4 |Valfritt, Välj CCYYMMDD eller YYMMDD |
| GS5 |Valfritt väljer du HHMM, HHMMSS eller HHMMSSdd |
| GS7 |Valfritt väljer du ett värde för det ansvariga organet i list rutan |
| GS8 |Valfri version av dokumentet |
| Utbytes kontroll nummer (ISA13) |Obligatoriskt anger du ett värde intervall för Interchange Control Number. Ange ett numeriskt värde med minst 1 och högst 999999999 |
| Grupp kontroll nummer (GS06) |Obligatoriskt anger du ett nummer intervall för grupp kontroll numret. Ange ett numeriskt värde med minst 1 och högst 999999999 |
| Kontroll nummer för transaktions uppsättning (ST02) |Obligatoriskt anger du ett nummer intervall för kontroll numret för transaktions uppsättningen. Ange ett intervall med numeriska värden med minst 1 och högst 999999999 |
| Protokollprefixet |Valfritt, anges för det antal transaktions uppsättnings kontroll nummer som används i bekräftelse. Ange ett numeriskt värde för två fält i mitten och ett alfanumeriskt värde (om det behövs) för fälten prefix och suffix. De mittersta fälten är obligatoriska och innehåller de lägsta och högsta värdena för kontroll numret |
| Huvudnamnssuffix |Valfritt, anges för det antal transaktions uppsättnings kontroll nummer som används i en bekräftelse. Ange ett numeriskt värde för de två fälten i mitten och ett alfanumeriskt värde (om det behövs) för fälten prefix och suffix. De mittersta fälten är obligatoriska och innehåller de lägsta och högsta värdena för kontroll numret |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Förutom teckenuppsättningen kan du ange en annan uppsättning avgränsare för varje meddelande typ. Om en teckenuppsättning inte har angetts för ett visst meddelande schema används standard teckenuppsättningen.

![Ange avgränsare för meddelande typer](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Egenskap | Description |
| --- | --- |
| Teckenuppsättning som ska användas |Om du vill validera egenskaperna väljer du teckenuppsättningen X12. Alternativen är Basic, Extended och UTF8. |
| Schema |Välj ett schema från den nedrullningsbara listan. När du har slutfört varje rad läggs en ny rad till automatiskt. För det valda schemat väljer du de avgränsnings uppsättningar som du vill använda, baserat på avgräns beskrivningarna nedan. |
| Indatatyp |Välj en indatatyp i list rutan. |
| Komponent avgränsare |Om du vill separera sammansatta data element anger du ett enda-Character. |
| Data Elements avgränsare |Om du vill separera enkla data element i sammansatta data element anger du ett enskilt. |
| Ersättnings Character |Ange ett ersättnings tecken som används för att ersätta alla avgränsnings tecken i nytto Last data när du genererar det utgående X12-meddelandet. |
| Segment begränsare |Om du vill ange ett EDI-segments slut anger du ett enskilt tecken. |
| Huvudnamnssuffix |Välj det tangent som används med segment identifieraren. Om du anger ett suffix, kan data elementet för segment begränsas vara tomt. Om avgränsarna för segment lämnas tomma måste du ange ett suffix. |

> [!TIP]
> Om du vill ange specialtecken redigerar du avtalet som JSON och anger ASCII-värdet för specialtecknet.

### <a name="validation"></a>Validering

![Ange verifierings egenskaper för att skicka meddelanden](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

När du Slutför varje validerings rad läggs en ny automatiskt till. Om du inte anger några regler använder valideringen raden "standard".

| Egenskap | Description |
| --- | --- |
| Meddelandetyp |Välj typ av EDI-meddelande. |
| EDI-verifiering |Utföra EDI-verifiering på data typer som definieras av schemats EDI-egenskaper, längd begränsningar, tomma data element och avslutande avgränsare. |
| Utökad verifiering |Om data typen inte är EDI, är verifiering av data elementets krav och tillåten upprepning, uppräkningar och verifiering av data element längd (min/max). |
| Tillåt inledande/avslutande nollor |Behåll eventuella ytterligare inledande eller avslutande noll och blank stegs tecken. Ta inte bort de här tecknen. |
| Trimma inledande/avslutande nollor |Ta bort inledande eller avslutande noll tecken. |
| Avslutande avgränsnings princip |Generera avslutande avgränsare. <p>Välj **tillåts inte** för att förhindra avslutande avgränsare och avgränsare i skickade Interchange. Om Interchange har avslutande avgränsare och avgränsare, deklareras inte Interchange som giltig. <p>Välj **valfritt** om du vill skicka ändringar med eller utan efterföljande avgränsare och avgränsare. <p>Välj **obligatoriskt** om den skickade Interchange måste ha efterföljande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta ditt skapade avtal

1.  När du är klar med att ange alla avtals egenskaper går du till sidan **Lägg till** och väljer **OK** för att slutföra skapandet av ditt avtal och återgår till ditt integrations konto.

    Ditt nyligen tillagda avtal visas nu i **avtals** listan.

2.  Du kan också visa dina avtal i Översikt över integrations kontot. På integrations konto menyn väljer du **Översikt**och väljer sedan **avtals** panelen.

    ![Välj avtals panelen](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)