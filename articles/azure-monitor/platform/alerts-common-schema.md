---
title: Gemensamt varningsschema för Azure-övervakarevarningar
description: Förstå det gemensamma varningsschemat, varför du ska använda det och hur du aktiverar det
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249053"
---
# <a name="common-alert-schema"></a>Vanligt aviseringsschema

I den här artikeln beskrivs vad det gemensamma varningsschemat är, fördelarna med att använda det och hur du aktiverar det.

## <a name="what-is-the-common-alert-schema"></a>Vad är det gemensamma varningsschemat?

Det gemensamma varningsschemat standardiserar förbrukningsupplevelsen för varningsmeddelanden i Azure idag. Historiskt sett har de tre varningstyperna i Azure idag (mått, logg och aktivitetslogg) haft sina egna e-postmallar, webhook-scheman osv. Med det gemensamma varningsschemat kan du nu få aviseringar med ett konsekvent schema.

Alla varningsinstanser beskriver **den resurs som påverkades** och **orsaken till aviseringen**, och dessa instanser beskrivs i det gemensamma schemat i följande avsnitt:
* **Essentials**: En uppsättning **standardiserade fält**, som är vanliga för alla varningstyper, som beskriver **vilken resurs** aviseringen är på tillsammans med ytterligare vanliga varningsmetadata (till exempel allvarlighetsgrad eller beskrivning). 
* **Aviseringskontext:** En uppsättning fält som beskriver **orsaken till aviseringen**, med fält som varierar **beroende på aviseringstypen**. En måttavisering skulle till exempel ha fält som måttnamn och måttvärde i aviseringskontexten, medan en aktivitetsloggavisering skulle ha information om händelsen som genererade aviseringen. 

De typiska integrationsscenarier som vi hör från kunder innebär routning av aviseringsinstansen till det berörda teamet baserat på vissa pivot (till exempel resursgrupp), varefter det ansvariga teamet börjar arbeta med den. Med det gemensamma varningsschemat kan du ha standardiserad routningslogik över varningstyper genom att utnyttja de väsentliga fälten, vilket gör att kontextfälten är detsamma som för de berörda teamen att undersöka vidare.

Det innebär att du potentiellt kan ha färre integrationer, vilket gör processen att hantera och underhålla dem till en _mycket_ enklare uppgift. Dessutom visas framtida nyttolastanrikningar (till exempel anpassning, diagnostikanrikning osv.) i det gemensamma schemat.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Vilka förbättringar medför det gemensamma varningsschemat?

Det gemensamma varningsschemat visar sig i första hand i dina varningsmeddelanden. De förbättringar som du kommer att se visas nedan:

| Åtgärd | Förbättringar |
|:---|:---|
| SMS | En konsekvent SMS-mall för alla varningstyper. |
| E-post | En konsekvent och detaljerad e-postmall, så att du enkelt kan diagnostisera problem på ett ögonblick. Inbäddade djuplänkar till aviseringsinstansen på portalen och den berörda resursen säkerställer att du snabbt kan hoppa in i reparationsprocessen. |
| Webhook/Logic App/Azure-funktion/Automation-runbook | En konsekvent JSON-struktur för alla varningstyper, vilket gör att du enkelt kan skapa integrationer över de olika varningstyperna. |

Det nya schemat kommer också att möjliggöra en rikare upplevelse av varningsförbrukning i både Azure-portalen och Azure-mobilappen inom den närmaste framtiden. 

[Läs mer om schemadefinitioner för Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> Följande åtgärder stöder inte det gemensamma varningsschemat: ITSM Connector.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Hur aktiverar jag det gemensamma varningsschemat?

Du kan välja eller välja bort det gemensamma varningsschemat via åtgärdsgrupper, både på portalen och via REST API. Växlingsknappen för att växla till det nya schemat finns på åtgärdsnivå. Du måste till exempel separat välja en e-poståtgärd och en webhook-åtgärd.

> [!NOTE]
> 1. Följande varningstyper stöder det gemensamma schemat som standard (ingen opt in krävs):
>     * Varningar för smart identifiering
> 1. Följande varningstyper stöder för närvarande inte det gemensamma schemat:
>     * Aviseringar som genereras av [Azure Monitor för virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Aviseringar som genereras av [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Via Azure-portalen

![Gemensamt varningsschema väljer i](media/alerts-common-schema/portal-opt-in.png)

1. Öppna en befintlig eller en ny åtgärd i en åtgärdsgrupp. 
1. Välj Ja för växlingsknappen för att aktivera det gemensamma varningsschemat som visas.

### <a name="through-the-action-groups-rest-api"></a>Genom åtgärdsgrupperna REST API

Du kan också använda [API:et för åtgärdsgrupper](https://docs.microsoft.com/rest/api/monitor/actiongroups) för att anmäla dig till det gemensamma varningsschemat. När du gör rest-API-anropet [för att skapa eller uppdatera](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) rest-API kan du ställa in flaggan "useCommonAlertSchema" på 'true' (to opt in) eller 'false' (to opt out) for any of the following actions - email/webhook/logic app/Azure Function/automation runbook.

Följande begärandetext som görs till REST API för [att skapa eller uppdatera](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) kommer att göra följande:

* Aktivera det gemensamma varningsschemat för e-poståtgärden "John Doe's email"
* Inaktivera det gemensamma varningsschemat för e-poståtgärden "Jane Smiths e-post"
* Aktivera det gemensamma varningsschemat för webhook-åtgärden "Exempel på webhook"

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

- [Vanliga varningsschemadefinitioner för Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)
- [Lär dig hur du skapar en logikapp som utnyttjar det gemensamma varningsschemat för att hantera alla dina aviseringar.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



