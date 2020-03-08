---
title: Utvecklings resurser – Language Understanding
description: 'SDK: er, REST API: er, CLI, hjälper dig att utveckla Language Understanding-appar (LUIS) i ditt programmeringsspråk. Hantera dina Azure-resurser och LUIS-förutsägelser.'
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f9d531f800ba0729a1f679c0bf3b11c8958c78ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391950"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK-, REST-och CLI-utvecklarresurser för Language Understanding (LUIS)

SDK: er, REST API: er, CLI, hjälper dig att utveckla Language Understanding-appar (LUIS) i ditt programmeringsspråk. Hantera dina Azure-resurser och LUIS-förutsägelser.

## <a name="azure-resource-management"></a>Resurs hantering i Azure

Använd Azure-Cognitive Services hanterings lager för att skapa, redigera, Visa och ta bort Language Understanding eller kognitiva tjänst resurser.

Hitta referens dokumentation baserat på verktyget:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding redigerings-och förutsägelse begär Anden

Language Understandings tjänsten nås från en Azure-resurs som du måste skapa. Det finns två resurser:

* Använd **redigerings** resursen för utbildning för att skapa, redigera, träna och publicera.
* Använd **förutsägelse** för körning för att skicka användarens text och få en förutsägelse.

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Använd [Cognitive Services exempel kod](https://github.com/Azure-Samples/cognitive-services-quickstart-code) för att lära dig och använda de vanligaste uppgifterna.

### <a name="rest-specifications"></a>REST-specifikationer

[Luis rest-specifikationerna](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), tillsammans med alla [Azure rest-specifikationer](https://github.com/Azure/azure-rest-api-specs), är offentligt tillgängliga på GitHub.

### <a name="rest-apis"></a>REST-API:er

Både redigerings-och förutsägelse slut punkts-API: er är tillgängliga från REST API: er:

|Typ|Version|
|--|--|
|Redigering|[2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Förhandsgranska v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|förutsägelse|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-slutpunkter

LUIS har för närvarande två typer av slut punkter:

* redigering av utbildnings slut punkten
* fråga förutsägelse på körnings slut punkten.

|Syfte|URL|
|--|--|
|Redigering av utbildnings slut punkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2-körning-alla förutsägelser för runtime-slutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 runtime-versioner förutsägelse på runtime-slutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 körnings plats förutsägelse på runtime-slutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

I följande tabell beskrivs parametrarna, som anges med klammerparenteser `{}`, i föregående tabell.

|Parameter|Syfte|
|--|--|
|`your-resource-name`|Namn på Azure-resurs|
|`q` eller `query`|uttryck-texten som skickas från klientprogram, till exempel chattrobot|
|`version`|namn på 10-siffrig version|
|`slot`| `production` eller `staging`|

### <a name="language-based-sdks"></a>Språkbaserade SDK: er

|Språk |Referens dokumentation|Paket|Exempel|Snabbstarter|
|--|--|--|--|--|
|C#|[Redigering](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Förutsägelse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet-redigering](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet förutsägelse](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Skapa och hantera app](sdk-authoring.md?pivots=programming-language-csharp)<br>[Slut punkt för fråga förutsägelse](sdk-query-prediction-endpoint.md)|
|Go|[Redigering och förutsägelse](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Förutsägelse](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Redigering och förutsägelse med REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Redigering och förutsägelse](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven-redigering](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven förutsägelse](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Förutsägelse](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Redigering och förutsägelse](luis-get-started-get-intent-from-rest.md)
|Node.js|[Redigering](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Förutsägelse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM-redigering](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM förutsägelse](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Förutsägelse](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Redigering och förutsägelse med REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Redigering och förutsägelse](sdk-authoring.md?pivots=programming-language-python)|[–](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Redigering](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Redigering](sdk-authoring.md?pivots=programming-language-python)<br>[Förutsägelse med REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containrar

Language Understanding (LUIS) innehåller en [behållare](luis-container-howto.md) för att tillhandahålla lokala och befintliga versioner av din app.

### <a name="export-and-import-formats"></a>Exportera och importera format

Language Understanding ger möjlighet att hantera din app och dess modeller i ett JSON-format, `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown))-format och ett komprimerat paket för language Understanding containern.

Import och export av dessa format är tillgängligt från API: erna och från LUIS-portalen. Portalen innehåller import och export som en del av listan över appar och versioner.

## <a name="other-tools-and-sdks"></a>Andra verktyg och SDK: er

Bot Framework är tillgängligt som [en SDK](https://github.com/Microsoft/botframework) på en rad olika språk och som en tjänst med hjälp av [Azure bot service](https://dev.botframework.com/).

Bot Framework innehåller [flera verktyg](https://github.com/microsoft/botbuilder-tools) som hjälper dig med language Understanding, inklusive:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – Bygg Luis language förståelseing Models med markdown-filer
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – skapa och hantera dina Luis.AI-program
* [Skicka](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– hantera över-och underordnade appar
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – skapa automatiskt C#/TYPESCRIPT-klasser för Luis-intentor och entiteter.
* [Bot Framework-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) – ett Skriv bords program som gör det möjligt för bot-utvecklare att testa och felsöka robotar som skapats med bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – ett integrerat utvecklingsverktyg för utvecklare och flera disciplin team för att bygga robotar och konversations upplevelser med Microsoft bot Framework

## <a name="next-steps"></a>Nästa steg

* Läs mer om vanliga [http-felkoder](luis-reference-response-codes.md)
* [Referens dokumentation](https://docs.microsoft.com/azure/index) för alla API: er och SDK: er
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) och [Azure bot service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitiva behållare](../cognitive-services-container-support.md)
