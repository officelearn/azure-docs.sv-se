---
title: Ordlista – Ansikts-API-tjänsten
titleSuffix: Azure Cognitive Services
description: Ordlistan beskrivs de termer som du kan stöta på när du arbetar med Ansikts-API-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 215b780bc403ab2df40567c3eb5a7ae86c9fe130
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127371"
---
# <a name="glossary"></a>Ordlista

## <a name="a"></a>A

#### <a name="attributes"></a>Attribut

Attribut är valfria i de [identifiering](#Detection-Face-Detection) resultat, till exempel [ålder](#Age-Attribute), [kön](#Gender-Attribute), [head attityd](#Head-Pose-Attribute), [ansiktsigenkänning hår](#Facial-Hair-Attribute), [Leende](#Smile-Attribute).
De kan hämtas från den [identifiering](#Detection-Face-Detection) API genom att ange frågeparametrar: returnFaceAttributes. Attribut ge extra information angående valda [ansikten](#Face), förutom den [står inför ID](#Face-ID) och [rektangel](#Face-Rectangle).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Ålder (attribut)

Ålder är en av de [attribut](#Attributes) som beskriver en ålder för ett visst ansikte. Ålder-attributet är valfri i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran genom att ange parametern returnFaceAttributes.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="candidate"></a>Kandidat

Kandidater är i stort sett [identifiering](#Identification) resultat (t.ex. identifierade personer och identifieringar förtroende). En kandidat representeras av den [PersonID](#Person-ID) och [förtroende](#Confidence), som anger att personen som identifieras med hög tillförlitlighet.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Konfidensbedömning

Säkerheten är ett mått som avslöja likheten mellan [ansikten](#Face) eller [Person](#Person) i numeriska värden – som används i [identifiering](#Identification), och [ verifiering](#Verification) att ange likheter med sökt, identifieras och är verifierad resultat.

Mer information finns i följande handböcker: [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Identifiering/Ansiktsigenkänning

Ansiktsigenkänning är åtgärden för att hitta ansikten i bilder. Användarna kan ladda upp en bild eller ange en bild-URL i förfrågan. Identifierade ansikten returneras med [står inför ID: N](#Face-ID) som anger en unik identitet i Ansikts-API. Ansikts-platser i avbildningen i bildpunkter, samt den valfria rektanglar [attribut](#Attributes) för varje ansikte som [ålder](#Age-Attribute), [kön](#Gender-Attribute), [head attityd ](#Head-Pose-Attribute), [ansiktsigenkänning hår](#Facial-Hair-Attribute) och [Leende](#Smile-Attribute).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="face"></a>Ansikte

Är en enhetlig term för resultaten som härletts från Ansikts-API som är relaterade med identifierade ansikten. Slutligen ansikte representeras av en enhetlig identitet ([Face ID](#Face-ID)), en specifik region i bilder ([Ansiktsrektangeln](#Face-Rectangle)), och extra ansikte relaterade [attribut](#Face-Attributes-Facial-Attributes), till exempel [ålder](#Age-Attribute), [kön](#Gender-Attribute), [ansiktslandmärken](#Face-Landmarks-Facial-Landmarks) och [head attityd](#Head-Pose-Attribute). Dessutom ansikten kan returneras från [identifiering](#Detection-Face-Detection).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>Ansikts-API

Ansikts-API är ett molnbaserad API som ger de mest avancerade algoritmerna för ansiktsigenkänning och taligenkänning. De flesta funktioner för Ansikts-API kan delas in i två kategorier: ansikte [identifiering](#Detection-Face-Detection) med [attribut](#Face-Attributes-Facial-Attributes), och ansiktsigenkänning [erkännande](#Recognition).

Mer information finns i följande handböcker: [Ansikts-API-översikt](./Overview.md), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Attribut/Videodetektion Ansiktsattribut

Se [attribut](#Attributes).

#### <a name="face-id"></a>Ansikts-ID

Face ID härleds från den [identifiering](#Detection-Face-Detection) resultat, där en sträng som representerar en [ansikte](#Face) i [Ansikts-API](#Face-API).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Landmärken/Videodetektion Ansiktslandmärken

Landmärken är valfria i de [identifiering](#Detection-Face-Detection) resultat; semantiska ansiktsigenkänning punkter, till exempel ögon, näsa och munnen (illustreras i följande bild). Landmärken kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från booleskt nummer returnFaceLandmarks. Om returnFaceLandmarks anges som true, har returnerade ansikten landmärken attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Med rektangel

Ansiktsrektangeln härleds från den [identifiering](#Detection-Face-Detection) resultat, vilket är en stående rektangeln (vänster, överkant, bredd, höjd) i bilder i pixlar. Det övre vänstra hörnet av en [ansikte](#Face) (vänster, uppifrån), förutom bredd och höjd, anger du ansikte storlekar i x och y-axelns respektive.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Ansiktsigenkänning hår (attribut)

Ansiktsigenkänning hår är en av de [attribut](#Attributes) används för att beskriva ansiktsigenkänning hår längden på tillgängliga ansikten. Ansiktsigenkänning hår-attributet är valfri i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från returnFaceAttributes. Om returnFaceAttributes innehåller 'facialHair', har returnerade ansikten ansiktsigenkänning hår attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList är en samling [PersistedFace](#PersistedFace) och är Faktureringsenhet [Sök liknande](#Find-Similar). En FaceList levereras med en [FaceList ID](#FaceList-ID), samt andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande handböcker: [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>FaceList-ID

FaceList ID: T är en sträng för anges av användaren som används som en identifierare för en [FaceList](#FaceList). FaceList-ID måste vara unikt inom prenumerationen.

Mer information finns i följande handböcker: [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Hitta liknande

Detta API fungerar som en sökfråga/liknande ansikten baserat på en grupp med ansikten. Fråga ansikten och ansikte samlingar representeras som [står inför ID: N](#Face-ID) eller [FceList ID](#FaceList-ID)/[LargeFaceList ID](#LargeFaceList-ID) i begäran. Returnerade resultat har sökt liknande ansikten, representeras av [står inför ID: N](#Face-ID) eller [PersistedFace-ID: N](#PersistedFace-ID).

Mer information finns i följande handböcker: [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Kön (attribut)

Kön är en av de [attribut](#Attributes) används för att beskriva kön med tillgängliga ansikten. Attributet kön är valfri i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från returnFaceAttributes. Om returnfaceAttributes innehåller 'kön', har returnerade ansikten kön attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Gruppering

Ansiktsgruppering är gruppering av en grupp med ansikten enligt ansiktsigenkänning likheter. Ansikte samlingar anges med face ID samlingar i begäran. Till följd av gruppering, liknande ansikten grupperas som [grupper](#Groups), och ansikten som inte liknar andra ansikte slås samman tillsammans som en röriga grupp. Det finns på den mest en [röriga grupp](#Messy-Group) i resultatet gruppering.

Mer information finns i handboken [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Grupper

Grupper härleds från den [gruppering](#Grouping) resultat. Varje grupp innehåller en uppsättning liknande ansikten, där ansikten anges med [står inför ID: N](#Face-ID).

Mer information finns i handboken [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>HEAD utgöra (attribut)

HEAD attityd är en av de [attribut](#Attributes) att representerar står inför orientering i 3D-rymden enligt distributionen, försäljningsargument och positionen vinklar, enligt följande bild. Värdeintervall för rulla och yaw är [-180, 180] och [-90, 90] i grader. I den aktuella versionen försäljningsargument värdet som returneras från identifiering är alltid 0. Attributet head attityd är valfri i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran av parametern returnFaceAttributes. Om returnFaceAttributes parametern innehåller ”headPose”, har de returnerade ansiktena head utgöra attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identifiering

Identifiering är att identifiera en eller flera ansikten från LargePersonGroup/PersonGroup.
En [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) är en samling [personer](#Person).
Ansikten och LargePersonGroup/PersonGroup representeras av [står inför ID: N](#Face-ID) och [LargePersonGroup-ID: N](#LargePersonGroup-ID)/[PersonGroup-ID: N](#PersonGroup-ID) respektive i den begäran.
Identifierade resultaten [kandidater](#Candidate), representeras av [personer](#Person) med tillförsikt.
Flera ansikten i inkommande betraktas separat och varje ansikte har sin egen identifierade resultatet.

> [!NOTE]
> LargePersonGroup/PersonGroup bör utbildas har innan identifiering. Om du inte har tränats LargePersonGroup/PersonGroup eller utbildningen [status](#Status-Train) inte visas som ”lyckades” är (d.v.s. ”körs”, ”misslyckad” eller ”timeout-), svaret är 400.
> 

Mer information finns i följande handböcker: [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [ LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical är ett booleskt fält av [verifiering](#Verification) resultat som visar om två ansikten tillhör samma person.

Mer information finns i handboken [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>U

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Landmärken

Se [står inför landmärken](#Face-Landmarks-Facial-Landmarks).

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList är en samling [PersistedFace](#PersistedFace) och är Faktureringsenhet [Sök liknande](#Find-Similar). En LargeFaceList levereras med en [LargeFaceList ID](#LargeFaceList-ID), samt andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande handböcker: [LargeFaceList – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList – lista ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList-ID

LargeFaceList ID: T är en sträng för anges av användaren som används som en identifierare för en [LargeFaceList](#LargeFaceList). LargeFaceList-ID måste vara unikt inom prenumerationen.

Mer information finns i följande handböcker: [LargeFaceList – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup är en samling [personer](#Person) och är Faktureringsenhet [identifiering](#Identification). En LargePersonGroup levereras med en [LargePersonGroup ID](#LargePersonGroup-ID), samt andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande handböcker: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>LargePersonGroup-ID

LargePersonGroup ID: T är en sträng för anges av användaren som används som en identifierare för en [LargePersonGroup](#LargePersonGroup). LargePersonGroup-ID måste vara unikt inom prenumerationen.

Mer information finns i följande handböcker: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Röriga grupp

Röriga grupp härleds från den [gruppering](#Grouping) resultat; som innehåller ansikten inte liknar andra ansikte. Varje ansikte i en röriga grupp visas med den [står inför ID](#Face-ID).

Mer information finns i handboken [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Namn (Person)

Namnet är en användarvänlig beskrivande sträng för [Person](#Person). Till skillnad från den [Person-ID](#Person-ID), kan skapas dubbletter av namn på personer i en grupp.

Mer information finns i följande handböcker: [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Hämta PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Namn (LargePersonGroup/PersonGroup)

Namnet är också en användarvänlig beskrivande sträng för [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Till skillnad från den [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID), namnet på LargePersonGroups/PersonGroups kan kopieras och användas inom en prenumeration.

Mer information finns i följande handböcker: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [ PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace är en datastruktur i Ansikts-API. PersistedFace levereras med en [PersistedFace ID](#PersistedFace-ID), samt andra attribut som [namn](#Name), och [användardata](#UserData-User-Data).

Mer information finns i följande handböcker: [LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>Person-ID

Person-ID genereras när en [PersistedFace](#PersistedFace) har skapats. En sträng har skapats för att representera den här ansikte i [Ansikts-API](#Face-API).

Mer information finns i följande handböcker: [LargeFaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList – Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - Lägg till ansikte](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Person

Person är en datastruktur som hanteras i Ansikts-API. Person som levereras med en [Person-ID](#Person-ID), samt andra attribut som [namn](#Name), en samling [PersistedFace](#PersistedFace), och [användardata](#UserData-User-Data).

Mer information finns i följande handböcker: [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Hämta PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>Person-ID

Person-ID genereras när en [Person](#Person) har skapats. En sträng har skapats för att representera den här personen i [Ansikts-API](#Face-API).

Mer information finns i följande handböcker: [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Hämta PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

PersonGroup är en samling [personer](#Person) och är Faktureringsenhet [identifiering](#Identification). En PersonGroup levereras med en [PersonGroup ID](#PersonGroup-ID), samt andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande handböcker: [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>PersonGroup-ID

PersonGroup ID: T är en sträng för anges av användaren som används som en identifierare för en [PersonGroup](#PersonGroup). Grupp-ID måste vara unikt inom prenumerationen.

Mer information finns i följande handböcker: [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Utgöra (attribut)

Se [Head utgöra](#Head-Pose-Attribute).

## <a name="q"></a>FRÅGOR OCH

## <a name="r"></a>R

#### <a name="recognition"></a>Bekräftelse

Igenkänning av är en populär modul för ansikts-teknik som [Sök liknande](#Find-Similar), [gruppering](#Grouping), [identifiera](#Identification),[verifierar att två ansikten samma eller inte ](#Verification).

Mer information finns i följande handböcker: [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – gruppera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Rektangel (framsidan)

Se [ansiktsrektangeln](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="smile-attribute"></a>Le (attribut)

Leende är en av de [attribut](#Attributes) används för att beskriva Leende-uttrycket för tillgängliga ansikten. Attributet Leende är valfri i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från returnFaceAttributes. Om returnFaceAttributes innehåller 'le ”, har de returnerade ansiktena Leende attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Sökning efter liknande ansikte

Se [Sök liknande](#Find-Similar).

#### <a name="status-train"></a>Status (Train)

Statusen är en sträng som används för att beskriva hur du [utbildning LargeFaceList/LargePersonGroups/PersonGroups](#Train), inklusive notstarted, ”kör”, ”lyckades”, ”misslyckades”.

Mer information finns i handboken [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Prenumerationsnyckel

Prenumerationsnyckel är en sträng som du vill ange som en frågesträngsparameter för att kunna anropa Ansikts-API: er. Prenumerationsnyckeln finns på sidan Mina prenumerationer när du loggar in till Microsoft Cognitive Services-portalen. Det kommer att två nycklar som är associerade med varje prenumeration: en primär nyckel och en sekundär nyckel. Båda kan användas för att anropa API identiskt. Du behöver att skydda prenumerationsnycklar och du kan återskapa prenumerationsnycklar när som helst från Mina prenumerationer sidan och.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Träna (LargeFaceList/LargePersonGroup/PersonGroup)

Detta API används för att Förbearbeta den [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) att se till att den [hitta Liknande](#Find-Similar)/[identifiering](#Identification) prestanda. Om utbildningen inte körs, eller [utbildning Status](#Status-Train) inte visas som lyckades, ID: t för den här PersonGroup leder till fel.

Mer information finns i följande handböcker: [LargeFaceList – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup – träna](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [står inför – identifiera ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/användardata

Informationen är extra information som associeras med [Person](#Person) och [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Användardata anges av användare för att göra det enklare att använda, förstå och Kom ihåg att data.

Mer information finns i följande handböcker: [LargePersonGroup – skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - uppdatera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Uppdatera PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Verifiering

Detta API används för att kontrollera om två ansikten är lika eller inte. Båda sidorna representeras som möter ID: N i förfrågan. Verifiera resultaten innehåller typen Boolean ([isIdentical](#Is-Identical)) som anger samma om värdet är true och ett antal fält ([förtroende](#Confidence)) som anger hur stor säkerhet.

Mer information finns i handboken [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Y

## <a name="z"></a>Z
