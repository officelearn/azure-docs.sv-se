---
title: Azure innehåll kontrollant - Avbrottsmoderering arbetsflöden | Microsoft Docs
description: Använda arbetsflöden med innehåll måtta.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 079fcd119f1536f9e76ca57fccc76538b3c3ed78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351864"
---
# <a name="moderation-workflows"></a>Avbrottsmoderering arbetsflöden

Innehåll kontrollant innehåller verktyg och API: er för att hantera arbetsflöden. Du använder arbetsflöden med den [granska API jobbåtgärder](review-api.md) vill automatisera genereringen av hr-i-the-loop granska baserat på innehåll principer och tröskelvärden.

Granska API ger följande sätt att inkludera mänsklig tillsyn i innehåll avbrottsmoderering processen:

1. Den **jobbet** åtgärder för start av dator-stödd avbrottsmoderering och mänskliga granska skapas som ett steg.
1. Den **granska** åtgärder för mänskliga granska skapas utanför steget måtta.
1. Den **arbetsflöde** åtgärder för att hantera arbetsflöden som automatiserar skanning med tröskelvärden för granska skapas.

Den här artikeln beskriver den **arbetsflöde** åtgärder. Läs den [jobb och granskar](review-api.md) översikt att lära dig om innehåll avbrottsmoderering jobb och går igenom.

Checka ut den **standard** arbetsflödet är det bästa sättet att komma igång med förstår arbetsflöden innehåll kontrollant.

## <a name="your-first-workflow"></a>Första arbetsflödet

Första arbetsflödet medföljer din [granska verktyget team](https://contentmoderator.cognitive.microsoft.com/). Registrera dig om du inte har gjort det redan.

Navigera till den [granska verktygets arbetsflöden](Review-Tool-User-Guide/Workflows.md) skärmen på fliken Inställningar. Du ser en **standard** arbetsflöde som visas i följande bild:

![Innehåll kontrollant arbetsflöden](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Öppna standardarbetsflöde

Använd den **redigera** möjligheten att öppna arbetsflödet redigerar sida som visas i följande bild: ![innehåll kontrollant standardarbetsflöde](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Vyn designer

Du ser den **Designer** för arbetsflödet. Frågedesigner innehåller följande steg:

1. Den **villkoret** för arbetsflödet som ska utvärderas. I det här fallet arbetsflödet anropen innehåll kontrollantens bild API och kontrollerar om den `isAdult` utdata är lika med `true`.
1. Den **åtgärd** som ska utföras om villkoret är uppfyllt. I det här fallet arbetsflödet skapar ett omdöme i verktyget granska om den `isAdult` utdata är `true`.

![Innehåll kontrollant standardarbetsflöde - designer](images/default-workflow-designer.png)

### <a name="the-json-view"></a>JSON-vyn

Välj den **JSON** fliken för att se JSON-definitionen av arbetsflödet.

    {
        "Type": "Logic",
        If": {
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

### <a name="key-learning"></a>Viktiga learning

Arbetsflöden innehåll kontrollant är enkel att konfigurera och flexibel. Om inbyggda designern inte uppfyller dina krav, skriva arbetsflödesdefinitionen i den **JSON** format. Använda JSON-definitionen med den [arbetsflöde API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) du skapar och hanterar arbetsflödet från ditt program.

## <a name="define-a-custom-workflow"></a>Definiera ett anpassat arbetsflöde

Innehåll kontrollant arbetsflödet funktionerna att definiera och använda anpassade arbetsflöden. Använd den [granska arbetsflöden anvisningar](Review-Tool-User-Guide/Workflows.md) artikel för att definiera ett anpassat arbetsflöde. Det här arbetsflödet använder innehåll kontrollant OCR kapaciteten för att extrahera text från en avbildning som exempel. Den skapar sedan en granskning i verktyget granska.

### <a name="the-sample-image"></a>Exempelbild

Spara den [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) till din lokala enhet. Du behöver den här avbildningen för din övningen.

### <a name="the-designer-view"></a>Vyn designer

Välj den **Designer** fliken och [arbetsflöde skapa kursen](Review-Tool-User-Guide/Workflows.md) att definiera ett anpassat arbetsflöde. Följande bild visar designern **villkoret** vyn. Referera till guiden för att se resten av stegen.

![Innehåll kontrollant - arbetsflödesvillkor](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>JSON-vyn

Välj den **JSON** fliken för att se följande JSON-definitionen av anpassade arbetsflödet. Observera hur **om sedan** instruktioner i JSON-definitionen motsvarar de steg som du har definierat designer i vyn.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Resultatet av arbetsflöde

När du har testat arbetsflödet från skärmen arbetsflöden skapas följande. Navigera till den **bild** fliken **granska** att se din granskning.
Arbetsflödet skapa granskningen eftersom primära villkor testas positivt för förekomsten av texten. Granska också den **`a`** tagg i avbildningen granskning.

![Innehåll kontrollant - enkelt arbetsflöde utdata](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Avancerade arbetsflöde med kombination

### <a name="the-sample-image"></a>Exempelbild

Använda samma [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) som användes i föregående avsnitt.

Den här gången runt dock ändra din primära villkor i en kombination av båda kontrollerna. Kontrollera om det finns några svordomar förutom söker efter text. Arbetsflödet skapar ett omdöme om text hittas **och** identifierar svordomar i den.

### <a name="the-designer-view"></a>Vyn designer

Så här ändrar du den **villkoret** till en **kombination**, ändra arbetsflödet. Följande bild visar den nya vyn visas i designern.

![Innehåll kontrollant - ändrade arbetsflödesvillkor](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>JSON-vyn

Välj den **JSON** fliken för att se följande JSON-definitionen av anpassade ändrade arbetsflödet. Observera hur **om sedan** instruktioner i JSON-definitionen motsvarar de nya åtgärder som du har lagt till i arbetsflödet.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Resultatet av arbetsflöde

Du upptäcker att inga granska skapas när du har testat arbetsflödet igen. För att bekräfta frånvaro ses, navigerar du till den **bild** fliken **granska**.
Arbetsflödet har inte skapat granskningen eftersom den inte gick att identifiera svordomar i den extraherade texten.

![Innehåll kontrollant - ändrade arbetsflödet utdata](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Arbetsflödet API

Den [arbetsflödesåtgärder](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) ange programmeringsgränssnitt för arbetsflödesfunktioner för. Du skapar arbetsflöden, hämta arbetsflödesinformation om och uppdatera arbetsflödesdefinitioner för med hjälp av arbetsflödet API.

### <a name="get-all-workflow-details"></a>Hämta [All] arbetsflödesinformation

Den **arbetsflödet Get** åtgärden accepterar indata som följande:

- **team**: team-ID som du skapade när du konfigurerar din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: namnet på arbetsflödet. Använd `default` börja med.
- **OCP-Apim-prenumeration-nyckeln**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

Om åtgärden lyckas den **svarsstatusen** är `200 OK` och **svar innehåll** arbetsflödesdefinitionen visar i JSON-format.
Mer information finns i [arbetsflöde API konsolen quickstart](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Skapa eller uppdatera arbetsflöde

Skapa och uppdatera åtgärden kan skapa arbetsflöde från API: et.

Den **arbetsflöde – skapa eller uppdatera** åtgärden accepterar indata som följande:

- **team**: team-ID som du skapade när du konfigurerar din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: namnet på arbetsflödet. Använd `default` börja med.
- **OCP-Apim-prenumeration-nyckeln**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

Om åtgärden lyckas den **svarsstatusen** är `200 OK` och **svar innehåll** rutan visar `true`. Mer information, [Testkör den `Create` åtgärden](try-review-api-job.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar anpassade arbetsflöden, ta en titt på [granska verktygets arbetsflödet kursen](Review-Tool-User-Guide/Workflows.md). 

Testkör den [arbetsflöde API konsolen](try-review-api-job.md) och använda REST API-kodexempel. 

Slutligen använder dina anpassade arbetsflöden med den **jobbet** åtgärder som shon i [jobbet API konsolen](try-review-api-job.md) och [jobb .NET quickstart](moderation-jobs-quickstart-dotnet.md).
