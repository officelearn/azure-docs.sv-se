---
title: Övervaka B2B-meddelanden med hjälp av Azure Monitor
description: Felsöka AS2-, X12- och EDIFACT-meddelanden genom att konfigurera Azure Monitor-loggar och samla in diagnostikdata för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907986"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Konfigurera Azure Monitor-loggar och samla in diagnostikdata för B2B-meddelanden i Azure Logic Apps

När du har konfigurerat B2B-kommunikation mellan handelspartner i ditt integrationskonto kan dessa partner utbyta meddelanden med hjälp av protokoll som AS2, X12 och EDIFACT. Om du vill kontrollera att den här kommunikationen fungerar som förväntat kan du konfigurera [Azure Monitor-loggar](../azure-monitor/platform/data-platform-logs.md) för ditt integrationskonto. [Azure Monitor](../azure-monitor/overview.md) hjälper dig att övervaka dina molnmiljöer och lokala miljöer så att du lättare kan underhålla deras tillgänglighet och prestanda. Genom att använda Azure Monitor-loggar kan du registrera och lagra data om körningsdata och händelser, till exempel utlösarhändelser, köra händelser och åtgärdshändelser på en [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md). För meddelanden samlar loggning också in information som:

* Antal meddelanden och status
* Status för bekräftelser
* Korrelationer mellan meddelanden och bekräftelser
* Detaljerade felbeskrivningar för fel

Med Azure Monitor kan du skapa [loggfrågor](../azure-monitor/log-query/log-query-overview.md) som hjälper dig att hitta och granska den här informationen. Du kan också [använda dessa diagnostikdata med andra Azure-tjänster](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), till exempel Azure Storage och Azure Event Hubs.

Installera [Logic Apps B2B-lösningen](#install-b2b-solution) i Azure-portalen om du vill konfigurera loggning för ditt integrationskonto. Den här lösningen innehåller aggregerad information för B2B-meddelandehändelser. Sedan, för att aktivera loggning och skapa frågor för den här informationen, ställa in [Azure Monitor loggar](#set-up-resource-logs).

Den här artikeln visar hur du aktiverar Azure Monitor-loggning för ditt integrationskonto.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

* En Log Analytics-arbetsyta. Om du inte har en Log Analytics-arbetsyta kan du läsa om hur du [skapar en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md).

* En logikapp som konfigureras med Azure Monitor-loggning och skickar den informationen till en Log Analytics-arbetsyta. Lär dig hur du [konfigurerar Azure Monitor-loggar för logikappen](../logic-apps/monitor-logic-apps.md).

* Ett integrationskonto som är länkat till logikappen. Läs om hur du [länkar ditt integrationskonto till logikappen](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Installera Logic Apps B2B-lösning

Innan Azure Monitor-loggar kan spåra B2B-meddelandena för logikappen lägger du till **Logic Apps B2B-lösningen** på din Log Analytics-arbetsyta.

1. I sökrutan [för Azure-portalen](https://portal.azure.com)anger du `log analytics workspaces`och väljer sedan Log **Analytics-arbetsytor**.

   ![Välj "Log Analytics-arbetsytor"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Välj arbetsyta under **Log Analytics-arbetsytor.**

   ![Välj arbetsytan Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Välj **Visa lösningar**under **Kom igång med Logganalys** > **Konfigurera övervakningslösningar**i fönstret Översikt .

   ![I fönstret Översikt väljer du "Visa lösningar"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Välj **Lägg till**i fönstret Översikt .

   ![Lägg till ny lösning i översiktsfönstret](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. När **Marketplace** har öppnats anger `logic apps b2b`du i sökrutan och väljer **Logic Apps B2B**.

   ![På Marketplace väljer du "Logic Apps Management"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. Välj **Skapa**i fönstret lösningsbeskrivning .

   ![Välj "Skapa" om du vill lägga till "Logic Apps B2B"-lösning](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Granska och bekräfta log analytics-arbetsytan där du vill installera lösningen och välj **Skapa** igen.

   ![Välj "Skapa" för "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   När Azure har distribuerat lösningen till Azure-resursgruppen som innehåller din Log Analytics-arbetsyta visas lösningen i arbetsytans sammanfattningsfönster. När B2B-meddelanden bearbetas uppdateras meddelandeantalet i det här fönstret.

   ![Sammanfattningsfönster för arbetsyta](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurera Azure Monitor-loggar

Du kan aktivera Azure Monitor-loggning direkt från ditt integrationskonto.

1. Leta reda på och välj ditt integrationskonto i [Azure-portalen.](https://portal.azure.com)

   ![Hitta och välj ditt integrationskonto](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Välj Diagnostikinställningar under **Övervakning**på integrationskontots **meny.** Välj **Lägg till diagnostikinställning**.

   ![Under "Övervakning" väljer du "Diagnostikinställningar"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Så här skapar du inställningen:

   1. Ange ett namn för inställningen.

   1. Välj **Skicka till logganalys**.

   1. För **prenumeration**väljer du den Azure-prenumeration som är associerad med din Log Analytics-arbetsyta.

   1. För **Logganalysarbetsyta**väljer du den arbetsyta som du vill använda.

   1. Under **loggen**väljer du kategorin **IntegrationAccountTrackingEvents,** som anger den händelsekategori som du vill spela in.

   1. När du är klar väljer du **Spara**.

   Ett exempel: 

   ![Konfigurera Azure Monitor-loggar för att samla in diagnostikdata](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Visa meddelandestatus

När logikappen har körts kan du visa status och data om dessa meddelanden på logganalysarbetsytan.

1. Leta reda på och öppna arbetsytan Log Analytics i sökrutan för [Azure Portal.](https://portal.azure.com)

1. På arbetsytans meny väljer du**Logic Apps B2B**för **arbetsytan.** > 

   ![Sammanfattningsfönster för arbetsyta](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Om panelen Logic Apps B2B inte omedelbart visar resultat efter en körning kan du prova att välja **Uppdatera** eller vänta en kort stund innan du försöker igen.

   Som standard visar panelen **Logic Apps B2B** data baserat på en enda dag. Om du vill ändra dataomfånget till ett annat intervall markerar du scopekontrollen högst upp på sidan:

   ![Ändra intervall](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. När instrumentpanelen för meddelandestatus har visats kan du visa mer information för en viss meddelandetyp, som visar data baserat på en enda dag. Välj panelen för **AS2,** **X12**eller **EDIFACT**.

   ![Visa status för meddelanden](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   En lista med meddelanden visas för den valda panelen. Så här kan det till exempel se ut i en AS2-meddelandelista:

   ![Statusar och information för AS2-meddelanden](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Mer information om egenskaperna för varje meddelandetyp finns i följande beskrivningar av meddelandeegenskaper:

   * [EGENSKAPER FÖR AS2-meddelande](#as2-message-properties)
   * [Egenskaper för X12-meddelanden](#x12-message-properties)
   * [Egenskaper för EDIFACT-meddelanden](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Egenskapsbeskrivningar och namnformat för AS2-, X12- och EDIFACT-meddelanden

För varje meddelandetyp följer här egenskapsbeskrivningarna och namnformaten för nedladdade meddelandefiler.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Beskrivningar av AS2-meddelandeegenskap

Här är egenskapsbeskrivningarna för varje AS2-meddelande.

| Egenskap | Beskrivning |
|----------|-------------|
| **Avsändare** | Gästpartnern som anges i **Mottagningsinställningar**eller den värdpartner som anges i **Skicka inställningar** för ett AS2-avtal |
| **Mottagare** | Värdpartnern som anges i **Mottagningsinställningar**eller gästpartnern som anges i **Skicka inställningar** för ett AS2-avtal |
| **Logikapp** | Logikappen där AS2-åtgärderna ställs in |
| **Status** | Status för AS2-meddelandet <br>Framgång = Tog emot eller skickade ett giltigt AS2-meddelande. Inget MDN har konfigurerats. <br>Framgång = Tog emot eller skickade ett giltigt AS2-meddelande. MDN har konfigurerats och tagits emot eller så skickas MDN. <br>Misslyckades = Tog emot ett ogiltigt AS2-meddelande. Inget MDN har konfigurerats. <br>Väntande = Tog emot eller skickade ett giltigt AS2-meddelande. MDN har konfigurerats och MDN förväntas. |
| **ACK (se)** | Mdn-meddelandestatus <br>Accepterad = Tog emot eller skickade ett positivt MDN. <br>Väntande = Väntar på att ta emot eller skicka ett MDN. <br>Avvisad = Tog emot eller skickade ett negativt MDN. <br>Krävs inte = MDN har inte ställts in i avtalet. |
| **Riktning** | AS2-meddelanderiktningen |
| **Spårnings-ID** | ID:t som korrelerar alla utlösare och åtgärder i en logikapp |
| **Meddelande-ID** | AS2-meddelande-ID:et från AS2-meddelanderubrikerna |
| **Tidsstämpel** | Den tidpunkt då AS2-åtgärden bearbetade meddelandet |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beskrivningar av X12-meddelandeegenskap

Här är egenskapsbeskrivningarna för varje X12-meddelande.

| Egenskap | Beskrivning |
|----------|-------------|
| **Avsändare** | Gästpartnern som anges i **Mottagningsinställningar**eller den värdpartner som anges i **Skicka inställningar** för ett X12-avtal |
| **Mottagare** | Värdpartnern som anges i **Mottagningsinställningar**eller gästpartnern som anges i **Skicka inställningar** för ett X12-avtal |
| **Logikapp** | Logikappen där X12-åtgärderna ställs in |
| **Status** | Status för X12-meddelandet <br>Framgång = Tog emot eller skickade ett giltigt X12-meddelande. Ingen funktionell ack har ställts in. <br>Framgång = Tog emot eller skickade ett giltigt X12-meddelande. Funktionell ack ställs in och tas emot, eller så skickas en funktionell ack. <br>Misslyckades = Tog emot eller skickade ett ogiltigt X12-meddelande. <br>Väntande = Tog emot eller skickade ett giltigt X12-meddelande. Funktionell ack är inställd och en funktionell ack förväntas. |
| **ACK (se)** | Funktionell Ack (997) status <br>Accepterad = Tog emot eller skickade en positiv funktionell ack. <br>Avvisad = Tog emot eller skickade ett negativt funktionellt ack. <br>Väntande = Väntar på en funktionell ack men inte mottagen. <br>Väntande = Genererade en funktionell ack men kan inte skicka till partner. <br>Krävs inte = Funktionell ack har inte ställts in. |
| **Riktning** | X12-meddelanderiktningen |
| **Spårnings-ID** | ID:t som korrelerar alla utlösare och åtgärder i en logikapp |
| **Msg Typ** | Meddelandetypen EDI X12 |
| **Icn** | Utväxlingskontrollnumret för X12-meddelandet |
| **TSCN (TSCN)** | Kontrollnumret för transaktionsuppsättningen för X12-meddelandet |
| **Tidsstämpel** | Den tidpunkt då X12-åtgärden bearbetade meddelandet |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT-meddelandeegenskapsbeskrivningar

Här är egenskapsbeskrivningarna för varje EDIFACT-meddelande.

| Egenskap | Beskrivning |
|----------|-------------|
| **Avsändare** | Gästpartnern som anges i **Mottagningsinställningar**eller den värdpartner som anges i **Skicka inställningar** för ett EDIFACT-avtal |
| **Mottagare** | Värdpartnern som anges i **Mottagningsinställningar**eller gästpartnern som anges i **Skicka inställningar** för ett EDIFACT-avtal |
| **Logikapp** | Logikappen där EDIFACT-åtgärderna ställs in |
| **Status** | Status för EDIFACT-meddelandet <br>Framgång = Tog emot eller skickade ett giltigt EDIFACT-meddelande. Ingen funktionell ack har ställts in. <br>Framgång = Tog emot eller skickade ett giltigt EDIFACT-meddelande. Funktionell ack ställs in och tas emot, eller så skickas en funktionell ack. <br>Misslyckades = Tog emot eller skickade ett ogiltigt EDIFACT-meddelande <br>Väntande = Tog emot eller skickade ett giltigt EDIFACT-meddelande. Funktionell ack är inställd och en funktionell ack förväntas. |
| **ACK (se)** | Funktionell Ack-status (CONTRL) <br>Accepterad = Tog emot eller skickade en positiv funktionell ack. <br>Avvisad = Tog emot eller skickade ett negativt funktionellt ack. <br>Väntande = Väntar på en funktionell ack men inte mottagen. <br>Väntande = Genererade en funktionell ack men kan inte skicka till partner. <br>Krävs inte = Funktionell Ack har inte ställts in. |
| **Riktning** | EDIFACT-meddelanderiktningen |
| **Spårnings-ID** | ID:t som korrelerar alla utlösare och åtgärder i en logikapp |
| **Msg Typ** | Meddelandetypen EDIFACT |
| **Icn** | Utväxlingskontrollnumret för EDIFACT-meddelandet |
| **TSCN (TSCN)** | Kontrollnumret för transaktionsuppsättningen för EDIFACT-meddelandet |
| **Tidsstämpel** | Den tidpunkt då EDIFACT-åtgärden bearbetade meddelandet |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Nästa steg

* [Skapa frågor om övervakning och spårning](../logic-apps/create-monitoring-tracking-queries.md)