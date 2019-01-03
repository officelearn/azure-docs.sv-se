---
title: Konfigurera aviseringar om hälsotillståndet för befintliga problem system med en webhook | Microsoft Docs
description: Få personligt anpassade meddelanden om service health-händelser till problemhanteringssystemet befintliga.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2018
ms.author: shtabriz
ms.openlocfilehash: 740adefbb8bfa53e96b5ed0e1c7181abf22f1490
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580287"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Konfigurera aviseringar om hälsotillståndet för befintliga problem system med en webhook

Den här artikeln visar hur du konfigurerar service health-aviseringar för att skicka data via Webhooks till din befintliga meddelandesystem.

Idag kan du konfigurera service health-aviseringar så att när en Incident i Azure-tjänsten påverkar dig, du får meddelanden via SMS eller e-post.
Du kanske redan har befintliga externa meddelandesystemet som du vill använda.
Det här dokumentet visar de viktigaste delarna i webhook-nyttolasten och hur du kan skapa anpassade varningar att bli meddelad när tjänstproblem påverkar dig.

Om du vill använda en förkonfigurerad integration, se hur du:
* [Konfigurera aviseringar med ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurera aviseringar med PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurera aviseringar med OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Konfigurera ett anpassat meddelande med hjälp av webhook-nyttolasten service health
Om du vill ställa in dina egna anpassade webhook-integrering behöver att parsa JSON-nyttolasten som skickas när meddelanden om hälsostatus för tjänsten.

Titta [här om du vill se ett exempel](../azure-monitor/platform/activity-log-alerts-webhook.md) av vad de `ServiceHealth` webhook-nyttolasten kan se ut.

Du kan identifiera det här är en hälsoavisering för tjänst genom att titta på `context.eventSource == "ServiceHealth"`. Därifrån kan är de egenskaper som är mest relevant för att mata in:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>Skapa en direktlänk till instrumentpanelen för Tjänstehälsa för en incident
Du kan skapa en direktlänk till instrumentpanelen Service Health på skrivbordet eller mobila genom att generera en specialiserad URL. Använd den `trackingId`, samt de första och sista tre siffrorna i din `subscriptionId`, för att skapa:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Exempel: om din `subscriptionId` är `bba14129-e895-429b-8809-278e836ecdb3` och din `trackingId` är `0DET-URB`, tjänstens hälsotillstånd URL är:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Använda nivån för att identifiera problemets allvarlighetsgrad
Från lägsta allvarlighetsgrad till högsta allvarlighetsgrad den `level` egenskapen i nyttolasten kan vara något av `Informational`, `Warning`, `Error`, och `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Parsning av de berörda tjänsterna för att förstå vilka incidenten
Service health-aviseringar kan informera dig om problem i flera regioner och tjänster. Om du vill ha fullständig information du behöver att parsa värdet för `impactedServices`.
Innehållet i är en [JSON undantagna](http://json.org/) string, när unescaped, innehåller ett annat JSON-objekt som kan parsas regelbundet.

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

Detta visar att det finns problem med ”aviseringar och mått” i både Australien, östra och sydöstra, samt problem med ”App Service” i Australien, sydöstra Australien.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testa webhook-integrering via en HTTP POST-begäran
1. Skapa service health-nyttolasten som du vill skicka. Du hittar ett exempel service health webhook-nyttolasten på [Webhooks för Azure-aktivitetsloggar loggaviseringar](../azure-monitor/platform/activity-log-alerts-webhook.md).

2. Skapa en HTTP POST-begäran enligt följande:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Du bör få ett `2XX - Successful` svar.

4. Gå till [PagerDuty](https://www.pagerduty.com/) att bekräfta att din integrering har har ställts in.

## <a name="next-steps"></a>Nästa steg
- Granska den [avisering webhook för aktivitetslogg](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Lär dig mer om [service health meddelanden](../azure-monitor/platform/service-notifications.md).
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).