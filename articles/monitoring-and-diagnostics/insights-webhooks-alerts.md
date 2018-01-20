---
title: "Konfigurera webhooks på Azure mått aviseringar | Microsoft Docs"
description: "Ändra Azure aviseringar till andra Azure-system."
author: johnkemnetz
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: johnkem
ms.openlocfilehash: 06ec1263046f7878871de628b6a0ac25682b2f83
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Konfigurera en webhook på en Azure mått avisering
Webhooks kan du dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade åtgärder. Du kan använda en webhook på en avisering och dirigerar den till tjänster som skicka SMS, logga programfel, meddela ett team via chatt/meddelandetjänster eller göra en mängd olika andra åtgärder. Den här artikeln beskriver hur du ställer in en webhook på en Azure mått avisering och hur nyttolasten för HTTP POST till en webhook ser ut. Mer information om installation och schemat för en Azure-aktivitetsloggen varning (varning på händelser) [se den här sidan i stället](insights-auditlog-to-webhook-email.md).

Azure-aviseringar HTTP POST avisering innehållet i JSON-format, schemat som definieras nedan, till en webhook URI som du anger när du skapar en avisering. Den här URI måste vara en giltig HTTP eller HTTPS-slutpunkt. Azure skickar en post per begäran när en avisering aktiveras.

## <a name="configuring-webhooks-via-the-portal"></a>Hur du konfigurerar webhooks via portalen
Du kan lägga till eller uppdatera webhook URI på skärmen skapa/uppdatera aviseringar i den [portal](https://portal.azure.com/).

![Lägg till en varningsregel](./media/insights-webhooks-alerts/Alertwebhook.png)

Du kan också konfigurera en avisering för att skicka till en webhook URI med hjälp av den [Azure PowerShell-Cmdlets](insights-powershell-samples.md#create-metric-alerts), [plattformsoberoende CLI](insights-cli-samples.md#work-with-alerts), eller [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autentisera webhooken
Webhooken kan autentisera med hjälp av tokenbaserad auktorisering. Webhooken URI sparas med ett token ID, t.ex. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Nyttolasten i schemat
POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla mått-baserade aviseringar.

```JSON
{
    "WebhookName": "Alert1515515157799",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                "metricName": "Requests",
                "metricUnit": "Count",
                "metricValue": "10",
                "threshold": "10",
                "windowSize": "15",
                "timeAggregation": "Average",
                "operator": "GreaterThanOrEqual"
            },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
        },
        "properties": {
            "key1": "value1",
            "key2": "value2"
        }
    }
}
```


| Fält | Obligatorisk | Fast uppsättning värden | Anteckningar |
|:--- |:--- |:--- |:--- |
| status |Y |”Aktiverad”, ”löst” |Status för aviseringen som är baserad på villkor som du har angett. |
| Kontexten |Y | |Kontexten. |
| tidsstämpel |Y | |Den tid då aviseringen utlöstes. |
| id |Y | |Varje regel för varning har ett unikt id. |
| namn |Y | |Aviseringsnamn. |
| description |Y | |Beskrivning av aviseringen. |
| conditionType |Y |”Mått”, ”Event” |Två typer av aviseringar stöds. En baserat på ett mått villkor och den andra baserat på en händelse i aktivitetsloggen. Använd det här värdet för att kontrollera om aviseringen är baserad på mått eller händelse. |
| tillstånd |Y | |Att söka efter specifika fält baserat på conditionType. |
| metricName |för mått aviseringar | |Namnet på måttet som definierar vad regeln övervakar. |
| metricUnit |för mått aviseringar |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds" |Den enhet som tillåts i måttet. [Tillåtna värden i den här listan](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |för mått aviseringar | |Det faktiska värdet för det mått som orsakade aviseringen. |
| Tröskelvärde |för mått aviseringar | |Tröskelvärdet som aviseringen har aktiverats. |
| Fönsterstorlek |för mått aviseringar | |Tidsperioden som används för att övervaka avisering baserat på tröskelvärdet. Måste vara mellan 5 minuter och 1 dag. Varaktighet i ISO 8601-format. |
| timeAggregation |för mått aviseringar |”Medel”, ”senaste”, ”högsta”, ”Minimum” och ”None”, ”totalt” |Hur ska de data som samlas in kombineras med tiden. Standardvärdet är medelvärde. [Tillåtna värden i den här listan](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| Operatorn |för mått aviseringar | |Operatorn som används för att jämföra aktuella mått data till angiven tröskel. |
| subscriptionId |Y | |Azure prenumerations-ID. |
| resourceGroupName |Y | |Namnet på resursgruppen för resursen påverkas. |
| resourceName |Y | |Resursnamn för resursen påverkas. |
| resourceType |Y | |Resurstypen för resursen påverkas. |
| resourceId |Y | |Resurs-ID för resursen påverkas. |
| resourceRegion |Y | |Region eller platsen för resursen påverkas. |
| portalLink |Y | |Direktlänk till portalen resurs sammanfattningssidan. |
| properties |N |Valfri |En uppsättning `<Key, Value>` par (d.v.s. `Dictionary<String, String>`) som innehåller information om händelsen. För egenskapsfältet är valfritt. Användare kan ett anpassat användargränssnitt eller logik app-baserat arbetsflöde ange nyckel/värden som kan skickas via nyttolasten. Alternativa sätt att skicka anpassade egenskaper till webhooken är webhook uri sig själv (som frågeparametrar) |

> [!NOTE]
> Egenskapsfältet kan endast anges med hjälp av den [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure-aviseringar och webhooks i videon [integrera Azure aviseringar med PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Köra Azure Automation-skript (Runbooks) på Azure-aviseringar](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Använd Logikapp för att skicka ett SMS via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [Använda Logikapp för att skicka en Slack-meddelande från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [Använd Logikapp för att skicka ett meddelande till en Azure-kö i en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
