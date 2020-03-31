---
title: Skapa händelsedrivna maskininlärningsarbetsflöden
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder händelserutnät med Azure Machine Learning för att aktivera händelsedrivna lösningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129721"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Skapa händelsedrivna arbetsflöden för maskininlärning (förhandsversion)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) stöder Azure Machine Learning-händelser. Du kan prenumerera och använda händelser som körstatus ändrad, kör slutförande, modellregistrering, modelldistribution och datadriftidentifiering inom en arbetsyta.

Mer information om händelsetyper finns i [Azure Machine Learning-integrering med Event Grid](concept-event-grid-integration.md) och Azure Machine [Learning-händelserutnätets schema](/azure/event-grid/event-schema-machine-learning).

Använd Händelserutnät för att aktivera vanliga scenarier som:

* Skicka e-postmeddelanden vid körningsfel och kör slutförande
* Använda en azure-funktion när en modell har registrerats
* Strömma händelser från Azure Machine Learning till olika slutpunkter
* Utlösa en ML-pipeline när drift upptäcks

> [!NOTE] 
> Kör StatusChanged-händelser utlöses för närvarande endast när körningsstatusen **misslyckades**
>

## <a name="prerequisites"></a>Krav
* Deltagare eller ägare tillgång till Azure Machine Learning arbetsyta som du kommer att skapa händelser för.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Konfigurera EventGrid med Azure-portalen

1. Öppna [Azure-portalen](https://portal.azure.com) och gå till din Azure Machine Learning-arbetsyta.

1. Välj __Händelser__ i det vänstra fältet och välj sedan **Händelseprenumerationer**. 

    ![välj-händelser-i-arbetsyta.png](./media/how-to-use-event-grid/select-event.png)

1. Välj den händelsetyp som ska förbrukas. Följande skärmbild har till exempel valt __Modell registrerad,__ __Modell distribuerad,__ __Kör slutförd__och __Dataset drift upptäckt:__

    ![tilläggshändelsetyp](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Välj den slutpunkt som händelsen ska publiceras på. I följande skärmbild är __händelsehubben__ den valda slutpunkten:

    ![välj-händelse-hanterare](./media/how-to-use-event-grid/select-event-handler.png)

När du har bekräftat ditt val klickar du på __Skapa__. Efter konfigurationen kommer dessa händelser att skickas till slutpunkten.


### <a name="configure-eventgrid-using-the-cli"></a>Konfigurera EventGrid med CLI

Du kan antingen installera den senaste [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)eller använda Azure Cloud Shell som tillhandahålls som en del av din Azure-prenumeration.

Om du vill installera händelserutnätets tillägg använder du följande kommando från CLI:

```azurecli-interactive
az add extension --name eventgrid
```

I följande exempel visas hur du väljer en Azure-prenumeration och skapar e en ny händelseprenumeration för Azure Machine Learning:

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

## <a name="filter-events"></a>Filtrera händelser

När du konfigurerar dina händelser kan du använda filter för att endast utlösa på specifika händelsedata. I exemplet nedan, för körning status ändrade händelser, kan du filtrera efter körningstyper. Händelsen utlöses bara när villkoren är uppfyllda. Läs [azure machine learning-händelserutnätets schema](/azure/event-grid/event-schema-machine-learning) om du vill veta mer om händelsedata som du kan filtrera efter. 

1. Gå till Azure-portalen, välj en ny prenumeration eller en befintlig. 

1. Välj filterfliken och rulla nedåt till Avancerade filter. I **nyckeln** och **värdet** finns de egenskapstyper som du vill filtrera efter. Här kan du se händelsen kommer bara att utlösa när körningstypen är en pipeline-körning eller pipeline stegkörning.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrera händelser":::

## <a name="sample-scenarios"></a>Exempelscenarier

### <a name="use-a-logic-app-to-send-email-alerts"></a>Använda en Logikapp för att skicka e-postaviseringar

Utnyttja [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) för att konfigurera e-postmeddelanden för alla dina händelser. Anpassa med villkor och ange mottagare för att möjliggöra samarbete och medvetenhet mellan team som arbetar tillsammans.

1. Gå till arbetsytan Azure Machine Learning i Azure Machine Learning och välj fliken händelser i det vänstra fältet. Härifrån väljer du __Logic-appar__. 

    ![välj logik-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Logga in i logikappgränssnittet och välj Machine Learning-tjänsten som ämnestyp. 

    ![välj-ämne-typ](./media/how-to-use-event-grid/select-topic-type.png)

1. Välj vilka händelser som ska meddelas. Till exempel följande skärmdump __RunCompleted__.

    ![välj-händelse-runkompkulera](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Du kan använda filtreringsmetoden i avsnittet ovan eller lägga till filter för att bara utlösa logikappen på en delmängd av händelsetyper. I följande skärmbild används ett __prefixfilter__ av __/datadriftID/runs/.__

    ![filter-händelser](./media/how-to-use-event-grid/filtering-events.png)

1. Lägg sedan till ett steg för att använda den här händelsen och söka efter e-post. Det finns flera olika e-postkonton som du kan använda för att ta emot händelser. Du kan också konfigurera villkor för när du ska skicka en e-postavisering.

    ![välj-e-åtgärd](./media/how-to-use-event-grid/select-email-action.png)

1. Välj __Skicka ett e-postmeddelande__ och fyll i parametrarna. I ämnet kan du inkludera __händelsetypen__ och __ämnet för__ att filtrera händelser. Du kan också inkludera en länk till arbetsytan för körningar i meddelandetexten. 

    ![konfigurera-e-post-brödtext](./media/how-to-use-event-grid/configure-email-body.png)

1. Om du vill spara den här åtgärden väljer du **Spara som** i det vänstra hörnet på sidan. Bekräfta skapandet av åtgärden från den högra stapeln som visas.

    ![bekräfta-logik-app-skapa](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Använda en Logikapp för att utlösa omskolningsarbetsflöden när dataavdrift inträffar

Modeller går inaktuella med tiden och förblir inte användbara i det sammanhang som körs i. Ett sätt att avgöra om det är dags att omskola modellen är att upptäcka datadrift. 

Det här exemplet visar hur du använder händelserutnät med en Azure Logic App för att utlösa omskolning. Exemplet utlöser en Azure Data Factory-pipeline när datadrift uppstår mellan en modells utbildning och betjänar datauppsättningar.

Innan du börjar utför du följande åtgärder:

* Konfigurera en datauppsättningsövervakare för att [identifiera datadrift]( https://aka.ms/datadrift) på en arbetsyta
* Skapa en publicerad [Azure Data Factory pipeline](https://docs.microsoft.com/azure/data-factory/).

I det här exemplet används en enkel Data Factory-pipeline för att kopiera filer till ett blob-arkivet och köra en publicerad Machine Learning-pipeline. Mer information om det här scenariot finns i hur du konfigurerar ett [Machine Learning-steg i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-steg](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Börja med att skapa logikappen. Gå till [Azure-portalen,](https://portal.azure.com)sök efter Logic Apps och välj skapa.

    ![sök-logik-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Fyll i den begärda informationen. För att förenkla upplevelsen, använd samma prenumeration och resursgrupp som din Azure Data Factory Pipeline och Azure Machine Learning arbetsyta.

    ![konfigurera-logik-app-för-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. När du har skapat logikappen väljer du __När en händelse för resurshändelse för händelserutnät inträffar__. 

    ![välj-händelse-rutnät-utlösare](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Logga in och fyll i information för evenemanget. Ange __resursnamnet__ till arbetsytans namn. Ange __händelsetypen__ till __DatasetDriftDetected__.

    ![inloggning och tilläggshändelse](./media/how-to-use-event-grid/login-and-add-event.png)

1. Lägg till ett nytt steg och sök efter __Azure Data Factory__. Välj __Skapa en pipeline-körning__. 

    ![skapa-adfpipeline-körning](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Logga in och ange den publicerade Azure Data Factory-pipelinen som ska köras.

    ![ange-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Spara och skapa logikappen med hjälp av **spara-knappen** längst upp till vänster på sidan. Om du vill visa din app går du till arbetsytan i [Azure-portalen](https://portal.azure.com) och klickar på **Händelser**.

    ![visa-logik-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu utlöses datafabrikspipelinen när drift inträffar. Visa information om datadriftkörning och pipeline för maskininlärning på den [nya arbetsytan.](https://ml.azure.com) 

![visa-i-arbetsyta](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Använda Azure Functions för att distribuera en modell baserat på taggar

Ett Azure Machine Learning-modellobjekt innehåller parametrar som du kan pivotera distributioner på till exempel modellnamn, version, tagg och egenskap. Modellregistreringshändelsen kan utlösa en slutpunkt och du kan använda en Azure-funktion för att distribuera en modell baserat på värdet av dessa parametrar.

Ett exempel finns [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) i databasen och följ stegen i **readme-filen.**

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga händelser finns i [azure machine learning-händelseschemat](/azure/event-grid/event-schema-machine-learning)
