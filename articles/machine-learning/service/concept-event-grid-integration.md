---
title: Använda Azure Machine Learning händelser
titleSuffix: Azure Machine Learning
description: I den här artikeln får du lära dig hur du använder Azure Event Grid för att prenumerera, reagera på och avbryta prenumerationen på händelser som genereras av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: 888d294634078d2fe7737426f6cf025ae9948b0a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609351"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Använda Azure Machine Learning händelser (förhands granskning)

Azure Machine Learning hanterar hela livs cykeln för maskin inlärnings processen, inklusive modell utbildning, modell distribution och övervakning. Azure Machine Learning händelser gör att program kan reagera på händelser under Machine Learning-livscykeln, t. ex. slutförd utbildning, registrering och distribution av modeller samt identifiering av data drift, genom att använda moderna Server lös arkitekturer. 

Dessa händelser publiceras via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Med hjälp av Azure Portal, PowerShell eller Azure CLI kan kunder enkelt prenumerera på händelser genom att [Ange en eller flera händelse typer och filtrerings villkor](/azure/event-grid/event-filtering). Kunderna har också möjlighet att bygga en mängd olika händelse hanterare, till exempel Azure Functions, Azure Logic Apps eller allmänna Webhooks. Azure Machine Learning, tillsammans med Azure Event Grid, finns en hög tillgänglig, tillförlitlig och feltolerant händelse leverans plattform som du kan använda för att bygga händelse drivna program.

Information om hur du använder Azure Machine Learning med Event Grid finns i [Skapa händelse drivna Machine Learning arbets flöden (för hands version)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Händelse modellen 

Azure Event Grid läser händelser från källor, till exempel Azure Machine Learning och andra Azure-tjänster. Dessa händelser skickas sedan till händelse hanterare som Azure Event Hubs, Azure Functions, Logic Apps och andra. Följande diagram visar hur Event Grid ansluter källor och hanterare, men är inte en omfattande lista över stödda integreringar.

![Azure Event Grid funktionell modell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Mer information om händelse källor och händelse hanterare finns i [Vad är event Grid?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning händelse typer

Azure Machine Learning innehåller händelser i olika punkter av Machine Learning-livscykel: 

| Händelse typ | Beskrivning |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Utlöses när en körning av Machine Learning-experiment har slutförts |
| `Microsoft.MachineLearningServices.ModelRegistered` | Utlöses när en maskin inlärnings modell registreras i arbets ytan |
| `Microsoft.MachineLearningServices.ModelDeployed` | Utlöses när en distribution av en tjänst med en eller flera modeller har slutförts |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Utlöses när ett jobb för data avvikelse identifiering för två data uppsättningar har slutförts |

## <a name="subscribe-to-machine-learning-events"></a>Prenumerera på Machine Learning händelser

Prenumerationer för Azure Machine Learning händelser skyddas av rollbaserad åtkomst kontroll (RBAC). Endast [deltagare eller ägare](how-to-assign-roles.md#default-roles) av en arbets yta kan skapa, uppdatera och ta bort händelse prenumerationer.

Händelse prenumerationer kan filtreras baserat på olika villkor. Filter kan tillämpas på händelse prenumerationer antingen under [skapandet](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) av händelse prenumerationen eller [vid ett senare tillfälle](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrera efter händelse typ
En händelse prenumeration kan ange en eller flera Azure Machine Learning händelse typer.

### <a name="filter-by-event-subject"></a>Filtrera efter händelse ämne
Azure Event Grid stöder ämnes filter som baseras på __börjar med__ och __slutar med__ matchningar, så att händelser med ett matchande ämne levereras till prenumeranten. Olika Machine Learning-händelser har olika ämnes format.

| Händelse typ | Ämnes format | Exempel ämne |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |

### <a name="advanced-filtering"></a>Avancerad filtrering

Azure Event Grid stöder även avancerad filtrering baserat på ett publicerat händelse schema. Azure Machine Learning händelse schema information finns i [Azure Event Grid händelse schema för Azure Machine Learning](../../event-grid/event-schema-machine-learning.md).

Några exempel på avancerade filter som du kan utföra är:

* För att `Microsoft.MachineLearningServices.ModelRegistered` event, för att filtrera modellens tagg värde:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Mer information om hur du använder filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Använda Machine Learning händelser

Program som hanterar Machine Learning händelser bör följa några rekommenderade metoder:

> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att säkerställa att det kommer från det lagrings konto som du förväntar dig.
> * På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
> * När meddelanden kan komma in i rätt ordning och efter en viss fördröjning använder du etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  Använd också sekvenserare-fälten för att förstå ordningen på händelser för ett visst objekt.
> * Ignorera fält som du inte förstår. Den här övningen hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
> * Misslyckade eller avbrutna Azure Machine Learning åtgärder utlöser inte en händelse. Om en modell distribution till exempel Miss lyckas, utlöses inte Microsoft. MachineLearningServices. ModelDeployed. Tänk på detta felläge när du utformar dina program. Du kan alltid använda Azure Machine Learning SDK, CLI eller portal för att kontrol lera status för en åtgärd och förstå de detaljerade orsakerna till problemet.

Azure Event Grid gör det möjligt för kunderna att bygga ut de sammankopplade meddelande hanteringarna, som kan utlösas av Azure Machine Learning händelser. Några viktiga exempel på Meddelande hanterare är:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory pipeline
* Allmänna webhookar som kan finnas på Azure-plattformen eller någon annan stans

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure Machine Learning händelser ett försök:

- [Om Event Grid](../../event-grid/overview.md)
- [Azure Event Grid händelse schema för Azure Machine Learning](../../event-grid/event-schema-machine-learning.md)
- [Skapa händelse drivna arbets flöden med Azure Machine Learning](how-to-use-event-grid.md)
