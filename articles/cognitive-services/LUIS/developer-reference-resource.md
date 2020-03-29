---
title: Utvecklarresurser - Språkförståelse
description: SDK:er, REST API:er, CLI, hjälper dig att utveckla LUIS-appar (Language Understanding) i programmeringsspråket. Hantera dina Azure-resurser och LUIS-förutsägelser.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457992"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Resurser för SDK-, REST- och CLI-utvecklare för språk understanding (LUIS)

SDK:er, REST API:er, CLI, hjälper dig att utveckla LUIS-appar (Language Understanding) i programmeringsspråket. Hantera dina Azure-resurser och LUIS-förutsägelser.

## <a name="azure-resource-management"></a>Azure-resurshantering

Använd Azure Cognitive Services Management-lagret för att skapa, redigera, lista och ta bort språkförståelse eller kognitiv tjänstresurs.

Hitta referensdokumentation baserat på verktyget:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Redigerings- och förutsägelsebegäranden för språkförståelse

Tjänsten Språk understanding nås från en Azure-resurs som du behöver skapa. Det finns två resurser:

* Använd **redigeringsresursen** för utbildning för att skapa, redigera, träna och publicera.
* Använd **förutsägelsen** för körning för att skicka användarens text och få en förutsägelse.

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Använd [Cognitive Services exempelkod](https://github.com/Azure-Samples/cognitive-services-quickstart-code) för att lära dig och använda de vanligaste uppgifterna.

### <a name="rest-specifications"></a>REST-specifikationer

[LUIS REST-specifikationerna](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), tillsammans med alla [Azure REST-specifikationer,](https://github.com/Azure/azure-rest-api-specs)är allmänt tillgängliga på GitHub.

### <a name="rest-apis"></a>REST API:er

Api:er för både redigering och förutsägelseslutpunkt är tillgängliga från REST-API:er:

|Typ|Version|
|--|--|
|Redigering|[V2 (på andra)](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[förhandsgranska V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Förutsägelse|[V2 (på andra)](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3 (på andra)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-ändpunkter

LUIS har för närvarande två typer av slutpunkter:

* författande på slutpunkten för utbildning
* frågeprediktion på slutpunkten för körning.

|Syfte|URL|
|--|--|
|Redigering på slutpunkt för utbildning|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime - alla förutsägelser på körningslutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime - versionsförutsägelse på slutpunkt för körning|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime - kortplatsförutsägelse på slutpunkten för körning|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

I följande tabell beskrivs parametrarna, som `{}`betecknas med klammerparenteser , i föregående tabell.

|Parameter|Syfte|
|--|--|
|`your-resource-name`|Namn på Azure-resurs|
|`q` eller `query`|yttrande text som skickas från klientprogram som chatt bot|
|`version`|10 tecken version namn|
|`slot`| `production` eller `staging`|

### <a name="language-based-sdks"></a>Språkbaserade SDK:er

|Språk |Referensdokumentation|Paket|Exempel|Snabbstarter|
|--|--|--|--|--|
|C#|[Redigering](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Prognos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet författande](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet förutsägelse](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Skapa och hantera en app](sdk-authoring.md?pivots=programming-language-csharp)<br>[Slutpunkt för frågeförutsägelse](sdk-query-prediction-endpoint.md)|
|Go|[Redigering och förutsägelse](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Prognos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Redigering och förutsägelse med REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Redigering och förutsägelse](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven författande](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven förutsägelse](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Prognos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Redigering och förutsägelse](luis-get-started-get-intent-from-rest.md)
|Node.js|[Redigering](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Prognos](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM-redigering](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM förutsägelse](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Prognos](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Redigering och förutsägelse med REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Redigering och förutsägelse](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Redigering](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Redigering](sdk-authoring.md?pivots=programming-language-python)<br>[Förutsägelse med REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containrar

Språkförståelse (LUIS) innehåller en [behållare](luis-container-howto.md) för att tillhandahålla lokala och innehålla versioner av appen.

### <a name="export-and-import-formats"></a>Exportera och importera format

Språkförståelse ger möjlighet att hantera din app `.LU` och dess modeller i ett JSON-format,[(LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)format och ett komprimerat paket för språkförståelsebehållaren.

Det finns även import och export av dessa format från API:erna och från LUIS-portalen. Portalen tillhandahåller import och export som en del av listan Appar och versioner.

## <a name="other-tools-and-sdks"></a>Andra verktyg och SDK:er

Bot-ramverket är tillgängligt som [en SDK](https://github.com/Microsoft/botframework) på en mängd olika språk och som en tjänst med [Azure Bot Service](https://dev.botframework.com/).

Bot framework provides several tools to help with Language Understanding, including: Bot framework provides several tools to help with Language Understanding, including: Bot framework provides [several tools](https://github.com/microsoft/botbuilder-tools) to help with Language Understanding, including: Bot framework

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Skapa LUIS-språkförståelsemodeller med hjälp av markdown-filer
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Skapa och hantera dina LUIS.ai-program
* [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- hantera överordnade och underordnade appar
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Generera automatiskt stöd C#/Typescript klasser för din LUIS avsikter och entiteter.
* [Bot Framework emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) - ett skrivbordsprogram som gör bot utvecklare att testa och felsöka bots byggda med Hjälp av Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - ett integrerat utvecklingsverktyg för utvecklare och tvärvetenskapliga team för att bygga robotar och konversationsupplevelser med Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Verktyg som stöder kontinuerlig integrering och distribution för NLU-tjänster.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om de vanliga [HTTP-felkoderna](luis-reference-response-codes.md)
* [Referensdokumentation](https://docs.microsoft.com/azure/index) för alla API:er och SDK:er
* [Bot-ramverket](https://github.com/Microsoft/botbuilder-dotnet) och [Azure Bot-tjänsten](https://dev.botframework.com/)
* [LUDown (på andra)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitiva behållare](../cognitive-services-container-support.md)
