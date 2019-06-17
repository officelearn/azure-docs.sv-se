---
title: Ansikte erkännande begrepp
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp om ansiktsigenkänning.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65890880"
---
# <a name="face-recognition-concepts"></a>Ansikte erkännande begrepp

Den här artikeln förklarar begreppet de kontrollera, Sök liknande, grupp och identifiera ansikte igenkänning av åtgärderna och underliggande datastrukturer. Igenkänning av beskriver allmänna ordalag arbetet för att jämföra två olika ansikten för att avgöra om de är liknande eller tillhör samma person.

## <a name="recognition-related-data-structures"></a>Igenkänning av-relaterade datastrukturer

Igenkänning av åtgärderna använder främst följande datastrukturer. De här objekten lagras i molnet och kan refereras av deras ID-strängar. ID-strängar är alltid unikt inom en prenumeration. Namnfält kan dupliceras.

|Namn|Beskrivning|
|:--|:--|
|DetectedFace| Den här enda ansikts-representation hämtas av den [ansiktsspårning](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) igen. Dess ID upphör att gälla 24 timmar efter att den har skapats.|
|PersistedFace| När DetectedFace objekt läggs till i en grupp, till exempel FaceList eller Person, blir de PersistedFace objekt. De kan vara [hämtas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) vid någon tidpunkt och inte upphör att gälla.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) eller [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Den här datastrukturen är en utvalda lista över PersistedFace objekt. En FaceList har ett unikt ID, en namnsträng och eventuellt en datasträng för användaren.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Den här datastrukturen är en lista över PersistedFace-objekt som tillhör samma person. Den har ett unikt ID, en namnsträng och eventuellt en datasträng för användaren.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) eller [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Den här datastrukturen är en utvalda lista över personobjekt. Den har ett unikt ID, en namnsträng och eventuellt en datasträng för användaren. En PersonGroup måste vara [tränade](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) innan den kan användas i igenkänning av åtgärder.|

## <a name="recognition-operations"></a>Igenkänning av åtgärder

Det här avsnittet beskriver hur de fyra igenkänning av åtgärderna använder datastrukturer som beskrevs tidigare. En bred beskrivning av varje åtgärd för igenkänning av finns i [översikt](../Overview.md).

### <a name="verify"></a>Verifiera

Den [Kontrollera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) åtgärden tar en face ID från DetectedFace eller PersistedFace och antingen en annan face ID eller en Person-objekt och avgör om de tillhör samma person. Om du skickar in ett Person-objekt kan du välja att skicka i en PersonGroup som denne tillhör för att förbättra prestanda.

### <a name="find-similar"></a>Hitta liknande

Den [Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) åtgärden tar en face ID från DetectedFace eller PersistedFace och antingen en FaceList eller en matris med andra face ID: N. Med en FaceList returneras en mindre FaceList av ansikten som liknar visst ansikte. Med en matris med face ID: N returnerar den på samma sätt en mindre matris.

### <a name="group"></a>Grupp

Den [grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) åtgärden tar en matris med utvalda face ID: N från DetectedFace eller PersistedFace och returnerar samma ID: N som är grupperade i flera mindre matriser. Varje ”grupper”-matrisen innehåller face ID: N som ser ut. En enda ”messyGroup”-matris innehåller face ID: N för som ingen likheter hittades.

### <a name="identify"></a>Identifiera

Den [identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) åtgärden tar en eller flera face ID: N från DetectedFace eller PersistedFace och en PersonGroup och returnerar en lista över personobjekt som varje ansikte kan tillhöra. Returnerade Person objekt är omslutna som kandidat för objekt som har ett värde för förutsägelse förtroende.

## <a name="input-data"></a>Indata

Använd följande tips för att säkerställa att din indatabilder ger de mest korrekta resultat:

* Format som stöds inmatad bild är JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildens filstorlek bör inte vara större än 4 MB.
* När du skapar personobjekt kan du använda foton som ger olika typer av vinklar och belysning.
* Vissa ansikten kan det uppstå problem på grund av tekniska utmaningar, till exempel:
  * Bilder med extrem belysning, till exempel allvarliga bakgrundsbelysningen.
  * Hinder som blockerar ena eller båda ögon.
  * Skillnader i hår typ eller videodetektion hår.
  * Ändringar i ansiktsigenkänning utseende på grund av ålder.
  * Extreme ansiktsuttryck.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med principerna för igenkänning av ansikte, lär du dig hur du skriver ett skript som identifierar ansikten mot en tränad PersonGroup.

* [Identifiera ansikten i bilder](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)