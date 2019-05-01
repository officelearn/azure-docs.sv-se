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
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "64416005"
---
# <a name="face-recognition-concepts"></a>Ansikte erkännande begrepp

Den här artikeln förklarar begreppet olika ansikte igenkänning av åtgärderna (verifiering, Sök liknande, gruppering, identifiering) och de underliggande datastrukturerna. Brett, igenkänning av beskriver arbetet för att jämföra två olika ansikten för att avgöra om de är liknande eller tillhör samma person.

## <a name="recognition-related-data-structures"></a>Igenkänning av-relaterade datastrukturer

Igenkänning av åtgärderna använder främst följande datastrukturer. De här objekten lagras i molnet och kan refereras av deras ID-strängar. ID-strängar är därför alltid unikt inom en prenumeration medan namnfält kan dupliceras.

|Namn|Beskrivning|
|:--|:--|
|**DetectedFace**| Det här är en enda ansikts-representation som hämtades av den [ansiktsspårning](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) igen. Dess ID upphör att gälla 24 timmar efter att den har skapats.|
|**PersistedFace**| När **DetectedFace** objekt läggs till i en grupp (till exempel **FaceList** eller **Person**), de blir **PersistedFace** objekt, vilket kan vara [hämtas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) på någon gång och inte upphör att gälla.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Det här är en utvalda lista över **PersistedFace** objekt. En **FaceList** har ett unikt ID, en namnsträng och eventuellt en datasträng för användaren.|
|**[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Det här är en lista över **PersistedFace** objekt som tillhör samma person. Den har ett unikt ID, en namnsträng och eventuellt en datasträng för användaren.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Det här är en utvalda lista över **Person** objekt. Den har ett unikt ID, en namnsträng och eventuellt en datasträng för användaren. En **PersonGroup** måste vara [tränade](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) innan den kan användas i igenkänning av åtgärder.|

## <a name="recognition-operations"></a>Igenkänning av åtgärder

Det här avsnittet beskriver hur de fyra igenkänning av åtgärderna använder ovan datastrukturer. Se den [översikt](../Overview.md) en bred beskrivning av varje igenkänning av åtgärd.

### <a name="verification"></a>Verifiering

Den [Kontrollera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) åtgärden tar en face ID (**DetectedFace** eller **PersistedFace**) och antingen en annan face ID eller en **Person** objekt och Anger om de tillhör samma person. Om du skickar in en **Person**, du kan välja att skicka i en **PersonGroup** som **Person** tillhör för att förbättra prestanda.

### <a name="find-similar"></a>Hitta liknande

Den [hitta liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) åtgärden tar en face ID (**DetectedFace** eller **PersistedFace**) och antingen en **FaceList** eller en matris med andra ansikte ID: N. Med en **FaceList**, returneras en mindre **FaceList** av ansikten som liknar visst ansikte. Med en matris med face ID: N returnerar den på samma sätt en mindre matris.

### <a name="grouping"></a>Gruppering

Den [grupp](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) åtgärden tar en matris med utvalda face ID: N (**DetectedFace** eller **PersistedFace**) och returnerar samma ID: N som är grupperade i flera mindre matriser. Varje ”grupper”-matrisen innehåller face ID: N som ser ut och en enda ”messyGroup”-matris innehåller face ID: N för som ingen likheter hittades.

### <a name="identification"></a>Identifiering

Den [identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) åtgärden tar en eller flera face ID: N (**DetectedFace** eller **PersistedFace**) och en **PersonGroup** och returnerar en lista av **Person** objekt som varje ansikte kan tillhör. Returnerade **Person** objekt är omslutna som **kandidat** objekt som har ett värde för förutsägelse förtroende.

## <a name="input-data"></a>Indata

Använd följande tips för att säkerställa att din indatabilder ger de mest korrekta resultat:

* Format som stöds inmatad bild är JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildens filstorlek bör inte vara större än 4 MB.
* När du skapar **Person** objekt, bör du använda foton som ger en mängd olika vinklar och belysning.
* Vissa ansikten identifieras inte på grund av tekniska utmaningar:
  * Bilder med extrem belysning (till exempel allvarliga bakgrundsbelysningen).
  * Hinder som blockerar ena eller båda ögon.
  * Skillnader i hår format eller videodetektion hår.
  * Ändringar i ansikte utseende på grund av ålder.
  * Extreme ansiktsuttryck.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med principerna för igenkänning av ansikte, lär du dig hur du skriver ett enkelt skript som identifierar ansikten mot en tränad **PersonGroup**.

* [Så här identifierar ansikten i bilder](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)