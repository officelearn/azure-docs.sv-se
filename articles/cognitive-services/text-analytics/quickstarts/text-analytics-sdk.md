---
title: 'Snabbstart: Text Analytics klientbibliotek v3 | Microsoft-dokument'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att ansluta dina program till TEXT Analytics API från Azure Cognitive Services.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240129"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Snabbstart: Använda klientbiblioteket i Text Analytics

Kom igång med textanalysklientbiblioteket. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Använd textanalysklientbiblioteket för att utföra:

* Sentimentanalys
* Språkidentifiering
* Enhetsidentifiering
* Extrahering av nyckelfraser

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Den senaste förhandsversionen av `3.0-preview`API:et för textanalys är , som innehåller en offentlig förhandsgranskning för förbättrad [sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [namngiven entitetsigenkänning (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Den senaste `2.1`stabila versionen är .
>    * Var noga med att bara följa instruktionerna för den version du använder.
> * Koden i den här artikeln använder synkrona metoder och lagring av o-säkrade autentiseringsuppgifter av enkelhetsskäl. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Den här snabbstarten `3.0-preview` är endast för en version av textanalysklientbiblioteket, som innehåller en offentlig förhandsgranskning för förbättrad [sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [namngiven entitetsigenkänning (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)
> * Koden i den här artikeln använder synkrona metoder och lagring av o-säkrade autentiseringsuppgifter av enkelhetsskäl. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Den senaste förhandsversionen av `3.0-preview`API:et för textanalys är , som innehåller en offentlig förhandsgranskning för förbättrad [sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [namngiven entitetsigenkänning (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Den senaste `2.1`stabila versionen är .
>    * Var noga med att bara följa instruktionerna för den version du använder.
> * Koden i den här artikeln använder synkrona metoder och lagring av o-säkrade autentiseringsuppgifter av enkelhetsskäl. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan.
> * Du kan också köra den här versionen av Text Analytics-klientbiblioteket [i webbläsaren](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Den senaste förhandsversionen av `3.0-preview`API:et för textanalys är , som innehåller en offentlig förhandsgranskning för förbättrad [sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) och [namngiven entitetsigenkänning (NER).](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) Den senaste `2.1`stabila versionen är .
>    * Var noga med att bara följa instruktionerna för den version du använder.
> * Koden i den här artikeln använder synkrona metoder och lagring av o-säkrade autentiseringsuppgifter av enkelhetsskäl. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Ytterligare språkstöd

Om du har klickat på den här fliken har du förmodligen inte sett en snabbstart i ditt favoritprogrammeringsspråk. Oroa dig inte, vi har ytterligare snabbstarter tillgängliga. Använd tabellen för att hitta rätt exempel för programmeringsspråket.

| Språk | Tillgänglig version | 
|----------|------------------------|
| Ruby     | [Version 2.1](ruby-sdk.md) | 
| Go       | [Version 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska en lösning](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Översikt över Textanalys](../overview.md)
* [Sentimentanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Erkännande av entitet](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språkigenkänning](../how-tos/text-analytics-how-to-language-detection.md)
