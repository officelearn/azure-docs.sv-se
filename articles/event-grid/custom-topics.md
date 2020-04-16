---
title: Anpassade ämnen i Azure Event Grid
description: Beskriver anpassade ämnen i Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394363"
---
# <a name="custom-topics-in-azure-event-grid"></a>Anpassade ämnen i Azure Event Grid
Ett händelserutnätsavsnitt innehåller en slutpunkt där källan skickar händelser. Utgivaren skapar ämnet för händelserutnätet och avgör om en händelsekälla behöver ett ämne eller mer än ett ämne. Ett ämne används för en samling relaterade händelser. För att svara på vissa typer av händelser bestämmer prenumeranter vilka ämnen som ska prenumerera på.

**Anpassade ämnen** är program- och tredjepartsämnen. När du skapar eller tilldelas åtkomst till ett anpassat ämne visas detta anpassade ämne i din prenumeration. 

När du utformar programmet har du flexibilitet när du bestämmer hur många ämnen som ska skapas. Skapa ett anpassat **ämne** för **varje kategori av relaterade händelser**för stora lösningar. Tänk dig ett program som skickar händelser som handlar om att ändra användarkonton och bearbeta beställningar. Det är osannolikt alla händelsehanteraren vill ha båda händelsekategorier. Skapa två anpassade ämnen och låt händelsehanteraren prenumerera på det mest relevanta. För små lösningar kanske du föredrar att skicka alla händelser till ett enda ämne. Händelseprenumeranter kan filtrera efter de händelsetyper de vill ha.

## <a name="event-schema"></a>Händelseschema
En detaljerad översikt över händelseschema finns i [Azure Event Grid-händelseschema](event-schema.md). För anpassade ämnen bestämmer händelseutgivaren **dataobjektet.** Data på den översta nivån ska ha samma fält som standardresursdefinierade händelser.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Följande avsnitt innehåller länkar till självstudier för att skapa anpassade ämnen med azure portal-, CLI-, PowerShell- och Azure Resource Manager-mallar (ARM). 


## <a name="azure-portal-tutorials"></a>Självstudier för Azure Portal
|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: skapa och dirigera anpassade händelser med Azure-portalen](custom-event-quickstart-portal.md) | Visar hur du använder portalen för att skicka anpassade händelser. |
| [Snabbstart: dirigera anpassade händelser till Azure-kölagring](custom-event-to-queue-storage.md) | Beskriver hur du skickar anpassade händelser till ett kölagringsutrymme. |
| [Så här lägger du upp i anpassat ämne](post-to-custom-topic.md) | Visar hur du publicerar en händelse i ett anpassat ämne. |


## <a name="azure-cli-tutorials"></a>Azure CLI-självstudier
|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: skapa och dirigera anpassade händelser med Azure CLI](custom-event-quickstart.md) | Visar hur du använder Azure CLI för att skicka anpassade händelser. |
| [Anpassat ämne för Azure CLI: skapa det anpassade avsnittet Händelserutnät](./scripts/event-grid-cli-create-custom-topic.md)|Exempelskript som skapar ett anpassat ämne. Skriptet hämtar slutpunkten och en nyckel.|
| [Azure CLI: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-cli-subscribe-custom-topic.md)|Exempelskript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en WebHook.|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell-självstudier
|Titel  |Beskrivning  |
|---------|---------|
| [Snabbstart: skapa och dirigera anpassade händelser med Azure PowerShell](custom-event-quickstart-powershell.md) | Visar hur du använder Azure PowerShell för att skicka anpassade händelser. |
| [Anpassat ämne för PowerShell: skapa anpassat ämne för händelserutnät](./scripts/event-grid-powershell-create-custom-topic.md)|Exempelskript som skapar ett anpassat ämne. Skriptet hämtar slutpunkten och en nyckel.|
| [PowerShell: prenumerera på händelser för ett anpassat ämne](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Exempelskript som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en WebHook.|

## <a name="arm-template-tutorials"></a>Självstudier för ARM-mall
|Titel  |Beskrivning  |
|---------|---------|
| [Resource Manager-mall: anpassat ämne och WebHook-slutpunkt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | En Resource Manager-mall som skapar ett anpassat ämne och en prenumeration för det anpassade avsnittet. Den skickar händelser till en WebHook. |
| [Resurshanterarens mall: anpassade avsnitt och slutpunkt för händelsehubbar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure Event Hubs. |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Systemämnen](system-topics.md)
- [Domäner](event-domains.md)