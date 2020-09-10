---
title: 'Snabb start: text utvinning med Textanalys klient biblioteket'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att utföra sentiment analys med mera, med hjälp av API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 09/09/2020
ms.author: aahi
keywords: text utvinning, sentiment-analys, text analys
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 104ae91b59bc04fc3227585a55a744d500d6db29
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649002"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Snabb start: Använd Textanalys klient bibliotek

Använd den här artikeln för att komma igång med Textanalys klient biblioteket. Följ de här stegen för att installera paketet och prova exempel koden för utvinnings text.

Använd Textanalys klient bibliotek för att utföra:

* Sentimentanalys
* Språkidentifiering
* Igenkänning av enhet
* Extrahering av nyckelfraser

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.
> * Du kan också köra den här versionen av klient biblioteket för Textanalys [i webbläsaren](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
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

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska en lösning](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Översikt över Textanalys](../overview.md)
* [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Igenkänning av enhet](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
