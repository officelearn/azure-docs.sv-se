---
title: Moderering arbetsflöden – Content Moderator
titlesuffix: Azure Cognitive Services
description: Använda arbetsflöden med granska API-jobbet åtgärderna för att automatisera human-i-the-loop granskningar baserat på din innehållsprinciper och tröskelvärden.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 56b3bffc7aca1ace4dfae78cdcd4394daae9360e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872802"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Automatisera moderering granskningar med arbetsflöden

Content Moderator ingår verktyg och API: er för att hantera arbetsflöden. Du använder arbetsflöden med den [granska API jobbåtgärder](review-api.md) att automatisera human-i-the-loop granskning skapas baserat på ditt innehåll och tröskelvärden.

Granska API: et erbjuder de följande sätten att tar med mänsklig övervakning i innehållsmoderering processen:

1. Den **jobbet** åtgärder för att starta datorstödd moderering och mänskliga granska skapas som ett steg.
1. Den **granska** åtgärder för mänskliga granska skapas utanför moderering steg.
1. Den **arbetsflöde** åtgärder för hantering av arbetsflöden som automatiserar sökning med tröskelvärden för granska skapas.

Den här artikeln beskriver den **arbetsflöde** åtgärder. Läs den [jobb och granskar](review-api.md) översikt om du vill veta mer om innehållsmoderering jobb och går igenom.

Checka ut den **standard** arbetsflödet är det bästa sättet att komma igång med förstå arbetsflöden i Content Moderator.

## <a name="your-first-workflow"></a>Ditt första arbetsflöde

Ditt första arbetsflöde medföljer din [granska verktyget team](https://contentmoderator.cognitive.microsoft.com/). Registrera dig om du inte gjort det redan.

Navigera till den [granska verktygets arbetsflöden](Review-Tool-User-Guide/Workflows.md) skärmen under fliken Inställningar. Du ser en **standard** arbetsflöde enligt följande bild:

![Content Moderator-arbetsflöden](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Öppna standardarbetsflöde

Använd den **redigera** möjligheten att öppna arbetsflödet redigerar sida som visas i följande bild: ![Content Moderator standardarbetsflöde](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Vyn designer

Du ser den **Designer** flik för arbetsflödet. I designern visas följande steg:

1. Den **villkor** för arbetsflödet som ska utvärderas. I det här fallet arbetsflöde anrop Content Moderators bild-API och kontroller om den `isAdult` utdata är lika med `true`.
1. Den **åtgärd** som ska utföras om villkoret är uppfyllt. I det här fallet arbetsflödet skapar en granskning i granskningsverktyget om den `isAdult` utdata är `true`.

![Content Moderator standardarbetsflöde - designer](images/default-workflow-designer.png)

### <a name="the-json-view"></a>JSON-vy

Välj den **JSON** fliken för att se JSON-definitionen för arbetsflödet.

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

Arbetsflöden i Content Moderator är enkelt att konfigurera och flexibla. Om den inbyggda designern inte uppfyller dina krav, skriva arbetsflödesdefinitionen i den **JSON** format. Använda JSON-definition med den [arbetsflöde API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) du skapar och hanterar arbetsflödet från ditt program.

## <a name="define-a-custom-workflow"></a>Definiera en anpassad arbetsflöde

Funktioner för Content Moderator-arbetsflödet gör att definiera och använda anpassade arbetsflöden. Använd den [granska arbetsflöden how-to](Review-Tool-User-Guide/Workflows.md) artikeln om du vill definiera en anpassad arbetsflöde. Det här arbetsflödet använder Content Moderator OCR-funktionen för att extrahera text från en exempelbild. En granskning skapas sedan i granskningsverktyget.

### <a name="the-sample-image"></a>Exempelbild

Spara den [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) till din lokala enhet. Du behöver den här avbildningen för din övning.

### <a name="the-designer-view"></a>Vyn designer

Välj den **Designer** fliken och [arbetsflöde skapa självstudien](Review-Tool-User-Guide/Workflows.md) att definiera en anpassad arbetsflöde. Följande bild visar designern **villkor** vy. Referera till självstudie för att se resten av stegen.

![Content Moderator - arbetsflödesvillkor](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>JSON-vy

Välj den **JSON** fliken för att se följande JSON-definition för ditt eget arbetsflöde. Observera hur **If-Then** instruktioner i JSON-definition som motsvarar de steg som du har definierat med hjälp av vyn designer.

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

### <a name="workflow-result"></a>Arbetsflödesresultat

När du har testat arbetsflödet från skärmen arbetsflöden skapas följande granskningen. Navigera till den **bild** fliken **granska** att se din granskning.
Arbetsflödet skapa granskningen eftersom det primära villkoret testas positivt för förekomsten av texten. Granska även markerat den **`a`** tagg i granskningen bild.

![Content Moderator – enkel arbetsflödets utdata](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Avancerad arbetsflödesmodul med kombination

### <a name="the-sample-image"></a>Exempelbild

Använd samma [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) som användes i föregående avsnitt.

Dock ändra den här gången primära villkoret i en kombination av två kontroller. Kontrollera om det finns några svordomar, förutom kontroll för text. Arbetsflödet skapar en granskning om text hittas **och** identifierar svordomar i den.

### <a name="the-designer-view"></a>Vyn designer

Ändra den **villkor** till en **kombination**, ändra arbetsflödet. Följande bild visar den nya vyn visas i designern.

![Content Moderator - ändrade arbetsflödesvillkor](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>JSON-vy

Välj den **JSON** fliken för att se följande JSON-definition ändrade anpassade arbetsflödet. Observera hur **If-Then** instruktioner i JSON-definition som motsvarar de nya åtgärder som du har lagt till i arbetsflödet.

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

    
### <a name="workflow-result"></a>Arbetsflödesresultat

Du upptäcker att inga granskning skapas när du har testat arbetsflödet igen. För att bekräfta att det inte finns någon granskning, navigera till den **bild** fliken **granska**.
Arbetsflödet har inte skapat granskningen eftersom det inte gick att identifiera olämpligt språk på extrahera texten.

![Content Moderator - ändrade arbetsflödets utdata](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Arbetsflödet API

Den [arbetsflödesåtgärder](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) ger programmeringsgränssnitt för arbetsflödesfunktioner. Du skapar arbetsflöden, få information om arbetsflöde och uppdaterar arbetsflödesdefinitioner med hjälp av API för arbetsflödet.

### <a name="get-all-workflow-details"></a>Information om arbetsflöde Get [All]

Den **arbetsflödet Get** åtgärden godkänner följande indata:

- **team**: Lag-ID som du skapade när du ställer in din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Namnet på ditt arbetsflöde. Använd `default` börja med.
- **Ocp-Apim-Subscription-Key**: På den **inställningar** fliken. Mer information finns i [Översikt](overview.md).

Om åtgärden lyckas den **svarsstatus** är `200 OK` och **svarsinnehåll** arbetsflödesdefinitionen visar JSON-format.
Mer information finns i [arbetsflöde API konsolen Snabbstart](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Skapa eller uppdatera arbetsflödet

Skapande och uppdatering igen kan du skapa arbetsflödet från API: et.

Den **arbetsflöde – skapa eller uppdatera** åtgärden godkänner följande indata:

- **team**: Lag-ID som du skapade när du ställer in din [granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Namnet på ditt arbetsflöde. Använd `default` börja med.
- **Ocp-Apim-Subscription-Key**: På den **inställningar** fliken. Mer information finns i [Översikt](overview.md).

Om åtgärden lyckas den **svarsstatus** är `200 OK` och **svarsinnehåll** rutan visar `true`. Mer information, [Testkör den `Create` åtgärden](try-review-api-job.md).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skapa anpassade arbetsflöden, Kolla in den [granska verktygets arbetsflöde självstudien](Review-Tool-User-Guide/Workflows.md). 

Testkör den [arbetsflöde för API-konsol](try-review-api-job.md) och använda REST API-kodexempel. 

Använd slutligen dina anpassade arbetsflöden med den **jobbet** åtgärder som shon i [jobbet API-konsol](try-review-api-job.md) och [Snabbstart för jobb .NET](moderation-jobs-quickstart-dotnet.md).
