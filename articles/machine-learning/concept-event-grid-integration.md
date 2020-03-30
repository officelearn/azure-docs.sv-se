---
title: Använda Azure Machine Learning-händelser
titleSuffix: Azure Machine Learning
description: I den här artikeln får du lära dig hur du använder Azure Event Grid för att prenumerera, reagera och avsluta prenumerationen på händelser som genereras av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139053"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Använda Azure Machine Learning-händelser (förhandsversion)

Azure Machine Learning hanterar hela livscykeln för maskininlärningsprocessen, inklusive modellutbildning, modelldistribution och övervakning. Azure Machine Learning-händelser gör det möjligt för program att reagera på händelser under maskininlärningslivscykeln, till exempel slutförande av utbildningskörningar, registrering och distribution av modeller och identifiering av datadrift, med hjälp av moderna serverlösa Arkitekturer. 

Dessa händelser publiceras via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Med Hjälp av Azure Portal, Powershell eller Azure CLI kan kunder enkelt prenumerera på händelser genom [att ange en eller flera händelsetyper och filtrera villkor](/azure/event-grid/event-filtering). Kunderna har också möjlighet att skapa ett brett utbud av händelsehanterare som Azure Functions, Azure Logic Apps eller allmänna webhooks. Azure Machine Learning, tillsammans med Azure Event Grid, tillhandahåller en hög tillgänglig, tillförlitlig och feltolerant händelseleveransplattform för dig att skapa händelsedrivna program.

Information om hur du använder Azure Machine Learning med händelserutnät finns i [Skapa arbetsflöden för händelsedriven maskininlärning (förhandsversion).](how-to-use-event-grid.md)

## <a name="the-event-model"></a>Händelsemodellen 

Azure Event Grid läser händelser från källor, till exempel Azure Machine Learning och andra Azure-tjänster. Dessa händelser skickas sedan till händelsehanterare som Azure Event Hubs, Azure Functions, Logic Apps och andra. Följande diagram visar hur Event Grid ansluter källor och hanterare, men är inte en omfattande lista över integrationer som stöds.

![Funktionell modell för Azure Event Grid](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Mer information om händelsekällor och händelsehanterare finns i [Vad är händelserutnät?](/azure/event-grid/overview)

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning-händelsetyper

Azure Machine Learning innehåller händelser i de olika punkterna i maskininlärningslivscykeln: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Utlöses när en maskininlärningsexperimentkörning är klar |
| `Microsoft.MachineLearningServices.ModelRegistered` | Utlöses när en maskininlärningsmodell registreras på arbetsytan |
| `Microsoft.MachineLearningServices.ModelDeployed` | Utlöses när en distribution av inferenstjänst med en eller flera modeller slutförs |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Utlöses när ett datadriftidentifieringsjobb för två datauppsättningar har slutförts |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Utlöses när en körningsstatus ändras, som för närvarande bara utlöses när en körningsstatus är "misslyckades" |

## <a name="subscribe-to-machine-learning-events"></a>Prenumerera på machine learning-evenemang

Prenumerationer för Azure Machine Learning-händelser skyddas av rollbaserad åtkomstkontroll (RBAC). Endast [deltagare eller ägare](how-to-assign-roles.md#default-roles) av en arbetsyta kan skapa, uppdatera och ta bort händelseprenumerationer.

Händelseprenumerationer kan filtreras baserat på en mängd olika villkor. Filter kan tillämpas på händelseprenumerationer antingen när händelseprenumerationen [skapas](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) eller [vid ett senare tillfälle](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrera efter händelsetyp
En händelseprenumeration kan ange en eller flera Azure Machine Learning-händelsetyper.

### <a name="filter-by-event-subject"></a>Filtrera efter händelseämne
Azure Event Grid stöder __ämnesfilter__ baserat på börjar med och __slutar med__ matchningar, så att händelser med ett matchande ämne levereras till prenumeranten. Olika maskininlärningshändelser har olika ämnesformat.

| Händelsetyp | Ämnesformat | Exempelämne |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Avancerad filtrering

Azure Event Grid stöder också avancerad filtrering baserat på publicerat händelseschema. Information om Azure Machine Learning-händelseschema finns i [Azure Event Grid-händelseschema för Azure Machine Learning](../event-grid/event-schema-machine-learning.md).

Några exempel på avancerade filtreringar som du kan utföra är:

* För `Microsoft.MachineLearningServices.ModelRegistered` händelse, för att filtrera modellens taggvärde:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Mer information om hur du använder filter finns i [Filtrera händelser för händelserutnät](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Använda maskininlärningshändelser

Program som hanterar machine learning-händelser bör följa några rekommenderade metoder:

> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare är det viktigt att inte anta att händelser kommer från en viss källa, utan för att kontrollera ämnet för meddelandet för att säkerställa att det kommer från arbetsytan för maskininlärning som du förväntar dig.
> * På samma sätt kontrollerar du att eventType är en som du är beredd att bearbeta och förutsätter inte att alla händelser du får kommer att vara de typer du förväntar dig.
> * När meddelanden kan komma i oordning och efter viss fördröjning kan du använda etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  Använd även sequencer-fälten för att förstå ordningen på händelser på ett visst objekt.
> * Ignorera fält som du inte förstår. Den här metoden hjälper dig att hålla dig motståndskraftig mot nya funktioner som kan läggas till i framtiden.
> * Misslyckade eller avbrutna Azure Machine Learning-åtgärder utlöser inte en händelse. Om till exempel en modelldistribution misslyckas utlöses inte Microsoft.MachineLearningServices.ModelDeployed. Tänk på ett sådant felläge när du utformar dina program. Du kan alltid använda Azure Machine Learning SDK, CLI eller portal för att kontrollera status för en åtgärd och förstå de detaljerade felorsaker.

Azure Event Grid gör det möjligt för kunder att skapa de-kopplade meddelandehanterare, som kan utlösas av Azure Machine Learning-händelser. Några anmärkningsvärda exempel på meddelandehanterare är:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory Pipeline
* Allmänna webhooks, som kan finnas på Azure-plattformen eller någon annanstans

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure Machine Learning-händelser ett försök:

- [Om Event Grid](../event-grid/overview.md)
- [Azure Event Grid-händelseschema för Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Skapa händelsedrivna arbetsflöden med Azure Machine Learning](how-to-use-event-grid.md)
