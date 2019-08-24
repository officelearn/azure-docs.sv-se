---
title: Spåra B2B-meddelanden med Azure Monitor loggar – Azure Logic Apps | Microsoft Docs
description: Spåra B2B-kommunikation för integrations konton och Azure Logic Apps med Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 33c4efb2b783b5071513f069beac9cdf73c373a8
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997849"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Spåra B2B-meddelanden med Azure Monitor-loggar

När du har konfigurerat B2B-kommunikation mellan handels partner i ditt integrations konto kan dessa partners utbyta meddelanden med protokoll som AS2, X12 och EDIFACT. Om du vill kontrol lera att dessa meddelanden bearbetas korrekt kan du spåra dessa meddelanden med [Azure Monitor loggar](../log-analytics/log-analytics-overview.md). Du kan till exempel använda dessa webbaserade spårnings funktioner för att spåra meddelanden:

* Antal meddelanden och status
* Bekräftelse status
* Korrelera meddelanden med bekräftelser
* Detaljerade fel beskrivningar för fel
* Sök funktioner

> [!NOTE]
> Den här sidan beskriver tidigare steg för hur du utför dessa uppgifter med Microsoft Operations Management Suite (OMS), som tas [ur bruk i januari 2019](../azure-monitor/platform/oms-portal-transition.md), ersätter dessa steg med Azure Log Analytics i stället. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

* En Logic-app som är konfigurerad med diagnostikloggning. Lär dig [hur du skapar en logisk app](quickstart-create-first-logic-app-workflow.md) och [hur du ställer in loggning för den Logic app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Ett integrations konto som har kon figurer ATS med övervakning och loggning. Lär dig [hur du skapar ett integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och [hur du konfigurerar övervakning och loggning för det kontot](../logic-apps/logic-apps-monitor-b2b-message.md).

* [Publicera diagnostikdata till Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)om du inte redan gjort det.

* När du uppfyller de tidigare kraven måste du också ha en Log Analytics arbets yta som du använder för att spåra B2B-kommunikation via Log Analytics. Om du inte har en Log Analytics arbets yta, lär du dig [hur du skapar en Log Analytics arbets yta](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Installera Logic Apps B2B lösning

Innan du kan ha Azure Monitor loggar som spårar B2B-meddelanden för din Logic app lägger du till **Logic Apps B2B** -lösningen i Azure Monitor loggar. Lär dig mer om att [lägga till lösningar i Azure Monitor-loggar](../azure-monitor/learn/quick-create-workspace.md).

1. I [Azure-portalen](https://portal.azure.com), väljer du **Alla tjänster**. Sök efter "Log Analytics" i sökrutan och välj **Log Analytics**.

   ![Välj Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Under **Log Analytics**, leta upp och välj arbets ytan Log Analytics. 

   ![Välj Log Analytics arbets yta](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Under **Kom igång med Log Analytics** > **Konfigurera övervaknings lösningar**väljer du **Visa lösningar**.

   ![Välj "Visa lösningar"](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. På sidan Översikt väljer du **Lägg till**, vilket öppnar listan med **hanterings lösningar** . Välj **Logic Apps B2B**i listan. 

   ![Välj Logic Apps B2B lösning](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Om du inte hittar lösningen går du till slutet av listan och väljer **Läs in mer** tills lösningen visas.

1. Välj **skapa**, bekräfta Log Analytics arbets ytan där du vill installera lösningen och välj sedan **skapa** igen.   

   ![Välj "skapa" för Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Om du inte vill använda en befintlig arbets yta kan du också skapa en ny arbets yta för tillfället.

1. När du är klar går du tillbaka till sidan **Översikt** för din arbets yta. 

   Logic Apps B2B lösningen visas nu på översikts sidan. 
   När B2B-meddelanden bearbetas uppdateras antalet meddelanden på den här sidan.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Visa information om B2B-meddelanden

När B2B-meddelanden har bearbetats kan du se status och information för dessa meddelanden på panelen **Logic Apps B2B** .

1. Gå till din Logic Analytics-arbetsyta och öppna översikts sidan. Välj **Logic Apps B2B**.

   ![Antal uppdaterade meddelanden](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Som standard visar panelen **Logic Apps B2B** data baserat på en enda dag. Om du vill ändra data omfånget till ett annat intervall väljer du kontrollen omfång överst på sidan:
   > 
   > ![Ändra intervall](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. När instrument panelen för meddelande status visas kan du Visa mer information om en viss meddelande typ, som visar data baserat på en enda dag. Välj panelen för **AS2**, **X12**eller **EDIFACT**.

   ![Visa meddelande status](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   En lista med meddelanden visas för din valda panel. 
   Mer information om egenskaperna för varje meddelande typ finns i följande beskrivningar av meddelande egenskaper:

   * [Egenskaper för AS2-meddelande](#as2-message-properties)
   * [Egenskaper för X12-meddelande](#x12-message-properties)
   * [Egenskaper för EDIFACT-meddelande](#EDIFACT-message-properties)

   Till exempel kan en lista över AS2-meddelanden se ut så här:

   ![Visa AS2-meddelanden](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Om du vill visa eller exportera indata och utdata för vissa meddelanden väljer du dessa meddelanden och väljer **Hämta**. När du uppmanas till det sparar du zip-filen på den lokala datorn och extraherar sedan filen. 

   Den extraherade mappen innehåller en mapp för varje valt meddelande. 
   Om du ställer in bekräftelser innehåller mappen Message även filer med bekräftelse information. 
   Varje mapp för meddelanden har minst följande filer: 
   
   * Läsbara filer med information om nytto last och utmatnings nytto Last
   * Kodade filer med indata och utdata

   För varje meddelande typ kan du hitta mapp-och fil namns formaten här:

   * [Mapp-och fil namns format för AS2](#as2-folder-file-names)
   * [Mapp-och fil namns format för X12](#x12-folder-file-names)
   * [Mapp-och fil namns format för EDIFACT](#edifact-folder-file-names)

   ![Hämta meddelandefiler](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Om du vill visa alla åtgärder som har samma körnings-ID väljer du ett meddelande från meddelande listan på sidan **loggs ökning** .

   Du kan sortera dessa åtgärder efter kolumn eller söka efter vissa resultat.

   ![Åtgärder med samma körnings-ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Om du vill söka resultat med fördefinierade frågor väljer du **Favoriter**.

   * Lär dig [hur du skapar frågor genom att lägga till filter](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Eller Lär dig mer om [hur du hittar data med loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md).

   * Om du vill ändra frågan i sökrutan uppdaterar du frågan med de kolumner och värden som du vill använda som filter.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Egenskaps beskrivningar och namn format för AS2-, X12-och EDIFACT-meddelanden

Här är egenskaps beskrivningar och namn format för hämtade meddelandefiler för varje meddelande typ.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Beskrivningar av AS2 meddelande egenskaper

Här är egenskaps beskrivningarna för varje AS2-meddelande.

| Egenskap | Beskrivning |
| --- | --- |
| Avsändaren | Gäst partnern som anges i **ta emot inställningar**eller värd partnern som anges i **Skicka inställningar** för ett AS2-avtal |
| Mottagare | Värd partnern som anges i **ta emot inställningar**eller gäst partnern som anges i **Skicka inställningar** för ett AS2-avtal |
| Logikapp | Den Logic app där AS2-åtgärderna har kon figurer ATS |
| State | Status för AS2-meddelande <br>Lyckades = tar emot eller skickade ett giltigt AS2-meddelande. Ingen MDN har kon figurer ATS. <br>Lyckades = tar emot eller skickade ett giltigt AS2-meddelande. MDN konfigureras och tas emot, eller så skickas MDN. <br>Misslyckades = ett ogiltigt AS2-meddelande togs emot. Ingen MDN har kon figurer ATS. <br>Väntar = tar emot eller skickade ett giltigt AS2-meddelande. MDN har ställts in och MDN förväntas. |
| Följ | Status för MDN-meddelande <br>Accepterad = mottagen eller skickad positiv MDN. <br>Väntar = väntar på att ta emot eller skicka en MDN. <br>Avvisad = ta emot eller skicka ett negativt MDN. <br>Krävs inte = MDN har inte ställts in i avtalet. |
| Direction | AS2 meddelande riktning |
| Korrelations-ID | Det ID som motsvarar alla utlösare och åtgärder i en Logic app |
| Meddelande-ID | AS2 meddelande-ID från AS2-meddelandehuvuden |
| Timestamp | Tiden då AS2-åtgärden bearbetade meddelandet |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>AS2 namn format för hämtade meddelandefiler

Här är namn formaten för varje nedladdad AS2-mapp och filer.

| Mapp eller fil | Namn format |
| :------------- | :---------- |
| Mapp för meddelanden | ingen \_\_[mottagare]\_AS2\_[korrelations-ID] [meddelande-ID] [tidsstämpel] \_ |
| Indata, utdata och om konfiguration, bekräftelse fil | **Indatakälla**: [\_avsändare] [mottagare]\_AS2\_[korrelations\_-ID] input_payload. txt </p>**Utmatnings nytto Last**:\_[Sender]\_[\_mottagare] AS2 [korrelations\_-ID]\_utdata nytto Last. txt </p></p>**Indata**: [Sender\_] [mottagare\_]\_AS2 [korrelations-\_ID] Inputs. txt </p></p>**Utdata**: [Sender]\_[mottagare]\_AS2\_[korrelations-ID]\_utdata. txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beskrivningar av X12 meddelande egenskaper

Här är egenskaps beskrivningarna för varje X12-meddelande.

| Egenskap | Beskrivning |
| --- | --- |
| Avsändaren | Gäst partnern som anges i **ta emot inställningar**eller värd partnern som anges i **Skicka inställningar** för ett X12-avtal |
| Mottagare | Värd partnern som anges i **ta emot inställningar**eller gäst partnern som anges i **Skicka inställningar** för ett X12-avtal |
| Logikapp | Den Logic app där X12-åtgärderna har kon figurer ATS |
| State | Status för X12-meddelande <br>Lyckades = tar emot eller skickade ett giltigt X12-meddelande. Ingen funktions ack har kon figurer ATS. <br>Lyckades = tar emot eller skickade ett giltigt X12-meddelande. Funktions ack har kon figurer ATS och tagits emot, eller en funktionell ack skickas. <br>Misslyckades = mottog eller skickade ett ogiltigt X12-meddelande. <br>Väntar = tar emot eller skickade ett giltigt X12-meddelande. Funktions ack har kon figurer ATS och en funktionell ack förväntas. |
| Följ | Status för funktionell ack (997) <br>Accepterad = mottagen eller skickade en positiv funktionell ack. <br>Nekad = mottagen eller skickad negativ funktionell ack. <br>Väntar = en funktions ack förväntas men tas inte emot. <br>Väntar = genererar en funktionell ack men kan inte skicka till partner. <br>Inte obligatoriskt = funktions ack har inte kon figurer ATS. |
| Direction | X12 meddelande riktning |
| Korrelations-ID | Det ID som motsvarar alla utlösare och åtgärder i en Logic app |
| Typ av meddelande | Meddelande typen EDI-X12 |
| ICN | Utbytes kontroll numret för X12-meddelandet |
| TSCN | Kontroll nummer för transaktions uppsättning för X12-meddelandet |
| Timestamp | Tiden då X12-åtgärden bearbetade meddelandet |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 namn format för hämtade meddelandefiler

Här är namn formaten för varje nedladdad X12-mapp och filer.

| Mapp eller fil | Namn format |
| :------------- | :---------- |
| Mapp för meddelanden | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Indata, utdata och om konfiguration, bekräftelse fil | **Indatakälla**: [\_avsändare] [mottagare]\_X12\_[Interchange-Control-\_Number] input_payload. txt </p>**Utdatatyp för utdata**: [\_Sender] [\_mottagare\_] X12 [Interchange-Control-\_Number\_] utdata nytto Last. txt </p></p>**Indata**: [Sender\_] [mottagare\_]\_X12 [Interchange-Control-Number\_] Inputs. txt </p></p>**Utdata**: [Sender]\_[mottagare]\_X12\_[Interchange-Control-Number]\_outputs. txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Beskrivningar av EDIFACT meddelande egenskaper

Här är egenskaps beskrivningarna för varje EDIFACT-meddelande.

| Egenskap | Beskrivning |
| --- | --- |
| Avsändaren | Gäst partnern som anges i **ta emot inställningar**eller värd partnern som anges i **Skicka inställningar** för ett EDIFACT-avtal |
| Mottagare | Värd partnern som anges i **ta emot inställningar**eller gäst partnern som anges i **Skicka inställningar** för ett EDIFACT-avtal |
| Logikapp | Den Logic app där EDIFACT-åtgärderna har kon figurer ATS |
| State | Status för EDIFACT-meddelande <br>Lyckades = tar emot eller skickade ett giltigt EDIFACT-meddelande. Ingen funktions ack har kon figurer ATS. <br>Lyckades = tar emot eller skickade ett giltigt EDIFACT-meddelande. Funktions ack har kon figurer ATS och tagits emot, eller en funktionell ack skickas. <br>Misslyckades = mottaget eller skickat ett ogiltigt EDIFACT-meddelande <br>Väntar = tar emot eller skickade ett giltigt EDIFACT-meddelande. Funktions ack har kon figurer ATS och en funktionell ack förväntas. |
| Följ | Status för funktionell ack (CONTRL) <br>Accepterad = mottagen eller skickade en positiv funktionell ack. <br>Nekad = mottagen eller skickad negativ funktionell ack. <br>Väntar = en funktions ack förväntas men tas inte emot. <br>Väntar = genererar en funktionell ack men kan inte skicka till partner. <br>Inte obligatoriskt = funktions ack har inte kon figurer ATS. |
| Direction | EDIFACT meddelande riktning |
| Korrelations-ID | Det ID som motsvarar alla utlösare och åtgärder i en Logic app |
| Typ av meddelande | Meddelande typen EDIFACT |
| ICN | Utbytes kontroll numret för EDIFACT-meddelandet |
| TSCN | Kontroll nummer för transaktions uppsättning för EDIFACT-meddelandet |
| Timestamp | Tiden då EDIFACT-åtgärden bearbetade meddelandet |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT namn format för hämtade meddelandefiler

Här är namn formaten för varje nedladdad EDIFACT-mapp och filer.

| Mapp eller fil | Namn format |
| :------------- | :---------- |
| Mapp för meddelanden | ingen \_\_\_[mottagare]\_EDIFACT\_[Interchange-Control-Number] [global-Control-Number] [Transaction-set-Control-Number] [timestamp] \_ |
| Indata, utdata och om konfiguration, bekräftelse fil | **Indatakälla**: [\_avsändare] [mottagare]\_EDIFACT\_[Interchange-Control-\_Number] input_payload. txt </p>**Utdatatyp för utdata**: [\_Sender] [\_mottagare\_] EDIFACT [Interchange-Control-\_Number\_] utdata nytto Last. txt </p></p>**Indata**: [Sender\_] [mottagare\_]\_EDIFACT [Interchange-Control-Number\_] Inputs. txt </p></p>**Utdata**: [Sender]\_[mottagare]\_EDIFACT\_[Interchange-Control-Number]\_outputs. txt |
|          |             |

## <a name="next-steps"></a>Nästa steg

* [Fråga efter B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade spårnings scheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
