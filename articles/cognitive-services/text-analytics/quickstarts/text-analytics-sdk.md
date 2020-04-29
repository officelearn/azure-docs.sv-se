---
title: 'Snabb start: Textanalys klient bibliotek v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att ansluta dina program till API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: cc3f48ec1113f954336cfae0bda2cba2499d9a1d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240129"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Snabb start: Använd Textanalys klient bibliotek

Kom igång med Textanalys klient biblioteket. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

Använd Textanalys klient bibliotek för att utföra:

* Sentimentanalys
* Språkidentifiering
* Enhetsidentifiering
* Extrahering av nyckelfraser

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Den senaste för hands versionen av API för textanalys är `3.0-preview`, som innehåller en offentlig för hands version för bättre [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [ner (Named Entity Recognition)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Den senaste stabila versionen är `2.1`.
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Den här snabb starten är bara `3.0-preview` för version av textanalys klient biblioteket, som innehåller en offentlig för hands version för bättre [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [ner (Named Entity Recognition)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Den senaste för hands versionen av API för textanalys är `3.0-preview`, som innehåller en offentlig för hands version för bättre [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [ner (Named Entity Recognition)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Den senaste stabila versionen är `2.1`.
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.
> * Du kan också köra den här versionen av klient biblioteket för Textanalys [i webbläsaren](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Den senaste för hands versionen av API för textanalys är `3.0-preview`, som innehåller en offentlig för hands version för bättre [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [ner (Named Entity Recognition)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). Den senaste stabila versionen är `2.1`.
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Ytterligare språk stöd

Om du har klickat på den här fliken ser du förmodligen ingen snabb start på ditt favorit språk. Oroa dig inte, vi har ytterligare snabb starter tillgängliga. Använd tabellen för att hitta rätt exempel för ditt programmeringsspråk.

| Språk | Tillgänglig version | 
|----------|------------------------|
| Ruby     | [Version 2,1](ruby-sdk.md) | 
| Go       | [Version 2,1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska en lösning](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Översikt över Textanalys](../overview.md)
* [Sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
