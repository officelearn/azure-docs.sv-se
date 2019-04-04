---
title: Ordlista – Ansikts-API-tjänsten
titleSuffix: Azure Cognitive Services
description: Ordlistan beskrivs de termer som du kan stöta på när du arbetar med Ansikts-API-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: bd4285a2d0f882b8c766563f2304031f1f6e2898
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904805"
---
# <a name="glossary"></a>Ordlista

## <a name="a"></a>A

#### <a name="attributes"></a>Attribut

Attribut är valfria ansikts-funktioner som kan identifieras, till exempel [ålder](#age-attribute), [kön](#gender-attribute), [head attityd](#head-pose-attribute), [ansiktsigenkänning hår](#facial-hair-attribute), och [le](#smile-attribute). De kan hämtas från identifieringen API genom att ange den _returnFaceAttributes_ frågeparameter.

En fullständig lista över ansiktsattribut finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Ålder (attribut)

Ålder är en av de [attribut](#attributes) som beskriver en ålder för ett visst ansikte. Attributet ålder på resultaten av programuppdateringsidentifieringen är valfritt och kan kontrolleras med en begäran om identifiering genom att ange parametern returnFaceAttributes.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Kandidat

Kandidater är i stort sett [identifiering](#identification) resultat (t.ex. identifierade personer och identifieringar förtroende). En kandidat representeras av den [PersonID](#person-id) och [förtroende](#confidence), som anger att personen som identifieras med hög tillförlitlighet.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Konfidensbedömning

Säkerheten är ett mått som avslöja likheten mellan [ansikten](#face) eller [Person](#person) i numeriska värden – som används i [identifiering](#identification), och [ verifiering](#verification) att ange likheter med sökt, identifieras och är verifierad resultat.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – Sök efter liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Identifiering/Ansiktsigenkänning

Ansiktsigenkänning är åtgärden för att hitta ansikten i bilder. Användarna kan ladda upp en bild eller ange en bild-URL i förfrågan. Identifierade ansikten returneras med [står inför ID: N](#face-id) som anger en unik identitet i Ansikts-API. Ansikts-platser i avbildningen i bildpunkter, samt den valfria rektanglar [attribut](#attributes) för varje ansikte som [ålder](#age-attribute), [kön](#gender-attribute), [head attityd ](#head-pose-attribute), [ansiktsigenkänning hår](#facial-hair-attribute) och [Leende](#smile-attribute).

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Igenkänning (attribut)

Igenkänning är en av de [ansiktsattribut](#attributes). När en förfrågan returnerar en lista över känslor och deras identifiering förtroende för det angivna ansiktet. Förtroende poäng normaliseras: poängen över alla känslor läggs till. Känslor som returneras är lycka, sorg, neutral, ilska, förakt, avsky, överraskning och behöva betala.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Ansikte

Är en enhetlig term för resultaten som härletts från Ansikts-API som är relaterade med identifierade ansikten. Slutligen ansikte representeras av en enhetlig identitet ([Face ID](#face-id)), en specifik region i bilder ([Ansiktsrektangeln](#face-rectangle)), och extra ansikts-relaterade attribut som [ålder](#age-attribute), [kön](#gender-attribute), landmärken och [head attityd](#head-pose-attribute). Dessutom kan ansikten returneras från identifiering.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Ansikts-API

Ansikts-API är ett molnbaserad API som ger de mest avancerade algoritmerna för ansiktsigenkänning och taligenkänning. De flesta funktioner för Ansikts-API kan delas in i två kategorier: ansiktsspårning med attribut och ansikte [erkännande](#recognition).

Mer information finns i referensdokumentationen: [Översikt över API för ansiktsigenkänning](./Overview.md), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Ansiktsigenkänning – Sök efter liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ Ansiktsigenkänning – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Attribut/Videodetektion Ansiktsattribut

Se [attribut](#attributes).

#### <a name="face-id"></a>Ansikts-ID

Face ID härleds från identifiering resultatet som en sträng som representerar en [ansikte](#face) i [Ansikts-API](#face-api).

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Landmärken/Videodetektion Ansiktslandmärken

Landmärken är valfria i resultatet för identifiering. som är semantiska ansiktsigenkänning punkter, till exempel ögon, näsa och munnen (illustreras i följande bild). Landmärken kan kontrolleras med en begäran om identifiering av boolesk nummer returnFaceLandmarks. Om returnFaceLandmarks anges som true, har returnerade ansikten landmärken attribut.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Med rektangel

Ansiktsrektangeln härleds från Identifieringsresultat som, vilket är en stående rektangel (vänster, överkant, bredd, höjd) i bilder i pixlar. Det övre vänstra hörnet av en [ansikte](#face) (vänster, uppifrån), förutom bredd och höjd, anger du ansikte storlekar i x och y-axelns respektive.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Ansiktsigenkänning hår (attribut)

Ansiktsigenkänning hår är en av de [attribut](#attributes) används för att beskriva ansiktsigenkänning hår längden på tillgängliga ansikten. Ansiktsigenkänning hår attributet på resultaten av programuppdateringsidentifieringen är valfritt och kan styras med en begäran om identifiering av returnFaceAttributes. Om returnFaceAttributes innehåller 'facialHair', har returnerade ansikten ansiktsigenkänning hår attribut.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList är en samling [PersistedFace](#persistedface) och är Faktureringsenhet [Sök liknande](#find-similar). En FaceList levereras med en [FaceList ID](#facelist-id), samt andra attribut, till exempel namn och användardata.

Mer information finns i referensdokumentationen: [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>FaceList-ID

FaceList ID: T är en sträng för anges av användaren som används som en identifierare för en [FaceList](#facelist). FaceList-ID måste vara unikt inom prenumerationen.

Mer information finns i referensdokumentationen: [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Hitta liknande

Detta API fungerar som en sökfråga/liknande ansikten baserat på en grupp med ansikten. Fråga ansikten och ansikte samlingar representeras som [står inför ID: N](#face-id) eller [FceList ID](#facelist-id)/[LargeFaceList ID](#largefacelist-id) i begäran. Returnerade resultat har sökt liknande ansikten, representeras av [står inför ID: N](#face-id) eller PersistedFace ID: N.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – Sök efter liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Kön (attribut)

Kön är en av de [attribut](#attributes) används för att beskriva kön med tillgängliga ansikten. Attributet kön på resultaten av programuppdateringsidentifieringen är valfritt och kan styras med en begäran om identifiering av returnFaceAttributes. Om returnfaceAttributes innehåller 'kön', har returnerade ansikten kön attribut.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Gruppering

Ansiktsgruppering är gruppering av en grupp med ansikten enligt ansiktsigenkänning likheter. Ansikte samlingar anges med face ID samlingar i begäran. Till följd av gruppering, liknande ansikten grupperas som [grupper](#groups), och ansikten som inte liknar andra ansikte slås samman tillsammans som en röriga grupp. Det finns på den mest en [röriga grupp](#messy-group) i resultatet gruppering.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Grupper

Grupper härleds från den [gruppering](#grouping) resultat. Varje grupp innehåller en uppsättning liknande ansikten, där ansikten anges med [står inför ID: N](#face-id).

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>HEAD utgöra (attribut)

HEAD attityd är en av de [attribut](#attributes) att representerar står inför orientering i 3D-rymden enligt distributionen, försäljningsargument och positionen vinklar, enligt följande bild. Värdeintervall för rulla och yaw är [-180, 180] och [-90, 90] i grader. I den aktuella versionen försäljningsargument värdet som returneras från identifiering är alltid 0. Attributet head attityd på resultaten av programuppdateringsidentifieringen är valfritt och kan kontrolleras med en begäran om identifiering av parametern returnFaceAttributes. Om returnFaceAttributes parametern innehåller ”headPose”, har de returnerade ansiktena head utgöra attribut.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identifiering

Identifiering är att identifiera en eller flera ansikten från LargePersonGroup/PersonGroup.
En [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) är en samling [personer](#person).
Ansikten och LargePersonGroup/PersonGroup representeras av [står inför ID: N](#face-id) och [LargePersonGroup-ID: N](#largepersongroup-id)/[PersonGroup-ID: N](#persongroup-id) respektive i den begäran.
Identifierade resultaten [kandidater](#candidate), representeras av [personer](#person) med tillförsikt.
Flera ansikten i inkommande betraktas separat och varje ansikte har sin egen identifierade resultatet.

> [!NOTE]
> LargePersonGroup/PersonGroup bör utbildas har innan identifiering. Om du inte har tränats LargePersonGroup/PersonGroup eller utbildningen [status](#status-train) inte visas som ”lyckades” är (d.v.s. ”körs”, ”misslyckad” eller ”timeout-), svaret är 400.
> 

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical är ett booleskt fält av [verifiering](#verification) resultat som visar om två ansikten tillhör samma person.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Landmärken

Se ansiktslandmärken.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList är en samling [PersistedFace](#persistedface) och är Faktureringsenhet [Sök liknande](#find-similar). En LargeFaceList levereras med en [LargeFaceList ID](#largefacelist-id), samt andra attribut, till exempel namn och användardata.

Mer information finns i referensdokumentationen: [LargeFaceList – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList – lista ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList-ID

LargeFaceList ID: T är en sträng för anges av användaren som används som en identifierare för en [LargeFaceList](#largefacelist). LargeFaceList-ID måste vara unikt inom prenumerationen.

Mer information finns i referensdokumentationen: [LargeFaceList – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup är en samling [personer](#person) och är Faktureringsenhet [identifiering](#identification). En LargePersonGroup levereras med en [LargePersonGroup ID](#largepersongroup-id), samt andra attribut, till exempel namn och användardata.

Mer information finns i referensdokumentationen: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup-ID

LargePersonGroup ID: T är en sträng för anges av användaren som används som en identifierare för en [LargePersonGroup](#largepersongroup). LargePersonGroup-ID måste vara unikt inom prenumerationen.

Mer information finns i referensdokumentationen: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Röriga grupp

Röriga grupp härleds från den [gruppering](#grouping) resultat; som innehåller ansikten inte liknar andra ansikte. Varje ansikte i en röriga grupp visas med den [står inför ID](#face-id).

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Namn (Person)

Namnet är en användarvänlig beskrivande sträng för [Person](#person). Till skillnad från den [Person-ID](#person-id), kan skapas dubbletter av namn på personer i en grupp.

Mer information finns i referensdokumentationen: [Skapa LargePersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Name (LargePersonGroup/PersonGroup)

Namnet är också ett användarvänligt beskrivande sträng för [LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup). Till skillnad från den [LargePersonGroup ID](#largepersongroup-id)/[PersonGroup ID](#persongroup-id), namnet på LargePersonGroups/PersonGroups kan kopieras och användas inom en prenumeration.

Mer information finns i referensdokumentationen: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace är en datastruktur i Ansikts-API. PersistedFace levereras med en [PersistedFace ID](#persisted-face-id), samt andra attribut, till exempel namn och användardata.

Mer information finns i referensdokumentationen: [LargeFaceList – Lägg till Ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="persisted-face-id"></a>Beständiga Face ID

Beständiga Face ID genereras när en [PersistedFace](#persistedface) har skapats. En sträng har skapats för att representera den här ansikte i [Ansikts-API](#face-api).

Mer information finns i referensdokumentationen: [LargeFaceList – Lägg till Ansikts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Person

Person är en datastruktur som hanteras i Ansikts-API. Person som levereras med en [Person-ID](#person-id), samt andra attribut, till exempel namn, en samling [PersistedFace](#persistedface), och användardata.

Mer information finns i referensdokumentationen: [Skapa LargePersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>Person-ID

Person-ID genereras när en [Person](#person) har skapats. En sträng har skapats för att representera den här personen i [Ansikts-API](#face-api).

Mer information finns i referensdokumentationen: [Skapa LargePersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

PersonGroup är en samling [personer](#person) och är Faktureringsenhet [identifiering](#identification). En PersonGroup levereras med en [PersonGroup ID](#persongroup-id), samt andra attribut, till exempel namn och användardata.

Mer information finns i referensdokumentationen: [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>PersonGroup-ID

PersonGroup ID: T är en sträng för anges av användaren som används som en identifierare för en [PersonGroup](#persongroup). Grupp-ID måste vara unikt inom prenumerationen.

Mer information finns i referensdokumentationen: [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Utgöra (attribut)

Se [Head utgöra](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Igenkänning

Igenkänning av är en populär modul för ansikts-teknik som [Sök liknande](#find-similar), [gruppering](#grouping), [identifiera](#identification),[verifierar att två ansikten samma eller inte ](#verification).

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – Sök efter liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Rektangel (framsidan)

Se [ansiktsrektangeln](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Sökning efter liknande ansikte

Se [Sök liknande](#find-similar).

#### <a name="smile-attribute"></a>Le (attribut)

Leende är en av de [attribut](#attributes) används för att beskriva Leende-uttrycket för tillgängliga ansikten. Attributet Leende på resultaten av programuppdateringsidentifieringen är valfritt och kan styras med en begäran om identifiering av returnFaceAttributes. Om returnFaceAttributes innehåller 'le ”, har de returnerade ansiktena Leende attribut.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Ögonblicksbild

En ögonblicksbild är en tillfällig Fjärrlagring för vissa Ansikts-datatyper. Den fungerar som en typ av Urklipp vid funktionen Kopiera data från en prenumeration till en annan. Först användaren ”hämtar” en ögonblicksbild av data i ursprungligt abonnemang, och sedan de ”gäller” det till ett nytt dataobjekt i målprenumerationen. 

Mer information finns i [ansikte Migreringsguide](./face-api-how-to-topics/how-to-migrate-face-data.md) samt de [ögonblicksbild - ta](/rest/api/cognitiveservices/face/snapshot/take) och [ögonblicksbild - gäller](/rest/api/cognitiveservices/face/snapshot/apply) referensdokumentation (REST).

#### <a name="status-train"></a>Status (Train)

Statusen är en sträng som används för att beskriva proceduren för utbildning LargeFaceList/LargePersonGroups/PersonGroups, inklusive notstarted, ”kör”, ”lyckades”, ”misslyckades”.

Mer information finns i referensdokumentationen: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Prenumerationsnyckel

Prenumerationsnyckel är en sträng som du vill ange som en frågesträngsparameter för att kunna anropa Ansikts-API: er. Prenumerationsnyckeln finns på sidan Mina prenumerationer när du loggar in till Microsoft Cognitive Services-portalen. Det kommer att två nycklar som är associerade med varje prenumeration: en primär nyckel och en sekundär nyckel. Båda kan användas för att anropa API identiskt. Du behöver att skydda prenumerationsnycklar och du kan återskapa prenumerationsnycklar när som helst från Mina prenumerationer sidan och.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Train (LargeFaceList/LargePersonGroup/PersonGroup)

Detta API används för att Förbearbeta den [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup) att se till att den [hitta Liknande](#find-similar)/[identifiering](#identification) prestanda. Om utbildningen inte körs, eller [utbildning Status](#status-train) inte visas som lyckades, ID: t för den här PersonGroup leder till fel.

Mer information finns i referensdokumentationen: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/användardata

Informationen är extra information som associeras med [Person](#person) och [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). Användardata anges av användare för att göra det enklare att använda, förstå och Kom ihåg att data.

Mer information finns i referensdokumentationen: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - uppdatera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - uppdatera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Verifiering

Detta API används för att kontrollera om två ansikten är lika eller inte. Båda sidorna representeras som möter ID: N i förfrågan. Verifiera resultaten innehåller typen Boolean (isIdentical) som anger samma om värdet är true och ett antal fält ([förtroende](#confidence)) som anger hur stor säkerhet.

Mer information finns i referensdokumentationen: [Ansiktsigenkänning – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
