---
title: Använda Azure Monitor-loggar med Azure Logic Apps och Power Automate
description: Lär dig hur du kan använda Azure Logic Apps och Power Automate för att snabbt automatisera repeterbara processer med hjälp av Azure Monitor-kopplingen.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480019"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Azure Monitor Loggar anslutning för Logic Apps och Flow
[Med Azure Logic Apps](/azure/logic-apps/) och [Power Automate](https://ms.flow.microsoft.com) kan du skapa automatiserade arbetsflöden med hundratals åtgärder för en mängd olika tjänster. Med Azure Monitor Logs-kopplingen kan du skapa arbetsflöden som hämtar data från en Log Analytics-arbetsyta eller ett Application Insights-program i Azure Monitor. I den här artikeln beskrivs de åtgärder som ingår i kopplingen och en genomgång för att skapa ett arbetsflöde med hjälp av dessa data.

Du kan till exempel skapa en logikapp för att använda Azure Monitor-loggdata i ett e-postmeddelande från Office 365, skapa ett fel i Azure DevOps eller publicera ett Slack-meddelande.  Du kan utlösa ett arbetsflöde genom ett enkelt schema eller från någon åtgärd i en ansluten tjänst, till exempel när ett e-postmeddelande eller en tweet tas emot. 

## <a name="actions"></a>Åtgärder
I följande tabell beskrivs de åtgärder som ingår i Azure Monitor Logs-kopplingen. Båda kan du köra en loggfråga mot en Log Analytics arbetsyta eller Application Insights-program. Skillnaden är i hur data returneras.

> [!NOTE]
> Azure Monitor Logs-kopplingen ersätter [Azure Log Analytics-anslutningen](https://docs.microsoft.com/connectors/azureloganalytics/) och [Azure Application Insights-kopplingen](https://docs.microsoft.com/connectors/applicationinsights/). Den här kopplingen innehåller samma funktioner som de andra och är den metod som föredras för att köra en fråga mot en Log Analytics-arbetsyta eller ett programinsiktsprogram.


| Åtgärd | Beskrivning |
|:---|:---|
| [Kör fråge- och listresultat](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Returnerar varje rad som sitt eget objekt. Använd den här åtgärden när du vill arbeta med varje rad separat i resten av arbetsflödet. Åtgärden följs vanligtvis av en [för varje aktivitet](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Kör fråge- och visualisera resultat](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Returnerar alla rader i resultatuppsättningen som ett enda formaterat objekt. Använd den här åtgärden när du vill använda resultatuppsättningen tillsammans i resten av arbetsflödet, till exempel skicka resultaten i ett e-postmeddelande.  |

## <a name="walkthroughs"></a>Genomgångar
Följande självstudier illustrerar användningen av Azure Monitor-kopplingar i Azure Logic Apps. Du kan utföra samma exempel med Power Automate, den enda skillnaden är hur du skapar det ursprungliga arbetsflödet och kör det när det är klart. Konfigurationen av arbetsflödet och åtgärderna är densamma mellan båda. Se [Skapa ett flöde från en mall i Power Automate](https://docs.microsoft.com/power-automate/get-started-logic-template) för att komma igång.


### <a name="create-a-logic-app"></a>Skapa en logikapp

Gå till **Logic Apps** i Azure-portalen och klicka på **Lägg till**. Välj en **prenumeration,** **resursgrupp**och **region för** att lagra den nya logikappen och ge den ett unikt namn. Du kan aktivera inställningen **Logganalys** för att samla in information om körningsdata och händelser enligt beskrivningen i [Konfigurera Azure Monitor-loggar och samla in diagnostikdata för Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Den här inställningen krävs inte för att använda Azure Monitor Logs-kopplingen.

![Skapa en logikapp](media/logicapp-flow-connector/create-logic-app.png)


Klicka på **Granska + skapa** och sedan **skapa**. När distributionen är klar klickar du på **Gå till resurs** för att öppna Logic Apps **Designer**.

### <a name="create-a-trigger-for-the-logic-app"></a>Skapa en utlösare för logikappen
Under **Börja med en vanlig utlösare**väljer du **Återkommande**. Detta skapar en logikapp som körs automatiskt med jämna mellanrum. I rutan **Frekvens** för åtgärden väljer du **Dag** och i rutan **Intervall** anger du **1** för att köra arbetsflödet en gång per dag.

![Åtgärden Återkommande](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Genomgång: Visualiserade resultat för e-post
Följande självstudiekurs visar hur du skapar en logikapp som skickar resultaten av en Azure Monitor-loggfråga via e-post. 

### <a name="add-azure-monitor-logs-action"></a>Åtgärden Lägg till Azure Monitor Logs
Klicka på **+ Nytt steg** om du vill lägga till en åtgärd som körs efter upprepningsåtgärden. Under **Välj en åtgärd**skriver du **azure-övervakare** och väljer sedan **Azure Monitor Logs**.

![Åtgärden Azure Monitor Loggar](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Klicka på **Azure Log Analytics – Kör fråga och visualisera resultat**.

![Kör fråge- och visualisera resultatåtgärd](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Åtgärden Lägg till Azure Monitor Logs

Välj **prenumerations-** och **resursgruppen** för din Log Analytics-arbetsyta. Välj *Log Analytics Workspace* för **resurstypen** och välj sedan arbetsytans namn under **Resursnamn**.

Lägg till följande loggfråga i **frågefönstret.**  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Välj *Ange i fråga* för **tidsintervall och** **HTML-tabell** för **diagramtypen**.
   
![Kör fråge- och visualisera resultatåtgärd](media/logicapp-flow-connector/run-query-visualize-action.png)

E-postmeddelandet skickas av kontot som är kopplat till den aktuella anslutningen. Du kan ange ett annat konto genom att klicka på **Ändra anslutning**.

### <a name="add-email-action"></a>Lägg till e-poståtgärd

Klicka på **+ Nytt steg**och sedan på + Lägg till en **åtgärd**. Skriv **Outlook** under **Välj en åtgärd**och välj sedan Office **365 Outlook**.

![Välj Outlook-anslutning](media/logicapp-flow-connector/select-outlook-connector.png)

Välj **Skicka ett e-postmeddelande (V2)**.

![Valfönstret i Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Klicka var som helst i rutan **Brödtext** om du vill öppna ett **dynamiskt innehållsfönster** med värden från tidigare åtgärder i logikappen. Välj **Visa mer** och sedan **Brödtext** som är resultatet av frågan i åtgärden Logganalys.

![Välj brödtext](media/logicapp-flow-connector/select-body.png)

Ange e-postadressen till en mottagare i fönstret **Till** och ett ämne för e-postmeddelandet i **Ämne**. 

![Åtgärd för e-post](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Spara och testa logikappen
Klicka på **Spara** och **kör** sedan för att utföra en testkörning av logikappen.

![Spara och kör](media/logicapp-flow-connector/save-run.png)


När logikappen är klar kontrollerar du e-postmeddelandet till mottagaren som du har angett.  Du borde ha fått ett mail med en kropp som liknar följande:

![Exempel på e-post](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Nästa steg

- Läs mer om [loggfrågor i Azure Monitor](../log-query/log-query-overview.md).
- Läs mer om [Logic Apps](/azure/logic-apps/)
- Läs mer om [Microsoft Flow](https://ms.flow.microsoft.com).

