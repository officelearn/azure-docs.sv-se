---
title: "EDIFACT-meddelanden för B2B enterprise integration - Azure Logic Apps | Microsoft Docs"
description: "Exchange EDIFACT-meddelanden i EDI-format för B2B enterprise integrering med Azure Logikappar"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/26/2016
ms.author: LADocs; jonfan
ms.openlocfilehash: fc9a0068de5f9464133eec0b043fbba1dc0fbde7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="exchange-edifact-messages-for-enterprise-integration-with-logic-apps"></a>Exchange EDIFACT-meddelanden för enterprise-integrering med logic apps

Innan du kan utbyta EDIFACT-meddelanden för Logikappar i Azure, måste du skapa ett EDIFACT-avtal och spara avtalet i ditt konto för integrering. Här följer stegen för hur du skapar ett EDIFACT-avtal.

> [!NOTE]
> Den här sidan innehåller EDIFACT-funktioner för Azure Logic Apps. Mer information finns i [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Innan du börjar

Här är de objekt som du behöver:

* En [integrering konto](../logic-apps/logic-apps-enterprise-integration-accounts.md) som redan har definierat och som är associerade med din Azure-prenumeration  
* Minst två [partners](logic-apps-enterprise-integration-partners.md) som redan har definierats i ditt konto för integrering

> [!NOTE]
> När du skapar ett avtal måste innehållet i de meddelanden som du tar emot eller skickar till och från partnern matcha en avtalstyp.

När du [skapa ett konto för integrering](../logic-apps/logic-apps-enterprise-integration-accounts.md) och [lägga till partners](logic-apps-enterprise-integration-partners.md), du kan skapa ett EDIFACT-avtal genom att följa dessa steg.

## <a name="create-an-edifact-agreement"></a>Skapa ett EDIFACT-avtal 

1.  Logga in på [Azure Portal](http://portal.azure.com "Azure Portal"). I den vänstra menyn, Välj **fler tjänster**.

    > [!TIP]
    > Om du inte ser **fler tjänster**, du kan behöva expandera menyn först. Längst upp i den komprimerade menyn, Välj **menyn Visa**.

    ![På vänster-menyn väljer du ”fler tjänster”](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

2. I sökrutan skriver du ”integration” för filtret. Välj i resultatlistan **Integrationskonton**.

    ![Filtrera efter ”integration”, Välj ”Integrationskonton”](./media/logic-apps-enterprise-integration-edifact/edifact-1-3.png)

3. I den **Integrationskonton** bladet som öppnas väljer du det integration konto där du vill skapa avtal.
Om du inte ser några integrationskonton [skapa en första](../logic-apps/logic-apps-enterprise-integration-accounts.md "om integrationskonton").  

    ![Välj integration konto var du vill skapa avtalet](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Välj den **avtal** panelen. Om du inte har ett avtal sida vid sida, lägga till panelen först.   

    ![Välj ikonen ”avtal”](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. I bladet avtal som öppnas väljer du **Lägg till**.

    ![Välj ”Lägg till”](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Under **Lägg till**, ange en **namn** för ditt avtal. För **avtalstyp**väljer **EDIFACT**. Välj den **värden Partner**, **Värdidentiteten**, **gäst Partner**, och **gäst identitet** för ditt avtal.

    ![Ange avtalsuppgifter](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

    | Egenskap | Beskrivning |
    | --- | --- |
    | Namn |Avtalets namn |
    | Avtalstyp | Ska vara EDIFACT |
    | Värd-Partner |Ett avtal måste både värden och gästen partner. Den mottagande partnern representerar den organisation som konfigurerar avtalet. |
    | Värdidentiteten |En identifierare för värdpartnern som |
    | Gästen Partner |Ett avtal måste både värden och gästen partner. Gästen partnern representerar den organisation som har att göra affärer med den mottagande partnern. |
    | Gästen identitet |En identifierare för gäst-partner |
    | Ta emot inställningarna |Dessa egenskaper gäller för alla meddelanden som tagits emot av ett avtal. |
    | Skicka inställningar |Dessa egenskaper gäller för alla meddelanden som skickas av ett avtal. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurera hur ditt avtal handtag mottagna meddelanden

Nu när du har angett egenskaperna avtal, kan du konfigurera hur detta avtal identifierar och hanterar inkommande meddelanden som tagits emot från din partner via det här avtalet.

1.  Under **Lägg till**väljer **tar emot inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med den partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Ta emot inställningarna** är indelad i följande avsnitt: identifierare, bekräftelse, scheman, kontroll siffror, verifiering och interna inställningar.

    ![Konfigurera ”ta emot inställningar”](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera inkommande meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap | Beskrivning |
| --- | --- |
| UNB6.1 (mottagande referens lösenord) |Ange ett alfanumeriskt värde mellan 1 och 14 tecken. |
| UNB6.2 (mottagande referens kvalificerare) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |

### <a name="acknowledgments"></a>Bekräftelser

| Egenskap | Beskrivning |
| --- | --- |
| Inleverans av meddelande (CONTRL) |Markera kryssrutan för att returnera en teknisk (CONTRL) bekräftelse till interchange avsändaren. Bekräftelsen skickas till interchange avsändaren baserat på Skicka-inställningar för avtalet. |
| Bekräftelse (CONTRL) |Markera kryssrutan för att returnera en funktionell (CONTRL) bekräftelse till interchange avsändaren bekräftelsen skickas till interchange avsändaren baserat på Skicka-inställningar för avtalet. |

### <a name="schemas"></a>Scheman

| Egenskap | Beskrivning |
| --- | --- |
| UNH2.1 (TYP) |Välj en uppsättning transaktionstyp. |
| UNH2.2 (VERSION) |Ange versionsnummer för meddelandet. (Minimum, ett tecken; maximalt tre tecken). |
| UNH2.3 (VERSION) |Ange versionsnumret för meddelandet. (Minimum, ett tecken; maximalt tre tecken). |
| UNH2.5 (ASSOCIERADE TILLDELADE KOD) |Ange den tilldelade koden. (Maximalt sex tecken. Måste vara alfanumeriskt). |
| UNG2.1 (APP AVSÄNDAR-ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. |
| UNG2.2 (APP AVSÄNDAREN KOD KVALIFICERARE) |Ange ett alfanumeriskt värde med högst fyra tecken. |
| SCHEMAT |Välj tidigare överförda schema som du vill använda från ditt konto med associerade integrering. |

### <a name="control-numbers"></a>Kontrollen siffror
| Egenskap | Beskrivning |
| --- | --- |
| Tillåt inte Interchange kontrollen antal dubbletter |Välj den här egenskapen om du vill blockera dubbla externa utbyten. Om markerad kontrollerar EDIFACT avkoda åtgärden att den interchange kontrollen (UNB5) för mottagna datautbyte inte matchar några tidigare bearbetade interchange kontroll. Om en matchning hittas, bearbetas inte utbyte. |
| Leta efter UNB5-dubletter varje (dagar) |Om du väljer att inte tillåta dubbla interchange kontrollen siffror, du kan ange antalet dagar när du ska kontrollera genom att ge rätt värde för den här inställningen. |
| Tillåt inte dubletter av gruppkontrollnummer |Välj den här egenskapen om du vill blockera externa utbyten med dubbla grupp kontrollen siffror (UNG5). |
| Tillåt inte dubletter av transaktionsuppsättningsnummer |Välj den här egenskapen om du vill blockera externa utbyten med dubbla transaktion set kontrollen siffror (UNH1). |
| EDIFACT bekräftelse kontrollen tal |Ange ett värde för prefix, ett intervall av referensnummer och ett suffix om du vill ange transaktionen set referensnummer för användning i en bekräftelse. |

### <a name="validations"></a>Verifieringar

När du slutför varje validering rad läggs en annan automatiskt. Om du inte anger några regler använder verifieringen raden ”standard”.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ för EDI-meddelande. |
| EDI-validering |Validerar EDI-datatyper som definieras av schemat EDI egenskaper, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte EDI, validering på kravet element tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt inledande/efterföljande nollor |Behåll ytterligare inledande eller avslutande noll och utrymme tecken. Ta inte bort dessa tecken. |
| Ta bort inledande/efterföljande nollor |Ta bort inledande eller efterföljande noll och blanksteg. |
| Avslutande avgränsare princip |Generera avslutande avgränsare. <p>Välj **inte tillåtet** förhindra avslutande avgränsare och avgränsare i mottagna utbyte. Om utbyte har avslutande avgränsare och avgränsare, deklareras utbyte inte giltig. <p>Välj **valfritt** att acceptera externa utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatoriska** när mottagna interchange måste ha avslutande avgränsare och avgränsare. |

### <a name="internal-settings"></a>Internt inställningar

| Egenskap | Beskrivning |
| --- | --- |
| Skapa tomma XML-taggar om avslutande avgränsare är tillåtna |Välj den här kryssrutan om du vill interchange avsändaren är tom XML-taggar för avslutande avgränsare. |
| Dela upp interchange i transaktionsuppsättningar – inaktivera transaktionsuppsättningar vid fel|Parsar varje transaktion som anges i ett utbyte i ett annat XML-dokument genom att använda lämpliga kuvertet uppsättningen transaktion. Pausa bara som inte kan valideras mängderna transaktion. |
| Dela upp interchange i transaktionsuppsättningar – inaktivera interchange vid fel|Parsar varje transaktion som anges i ett utbyte i ett annat XML-dokument genom att tillämpa lämpliga kuvertet. Pausa hela interchange när en eller flera uppsättningar av transaktionen i utbyte inte kan valideras. | 
| Bevara Interchange – inaktivera transaktion anger vid fel |Lämnar utbyte intakta, skapar ett XML-dokument för hela gruppbaserad datautbyte. Pausa bara som inte kan verifieras när fortsätter att bearbeta alla andra transaktion anger mängderna transaktion. |
| Bevara interchange – inaktivera interchange vid fel |Lämnar utbyte intakta, skapar ett XML-dokument för hela gruppbaserad datautbyte. Pausa hela interchange när en eller flera uppsättningar av transaktionen i utbyte inte kan valideras. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurera hur ditt avtal skickar meddelanden

Du kan konfigurera hur detta avtal identifierar och hanterar utgående meddelanden som du skickar till dina partners via det här avtalet.

1.  Under **Lägg till**väljer **skicka inställningar**.
Konfigurera dessa egenskaper baserat på ditt avtal med din partner som utbyter meddelanden med dig. Egenskapsbeskrivningar finns i tabellerna i det här avsnittet.

    **Skicka inställningar** är indelad i följande avsnitt: identifierare, bekräftelse, scheman, kuvert, teckenuppsättningar och avgränsare, kontroll siffror och verifieringar.

    ![Konfigurera ”skicka inställningar”](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. När du är klar kan du se till att spara inställningarna genom att välja **OK**.

Ditt avtal är nu redo att hantera utgående meddelanden som överensstämmer med dina valda inställningar.

### <a name="identifiers"></a>Identifierare

| Egenskap | Beskrivning |
| --- | --- |
| UNB1.2 (Syntax version) |Välj ett värde mellan **1** och **4**. |
| UNB2.3 (omvänd routingadress för avsändare) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB3.3 (omvänd routingadress för mottagare) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |
| UNB6.1 (mottagande referens lösenord) |Ange ett alfanumeriskt värde med minst en och högst 14 tecken. |
| UNB6.2 (mottagande referens kvalificerare) |Ange ett alfanumeriskt värde med minst ett tecken och högst två tecken. |
| UNB7 (program referens-ID) |Ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken |

### <a name="acknowledgment"></a>Bekräftelse
| Egenskap | Beskrivning |
| --- | --- |
| Inleverans av meddelande (CONTRL) |Markera kryssrutan om den värdbaserade partnern förväntar sig att ta emot en teknisk (CONTRL) bekräftelse. Den här inställningen anger att den värdbaserade partner som skickar meddelandet, begär en bekräftelse från gäst-partner. |
| Bekräftelse (CONTRL) |Markera kryssrutan om den värdbaserade partnern förväntar sig att ta emot en funktionell (CONTRL) bekräftelse. Den här inställningen anger att den värdbaserade partner som skickar meddelandet, begär en bekräftelse från gäst-partner. |
| Skapa SG1/SG4-slinga för godkända transaktionsuppsättningar |Om du vill begära en funktionell bekräftelse, markera kryssrutan för att tvinga generering av SG1/SG4 loopar i funktionella CONTRL bekräftelser för godkända transaktionen uppsättningar. |

### <a name="schemas"></a>Scheman
| Egenskap | Beskrivning |
| --- | --- |
| UNH2.1 (TYP) |Välj en uppsättning transaktionstyp. |
| UNH2.2 (VERSION) |Ange versionsnummer för meddelandet. |
| UNH2.3 (VERSION) |Ange versionsnumret för meddelandet. |
| SCHEMAT |Välj scheman som ska användas. Scheman finns i ditt konto för integrering. För att komma åt dina scheman länka ditt integration konto till din logikapp. |

### <a name="envelopes"></a>Kuvert
| Egenskap | Beskrivning |
| --- | --- |
| UNB8 (bearbetar prioritetskod) |Ange en alfabetisk värde som inte är mer än ett tecken lång. |
| UNB10 (avtal för kommunikation) |Ange ett alfanumeriskt värde med minst ett tecken och högst 40 tecken. |
| UNB11 (testa Indicator) |Markera kryssrutan för att indikera att interchange genereras är testdata |
| Tillämpa UNA-segment (tjänststrängråd) |Markera kryssrutan för att generera ett UNA segment för datautbyte skickas. |
| Tillämpa UNG-segment (funktionsgrupprubrik) |Markera kryssrutan för att skapa gruppering segment i funktionella grupphuvud i meddelanden som skickas till gäst-partner. Följande värden används för att skapa UNG segment: <p>För **UNG1**, ange ett alfanumeriskt värde med minst ett tecken och högst sex tecken. <p>För **UNG2.1**, ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **UNG2.2**, ange ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG3.1**, ange ett alfanumeriskt värde med minst ett tecken och högst 35 tecken. <p>För **UNG3.2**, ange ett alfanumeriskt värde med högst fyra tecken. <p>För **UNG6**, ange ett alfanumeriskt värde med minst en och högst tre tecken. <p>För **UNG7.1**, ange ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **UNG7.2**, ange ett alfanumeriskt värde med minst ett tecken och högst tre tecken. <p>För **UNG7.3**, ange ett alfanumeriskt värde med minst 1 tecken och högst 6 tecken. <p>För **UNG8**, ange ett alfanumeriskt värde med minst ett tecken och högst 14 tecken. |

### <a name="character-sets-and-separators"></a>Teckenuppsättningar och avgränsare

Andra än teckenuppsättningen, kan du ange en annan uppsättning avgränsare som ska användas för varje meddelandetyp. Om en teckenuppsättning inte har angetts för ett visst meddelande-schema, används den standardinställda teckenuppsättningen.

| Egenskap | Beskrivning |
| --- | --- |
| UNB1.1 (System Identifier) |Välj EDIFACT-teckenuppsättningen som ska tillämpas på utgående utbyte. |
| Schemat |Välj ett schema från den nedrullningsbara listan. När du har slutfört varje rad läggs automatiskt en ny rad. Välj uppsättningen avgränsare som du vill använda, baserat på följande avgränsare beskrivningar för det aktuella schemat. |
| Indatatypen |Välj en typ av inkommande från den nedrullningsbara listan. |
| Komponenten avgränsare |Ange ett enskilt tecken för att avgränsa sammansatta dataelement. |
| Data elementet avgränsare |Ange ett enskilt tecken för att avgränsa enkla dataelement i sammansatta dataelement. |
| Segmentera Begränsare |Ange ett enskilt tecken för att visa slutet på ett EDI-segment. |
| Suffix |Välj det tecken som används med segment-ID. Om du har angett ett suffix kan dataelementet segment Begränsare vara tom. Om segment Begränsare lämnas tomt, måste du ange ett suffix. |

### <a name="control-numbers"></a>Kontrollen siffror
| Egenskap | Beskrivning |
| --- | --- |
| UNB5 (Interchange kontrollen tal) |Ange ett prefix, ett intervall med värden för datautbyte kontrollen Antal och ett suffix. Dessa värden används för att generera ett utgående utbyte. Prefixet och suffixet är valfria, medan kontrollen tal krävs. Kontrollera numret ökas för varje nytt meddelande; prefixet och suffixet desamma. |
| UNG5 (gruppen kontrollen tal) |Ange ett prefix, ett intervall med värden för datautbyte kontrollen Antal och ett suffix. Dessa värden används för att generera gruppnummer för kontrollen. Prefixet och suffixet är valfria, medan kontrollen tal krävs. Kontrollnumret ökas för varje nytt meddelande tills det största värdet har nåtts. prefixet och suffixet desamma. |
| UNH1 (meddelandenummer sidhuvud referens) |Ange ett prefix, ett intervall med värden för datautbyte kontrollen Antal och ett suffix. Dessa värden används för att generera referensnumret för meddelande-huvud. Prefixet och suffixet är valfria, medan referensnumret måste anges. Referensnumret ökas för varje nytt meddelande; prefixet och suffixet desamma. |

### <a name="validations"></a>Verifieringar

När du slutför varje validering rad läggs en annan automatiskt. Om du inte anger några regler använder verifieringen raden ”standard”.

| Egenskap | Beskrivning |
| --- | --- |
| Meddelandetyp |Välj typ för EDI-meddelande. |
| EDI-validering |Validerar EDI-datatyper som definieras av EDI-egenskaperna för schemat, längd, tom dataelement och avslutande avgränsare. |
| Utökad validering |Om datatypen inte EDI, validering på kravet element tillåts och upprepning, uppräkningar och elementet längd dataverifiering (min/max). |
| Tillåt inledande/efterföljande nollor |Behåll ytterligare inledande eller avslutande noll och utrymme tecken. Ta inte bort dessa tecken. |
| Ta bort inledande/efterföljande nollor |Ta bort inledande eller avslutande noll tecken. |
| Avslutande avgränsare princip |Generera avslutande avgränsare. <p>Välj **inte tillåtet** förhindra avslutande avgränsare och avgränsare i skickade utbyte. Om utbyte har avslutande avgränsare och avgränsare, deklareras utbyte inte giltig. <p>Välj **valfritt** att skicka externa utbyten med eller utan avslutande avgränsare och avgränsare. <p>Välj **obligatoriska** om skickade interchange måste ha avslutande avgränsare och avgränsare. |

## <a name="find-your-created-agreement"></a>Hitta din skapade avtal

1.  När du är klar med inställningen alla dina avtal egenskaper på den **Lägg till** bladet välj **OK** har skapat ditt avtal och gå tillbaka till ditt kontoblad för integrering.

    Ditt nya avtal nu visas i din **avtal** lista.

2.  Du kan också visa dina avtal i ditt Kontoöversikt för integrering. Välj på ditt kontoblad integration **översikt**och välj den **avtal** panelen. 

    ![Välj ”avtal” panelen om du vill visa alla avtal](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Visa Swagger-fil
Swagger-information för EDIFACT-anslutningen finns i [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Läs mer
* [Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  

