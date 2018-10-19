---
title: Spåra B2B-meddelanden med Azure Log Analytics – Azure Logic Apps | Microsoft Docs
description: Spåra B2B-kommunikation för integrationskonton och Azure Logic Apps med Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 06/19/2018
ms.openlocfilehash: 666c998a781f13ea2a26ccfc0b94aeead0308f5b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405692"
---
# <a name="track-b2b-communication-with-azure-log-analytics"></a>Spåra B2B-kommunikation med Azure Log Analytics

När du har konfigurerat B2B-kommunikation mellan två kör affärsprocesser eller program via ditt integrationskonto dessa entiteter kan utbyta meddelanden med varandra. Kontrollera om meddelandena bearbetas korrekt, kan du spåra AS2, X12, och EDIFACT-meddelanden med [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Du kan till exempel använda dessa webbaserade spårningsfunktioner för spårning av meddelanden:

* Antal meddelanden och status
* Bekräftelser status
* Korrelera meddelanden med bekräftelser
* Detaljerad felbeskrivningar för fel
* Sökfunktioner

## <a name="requirements"></a>Krav

* En logikapp som har konfigurerats med diagnostikloggning. Lär dig [så här skapar du en logikapp](quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den logikappen](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integrationskonto som har konfigurerats med övervakning och loggning. Lär dig [hur du skapar ett integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* Om du inte redan gjort [publicera diagnostiska data till Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> När du har uppfyllt kraven tidigare, bör du ha en arbetsyta i Log Analytics. Du bör använda samma arbetsyta för att spåra dina B2B-kommunikation i Log Analytics. 
>  
> Om du inte har en Log Analytics-arbetsyta kan du läsa [hur du skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="add-the-logic-apps-b2b-solution-to-azure"></a>Lägg till Logic Apps B2B-lösning till Azure

Om du vill att spåra B2B-meddelanden för din logikapp – logganalys, måste du lägga till den **Logic Apps B2B** lösning till Log Analytics. Läs mer om [lägga till lösningar i Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

1. I den [Azure-portalen](https://portal.azure.com), Välj **alla tjänster**. Sök efter ”log analytics” och välj sedan **Log Analytics** som visas här:

   ![Hitta Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. Under **Log Analytics**, hitta och välj din Log Analytics-arbetsyta. 

   ![Välj Log Analytics-arbetsytan](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Under **Management**, Välj **arbetsytan Sammanfattning**.

   ![Välj Log Analytics-portalen](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. När sidan öppnas, Välj **Lägg till** installera Logic Apps B2B-lösning.    
   ![Välj lösningsgalleriet](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

5. Under **hanteringslösningar**, hitta och skapa **Logic Apps B2B** lösning.     
   ![Välj Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   På startsidan på panelen för **Logic Apps B2B-meddelanden** visas nu. 
   Den här panelen uppdaterar meddelandeantalet när dina B2B-meddelanden bearbetas.

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-log-analytics"></a>Spåra meddelandestatus och information i Log Analytics

1. När dina B2B-meddelanden bearbetas kan visa du status och information om dessa meddelanden. På sidan Översikt väljer du den **Logic Apps B2B-meddelanden** panelen.

   ![Antal uppdaterade meddelanden](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Som standard den **Logic Apps B2B-meddelanden** panel visar data baserat på en dag. Välj scope-kontroll högst upp på sidan om du vill ändra vilka data till ett annat intervall:
   > 
   > ![Ändra omfång för data](media/logic-apps-track-b2b-messages-omsportal/server-filter.png)
   >

2. När meddelandet visas statusinstrumentpanelen kan du kan visa mer information om ett visst meddelande-typ, som visar data baserat på en dag. Välj panelen för **AS2**, **X12**, eller **EDIFACT**.

   ![Visa status för meddelandet](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   En lista med meddelanden visas för din valda panel. 
   Mer information om egenskaperna för varje meddelandetyp, finns i dessa beskrivningar för meddelande-egenskapen:

   * [Egenskaper för AS2-meddelande](#as2-message-properties)
   * [X12 meddelande egenskaper](#x12-message-properties)
   * [Egenskaper för EDIFACT-meddelande](#EDIFACT-message-properties)

   Här är exempelvis hur en lista över AS2-meddelande kan se ut:

   ![Visa AS2-meddelanden](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Välj de meddelandena för att visa eller exportera indata och utdata för specifika meddelanden, och välj **hämta**. När du uppmanas, spara ZIP-filen till den lokala datorn och sedan extrahera filen. 

   Den extrahera mappen innehåller en mapp för varje meddelande som är markerade. 
   Om du har konfigurerat bekräftelser även meddelandemappen filer med information om bekräftelse. 
   Varje meddelandemapp har minst de här filerna: 
   
   * Läsbart filer med nyttolasten indata och utdata nyttolast information
   * Kodade filer med indata och utdata

   Du kan hitta mappen och på filformat här för varje meddelandetyp:

   * [AS2-mapp och fil namnformat](#as2-folder-file-names)
   * [X12 mapp- och namnge format](#x12-folder-file-names)
   * [EDIFACT-mapp och fil namnformat](#edifact-folder-file-names)

   ![Ladda ned meddelandefiler](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Visa alla åtgärder som har samma körnings-ID, på den **Loggsökning** väljer du ett meddelande från listan.

   Du kan sortera de här åtgärderna efter kolumnen eller Sök efter specifika resultat.

   ![Åtgärder med samma körnings-ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Sökresultat med hjälp av fördefinierade frågor, Välj **Favoriter**.

   * Lär dig [hur du skapar frågor genom att lägga till filter](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Eller Läs mer om [hitta data med loggsökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md).

   * Uppdatera frågan med kolumner och värden som ska användas som filter om du vill ändra frågan i sökrutan.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Egenskapsbeskrivningar och format för AS2-, X 12 och EDIFACT-meddelanden

För varje meddelandetyp följer Egenskapsbeskrivningar och format för hämtade meddelandefiler.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2-meddelandet Egenskapsbeskrivningar

Här är Egenskapsbeskrivningar för varje AS2-meddelande.

| Egenskap  | Beskrivning |
| --- | --- |
| Avsändare | Gästpartner som anges i **ta emot inställningar**, eller den mottagande partnern som anges i **skicka inställningar** för ett AS2-avtal |
| Mottagare | Värdpartner som anges i **ta emot inställningar**, eller gästpartner som anges i **skicka inställningar** för ett AS2-avtal |
| Logikapp | Logikappen där AS2-åtgärder har ställts in |
| Status | Status för AS2-meddelande <br>Lyckades = mottagna eller skickat ett ogiltigt AS2-meddelande. Inga MDN har ställts in. <br>Lyckades = mottagna eller skickat ett ogiltigt AS2-meddelande. MDN ställs in och tas emot eller MDN skickas. <br>Det gick inte = tagits emot ett ogiltigt AS2-meddelande. Inga MDN har ställts in. <br>Väntande = mottagna eller skickat ett ogiltigt AS2-meddelande. MDN har ställts in och MDN förväntas. |
| Ack | Status för MDN-meddelandet <br>Godkänt = mottagna eller skickas ett positivt MDN. <br>Väntande = väntar på att ta emot eller skicka en MDN. <br>Avvisade = mottagna eller skickas en negativ MDN. <br>Krävs inte = MDN har inte ställts in i avtalet. |
| Riktning | Riktning för AS2-meddelande |
| Korrelations-ID | ID kopplat till alla utlösare och åtgärder i en logikapp |
| Meddelande-ID | AS2-meddelande-ID från rubriker för AS2-meddelande |
| Tidsstämpel | Den tid när AS2-åtgärden bearbetas meddelandet |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>AS2-namnformat för hämtade meddelandefiler

Här följer namnformat för varje mapp för hämtade AS2-meddelanden och filer.

| Mapp eller fil | Format för namn |
| :------------- | :---------- |
| Meddelandemapp | [avsändaren] \_[mottagare]\_AS2\_[Korrelations-ID]\_[meddelande-ID]\_[timestamp] |
| Indata och utdata om konfigurera bekräftelse filer | **Nyttolasten i indata**: [avsändaren]\_[mottagare]\_AS2\_[Korrelations-ID]\_input_payload.txt </p>**Nyttolasten utdata**: [avsändaren]\_[mottagare]\_AS2\_[Korrelations-ID]\_utdata\_payload.txt </p></p>**Indata**: [avsändaren]\_[mottagare]\_AS2\_[Korrelations-ID]\_inputs.txt </p></p>**Utdata**: [avsändaren]\_[mottagare]\_AS2\_[Korrelations-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 meddelande Egenskapsbeskrivningar

Här är Egenskapsbeskrivningar för varje X12 meddelande.

| Egenskap  | Beskrivning |
| --- | --- |
| Avsändare | Gästpartner som anges i **ta emot inställningar**, eller den mottagande partnern som anges i **skicka inställningar** för en X12 avtal |
| Mottagare | Värdpartner som anges i **ta emot inställningar**, eller gästpartner som anges i **skicka inställningar** för en X12 avtal |
| Logikapp | Logikappen där X12 åtgärder har ställts in |
| Status | Den X12 meddelande status <br>Lyckades = mottagna eller skickas en giltig X12 meddelande. Inga funktionella ack har ställts in. <br>Lyckades = mottagna eller skickas en giltig X12 meddelande. Funktionella ack ställs in och tas emot, eller en funktionell ack skickas. <br>Det gick inte = mottagna eller skickade ett ogiltigt X12 meddelande. <br>Väntande = mottagna eller skickas en giltig X12 meddelande. Funktionella ack har konfigurerats och en funktionell ack förväntas. |
| Ack | Funktionella Ack (997)-status <br>Godkänt = mottagna eller skickas en positiv funktionella bekräftelse <br>Avvisade = mottagna eller skickas en negativ funktionella bekräftelse <br>Väntande = förväntas en funktionell ack, men inte togs emot. <br>= Fram en funktionell ack men det går inte att skicka till partner. <br>Krävs inte = funktionell ack har inte ställts in. |
| Riktning | Den X12 meddelande riktning |
| Korrelations-ID | ID kopplat till alla utlösare och åtgärder i en logikapp |
| Meddelandetyp | Meddelandetypen EDI X 12 |
| ICN | Interchange-kontrollnummer för X12 meddelande |
| TSCN | Transaktionen ange Kontrollnumret för X12 meddelande |
| Tidsstämpel | Tid när X12 åtgärden bearbetas meddelandet |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 på Format för hämtade meddelandefiler

Här är de namnformat för var och en hämtade X12 meddelande mappen och filer.

| Mapp eller fil | Format för namn |
| :------------- | :---------- |
| Meddelandemapp | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Indata och utdata om konfigurera bekräftelse filer | **Nyttolasten i indata**: [avsändaren]\_[mottagare]\_X12\_[interchange-kontrollnummer]\_input_payload.txt </p>**Nyttolasten utdata**: [avsändaren]\_[mottagare]\_X12\_[interchange-kontrollnummer]\_utdata\_payload.txt </p></p>**Indata**: [avsändaren]\_[mottagare]\_X12\_[interchange-kontrollnummer]\_inputs.txt </p></p>**Utdata**: [avsändaren]\_[mottagare]\_X12\_[interchange-kontrollnummer]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT-meddelandet Egenskapsbeskrivningar

Här är Egenskapsbeskrivningar för varje EDIFACT-meddelandet.

| Egenskap  | Beskrivning |
| --- | --- |
| Avsändare | Gästpartner som anges i **ta emot inställningar**, eller den mottagande partnern som anges i **skicka inställningar** för EDIFACT-avtal |
| Mottagare | Värdpartner som anges i **ta emot inställningar**, eller gästpartner som anges i **skicka inställningar** för EDIFACT-avtal |
| Logikapp | Logikappen där EDIFACT-åtgärder har ställts in |
| Status | Status för EDIFACT-meddelande <br>Lyckades = mottagna eller skickat ett giltigt EDIFACT-meddelande. Inga funktionella ack har ställts in. <br>Lyckades = mottagna eller skickat ett giltigt EDIFACT-meddelande. Funktionella ack ställs in och tas emot, eller en funktionell ack skickas. <br>Det gick inte = mottagna eller skickade ett ogiltigt EDIFACT-meddelande <br>Väntande = mottagna eller skickat ett giltigt EDIFACT-meddelande. Funktionella ack har konfigurerats och en funktionell ack förväntas. |
| Ack | Funktionella Ack (997)-status <br>Godkänt = mottagna eller skickas en positiv funktionella bekräftelse <br>Avvisade = mottagna eller skickas en negativ funktionella bekräftelse <br>Väntande = förväntas en funktionell ack, men inte togs emot. <br>= Fram en funktionell ack men det går inte att skicka till partner. <br>Krävs inte = funktionella Ack har inte ställts in. |
| Riktning | Riktning för EDIFACT-meddelande |
| Korrelations-ID | ID kopplat till alla utlösare och åtgärder i en logikapp |
| Meddelandetyp | Meddelandetypen EDIFACT |
| ICN | Interchange-kontrollnummer för EDIFACT-meddelande |
| TSCN | Transaktionen ange kontrollnummer för EDIFACT-meddelande |
| Tidsstämpel | Tiden när EDIFACT-åtgärden bearbetas meddelandet |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT namnformat för hämtade meddelandefiler

Här följer namnformat för varje mapp för hämtade EDIFACT-meddelanden och filer.

| Mapp eller fil | Format för namn |
| :------------- | :---------- |
| Meddelandemapp | [avsändaren] \_[mottagare]\_EDIFACT\_[interchange-kontrollnummer]\_[global--kontrollnummer]\_[transaktion –--kontrollnummer]\_[timestamp] |
| Indata och utdata om konfigurera bekräftelse filer | **Nyttolasten i indata**: [avsändaren]\_[mottagare]\_EDIFACT\_[interchange-kontrollnummer]\_input_payload.txt </p>**Nyttolasten utdata**: [avsändaren]\_[mottagare]\_EDIFACT\_[interchange-kontrollnummer]\_utdata\_payload.txt </p></p>**Indata**: [avsändaren]\_[mottagare]\_EDIFACT\_[interchange-kontrollnummer]\_inputs.txt </p></p>**Utdata**: [avsändaren]\_[mottagare]\_EDIFACT\_[interchange-kontrollnummer]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Nästa steg

* [Fråga om B2B-meddelanden i Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårningsscheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
