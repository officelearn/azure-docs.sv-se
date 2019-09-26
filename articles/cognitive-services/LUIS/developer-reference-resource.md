---
title: Utvecklings resurser – Language Understanding
titleSuffix: Azure Cognitive Services
description: 'Utvecklare har både REST-API: er och SDK: er för Language Understanding.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 97684a4668c1d495fa690674f7bd94e064737bd4
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273956"
---
# <a name="developer-resources-for-language-understanding"></a>Utvecklings resurser för Language Understanding

Utvecklare kan använda både REST-API: er och SDK: er för Language Understanding. 

## <a name="azure-resource-management"></a>Resurs hantering i Azure

Använd Azure-Cognitive Services hanterings lager för att skapa, redigera, Visa och ta bort Language Understanding eller kognitiva tjänst resurser.

Hitta referens dokumentation baserat på verktyget:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding redigerings-och förutsägelse begär Anden

Language Understandings tjänsten nås från en Azure-resurs som du måste skapa. Det finns två resurser: redigera och förutsäga slut punkts resurser. Med båda dessa resurser kan du kontrol lera dina LUIS-resurser. 

### <a name="rest-apis"></a>REST API:er

Både redigerings-och förutsägelse slut punkts-API: er är tillgängliga från REST API: er:

* [Referens dokumentation](https://go.microsoft.com/fwlink/?linkid=2092087) för redigering
* Dokumentation om förutsägelse körnings [referens](https://go.microsoft.com/fwlink/?linkid=2092356)

### <a name="language-based-sdks"></a>Språkbaserade SDK: er

|Språk |Referensdokumentation|Paket|Exempel|Snabbstarter|
|--|--|--|--|--|
|C#|[Redigering](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Förutsägelse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet-redigering](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet förutsägelse](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.NET SDK-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Skapa och hantera app](sdk-csharp-quickstart-authoring-app.md)<br>[Slut punkt för fråga förutsägelse](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Redigering och förutsägelse](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Förutsägelse](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Redigering med REST](luis-get-started-go-add-utterance.md)<br>[Förutsägelse med REST](luis-get-started-go-get-intent.md)|
|Java|[Redigering och förutsägelse](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven-redigering](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven förutsägelse](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Förutsägelse](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Redigering](luis-get-started-java-add-utterance.md)<br>[Förutsägelse](luis-get-started-java-get-intent.md)
|Node.js|[Redigering](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Förutsägelse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM-redigering](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM förutsägelse](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Redigering](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Förutsägelse](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Redigering med REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Förutsägelse med REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Redigering och förutsägelse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[–](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Redigering](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Redigering](sdk-python-quickstart-authoring-app.md)<br>[Förutsägelse med REST](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Andra verktyg och SDK: er

Bot Framework är tillgängligt som [en SDK](https://github.com/Microsoft/botframework) på en rad olika språk och som en tjänst med hjälp av [Azure bot service](https://dev.botframework.com/). 

Bot Framework innehåller [flera verktyg](https://github.com/microsoft/botbuilder-tools) som hjälper dig med language Understanding, inklusive:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – Bygg Luis language förståelseing Models med markdown-filer
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – skapa och hantera dina Luis.AI-program
* [Skicka](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– hantera över-och underordnade appar
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – skapa automatiskt C#/TYPESCRIPT-klasser för Luis-intentor och entiteter.
* [Bot-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) – ett Skriv bords program som gör det möjligt för bot-utvecklare att testa och felsöka robotar som skapats med bot Framework SDK


## <a name="next-steps"></a>Nästa steg

Läs mer om vanliga [http-felkoder](luis-reference-response-codes.md)