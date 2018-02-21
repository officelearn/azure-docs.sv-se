---
title: "Konfigurera webhooks på Azure mått aviseringar | Microsoft Docs"
description: "Lär dig mer om att ändra Azure aviseringar till andra system än Azure."
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
ms.openlocfilehash: 049803e7701c68559103d9b1fa5dfacf820d0548
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Konfigurera en webhook på en Azure mått avisering
Du kan använda webhooks för att dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade åtgärder. Du kan använda en webhook på en avisering och dirigerar den till tjänster som skickar SMS-meddelanden att logga buggar, för att meddela ett team via chatt eller -tjänster eller för olika åtgärder. 

Den här artikeln beskriver hur du ställer in en webhook på en Azure mått avisering. Där visas också hur nyttolasten för HTTP POST till en webhook ser ut. Information om installation och schemat för en Azure aktivitet finns i loggen avisering (varning på händelser) [anropa en webhook för ett Azure log varning](insights-auditlog-to-webhook-email.md).

Azure-aviseringar använder HTTP POST för att skicka aviseringen innehållet i JSON-format till en webhook URI som du anger när du skapar en avisering. Schemat har definierats i den här artikeln. URI: N måste vara en giltig HTTP eller HTTPS-slutpunkt. Azure skickar en post per begäran när en avisering aktiveras.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurera webhooks via Azure portal
Lägga till eller uppdatera webhook URI, i den [Azure-portalen](https://portal.azure.com/), gå till **skapa/uppdatera aviseringar**.

![Lägg till en varningsregel ruta](./media/insights-webhooks-alerts/Alertwebhook.png)

Du kan också konfigurera en avisering för att skicka till en webhook URI med hjälp av [Azure PowerShell-cmdlets](insights-powershell-samples.md#create-metric-alerts), [plattformsoberoende CLI](insights-cli-samples.md#work-with-alerts), eller [Azure övervakaren REST API: er](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan autentisera med hjälp av tokenbaserad auktorisering. Webhooken URI sparas med ett token-ID. Exempel: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Nyttolasten i schemat
POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla mått-baserade aviseringar:

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
| status |Y |Aktiverat, löst |Status för aviseringen baserat på villkor du anger. |
| Kontexten |Y | |Kontexten. |
| tidsstämpel |Y | |Den tid då aviseringen utlöstes. |
| id |Y | |Varje regel för varning har ett unikt ID. |
| namn |Y | |Aviseringsnamn. |
| description |Y | |En beskrivning av aviseringen. |
| conditionType |Y |Mått, händelse |Två typer av aviseringar som stöds: mått och händelser. Mått aviseringar baseras på ett mått villkor. Aviseringar är baserade på en händelse i aktivitetsloggen. Använd det här värdet för att kontrollera om aviseringen är baserat på ett mått eller en händelse. |
| tillstånd |Y | |Specifika fält att söka baserat på de **conditionType** värde. |
| metricName |För mått aviseringar | |Namnet på måttet som definierar vad regeln övervakar. |
| metricUnit |För mått aviseringar |Byte, BytesPerSecond, Count, CountPerSecond, procent, sekunder |Den enhet som tillåts i måttet. Se [tillåtna värden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |För mått aviseringar | |Det faktiska värdet för det mått som orsakade aviseringen. |
| Tröskelvärde |För mått aviseringar | |Tröskelvärdet som aviseringen har aktiverats. |
| Fönsterstorlek |För mått aviseringar | |Tidsperioden som används för att övervaka avisering baserat på tröskelvärdet. Värdet måste vara mellan 5 minuter och 1 dag. Värdet måste vara i formatet för ISO 8601-varaktighet. |
| timeAggregation |För mått aviseringar |Genomsnittlig, senaste, högsta, Minimum, None, totalt |Hur ska de data som samlas in kombineras med tiden. Standardvärdet är medelvärde. Se [tillåtna värden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| Operatorn |För mått aviseringar | |Den operator som används för att jämföra den aktuella mått data med angiven tröskel. |
| subscriptionId |Y | |Azure prenumerations-ID. |
| resourceGroupName |Y | |Namnet på resursgruppen för resursen som påverkas. |
| resourceName |Y | |Resursnamn för resursen som påverkas. |
| resourceType |Y | |Resurstypen för resursen som påverkas. |
| resourceId |Y | |Resurs-ID för resursen som påverkas. |
| resourceRegion |Y | |Region eller platsen för resursen som påverkas. |
| portalLink |Y | |En direktlänk till portalen resurs sammanfattningssidan. |
| properties |N |Valfri |En uppsättning nyckel/värde-par som innehåller information om händelsen. Till exempel `Dictionary<String, String>`. För egenskapsfältet är valfritt. I ett anpassat gränssnitt eller logik app-baserat arbetsflöde kan ange användarna nyckel/värde-par som kan skickas via nyttolasten. Ett annat sätt att skicka anpassade egenskaper tillbaka till webhooken är via webhook URI (som frågeparametrar). |

> [!NOTE]
> Du kan ange den **egenskaper** fältet genom att använda [Azure övervakaren REST API: er](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure-aviseringar och webhooks i videon [integrera Azure aviseringar med PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080).
* Lär dig hur du [köra Azure Automation-skript (runbooks) på Azure-aviseringar](http://go.microsoft.com/fwlink/?LinkId=627081).
* Lär dig hur du [använder en logikapp för att skicka ett SMS-meddelande via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Lär dig hur du [använder en logikapp för att skicka meddelandet Slack från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Lär dig hur du [använder en logikapp för att skicka ett meddelande till en Azure-kö från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).
