---
title: Transformeringar och jobb i Azure Media Services | Microsoft Docs
description: När du använder Media Services kan behöva du skapa en transformering för att beskriva regler och specifikationer för bearbetning av dina videor. Den här artikeln ger en översikt över vad transformeringen är och hur du använder den.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 214d4d3d11255e417f3df1e5f6e648b2a30225ea
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377316"
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb

## <a name="overview"></a>Översikt 

Den senaste versionen av Azure Media Services REST API (v3) introducerar en ny mallbaserad arbetsflöde resurs för kodning och/eller analysera videor, kallas en **transformera**. **Omvandlar** kan användas för att konfigurera vanliga uppgifter för kodning eller analying videor. Varje **transformera** beskriver ett enkelt arbetsflöde uppgifter för att bearbeta video- eller ljudinnehåll-filer. 

Den **transformera** objekt är receptet och en **jobbet** är den faktiska begäran till Azure Media Services att tillämpa som **omvandla** till en given video- eller ljudinnehåll datainnehållet. **Jobb** anger information som platsen för indatavideon och platsen för utdatan. Du kan ange platsen för din video med hjälp av: http (s)-URL: er, SAS URL: er eller en sökväg till filer som finns lokalt eller i Azure Blob storage. Du kan ha upp till 100 transformeringar i ditt Azure Media Services-konto och skicka jobb under dessa transformeringar. Du kan sedan prenumererar på händelser som tillståndsändringar för jobb, med hjälp av meddelanden, som integreras direkt med Azure Event Grid-meddelandesystem. 

Eftersom detta API drivs av Azure Resource Manager, kan du använda Resource Manager-mallar för att skapa och distribuera transformeringar i Media Services-kontot. Rollbaserad åtkomstkontroll kan också anges på resursnivån i den här API: et, så att du kan låsa åtkomst till specifika resurser som transformeringar.

## <a name="transform-definition"></a>Omvandla definition

I följande tabell visar Transformeringens egenskaper och ger definitionerna.

|Namn|Typ|Beskrivning|
|---|---|---|
|Id|sträng|Fullständigt kvalificerade resurs-ID för resursen.|
|namn|sträng|Namnet på resursen.|
|Properties.Created |sträng|UTC-datum och tid när transformering skapades i ”åååå-MM-: ssZ ' format.|
|properties.description |sträng|En valfri utförlig beskrivning av transformeringen.|
|properties.lastModified |sträng|UTC-datum och tid när vi senast uppdaterades, i ”åååå-MM-: ssZ ' format.|
|Properties.outputs |TransformOutput]|En matris med en eller flera TransformOutputs som vi ska skapa.|
|typ|sträng|Typ av resursen.|

Läs den fullständiga definitionen [omvandlar](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Jobbdefinition

I följande tabell visar egenskaper för jobbets och ger definitionerna.

|Namn|Typ|Beskrivning|
|---|---|---|
|Id|sträng|Fullständigt kvalificerade resurs-ID för resursen.|
|namn|sträng|Namnet på resursen.|
|Properties.Created |sträng|UTC-datum och tid när transformering skapades i ”åååå-MM-: ssZ ' format.|
|properties.description |sträng|En valfri utförlig beskrivning av jobbet.|
|properties.lastModified |sträng|UTC-datum och tid när vi senast uppdaterades, i ”åååå-MM-: ssZ ' format.|
|Properties.outputs |JobOutput []: JobOutputAsset] |Utdata för jobbet.|
|Properties.Priority |Prioritet |Prioritet som jobbet ska bearbetas. Jobb med högre prioritet bearbetas före jobb med lägre prioritet. Om inte är aktiverad, standardvärdet är normal.
|Properties.state |JobState |Det aktuella tillståndet för jobbet.
|typ|sträng|Typ av resursen.|

Läs den fullständiga definitionen [jobb](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Typiskt arbetsflöde och exempel

1. Skapa en transformering 
2. Skicka jobb under den transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering, om du inte planerar att använda den här ”recept” i framtiden. 

Anta att du vill extrahera första bildruta från dina videor som en miniatyrbild – är vad du måste göra: 

1. Definiera regeln för bearbetning – till exempel, använda första ramen videoklippets som miniatyrbilden 
2. För varje video som du har som indata till tjänsten, skulle du sedan kan instruera tjänsten: 
    1. Var du hittar videon, och 
    2. Var du vill skriva utdata – till exempel på miniatyrbilden 

## <a name="next-steps"></a>Nästa steg

[Stream-filer](stream-files-dotnet-quickstart.md)
