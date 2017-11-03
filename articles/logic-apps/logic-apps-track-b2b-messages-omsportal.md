---
title: "Spåra B2B-meddelanden i Operations Management Suite - Azure Logic Apps | Microsoft Docs"
description: "Spåra B2B-kommunikation för ditt konto och logik integrationsappar i Operations Management Suite (OMS) med Azure logganalys"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3ef7a4054be80547b0d91ad1f13777d915005f8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>Spåra B2B-kommunikation i Microsoft Operations Management Suite (OMS)

När du har skapat B2B-kommunikation mellan två kör affärsprocesser eller program via kontot integration dessa enheter kan utbyta meddelanden med varandra. För att kontrollera om dessa meddelanden bearbetas på rätt sätt kan du spåra AS2-, X12, och EDIFACT meddelanden med [Azure logganalys](../log-analytics/log-analytics-overview.md) i den [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Du kan till exempel använda dessa webbaserade spårningsfunktioner för spårning av meddelanden:

* Antalet meddelanden och status
* Bekräftelser status
* Korrelera meddelanden med bekräftelser
* Detaljerade felbeskrivningar för fel
* Sökfunktioner

## <a name="requirements"></a>Krav

* En logikapp som har konfigurerats med diagnostikloggning. Läs [hur du skapar en logikapp](logic-apps-create-a-logic-app.md) och [hur du ställer in loggning för att logikapp](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integration-konto som har konfigurerats med övervakning och loggning. Läs [hur du skapar ett konto för integrering](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* Om du inte redan har gjort [publicera diagnostikdata till logganalys](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) i OMS.

> [!NOTE]
> När du har uppfyllt kraven tidigare, bör du ha en arbetsyta den [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Du bör använda samma OMS-arbetsyta för att spåra dina B2B-kommunikation i OMS. 
>  
> Lär dig mer om du inte har en OMS-arbetsyta [hur du skapar en OMS-arbetsyta](../log-analytics/log-analytics-get-started.md).

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>Lägg till Logic Apps B2B-lösning till Operations Management Suite (OMS)

Om du vill att OMS spåra B2B-meddelanden för din logikapp, måste du lägga till den **Logic Apps B2B** lösning till OMS-portalen. Lär dig mer om [att lägga till lösningar till OMS](../log-analytics/log-analytics-get-started.md).

1. I den [Azure-portalen](https://portal.azure.com), Välj **fler tjänster**. Sök efter ”logganalys” och välj sedan **logganalys** som visas här:

   ![Hitta logganalys](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. Under **logganalys**, söka efter och välj din OMS-arbetsyta. 

   ![Välj din OMS-arbetsyta](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Under **Management**, Välj **OMS-portalen**.

   ![Välj OMS-portalen](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. När sidan OMS öppnar väljer **lösningar galleriet**.    

   ![Välj lösningar galleri](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Under **alla lösningar för**, söka efter och välj **Logic Apps B2B**.     

   ![Välj Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Under **Logic Apps B2B**, Välj **Lägg till**.

   ![Välj Lägg till](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   På OMS-startsidan panelen för **Logic Apps B2B-meddelanden** visas nu. 
   Den här panelen uppdaterar antalet meddelanden när dina B2B-meddelanden bearbetas.

   ![OMS-startsidan, panelen Logic Apps B2B-meddelanden](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>Spåra meddelandestatus och information i Operations Management Suite

1. När dina B2B-meddelanden bearbetas visa du status och information om dessa meddelanden. På startsidan OMS väljer du den **Logic Apps B2B-meddelanden** panelen.

   ![Antal uppdaterade meddelande](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > Som standard den **Logic Apps B2B-meddelanden** innehåller data baserat på en dag. Välj scope kontrollen överst på sidan OMS om du vill ändra vilka data till ett annat intervall:
   > 
   > ![Ändra omfång för data](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. När meddelandet visas status instrumentpanelen kan du kan visa mer information om en specifik meddelandetyp som visar data baserat på en dag. Välj panelen för **AS2**, **X12**, eller **EDIFACT**.

   ![Visa status för meddelandet](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   En lista med meddelanden visas för din valda sida vid sida. 
   Mer information om egenskaper för varje meddelandetyp finns dessa beskrivningar för meddelande-egenskapen:

   * [Egenskaper för AS2-meddelande](#as2-message-properties)
   * [X12 meddelande egenskaper](#x12-message-properties)
   * [Egenskaper för EDIFACT-meddelande](#EDIFACT-message-properties)

   Till exempel är här en lista över AS2-meddelande kan se ut:

   ![Visa AS2-meddelanden](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Om du vill visa eller exportera indata och utdata för specifika meddelanden, Välj dessa meddelanden och välj **hämta**. När du uppmanas spara ZIP-filen till den lokala datorn och sedan extrahera filen. 

   Den extraherade mappen innehåller en mapp för varje vald meddelande. 
   Om du ställer in bekräftelser även meddelandemappen filer med information om bekräftelse. 
   Varje meddelandemapp har minst filerna: 
   
   * Läsbart filer med nyttolasten indata och utdata nyttolast information
   * Kodade filer med in- och utdataenheter

   För varje meddelandetyp hittar du i mappen och namnet filformat här:

   * [AS2 mapp- och name-format](#as2-folder-file-names)
   * [X12 mapp- och name format](#x12-folder-file-names)
   * [EDIFACT mapp- och name-format](#edifact-folder-file-names)

   ![Hämta meddelandefiler](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Visa alla åtgärder som har samma körnings-ID, på den **loggen Sök** väljer du ett meddelande från listan.

   Du kan sortera åtgärderna av kolumn, eller söka efter specifika resultat.

   ![Åtgärder med samma körnings-ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Sökresultat med färdiga frågor, Välj **Favoriter**.

   * Läs [skapa frågor genom att lägga till filter](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Eller Läs mer om [hitta data med loggen sökningar i logganalys](../log-analytics/log-analytics-log-searches.md).

   * Uppdatera frågan med kolumner och värden som du vill använda som filter om du vill ändra frågan i sökrutan.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Egenskapsbeskrivningar och format för AS2-, X 12- och EDIFACT-meddelanden

För varje meddelandetyp är här Egenskapsbeskrivningar och format för hämtade meddelandefiler.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 meddelandet Egenskapsbeskrivningar

Här är Egenskapsbeskrivningar för varje AS2-meddelande.

| Egenskap | Beskrivning |
| --- | --- |
| Avsändaren | Gäst-partner som anges i **tar emot inställningar**, eller värden partner som anges i **skicka inställningar** för ett AS2-avtal |
| Mottagaren | Värd-partner som anges i **tar emot inställningar**, eller gäst-partner som anges i **skicka inställningar** för ett AS2-avtal |
| Logikapp | Logikappen där AS2-åtgärder ställa in |
| Status | Status för AS2-meddelande <br>Lyckade = mottagna eller skickade ett ogiltigt AS2-meddelande. Ingen MDN har ställts in. <br>Lyckade = mottagna eller skickade ett ogiltigt AS2-meddelande. MDN ställa in och tas emot eller MDN skickas. <br>Det gick inte = tagits emot ett ogiltigt AS2-meddelande. Ingen MDN har ställts in. <br>Väntande = mottagna eller skickade ett ogiltigt AS2-meddelande. MDN ställs in och MDN förväntas. |
| Ack | Status för MDN-meddelande <br>Godkänt = tagits emot eller skickats positivt MDN. <br>Väntande = väntar på att ta emot och skicka en MDN. <br>Avvisade = mottagna eller skickas en negativ MDN. <br>Krävs inte = MDN har inte ställts in i avtalet. |
| Riktning | AS2 Meddelanderiktning |
| Korrelations-ID | Det ID som korrelerar alla utlösare och åtgärder i en logikapp |
| Meddelande-ID | AS2-meddelande-ID från meddelandehuvuden AS2 |
| tidsstämpel | Den tid när AS2-åtgärden bearbetas meddelandet |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>AS2 namnformat för hämtade meddelandefiler

Här följer namnformat för varje mapp för hämtade AS2-meddelanden och filer.

| Mapp eller fil | Format för namn |
| :------------- | :---------- |
| Meddelande-mappen | [avsändaren] \_[mottagaren]\_AS2\_[Korrelations-ID]\_[meddelande-ID]\_[tidsstämpel] |
| Indata, utdata och om att ställa in bekräftelse filer | **Nyttolasten indata**: [avsändaren]\_[mottagaren]\_AS2\_[Korrelations-ID]\_input_payload.txt </p>**Utdata nyttolast**: [avsändaren]\_[mottagaren]\_AS2\_[Korrelations-ID]\_utdata\_payload.txt </p></p>**Indata**: [avsändaren]\_[mottagaren]\_AS2\_[Korrelations-ID]\_inputs.txt </p></p>**Utdata**: [avsändaren]\_[mottagaren]\_AS2\_[Korrelations-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 meddelande Egenskapsbeskrivningar

Här följer Egenskapsbeskrivningar för varje X12 meddelande.

| Egenskap | Beskrivning |
| --- | --- |
| Avsändaren | Gäst-partner som anges i **tar emot inställningar**, eller värden partner som anges i **skicka inställningar** för ett X12 avtal |
| Mottagaren | Värd-partner som anges i **tar emot inställningar**, eller gäst-partner som anges i **skicka inställningar** för ett X12 avtal |
| Logikapp | Logikappen där X12 åtgärder ställs in |
| Status | Den X12 meddelandet status <br>Lyckade = mottagna eller skickas en giltig X12 meddelande. Inga funktionella ack har ställts in. <br>Lyckade = mottagna eller skickas en giltig X12 meddelande. Funktionella ack ställa in och tas emot eller en funktionell ack skickas. <br>Det gick inte = mottagna eller skickade ett ogiltigt X12 meddelande. <br>Väntande = mottagna eller skickas en giltig X12 meddelande. Funktionella ack har konfigurerats och en fungerande ack förväntas. |
| Ack | Funktionella Ack (997)-status <br>Godkänt = mottagna eller skickas en positiv funktionella bekräftelse. <br>Avvisade = mottagna eller skickas en negativ funktionella bekräftelse. <br>Väntande = förväntas en funktionell ack men inte tagits emot. <br>= Fram en funktionell ack men kan inte skicka till partner. <br>Krävs inte = funktionella ack har inte ställts in. |
| Riktning | Den X12 meddelande riktning |
| Korrelations-ID | Det ID som korrelerar alla utlösare och åtgärder i en logikapp |
| Typen av meddelande | Meddelandetypen EDI X 12 |
| ICN | Kontrollen Interchange numret för X12 meddelande |
| TSCN | Ange kontroll transaktionsnumret för X12 meddelande |
| tidsstämpel | Tid när X12 åtgärden bearbetas meddelandet |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 namn format för hämtade meddelandefiler

Här följer namnformat för för varje hämtat X12 mappar och filer.

| Mapp eller fil | Format för namn |
| :------------- | :---------- |
| Meddelande-mappen | [avsändaren] \_[mottagaren]\_X12\_[interchange-kontroll-nummer]\_[globala-kontroll-nummer]\_[-set-kontroll-transaktionsnumret]\_[tidsstämpel] |
| Indata, utdata och om att ställa in bekräftelse filer | **Nyttolasten indata**: [avsändaren]\_[mottagaren]\_X12\_[interchange-kontroll-nummer]\_input_payload.txt </p>**Utdata nyttolast**: [avsändaren]\_[mottagaren]\_X12\_[interchange-kontroll-nummer]\_utdata\_payload.txt </p></p>**Indata**: [avsändaren]\_[mottagaren]\_X12\_[interchange-kontroll-nummer]\_inputs.txt </p></p>**Utdata**: [avsändaren]\_[mottagaren]\_X12\_[interchange-kontroll-nummer]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT meddelandet Egenskapsbeskrivningar

Här är Egenskapsbeskrivningar för varje EDIFACT-meddelande.

| Egenskap | Beskrivning |
| --- | --- |
| Avsändaren | Gäst-partner som anges i **tar emot inställningar**, eller värden partner som anges i **skicka inställningar** för ett EDIFACT-avtal |
| Mottagaren | Värd-partner som anges i **tar emot inställningar**, eller gäst-partner som anges i **skicka inställningar** för ett EDIFACT-avtal |
| Logikapp | Logikappen där EDIFACT-åtgärder ställa in |
| Status | Status för EDIFACT-meddelande <br>Lyckade = mottagna eller skickade ett ogiltigt EDIFACT-meddelande. Inga funktionella ack har ställts in. <br>Lyckade = mottagna eller skickade ett ogiltigt EDIFACT-meddelande. Funktionella ack ställa in och tas emot eller en funktionell ack skickas. <br>Det gick inte = mottagna eller skickade ett ogiltigt EDIFACT-meddelande <br>Väntande = mottagna eller skickade ett ogiltigt EDIFACT-meddelande. Funktionella ack har konfigurerats och en fungerande ack förväntas. |
| Ack | Funktionella Ack (997)-status <br>Godkänt = mottagna eller skickas en positiv funktionella bekräftelse. <br>Avvisade = mottagna eller skickas en negativ funktionella bekräftelse. <br>Väntande = förväntas en funktionell ack men inte tagits emot. <br>= Fram en funktionell ack men kan inte skicka till partner. <br>Krävs inte = funktionella Ack har inte ställts in. |
| Riktning | EDIFACT Meddelanderiktning |
| Korrelations-ID | Det ID som korrelerar alla utlösare och åtgärder i en logikapp |
| Typen av meddelande | Meddelandetypen EDIFACT |
| ICN | Kontrollen Interchange numret för EDIFACT-meddelande |
| TSCN | Ange kontroll transaktionsnumret för EDIFACT-meddelande |
| tidsstämpel | Den tid när EDIFACT-åtgärden bearbetas meddelandet |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT namnformat för hämtade meddelandefiler

Här följer namnformat för varje mapp för hämtade EDIFACT-meddelanden och filer.

| Mapp eller fil | Format för namn |
| :------------- | :---------- |
| Meddelande-mappen | [avsändaren] \_[mottagaren]\_EDIFACT\_[interchange-kontroll-nummer]\_[globala-kontroll-nummer]\_[-set-kontroll-transaktionsnumret]\_[tidsstämpel] |
| Indata, utdata och om att ställa in bekräftelse filer | **Nyttolasten indata**: [avsändaren]\_[mottagaren]\_EDIFACT\_[interchange-kontroll-nummer]\_input_payload.txt </p>**Utdata nyttolast**: [avsändaren]\_[mottagaren]\_EDIFACT\_[interchange-kontroll-nummer]\_utdata\_payload.txt </p></p>**Indata**: [avsändaren]\_[mottagaren]\_EDIFACT\_[interchange-kontroll-nummer]\_inputs.txt </p></p>**Utdata**: [avsändaren]\_[mottagaren]\_EDIFACT\_[interchange-kontroll-nummer]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Nästa steg

* [Frågan för B2B-meddelanden i Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Spårning av anpassade scheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)