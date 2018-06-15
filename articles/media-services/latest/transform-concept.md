---
title: Omvandlar och jobb i Azure Media Services | Microsoft Docs
description: När du använder Media Services, måste du skapa en transformering för att beskriva regler och specifikationer för bearbetning av dina videor. Den här artikeln ger en översikt över transformeringen är och hur du använder den.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272088"
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb

## <a name="overview"></a>Översikt 

Den senaste versionen av Azure Media Services REST API (v3) introducerar en ny resurs mallbaserat arbetsflöde för kodning och/eller analysera videor som kallas en **transformera**. **Transformerar** kan användas för att konfigurera vanliga uppgifter för kodning eller analying videor. Varje **transformera** beskriver ett enkelt arbetsflöde för aktiviteter för bearbetning av video eller ljud filerna. 

Den **transformera** objektet är ritar och en **jobbet** är den faktiska begäranden till Azure Media Services ska användas som **transformera** till en viss inkommande video eller ljud innehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan. Du kan ange platsen för din video med: http (s)-URL: er, SAS-URL: er eller en sökväg till filer lokalt eller i Azure Blob storage. Du kan ha upp till 100 transformeringar i Azure Media Services-kontot och skicka jobb under dessa transformeringar. Du kan sedan prenumerera på händelser som jobbet status ändras, använder meddelanden som integreras direkt med Azure Event notification nätet. 

Eftersom detta API är drivs av Azure Resource Manager, kan du använda Resource Manager-mallar för att skapa och distribuera transformeringar i Media Services-kontot. Rollbaserad åtkomstkontroll kan också anges på nivån resurs i detta API så att du kan låsa åtkomst till specifika resurser som transformeringar.

## <a name="transform-definition"></a>Transformera definition

I följande tabell visas Transformeringens egenskaper och ger deras definitioner.

|Namn|Typ|Beskrivning|
|---|---|---|
|Id|sträng|Fullständiga resurs-ID för resursen.|
|namn|sträng|Namnet på resursen.|
|Properties.Created |sträng|UTC-datum och tid då transformeringen skapades i ”åååå-MM-ddTHH-format.|
|properties.description |sträng|En valfri utförlig beskrivning av transformeringen.|
|properties.lastModified |sträng|UTC-datum och tid då transformeringen senast uppdaterades, i ”åååå-MM-ddTHH-format.|
|Properties.outputs |TransformOutput]|En matris med en eller flera TransformOutputs som transformationen ska skapa.|
|typ|sträng|Typ av resursen.|

Se för en fullständig definition [transformerar](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Jobbdefinition

I följande tabell visas jobbets egenskaper och ger deras definitioner.

|Namn|Typ|Beskrivning|
|---|---|---|
|Id|sträng|Fullständiga resurs-ID för resursen.|
|namn|sträng|Namnet på resursen.|
|Properties.Created |sträng|UTC-datum och tid då transformeringen skapades i ”åååå-MM-ddTHH-format.|
|properties.description |sträng|En valfri utförlig beskrivning av jobbet.|
|properties.lastModified |sträng|UTC-datum och tid då transformeringen senast uppdaterades, i ”åååå-MM-ddTHH-format.|
|Properties.outputs |JobOutput []: [JobOutputAsset] |Utdata för jobbet.|
|Properties.Priority |Prioritet |Prioritet som den ska bearbetas. Jobb med högre prioritet bearbetas före jobb med lägre prioritet. Om inte är aktiverad som standard är normalt.
|Properties.state |JobState |Aktuell status för jobbet.
|typ|sträng|Typ av resursen.|

Se för en fullständig definition [jobb](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Vanligt arbetsflöde och exempel

1. Skapa en transformering 
2. Skicka jobb under den transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering om du inte planerar att använda ”recept” i framtiden. 

Anta att du vill extrahera den första rutan av dina videor som en miniatyrbild – är du kan vidta åtgärder: 

1. Definiera regeln för bearbetning – t.ex, använda den första bildrutan i videon som miniatyren 
2. Sedan för varje video som indata för tjänsten, anger du tjänsten: 
    1. Var du hittar den videon och 
    2. Var du vill skriva utdata – till exempel miniatyrbilden 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Stream-filer](stream-files-dotnet-quickstart.md)
