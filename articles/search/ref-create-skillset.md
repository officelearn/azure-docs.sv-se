---
title: Skapa kunskaper (REST api-version = 2017-11-11-Preview)-Azure Search | Microsoft Docs
description: En kunskaper är en samling med kognitiva kunskaper som utgör en berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Skapa kunskaper (api-version = 2017-11-11-Preview)

**Gäller för:** api-version-2017-11-11-Preview

En kunskaper är en samling av kognitiva kunskaper som används för bearbetning av naturligt språk och andra transformationer. Kunskaper inkluderar namngiven enhet extrahering viktiga frasen extrahering högoptimerat text i logiska sidor, bland annat.

Om du vill använda kunskaper refererar till den i Azure Search indexeraren och kör sedan indexerare för att importera data, anropa omvandlingar och berikande och mappa fälten utdata till ett index. En kunskaper är övergripande resursen, men den fungerar bara i indexeraren bearbetning. Som en övergripande resurs du utforma en kunskaper en gång och sedan använda det i flera indexerare. 

En kunskaper uttrycks i Azure Search via en HTTP PUT- eller POST-begäran. PLACERA-är brödtexten i begäran en JSON-schema som anger vilka kunskaper anropas. Kunskaper är sammankopplade via in-och utdata associationer, där utdata från en transformation blir indata till en annan.

En kunskaper måste ha minst en kunskap. Det finns ingen teoretisk gräns för högsta antalet kunskaper, men tre till fem är en vanlig konfiguration.  


> [!NOTE]
> Cognitive Search är en offentlig förhandsversion och kompetenskörning ges för närvarande kostnadsfritt. Priser för den här funktionen meddelas vid ett senare tillfälle.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Förfrågan  
 HTTPS krävs för alla tjänstbegäranden. Den **skapa kunskaper** begäran kan konstrueras med en PUT-metoden med kunskaper namn som en del av URL: en. Om kunskaper inte finns, skapas. Om det redan finns uppdateras den till den nya definitionen. Observera att du kan bara lägga en kunskaper i taget.  

 Kunskaper namnet måste uppfylla följande krav:

- Vara gemener
- Börja och sluta med en bokstav eller siffra
- Inte ha några snedstreck eller punkter
- Har färre än 128 tecken 

När du har startat namnet kunskaper med en bokstav eller siffra kan resten av namnet innehålla en bokstav, antal och tankstreck så länge streck inte är i följd.  

Den **api-versionen** parametern är obligatorisk. Den enda tillgängliga versionen är `2017-11-11-Preview`. Se [API-versioner i Azure Search](https://go.microsoft.com/fwlink/?linkid=834796) en lista över alla versioner. 


### <a name="request-headers"></a>Begärandehuvud  

 I följande tabell beskrivs de obligatoriska och valfria begärandehuvudena.  

|Huvudet i begäran|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ställ in på `application/json`|  
|*API-nyckel:*|Krävs. Den `api-key` används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **skapa kunskaper** begäran måste innehålla en `api-key` huvudet inställt till din administrationsnyckel (i stället för en fråga nyckel).|  

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta både tjänstens namn och `api-key` från tjänsten instrumentpanelen i Azure-portalen. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.  

### <a name="request-body-syntax"></a>Begäran brödtext syntax  

Brödtexten i begäran innehåller definitionen av kunskaper, som består av en eller flera specifika fullständigt kunskaper samt valfria parametrar för namn och beskrivning.  

Syntaxen för att strukturera nyttolasten i begäran är som följer. Ett exempel på begäran tillhandahålls nedan och även i [hur du definierar en kunskaper](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Exempel på begäran
 I följande exempel skapas en kunskaper som används för att utöka en samling med finansiella dokument.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Brödtexten i begäran är en JSON-dokument. Den här särskilda kunskaper använder två kunskaper asynkront, oberoende bearbetning av den `contents` som två olika transformationer. Du kan också dirigera utdata från en omvandling ska indata för en annan. Mer information finns i [hur du definierar en kunskaper](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Svar  

 Du bör se statuskod ”201 skapad” för en lyckad begäran.  

 Som standard innehåller svarstexten JSON för definitionen kunskaper som har skapats. Men om begärandehuvudet Prefer har angetts till returnerar = minimal svarstexten är tomt och kommer att lyckas statuskoden ”204 saknar innehåll” i stället för ”201 skapad”. Detta gäller oavsett om PUT eller POST används för att skapa kunskaper.   

## <a name="see-also"></a>Se också

+ [Översikt över kognitiva sökning](cognitive-search-concept-intro.md)
+ [Snabbstart: Försök kognitiva sökning](cognitive-search-quickstart-blob.md)
+ [Självstudier: Utökat indexering av Azure-blobbar](cognitive-search-tutorial-blob.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Mappning](cognitive-search-output-field-mapping.md)
+ [Hur du definierar ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Exempel: skapa en anpassad kunskap](cognitive-search-create-custom-skill-example.md)
+ [Fördefinierade sklls](cognitive-search-predefined-skills.md)