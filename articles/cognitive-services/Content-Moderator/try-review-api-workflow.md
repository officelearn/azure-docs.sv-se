---
title: Innehållsmoderering arbetsflöden från API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder innehållsmoderering arbetsflöden från API-konsolen.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 4ef8951b30fa7aede08a1af3c834192b5ed18649
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223858"
---
# <a name="workflows-from-the-api-console"></a>Arbetsflöden från API-konsol

Använd den [arbetsflödesåtgärder](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) i Azure Content Moderator för att skapa eller uppdatera ett arbetsflöde eller hämta information om arbetsflöde med hjälp av API för granskning. Du kan definiera enkel, komplexa och även kapslade uttryck för dina arbetsflöden genom att använda detta API. Arbetsflöden som visas i granskningsverktyg för ditt team och använda. Arbetsflöden som används av granska API-jobbåtgärder.

## <a name="prerequisites"></a>Förutsättningar

1. Gå till den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/). Registrera dig om du inte gjort det ännu. 
2. I Verktyg för granskning under **inställningar**väljer den **arbetsflöden** fliken som visas i Verktyg för granskning [arbetsflöde självstudien](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Bläddra till skärmen arbetsflöden

Content Moderator-instrumentpanelen, klicka **granska** > **inställningar** > **arbetsflöden**. Du kan se ett standardarbetsflöde.

  ![Standardarbetsflöde](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Hämta JSON-definition för standardarbetsflöde

Välj den **redigera** för arbetsflödet och välj sedan den **JSON** fliken. Du ser följande JSON-uttryck:

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>Hämta information om arbetsflöde

Använd den **arbetsflöde – Get** för att få information om ditt befintliga standardarbetsflöde.

I granskningsverktyget, går du till den [autentiseringsuppgifter](Review-Tool-User-Guide/credentials.md#the-review-tool) avsnittet.

### <a name="browse-to-the-api-reference"></a>Bläddra till API-referens

1. I den **autentiseringsuppgifter** väljer [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. När den **arbetsflöde – skapa eller uppdatera** öppnas, gå till den [arbetsflöde – Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) referens.

### <a name="select-your-region"></a>Välj region

För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.

  ![Arbetsflöde – Get valet](images/test-drive-region.png)

  Den **arbetsflöde – Get** API-konsolen öppnas.

### <a name="enter-parameters"></a>Ange parametrar

Ange värden för **team**, **workflowname**, och **Ocp-Apim-Subscription-Key** (din prenumerationsnyckel):

- **team**: lag-ID som du skapade när du ställer in din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: namnet på ditt arbetsflöde. Använd `default`.
- **OCP-Apim-Subscription-Key**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

  ![Hämta frågeparametrar och rubriker](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** visar följande JSON-arbetsflöde:

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Skapa ett arbetsflöde

I granskningsverktyget, går du till den [autentiseringsuppgifter](Review-Tool-User-Guide/credentials.md#the-review-tool) avsnittet.

### <a name="browse-to-the-api-reference"></a>Bläddra till API-referens

I den **autentiseringsuppgifter** väljer [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). Den **arbetsflöde – skapa eller uppdatera** öppnas.

### <a name="select-your-region"></a>Välj region

För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.

  ![Arbetsflöde – skapa eller uppdatera sidan val](images/test-drive-region.png)

  Den **arbetsflöde – skapa eller uppdatera** API-konsolen öppnas.

### <a name="enter-parameters"></a>Ange parametrar

Ange värden för **team**, **workflowname**, och **Ocp-Apim-Subscription-Key** (din prenumerationsnyckel):

- **team**: lag-ID som du skapade när du ställer in din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: namnet på ett nytt arbetsflöde.
- **OCP-Apim-Subscription-Key**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

  ![Arbetsflöde – skapa eller uppdatera konsolen frågeparametrar och rubriker](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Ange arbetsflödesdefinitionen

1. Redigera den **Begärandetext** om du vill ange JSON-begäran med information om **beskrivning** och **typ** (bild eller Text). 
2. För **uttryck**, kopiera standard arbetsflöde uttrycket från föregående avsnitt, som visas här:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Förfrågans brödtext ser ut som följande JSON-begäran:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** rutan visar `true`.

### <a name="check-out-the-new-workflow"></a>Kolla in det nya arbetsflödet

I Verktyg för granskning väljer **granska** > **inställningar** > **arbetsflöden**. Ett nytt arbetsflöde visas och är klart att användas.

  ![Granska verktyget listan över arbetsflöden](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Granska din information om nya arbetsflöde

1. Välj den **redigera** för arbetsflödet och välj sedan den **Designer** och **JSON** flikar.

   ![Designer fliken för ett valt arbetsflöde](images/workflow-console-new-workflow-designer.PNG)

2. Om du vill se JSON-vy av arbetsflödet, Välj den **JSON** fliken.

## <a name="next-steps"></a>Nästa steg

* Exempel mer komplexa arbetsflöden finns i [översikt för arbetsflöden](workflow-api.md).
* Lär dig hur du använder arbetsflöden med [innehåll moderering jobb](try-review-api-job.md).
