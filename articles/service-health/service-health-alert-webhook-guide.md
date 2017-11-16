---
title: "Konfigurera meddelanden om hälsostatus för befintliga problem system med hjälp av en webhook | Microsoft Docs"
description: "Hämta anpassade meddelanden om tjänsten hälsa händelser till din befintliga problem hanteringssystem."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Konfigurera meddelanden om hälsostatus för befintliga problem system med hjälp av en webhook

Den här artikeln visar hur du konfigurerar aviseringar tjänstens hälsa för att skicka data via Webhooks till ditt befintliga notification system.

Idag, kan du konfigurera tjänsten Health-aviseringar så att när en Incident för Azure-tjänsten påverkar dig du få meddelanden via SMS eller e-post.
Du kanske redan har befintliga externa aviseringar system som du vill använda.
Det här dokumentet visas de viktigaste delarna i webhook-nyttolasten och hur du kan skapa anpassade aviseringar att visa ett meddelande när problem med tjänsten påverkar dig.

Om du vill använda en förkonfigurerad integration se så här:
* [Konfigurera aviseringar med ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurera aviseringar med PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurera aviseringar med OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Konfigurera ett anpassat meddelande med hjälp av tjänstens hälsa webhook nyttolast
Om du vill ställa in din egen anpassade webhook-integration måste att parsa JSON-nyttolast som skickas under tjänstens hälsa för meddelanden.

Leta [här om du vill se ett exempel](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) om vad den `Service Health` webhook nyttolast ser ut.

Du kan identifiera det här är en avisering om tjänstens hälsa genom att titta på `context.eventSource == "ServiceHealth"`. Därifrån är de egenskaper som är mest relevant för att mata in:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>Skapa en direktlänk till Azure-tjänstens hälsa för en incident
Du kan skapa en direktlänk till din anpassade Azure tjänstens hälsa incident på skrivbordet eller mobila genom att generera en särskild URL. Använd den `trackingId`, samt de första och sista tre siffrorna i din `subscriptionId`, för att bilda:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Till exempel om din `subscriptionId` är `bba14129-e895-429b-8809-278e836ecdb3` och `trackingId` är `0DET-URB`, och sedan din anpassade Azure hälsa URL är:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Med nivån för att upptäcka problemets allvarlighetsgrad
Från lägsta allvarlighetsgrad till högsta allvarlighetsgrad den `level` egenskap i nyttolasten kan vara någon av `Informational`, `Warning`, `Error`, och `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Parsning av de berörda tjänsterna för att förstå vilka incidenten
Hälsovarningar för tjänsten kan informera dig om problem över flera regioner och tjänster. Om du vill ha fullständig information du behöver att parsa värdet för `impactedServices`.
Innehållet i en [JSON undantagstecken](http://json.org/) sträng, när unescaped, innehåller ett annat JSON-objekt som kan parsas regelbundet.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Blir:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Det visar att det finns problem med ”aviseringar och mått” i både östra och sydost, samt problem med ”Apptjänst” i Australien sydost.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran
1. Skapa nyttolasten av tjänstens hälsa som du vill skicka. Du hittar en webhook exempel tjänstens hälsa nyttolast på [Webhooks för Azure aktiviteten Logga varningar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Du bör få ett `2XX - Successful` svar.

4. Gå till [PagerDuty](https://www.pagerduty.com/) att bekräfta att din integrering har ställts in.

## <a name="next-steps"></a>Nästa steg
- Granska de [avisering webhook för aktivitetslogg](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Lär dig mer om [tjänsten meddelanden om hälsostatus](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Lär dig mer om [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md).