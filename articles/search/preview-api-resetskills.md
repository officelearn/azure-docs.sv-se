---
title: Återställa kunskaper (API-version = 2019-050 -06 – för hands version)
titleSuffix: Azure Cognitive Search
description: Förhands granskning REST API som används för stegvis berikning när du behöver hela eller delvis ombearbetning av en färdigheter.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832163"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Återställa kunskaper (API-version = 2019-05 -06 – för hands version)

> [!IMPORTANT] 
> Stegvis anrikning är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande ingen portal eller .NET SDK-support.

Begäran om att **återställa kunskaper** anger vilka kunskaper som ska bearbetas vid nästa körning av indexeraren. För indexerare som har cachelagring aktiverat kan du uttryckligen begära bearbetning för kunskaps uppdateringar som indexeraren inte kan identifiera. Om du till exempel gör externa ändringar, till exempel revisioner till en anpassad färdighet, kan du använda det här API: et för att köra om kunskapen igen. Utdata, till exempel ett kunskaps lager eller sökindex, uppdateras med hjälp av återanvändbara data från cachen och nytt innehåll per uppdaterad kunskap.

Du kan återställa en befintlig [färdigheter](https://docs.microsoft.com/rest/api/searchservice/create-skillset) med en http-placering och ange namnet på den färdigheter som ska uppdateras i begärande-URI: n. Du måste använda **API-version = 2019-05 -06-Preview** på begäran.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Du kan också använda POST och lägga till index namns namnet i bröd texten i begäran:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Begärandehuvud  

 I följande tabell beskrivs de obligatoriska och valfria begärandehuvuden.  

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange `application/json`|  
|*API-nyckel:*|Krävs. `api-key` används för att autentisera begäran till din Sök tjänst. Det är ett sträng värde som är unikt för din tjänst. Färdigheter-begäran för **återställning** måste innehålla ett `api-key`-huvud som är inställt på din administratörs nyckel (till skillnad från en frågegrupp).|  

Du behöver också tjänst namnet för att kunna konstruera URL: en för begäran. Du kan hämta både tjänst namnet och `api-key` från tjänst översikts sidan i Azure Portal. Mer information finns i [skapa en Azure kognitiv sökning-tjänst](https://docs.microsoft.com/azure/search/search-create-service-portal) för sid navigerings hjälp.  

## <a name="request-body-syntax"></a>Syntax för begär ande text  

Bröd texten i begäran är en matris med kunskaps namn.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Svar  

Status kod: 204 inget innehåll för ett lyckat svar. 

## <a name="see-also"></a>Se också

+ [Sök i REST-API: er](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP-statuskoder](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Översikt över AI-anrikning](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Konfigurera cachelagring och stegvis berikning](search-howto-incremental-index.md)
+ [Stegvis anrikning](cognitive-search-incremental-indexing-conceptual.md)