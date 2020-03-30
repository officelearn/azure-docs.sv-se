---
title: Skicka Azure Service Health-meddelanden via webhooks
description: Skicka anpassade meddelanden om tjänsthälsohändelser till ditt befintliga problemhanteringssystem.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062847"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Använd en webhook för att konfigurera hälsomeddelanden för problemhanteringssystem

Den här artikeln visar hur du konfigurerar Azure Service Health-aviseringar för att skicka data via webhooks till ditt befintliga meddelandesystem.

Du kan konfigurera servicehälsoaviseringar så att du får ett sms eller e-post när en Azure-tjänstincident påverkar dig.

Men du kanske redan har ett befintligt externt meddelandesystem på plats som du föredrar att använda. Den här artikeln identifierar de viktigaste delarna av webhook nyttolasten. Och den beskriver hur du skapar anpassade aviseringar för att meddela dig när relevanta serviceproblem uppstår.

Om du vill använda en förkonfigurerad integrering läser du:
* [Konfigurera aviseringar med ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurera aviseringar med PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurera aviseringar med OpsGenie](service-health-alert-webhook-opsgenie.md)

**Titta på en introduktionsvideo:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Konfigurera ett anpassat meddelande med hjälp av nyttolasten Service Health webhook
Om du vill konfigurera din egen anpassade webhook-integrering måste du tolka den JSON-nyttolast som skickas via servicehälsomeddelandet.

Se [ett exempel](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` webhook nyttolast.

Du kan bekräfta att det är en `context.eventSource == "ServiceHealth"`service hälsovarning genom att titta på . Följande egenskaper är de mest relevanta:
- **data.context.activityLog.status**
- **data.context.activityLog.nivå**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Skapa en länk till instrumentpanelen servicehälsa för en incident
Du kan skapa en direktlänk till instrumentpanelen för tjänstens hälsotillstånd på en stationär eller mobil enhet genom att generera en specialiserad URL. Använd *trackingId* och de tre första och sista tre siffrorna i din *subscriptionId* i det här formatet:

https://app.azure.com/h/<i></i>*&lt;trackingId&gt;*/*&lt;första tre och sista tre siffrorna&gt; i subscriptionId*

Om din *prenumerationId* till exempel är bba14129-e895-429b-8809-278e836ecdb3 och din *trackingId* är 0DET-URB, är din servicehälsoadress:

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Använd nivån för att identifiera problemets allvarlighetsgrad
Från lägsta till högsta allvarlighetsgrad kan **egenskapen level** i nyttolasten vara *Information,* *Varning,* *Fel*eller *Kritisk*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Tolka de påverkade tjänsterna för att fastställa incidentomfattningen
Service Health-aviseringar kan informera dig om problem i flera regioner och tjänster. För att få fullständig information måste du `impactedServices`tolka värdet för .

Innehållet som finns inuti är en förrymd [JSON-sträng](https://json.org/) som, när den inte tas upp, innehåller ett annat JSON-objekt som kan tolkas regelbundet. Ett exempel:

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

I det här exemplet visas problem för:
- "Varningar & Metrics" i Australien East och Australien Sydost.
- "App Service" i Sydöstra Australien.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran

Följ de här stegen:

1. Skapa servicehälsonyttolasten som du vill skicka. Se en exempeltjänsthälsa webhook nyttolast på [Webhooks för Azure-aktivitetsloggvarningar](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Du bör få ett "2XX - Lyckat" svar.

1. Gå till [PagerDuty](https://www.pagerduty.com/) för att bekräfta att integreringen har konfigurerats.

## <a name="next-steps"></a>Nästa steg
- Granska [webbkroksschemat för aktivitetsloggen.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Läs mer om [hälsomeddelanden](../azure-monitor/platform/service-notifications.md)för tjänster .
- Läs mer om [åtgärdsgrupper](../azure-monitor/platform/action-groups.md).