---
title: Samma avisering schema för Azure monitor-aviseringar
description: Förstå vanliga avisering schemat, varför du bör använda den och hur du aktiverar det
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149384"
---
# <a name="common-alert-schema"></a>Vanligt aviseringsschema

Den här artikeln beskriver vad det gemensamma schemat för aviseringen är, fördelarna med att använda det och hur du aktiverar den.

## <a name="what-is-the-common-alert-schema"></a>Vad är det gemensamma schemat för aviseringen?

Det gemensamma schemat för avisering standardiserar förbrukning upplevelsen för aviseringar i Azure idag. Historiskt sett har tre aviseringstyperna i Azure i dag (mått, logg och aktivitetsloggen) hade sina egna e-postmallar, webhook-scheman, osv. Med det gemensamma schemat för avisering, kan du nu få aviseringar med ett konsekvent schema.

Alla aviseringsinstansen beskriver **den resurs som påverkades** och **orsaken till aviseringen**, och dessa instanser beskrivs i det gemensamma schemat i följande avsnitt:
* **Essentials**: En uppsättning **standardiserade fält**Common över alla aviseringstyper som beskriver **vilka resurser** aviseringen är på tillsammans med ytterligare vanliga avisering metadata (till exempel allvarlighetsgrad eller beskrivning). 
* **Avisera kontext**: En uppsättning fält som beskriver den **orsaken till aviseringen**, med fält som varierar **baserat på typ av avisering**. En metrisk varning skulle till exempel ha fält som Måttnamn och måttvärde i varningskontexten, en aktivitetsloggavisering skulle ha information om den händelse som skapade aviseringen. 

Vanliga integrationsscenarier som vi får höra från våra kunder involverar routning av aviseringsinstansen till berörda teamet baserat på vissa pivot (till exempel resursgrupp) efter vilken ansvarig teamet börjar arbeta på den. Med det gemensamma schemat för aviseringar, kan du har in Routning logic över aviseringstyper genom att utnyttja viktiga fält, lämna fälten kontext som gäller för berörda team att undersöka vidare.

Det innebär att du kan ha färre integreringar, vilket gör processen med att hantera och underhålla dem. en _mycket_ lättare. Dessutom visar framtida avisering nyttolast enrichments (till exempel anpassning, diagnostiska berikande, etc.) endast upp i det gemensamma schemat.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Vilka förbättringar det gemensamma schemat för avisering ta med?

Det gemensamma schemat för avisering visas själva främst i dina aviseringar. De förbättringar som visas nedan:

| Åtgärd | Förbättringar|
|:---|:---|
| SMS | En konsekvent SMS-mall för alla aviseringstyper. |
| E-post | En konsekvent och detaljerade e-postmall, så att du kan enkelt diagnostisera problem på ett ögonblick. Inbäddade djup-länkar till aviseringsinstansen på portalen och resurser som påverkas se till att du snabbt kan gå in i processen för reparation. |
| Webhook/Logic App-/ Azure-funktion/Automation-Runbook | En konsekvent JSON struktur för alla aviseringstyper, där du kan enkelt skapa integreringar över olika aviseringstyper. |

Det nya schemat kan också en rikare upplevelse för avisering förbrukning i både Azure-portalen och Azure-mobilappen i den närmaste framtiden. 

[Läs mer om schemadefinitionerna för Webhooks/Logic Apps/Azure Functions/Automation-Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Det gemensamma schemat för aviseringen har inte stöd för följande åtgärder: ITSM-anslutningsprogram.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Hur aktiverar jag det gemensamma schemat för avisering

Du kan anmäla eller avanmäla dig i det gemensamma aviseringarna schemat via åtgärdsgrupper på båda portalen och via REST API. Visa/Dölj att växla till det nya schemat finns på åtgärd-nivå. Exempelvis kan behöva du separat anmäla dig till en e poståtgärd och webhook-åtgärd.

> [!NOTE]
> 1. Följande aviseringstyper stöder det gemensamma schemat som standard (ingen opt i krävs):
>     * Aviseringar för smart identifiering
> 1. Följande aviseringstyper stöd finns inte för det gemensamma schemat:
>     * Aviseringar som genereras av [Azure Monitor för virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Aviseringar som genereras av [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Via Azure portal

![Välja samma avisering schema](media/alerts-common-schema/portal-opt-in.png)

1. Öppna befintliga eller en ny åtgärd i en åtgärdsgrupp. 
1. Välj ”Ja” för växlingsknappen för att aktivera det gemensamma schemat för aviseringen som visas.

### <a name="through-the-action-groups-rest-api"></a>Via åtgärdsgrupper REST-API

Du kan också använda den [åtgärd grupper API](https://docs.microsoft.com/rest/api/monitor/actiongroups) att välja det gemensamma schemat för aviseringen. När du gör den [skapa eller uppdatera](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API-anrop, kan du ange flaggan ”useCommonAlertSchema” till 'true' (om du vill välja) eller ”FALSKT” (för att välja bort) för någon av följande åtgärder - e-post/webhook/logic app/Azure-funktion/automation-runbook.

Till exempel följande begärandetext görs till den [skapa eller uppdatera](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST-API kommer att göra följande:

* Aktivera vanliga avisering schemat för e-poståtgärden ”Johan Svenssons e-post”
* Inaktivera det gemensamma aviseringarna schemat för e-poståtgärden ”Jane Smith e-post”
* Aktivera vanliga avisering schemat för webhook-åtgärd ”exempel webhook”

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Nästa steg

- [Vanliga avisering schemadefinitioner för Webhooks/Logic Apps/Azure Functions/Automation-Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)



