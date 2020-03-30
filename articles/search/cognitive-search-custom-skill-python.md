---
title: Exempel på anpassad färdighet (Python)
titleSuffix: Azure Cognitive Search
description: För Python-utvecklare lär du dig verktygen och teknikerna för att skapa en anpassad färdighet med Azure Functions och Visual Studio. Anpassade kunskaper innehåller användardefinierade modeller eller logik som du kan lägga till i en AI-berikad indexeringspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210473"
---
# <a name="example-create-a-custom-skill-using-python"></a>Exempel: Skapa en anpassad färdighet med Python

I det här exemplet med Azure Cognitive Search skillset får du lära dig hur du skapar en anpassad webb-API-färdighet med Python och Visual Studio Code. I exemplet används en [Azure-funktion](https://azure.microsoft.com/services/functions/) som implementerar det [anpassade färdighetsgränssnittet](cognitive-search-custom-skill-interface.md).

Den anpassade färdigheten är enkel genom design (den sammanfogar två strängar) så att du kan fokusera på de verktyg och tekniker som används för anpassad kompetensutveckling i Python. När du lyckas med en enkel färdighet kan du förgrena dig med mer komplexa scenarier.

## <a name="prerequisites"></a>Krav

+ Granska det [anpassade färdighetsgränssnittet](cognitive-search-custom-skill-interface.md) för en introduktion till indata-/utdatagränssnittet som en anpassad färdighet bör implementera.

+ Konfigurera din miljö. Vi följde [den här självstudien från mot slut](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) för att konfigurera serverlös Azure-funktion med Visual Studio-kod och Python-tillägg. Självstudien leder dig genom installation av följande verktyg och komponenter: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio-kod](https://code.visualstudio.com/)
  + [Python-tillägg för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Grundläggande verktyg för Azure-funktioner](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Azure Functions-tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här exemplet används en Azure-funktion för att demonstrera konceptet värd för ett webb-API, men andra metoder är möjliga. Så länge du uppfyller [gränssnittskraven för en kognitiv färdighet](cognitive-search-custom-skill-interface.md)är det tillvägagångssätt du tar oväsentligt. Azure Functions gör det dock enkelt att skapa en anpassad färdighet.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

Med Azure Functions-projektmallen i Visual Studio Code skapas ett projekt som kan publiceras till en funktionsapp i Azure. Med en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser.

1. Öppna kommandopaletten genom att trycka på F1 i Visual Studio Code. Sök efter och välj `Azure Functions: Create new project...`i kommandopaletten .

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > Dessa steg har utformats för att slutföras utanför en arbetsyta. Välj därför inte en projektmapp som ingår i en arbetsyta.

1. Välj ett språk för funktionsappprojektet. För den här självstudien väljer du **Python**.
1. Välj Python-versionen (version 3.7.5 stöds av Azure Functions)
1. Välj en mall för projektets första funktion. Välj **HTTP-utlösare** om du vill skapa en HTTP-utlösad funktion i den nya funktionsappen.
1. Ange ett funktionsnamn. I det här fallet, låt oss använda **Concatenator** 
1. Välj **Funktion** som auktoriseringsnivå. Detta innebär att vi kommer att tillhandahålla en [funktionsnyckel](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) för att anropa funktionens HTTP-slutpunkt. 
1. Välj hur du vill öppna projektet. I det här steget väljer du **Lägg till arbetsyta för att** skapa funktionsappen på den aktuella arbetsytan.

I Visual Studio Code skapas funktionsappsprojektet på en ny arbetsyta. Det här projektet innehåller konfigurationsfilerna [host.json](../azure-functions/functions-host-json.md) och [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file), plus eventuella språkspecifika projektfiler. 

En ny HTTP-utlöst funktion skapas också i **concatenatormappen** i funktionsappprojektet. Inuti det kommer att finnas\_\_en fil som heter "init__.py", med detta innehåll:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Nu ska vi ändra den koden för att följa det [anpassade färdighetsgränssnittet).](cognitive-search-custom-skill-interface.md) Ändra koden med följande innehåll:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Metoden **transform_value** utför en åtgärd på en enskild post. Du kan ändra metoden för att uppfylla dina specifika behov. Kom ihåg att göra alla nödvändiga indatavalidering och att returnera eventuella fel och varningar som uppstår om åtgärden inte kunde slutföras för posten.

### <a name="debug-your-code-locally"></a>Felsöka koden lokalt

Visual Studio Code gör det enkelt att felsöka koden. Tryck på "F5" eller gå till **Felsökningsmenyn** och välj **Starta felsökning**.

Du kan ställa in brytpunkter på koden genom att trycka på "F9" på raden av intresse.

När du har börjat felsöka körs funktionen lokalt. Du kan använda ett verktyg som Postman eller Fiddler för att utfärda begäran till localhost. Observera platsen för den lokala slutpunkten i terminalfönstret. 

## <a name="publish-your-function"></a>Publicera din funktion

När du är nöjd med funktionsbeteendet kan du publicera det.

1. Öppna kommandopaletten genom att trycka på F1 i Visual Studio Code. Sök efter och välj Distribuera **till funktionsapp**i kommandopaletten. 

1. Välj den Azure-prenumeration där du vill distribuera ditt program.

1. Välj **+ Skapa ny funktionsapp i Azure**

1. Ange ett globalt unikt namn för din funktionsapp.

1. Välj Python-version (Python 3.7.x fungerar för den här funktionen).

1. Välj en plats för den nya resursen (till exempel västra US 2).

Nu skapas nödvändiga resurser i din Azure-prenumeration för att vara värd för den nya Azure-funktionen på Azure. Vänta tills distributionen har slutförts. Utdatafönstret visar distributionsprocessens status.

1. I [Azure-portalen](https://portal.azure.com)navigerar du till **Alla resurser** och letar efter den funktion som du publicerade med dess namn. Om du har döpt den **till Sammanfogare**väljer du resursen.

1. Klicka på knappen **</> Hämta funktionsadress.** På så sätt kan du kopiera webbadressen för att anropa funktionen.

## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har standardvärdentt för att testa funktionen på följande sätt:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Begärandetext
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Det här exemplet bör ge samma resultat som du såg tidigare när du kör funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Ansluta till pipelinen

Nu när du har en ny anpassad färdighet kan du lägga till den i din kompetens. Exemplet nedan visar hur du anropar färdigheten för att sammanfoga dokumentets titel och författare till ett enda fält som vi kallar merged_title_author. Ersätt `[your-function-url-here]` med URL:en för din nya Azure-funktion.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första anpassade färdighet. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. Klicka på följande länkar om du vill veta mer.

+ [Power Skills: en databas med anpassade färdigheter](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Lägga till en anpassad färdighet i en AI-anrikningspipeline](cognitive-search-custom-skill-interface.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Så här mappar du berikade fält](cognitive-search-output-field-mapping.md)
