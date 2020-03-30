---
title: Anropa en webhook med en klassisk måttavisering i Azure Monitor
description: Lär dig hur du omdirigerar Azure-måttaviseringar till andra, icke-Azure-system.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 27510871f9a022cb27c6b03b812ce1d37b47312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248988"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Anropa en webhook med en klassisk måttavisering i Azure Monitor

Du kan använda webhooks för att dirigera ett Azure-aviseringsmeddelande till andra system för efterbearbetning eller anpassade åtgärder. Du kan använda en webhook på en avisering för att dirigera den till tjänster som skickar SMS-meddelanden, för att logga buggar, för att meddela ett team via chatt- eller meddelandetjänster eller för olika andra åtgärder. 

I den här artikeln beskrivs hur du ställer in en webhook på en Azure-måttavisering. Det visar också hur nyttolasten för HTTP POST till en webhook ser ut. Information om konfigurationen och schemat för en Azure-aktivitetsloggavisering (avisering om händelser) finns i [Anropa en webhook i en Azure-aktivitetsloggavisering](alerts-log-webhook.md).

Azure-aviseringar använder HTTP POST för att skicka aviseringsinnehållet i JSON-format till en webhook URI som du anger när du skapar aviseringen. Schemat definieras senare i den här artikeln. Uri-uri:n måste vara en giltig HTTP- eller HTTPS-slutpunkt. Azure publicerar en post per begäran när en avisering aktiveras.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurera webhooks via Azure-portalen
Om du vill lägga till eller uppdatera webhook URI går du till **Skapa/uppdatera aviseringar**i [Azure-portalen](https://portal.azure.com/).

![Lägga till ett varningsregelfönster](./media/alerts-webhooks/Alertwebhook.png)

Du kan också konfigurera en avisering för att publicera i en webhook URI med hjälp av [Azure PowerShell cmdlets](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), en [plattformsoberoende CLI](../../azure-monitor/platform/cli-samples.md#work-with-alerts)eller [Azure Monitor REST API: er](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan autentisera med hjälp av tokenbaserad auktorisering. Webhook URI sparas med ett token-ID. Exempel: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schema för nyttolast
POST-åtgärden innehåller följande JSON-nyttolast och schema för alla måttbaserade aviseringar:

```JSON
{
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
```


| Field | Obligatorisk | Fast uppsättning värden | Anteckningar |
|:--- |:--- |:--- |:--- |
| status |Y |Aktiverad, löst |Statusen för aviseringen baserat på de villkor du anger. |
| Sammanhang |Y | |Aviseringskontexten. |
| timestamp |Y | |Den tidpunkt då aviseringen utlöstes. |
| id |Y | |Varje varningsregel har ett unikt ID. |
| namn |Y | |Aviseringsnamnet. |
| description |Y | |En beskrivning av aviseringen. |
| conditionType (villkorstyp) |Y |Mått, Händelse |Två typer av aviseringar stöds: mått och händelse. Måttaviseringar baseras på ett måttvillkor. Händelseaviseringar baseras på en händelse i aktivitetsloggen. Använd det här värdet för att kontrollera om aviseringen baseras på ett mått eller på en händelse. |
| Villkor |Y | |De specifika fält som ska kontrolleras baserat på **villkorstypvärdet.** |
| metricName |För måttaviseringar | |Namnet på måttet som definierar vad regeln övervakar. |
| metricUnit |För måttaviseringar |Byte, BytesPerSecond, Count, CountPerSecond, Procent, Sekunder |Enheten tillåts i måttet. Se [tillåtna värden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| måttVärde |För måttaviseringar | |Det faktiska värdet för måttet som orsakade aviseringen. |
| Tröskel |För måttaviseringar | |Tröskelvärdet som aviseringen aktiveras med. |
| fönsterSize |För måttaviseringar | |Den tidsperiod som används för att övervaka aviseringsaktivitet baserat på tröskelvärdet. Värdet måste vara mellan 5 minuter och 1 dag. Värdet måste vara i ISO 8601-varaktighetsformat. |
| timeAggregation |För måttaviseringar |Medelvärde, Sista, Högsta, Minimum, Ingen, Totalt |Hur de data som samlas in ska kombineras över tid. Standardvärdet är Medelvärde. Se [tillåtna värden](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |För måttaviseringar | |Operatorn som används för att jämföra aktuella måttdata med det inställda tröskelvärdet. |
| subscriptionId |Y | |Azure-prenumerations-ID. |
| resourceGroupName |Y | |Namnet på resursgruppen för den berörda resursen. |
| resourceName |Y | |Resursnamnet på den berörda resursen. |
| resourceType |Y | |Resurstypen för den berörda resursen. |
| resourceId |Y | |Resurs-ID för den berörda resursen. |
| resursRegion |Y | |Regionen eller platsen för den berörda resursen. |
| portalLink (portalLink) |Y | |En direktlänk till sammanfattningssidan för portalresurser. |
| properties |N |Valfri |En uppsättning nyckel-/värdepar som innehåller information om händelsen. Till exempel `Dictionary<String, String>`. Egenskapsfältet är valfritt. I ett anpassat användargränssnitt eller logikappbaserat arbetsflöde kan användare ange nyckel-/värdepar som kan skickas via nyttolasten. Ett alternativt sätt att skicka anpassade egenskaper tillbaka till webhook är via webhook URI själv (som frågeparametrar). |

> [!NOTE]
> Du kan bara ange **egenskapsfältet** med hjälp av [AZURE Monitor REST API:er](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure-aviseringar och webhooks i videon [Integrera Azure-aviseringar med PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Lär dig hur du [kör Azure Automation-skript (runbooks) på Azure-aviseringar](https://go.microsoft.com/fwlink/?LinkId=627081).
* Lär dig hur du [använder en logikapp för att skicka ett SMS-meddelande via Twilio från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Lär dig hur du [använder en logikapp för att skicka ett Slack-meddelande från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Lär dig hur du [använder en logikapp för att skicka ett meddelande till en Azure-kö från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

