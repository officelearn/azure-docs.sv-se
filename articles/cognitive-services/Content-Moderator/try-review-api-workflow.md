---
title: Azure innehåll kontrollant - innehåll avbrottsmoderering arbetsflöden från konsolen API | Microsoft Docs
description: Lär dig hur du använder innehåll avbrottsmoderering arbetsflöden från konsolen API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 700b2bea5e902141659266a94d61ceb810c1b802
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351774"
---
# <a name="workflows-from-the-api-console"></a>Arbetsflöden från konsolen API

Använd den [arbetsflödesåtgärder](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) i Azure Content kontrollant att skapa eller uppdatera ett arbetsflöde eller hämta arbetsflödesinformation om med granska API. Du kan definiera enkla, komplexa och även kapslade uttryck för dina arbetsflöden med hjälp av den här API. Arbetsflöden som visas i verktyget granska för ditt team som ska användas. Arbetsflöden som används av granska API jobbåtgärder.

## <a name="prerequisites"></a>Förutsättningar

1. Gå till den [granska verktyget](https://contentmoderator.cognitive.microsoft.com/). Registrera dig om du inte gjort det ännu. 
2. I verktyget granska under **inställningar**, Välj den **arbetsflöden** fliken som visas i verktyget granska [arbetsflöde kursen](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Bläddra till skärmen arbetsflöden

På instrumentpanelen innehåll kontrollant väljer **granska** > **inställningar** > **arbetsflöden**. Du ser ett standardarbetsflöde.

  ![Standardarbetsflöde](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Hämta JSON-definitionen av standardarbetsflöde

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

## <a name="get-workflow-details"></a>Hämta arbetsflödesinformation om

Använd den **arbetsflöde - Get** åtgärden att få information om befintliga standardarbetsflödet.

I verktyget granska går du till den [autentiseringsuppgifter](Review-Tool-User-Guide/credentials.md#the-review-tool) avsnitt.

### <a name="browse-to-the-api-reference"></a>Bläddra till API-referens

1. I den **autentiseringsuppgifter** väljer [API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. När den **arbetsflöde – skapa eller uppdatera** öppnas, gå till den [arbetsflöde - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) referens.

### <a name="select-your-region"></a>Välj din region

För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.

  ![Arbetsflöde – val av Get](images/test-drive-region.png)

  Den **arbetsflöde - Get** API-konsolen öppnas.

### <a name="enter-parameters"></a>Ange parametrar

Ange värden för **team**, **workflowname**, och **Ocp-Apim-prenumeration-nyckeln** (din prenumeration nyckel):

- **team**: team-ID som du skapade när du konfigurerar din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: namnet på arbetsflödet. Använd `default`.
- **OCP-Apim-prenumeration-nyckeln**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

  ![Hämta frågeparametrar och rubriker](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatusen** är `200 OK`, och **svar innehåll** visar följande JSON-arbetsflöde:

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

I verktyget granska går du till den [autentiseringsuppgifter](Review-Tool-User-Guide/credentials.md#the-review-tool) avsnitt.

### <a name="browse-to-the-api-reference"></a>Bläddra till API-referens

I den **autentiseringsuppgifter** väljer [API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). Den **arbetsflöde – skapa eller uppdatera** öppnas.

### <a name="select-your-region"></a>Välj din region

För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.

  ![Arbetsflöde – skapa eller uppdatera sidan val](images/test-drive-region.png)

  Den **arbetsflöde – skapa eller uppdatera** API-konsolen öppnas.

### <a name="enter-parameters"></a>Ange parametrar

Ange värden för **team**, **workflowname**, och **Ocp-Apim-prenumeration-nyckeln** (din prenumeration nyckel):

- **team**: team-ID som du skapade när du konfigurerar din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: namnet på ett nytt arbetsflöde.
- **OCP-Apim-prenumeration-nyckeln**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

  ![Arbetsflöde – skapa eller uppdatera konsolen frågeparametrar och rubriker](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Ange arbetsflödesdefinitionen

1. Redigera den **Begärandetext** om du vill ange JSON-förfrågan med information om **beskrivning** och **typ** (bild eller Text). 
2. För **uttryck**, kopiera standarduttrycket för arbetsflödet från föregående avsnitt, som visas här:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Frågans brödtext ser ut som följande JSON-förfrågan:

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
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatusen** är `200 OK`, och **svar innehåll** rutan visar `true`.

### <a name="check-out-the-new-workflow"></a>Kolla in det nya arbetsflödet

Välj i verktyget granska **granska** > **inställningar** > **arbetsflöden**. Ett nytt arbetsflöde visas och är redo att användas.

  ![Granska verktyget listan med arbetsflöden](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Granska dina nya arbetsflödesinformation

1. Välj den **redigera** för arbetsflödet och välj sedan den **Designer** och **JSON** flikar.

   ![Designer fliken för ett valt arbetsflöde](images/workflow-console-new-workflow-designer.PNG)

2. Om du vill visa arbetsflödet JSON, Välj den **JSON** fliken.

## <a name="next-steps"></a>Nästa steg

* Mer komplexa arbetsflöden exempel finns i [arbetsflöden översikt](workflow-api.md).
* Lär dig hur du använder arbetsflöden med [innehåll avbrottsmoderering jobb](try-review-api-job.md).
