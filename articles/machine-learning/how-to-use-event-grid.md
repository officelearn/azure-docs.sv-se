---
title: Skapa händelse drivna Machine Learning-arbetsflöden
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Event Grid med Azure Machine Learning för att aktivera händelse drivna lösningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: 49ee00d43820d5aeb50e44cff1b6c5a448b4ce81
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623919"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Skapa händelse drivna Machine Learning-arbetsflöden (för hands version)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) stöder Azure Machine Learning händelser. Du kan till exempel använda händelser från slut för ande, modell registrering, modell distribution och data Avkännings avkänning för en arbets yta.

Mer information finns i [Azure Machine Learning integration med event Grid](concept-event-grid-integration.md) och det [Azure Machine Learning Event Grid-schemat](/azure/event-grid/event-schema-machine-learning).

Använd Event Grid för att aktivera vanliga scenarier som:

* Skicka e-postmeddelanden vid slut för ande
* Använd en Azure-funktion när en modell har registrerats
* Strömma händelser från Azure Machine Learning till olika slut punkter
* Utlös en ML-pipeline när en avvikelse identifieras

## <a name="prerequisites"></a>Förutsättningar
* Deltagar-eller ägar åtkomst till Azure Machine Learning arbets ytan du skapar händelser för.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Konfigurera EventGrid med hjälp av Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com) och gå till arbets ytan Azure Machine Learning.

1. Välj __händelser__ i det vänstra fältet och välj sedan **händelse prenumerationer**. 

    ![Select-events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Välj den händelse typ som ska konsumeras. Följande skärm bild har till exempel valt __modell registrerad__, __modell distribution__, __körning slutförd__och __data uppsättnings avvikelse identifierad__:

    ![Lägg till händelse-typ](./media/how-to-use-event-grid/add-event-type.png)

1. Välj slut punkten att publicera händelsen till. I följande skärm bild är __Event Hub__ den valda slut punkten:

    ![Välj-händelse-hanterare](./media/how-to-use-event-grid/select-event-handler.png)

När du har bekräftat ditt val klickar du på __skapa__. Efter konfigurationen skickas de här händelserna till din slut punkt.

### <a name="configure-eventgrid-using-the-cli"></a>Konfigurera EventGrid med CLI

Du kan antingen installera den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)eller använda Azure Cloud Shell som tillhandahålls som en del av din Azure-prenumeration.

Om du vill installera Event Grid-tillägget använder du följande kommando från CLI:

```azurecli-interactive
az add extension --name eventgrid
```

Följande exempel visar hur du väljer en Azure-prenumeration och skapar en ny händelse prenumeration för Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>Exempel scenarier

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Använd Azure Functions för att distribuera en modell som baseras på Taggar

Ett Azure Machine Learning Model-objekt innehåller parametrar som du kan pivotera distributioner på, till exempel modell namn, version, tagg och egenskap. Modell registrerings händelsen kan utlösa en slut punkt och du kan använda en Azure-funktion för att distribuera en modell baserat på värdet för dessa parametrar.

Ett exempel finns i [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) -lagringsplatsen och följa stegen i **README** -filen.

### <a name="use-a-logic-app-to-send-email-alerts"></a>Använda en Logic app för att skicka e-postaviseringar

Utnyttja [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) för att konfigurera e-post för alla dina händelser. Anpassa med villkor och ange mottagare för att möjliggöra samarbete och medvetenhet mellan team som arbetar tillsammans.

1. Gå till arbets ytan Azure Machine Learning i Azure Portal och välj fliken händelser i det vänstra fältet. Härifrån väljer du __Logic Apps__. 

    ![Select-Logic-AP](./media/how-to-use-event-grid/select-logic-ap.png)

1. Logga in på Logic app UI och välj Machine Learning tjänst som typ av ämne. 

    ![Välj-ämne-typ](./media/how-to-use-event-grid/select-topic-type.png)

1. Välj vilken eller vilka händelser som ska meddelas för. Till exempel visas följande skärm bild __RunCompleted__.

    ![Select-Event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Du kan också lägga till filter för att endast utlösa Logic-appen på en delmängd av händelse typerna. I följande skärm bild används ett __prefix filter__ för __/datadriftID/Runs/__ .

    ![Filtrera händelser](./media/how-to-use-event-grid/filtering-events.png)

1. Lägg sedan till ett steg för att använda den här händelsen och Sök efter e-post. Det finns flera olika e-postkonton som du kan använda för att ta emot händelser. Du kan också konfigurera villkor för när en e-postavisering ska skickas.

    ![Välj-e-post-åtgärd](./media/how-to-use-event-grid/select-email-action.png)

1. Välj __Skicka ett e-postmeddelande__ och fyll i parametrarna. I ämnet kan du inkludera __händelse typen__ och __ämnet__ för att filtrera händelser. Du kan också ta med en länk till sidan arbets yta för körningar i meddelande texten. 

    ![Konfigurera – e-postbrödtext](./media/how-to-use-event-grid/configure-email-body.png)

1. Om du vill spara den här åtgärden väljer du **Spara som** i det vänstra hörnet på sidan. I det högra fältet som visas bekräftar du att åtgärden har skapats.

    ![bekräfta – Logic-app-Create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Använd en Logi Kap par för att utlösa återställnings arbets flöden när data händer

Modeller föråldras över tid och är inte användbara i den kontext som den körs i. Ett sätt att avgöra om det är dags att omträna modellen är att identifiera data drift. 

Det här exemplet visar hur du använder Event Grid med en Azure Logic-app för att utlösa omträning. Exemplet utlöser en Azure Data Factory pipeline när det uppstår en data avvikelse mellan en modells utbildning och betjänar data uppsättningar.

Innan du börjar utför du följande åtgärder:

* Konfigurera en data uppsättnings Övervakare för att [identifiera data drivgarn]( https://aka.ms/datadrift) i en arbets yta
* Skapa en pipeline för publicerade [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/).

I det här exemplet används en enkel Data Factory pipeline för att kopiera filer till ett BLOB-lager och köra en publicerad Machine Learning-pipeline. Mer information om det här scenariot finns i så här konfigurerar du ett [Machine Learning steg i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF-mlpipeline – steg](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Börja med att skapa Logic app. Gå till [Azure Portal](https://portal.azure.com), sök efter Logic Apps och välj Skapa.

    ![Sök efter logik – app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Fyll i den begärda informationen. För att förenkla upplevelsen kan du använda samma prenumeration och resurs grupp som Azure Data Factory pipeline och Azure Machine Learning arbets yta.

    ![Konfigurera-Logic-app-för-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. När du har skapat Logic-appen väljer du __när en Event Grid resurs händelse inträffar__. 

    ![Select-Event-Grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Logga in och fyll i informationen om händelsen. Ange __resurs namnet__ till arbets ytans namn. Ange __händelse typen__ till __DatasetDriftDetected__.

    ![Logga in och lägga till händelse](./media/how-to-use-event-grid/login-and-add-event.png)

1. Lägg till ett nytt steg och Sök efter __Azure Data Factory__. Välj __skapa en pipeline-körning__. 

    ![Create-adfpipeline-Run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Logga in och ange den publicerade Azure Data Factory pipelinen som ska köras.

    ![Ange – ADF-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Spara och skapa Logic-appen med knappen **Spara** längst upp till vänster på sidan. Om du vill visa din app går du till din arbets yta i [Azure Portal](https://portal.azure.com) och klickar på **händelser**.

    ![Visa Logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu utlöses Data Factory-pipelinen när en avvikelse inträffar. Visa information om data körnings-och maskin inlärnings pipelinen på den [nya arbets ytans Portal](https://ml.azure.com). 

![Visa-in-arbetsyte](./media/how-to-use-event-grid/view-in-workspace.png)


## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga händelser finns i [Azure Machine Learning händelse schema](/azure/event-grid/event-schema-machine-learning)
