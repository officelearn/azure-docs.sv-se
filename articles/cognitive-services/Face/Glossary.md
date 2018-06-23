---
title: Ordlista för Ansikts-API-tjänsten | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ordlistan beskrivs de termer som kan uppstå när du arbetar med Ansikts-API-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351927"
---
# <a name="glossary"></a>Ordlista

## <a name="a"></a>A

#### <a name="Attributes"></a>attribut

Attribut är valfria i den [identifiering](#Detection-Face-Detection) resultat, till exempel [ålder](#Age-Attribute), [kön](#Gender-Attribute), [head attityd](#Head-Pose-Attribute), [ansikte hår](#Facial-Hair-Attribute), [Leende](#Smile-Attribute).
De kan hämtas från den [identifiering](#Detection-Face-Detection) API genom att ange frågeparametrar: returnFaceAttributes. Attribut ge extra information angående valda [ytor](#Face); förutom den [står inför ID](#Face-ID) och [rektangel](#Face-Rectangle).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Age-Attribute"></a>Ålder (attribut)

Ålder är en av de [attribut](#Attributes) som beskriver en viss yta ålder. Attributet ålder är valfritt i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran genom att ange parametern returnFaceAttributes.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="Candidate"></a>Kandidat

Kandidater är i stort sett [identifiering](#Identification) resultat (t.ex. identifierade personer och identifieringar förtroende). En kandidat representeras av den [PersonID](#Person-ID) och [förtroende](#Confidence), som anger att personen som identifieras med hög tillförlitlighet.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="Confidence"></a>Förtroende

Förtroende är ett mått som avslöjar likheten mellan [ytor](#Face) eller [Person](#Person) i numeriska värden – som används i [identifiering](#Identification), och [ verifieringen](#Verification) att ange likheter eftersökt, identifierade och verifierade resultat.

Mer information finns i följande guider: [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"></a>Identifiering/ansikte identifiering

Ansikts-identifiering är åtgärden för att hitta ytor i bilder. Användarna kan ladda upp en bild eller ange en bild-URL i begäran. Identifierade ytor returneras med [står inför ID](#Face-ID) som anger en unik identitet i Ansikts-API. Rektanglar ansikte platser i bilden i pixlar, samt den valfria [attribut](#Attributes) för varje yta som [ålder](#Age-Attribute), [kön](#Gender-Attribute), [head attityd ](#Head-Pose-Attribute), [ansikte hår](#Facial-Hair-Attribute) och [Leende](#Smile-Attribute).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>Yta

Står inför är en enhetlig term resultat som härletts från Ansikts-API relaterat med identifierade ytor. Slutligen står inför representeras av en enhetlig identitet ([ansikte ID](#Face-ID)), ett visst område i bilder ([ansikte rektangel](#Face-Rectangle)), och extra ansikte relaterade [attribut](#Face-Attributes-Facial-Attributes), till exempel [ålder](#Age-Attribute), [kön](#Gender-Attribute), [landmärken](#Face-Landmarks-Facial-Landmarks) och [head attityd](#Head-Pose-Attribute). Dessutom ytor kan returneras från [identifiering](#Detection-Face-Detection).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-API"></a>Ansikts-API

Ansikts-API: T är en molnbaserad API som ger de mest avancerade algoritmerna för framsidan identifiering och Igenkännande. De flesta funktioner för Ansikts-API kan delas in i två kategorier: står inför [identifiering](#Detection-Face-Detection) med [attribut](#Face-Attributes-Facial-Attributes), och ansiktsigenkännings [recognition](#Recognition).

Mer information finns i följande guider: [Ansikts-API översikt](./Overview.md), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="Face-Attributes-Facial-Attributes"></a>Ansikts attribut/ansiktskirurgi attribut

Se [attribut](#Attributes).

#### <a name="Face-ID"></a>Ansikts-ID

Ansikts-ID: T härleds från den [identifiering](#Detection-Face-Detection) resultat, som en sträng som representerar en [står inför](#Face) i [Ansikts-API](#Face-API).

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-Landmarks-Facial-Landmarks"></a>Framsidan landmärken/ansiktskirurgi landmärken

Landmärken är valfria i den [identifiering](#Detection-Face-Detection) resultat; semantiska ansikte punkter, till exempel ögon, näsa och munnen (illustreras på följande bild). Landmärken kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från booleskt nummer returnFaceLandmarks. Om returnFaceLandmarks anges som SANT har returnerade ytor Landmärke attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>Ansikts-rektangel

Framsidan rektangel härleds från den [identifiering](#Detection-Face-Detection) resultat, som är ett stående rektangel (vänster, överkant, bredden och höjden) i bilder i bildpunkter. Det övre vänstra hörnet av en [ansiktsigenkännings](#Face) (vänster, uppifrån), förutom bredd och höjd, anger du står inför storlekar i x och y-axeln respektive.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Facial-Hair-Attribute"></a>Ansikte hår (attribut)

Ansikte hår är en av de [attribut](#Attributes) används för att beskriva ansikte hår längden på tillgängliga ytor. Attributet ansikte hår är valfritt i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från returnFaceAttributes. Om returnFaceAttributes innehåller 'facialHair', har returnerade ytor ansikte hår attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="FaceList"></a>FaceList

FaceList är en samling [PersistedFace](#PersistedFace) och är prisenhet [Sök liknande](#Find-Similar). En FaceList medföljer en [FaceList ID](#FaceList-ID), liksom andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande guider: [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="FaceList-ID"></a>FaceList-ID

FaceList ID är en anges av Användarsträng som används som en identifierare för en [FaceList](#FaceList). FaceList-ID måste vara unikt i prenumerationen.

Mer information finns i följande guider: [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="Find-Similar"></a>Sök efter liknande

Detta API är för/sökfråga liknande ytor baserat på en samling av ytor. Frågan ytor och står inför samlingar visas i form av [står inför ID](#Face-ID) eller [FceList ID](#FaceList-ID)/[LargeFaceList ID](#LargeFaceList-ID) i begäran. Returnerade resultatet är eftersökt liknande ytor som representeras av [står inför ID](#Face-ID) eller [PersistedFace-ID: N](#PersistedFace-ID).

Mer information finns i följande guider: [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="Gender-Attribute"></a>Kön (attribut)

Kön är en av de [attribut](#Attributes) används för att beskriva könen av tillgängliga ytor. Attributet kön är valfritt i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från returnFaceAttributes. Om returnfaceAttributes innehåller 'kön', har de returnerade ytorna kön attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Grouping"></a>Gruppering

Framsidan gruppering är gruppering av en samling av ytor enligt ansikte likheter. Framsidan samlingar anges med framsidan ID samlingar i begäran. På grund av gruppering och liknande ytor grupperas som [grupper](#Groups), och ytor som inte liknar andra står inför kombineras tillsammans som en rörigt grupp. Det finns högst, en [rörigt grupp](#Messy-Group) i resultatet gruppering.

Mer information finns i handboken [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="Groups"></a>Grupper

Grupper är härledda från den [gruppering](#Grouping) resultat. Varje grupp innehåller en samling liknande ansikten, där ytor anges med [står inför ID](#Face-ID).

Mer information finns i handboken [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>HEAD utgöra (attribut)

HEAD attityd är en av de [attribut](#Attributes) som representerar står inför orientering i 3D enligt sammanslagning, breddsteg och positionen vinklar, som visas i följande bild. Värdeintervall för Återställ och yaw är [-180, 180] och [-90, 90] i grader. I den aktuella versionen breddsteg värdet som returneras från identifiering är alltid 0. Attributet head attityd är valfritt i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran av parametern returnFaceAttributes. Om parametern returnFaceAttributes innehåller 'headPose', har returnerade ytor head utgöra attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>Identifiering

Identifiering är att identifiera en eller flera ytor från LargePersonGroup/PersonGroup.
En [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) är en samling [personer](#Person).
Ytor och LargePersonGroup/PersonGroup representeras av [står inför ID](#Face-ID) och [LargePersonGroup-ID: N](#LargePersonGroup-ID)/[PersonGroup-ID: N](#PersonGroup-ID) respektive i den begäran.
Identifierade resultatet är [kandidater](#Candidate), representeras av [personer](#Person) utan problem.
Flera ytor i indata är separat, och varje yta har sin egen identifierade resultat.

> [!NOTE]
> LargePersonGroup/PersonGroup bör få utbildning har innan identifiering. Om LargePersonGroup/PersonGroup inte har tränats, eller utbildning [status](#Status-Train) inte visas som ”lyckades” (d.v.s. ' körs', 'misslyckade' eller 'timeout') begäran svaret är 400.
> 

Mer information finns i följande guider: [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [ LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical är ett booleskt fält av [verifiering](#Verification) resultat som visar om två ytor tillhör samma person.

Mer information finns i handboken [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>U

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Landmärken

Se [står inför landmärken](#Face-Landmarks-Facial-Landmarks).

#### <a name="LargeFaceList"></a>LargeFaceList

LargeFaceList är en samling [PersistedFace](#PersistedFace) och är prisenhet [Sök liknande](#Find-Similar). En LargeFaceList medföljer en [LargeFaceList ID](#LargeFaceList-ID), liksom andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande guider: [LargeFaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - listan står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="LargeFaceList-ID"></a>LargeFaceList-ID

LargeFaceList ID är en anges av Användarsträng som används som en identifierare för en [LargeFaceList](#LargeFaceList). LargeFaceList-ID måste vara unikt i prenumerationen.

Mer information finns i följande guider: [LargeFaceList - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="LargePersonGroup"></a>LargePersonGroup

LargePersonGroup är en samling [personer](#Person) och är prisenhet [identifiering](#Identification). En LargePersonGroup medföljer en [LargePersonGroup ID](#LargePersonGroup-ID), liksom andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande guider: [LargePersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person - listan](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="LargePersonGroup-ID"></a>LargePersonGroup-ID

LargePersonGroup ID är en anges av Användarsträng som används som en identifierare för en [LargePersonGroup](#LargePersonGroup). LargePersonGroup-ID måste vara unikt i prenumerationen.

Mer information finns i följande guider: [LargePersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="Messy-Group"></a>Rörigt grupp

Rörigt grupp har härletts från den [gruppering](#Grouping) resultat, som innehåller ytor inte liknar andra står inför. Varje yta i en rörigt grupp anges av den [står inför ID](#Face-ID).

Mer information finns i handboken [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Namnet (Person)

Namnet är en användarvänlig beskrivande sträng för [Person](#Person). Till skillnad från den [Person ID](#Person-ID), namnet på personer som kan dupliceras i en grupp.

Mer information finns i följande guider: [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Hämta PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Name"></a>Namn (LargePersonGroup/PersonGroup)

Namnet är också en användarvänlig beskrivande sträng för [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Till skillnad från den [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID), namnet på LargePersonGroups/PersonGroups kan dupliceras inom en prenumeration.

Mer information finns i följande guider: [LargePersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [ PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace är en datastruktur i Ansikts-API. PersistedFace levereras med en [PersistedFace ID](#PersistedFace-ID), liksom andra attribut som [namn](#Name), och [användardata](#UserData-User-Data).

Mer information finns i följande guider: [LargeFaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person-ID"></a>Person-ID

Person ID genereras när en [PersistedFace](#PersistedFace) har skapats. Skapa en sträng som representerar den här riktade [Ansikts-API](#Face-API).

Mer information finns i följande guider: [LargeFaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - lägga till står inför](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person"></a>Person

Personen är en datastruktur som hanteras i Ansikts-API. Personen som medföljer en [Person ID](#Person-ID), liksom andra attribut som [namn](#Name), en samling [PersistedFace](#PersistedFace), och [användardata](#UserData-User-Data).

Mer information finns i följande guider: [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Hämta PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Person-ID"></a>Person-ID

Person ID genereras när en [Person](#Person) har skapats. Skapa en sträng som representerar den här personen i [Ansikts-API](#Face-API).

Mer information finns i följande guider: [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - hämta](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Hämta PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup är en samling [personer](#Person) och är prisenhet [identifiering](#Identification). En PersonGroup medföljer en [PersonGroup ID](#PersonGroup-ID), liksom andra attribut som [namn](#Name) och [användardata](#UserData-User-Data).

Mer information finns i följande guider: [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person - listan](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="PersonGroup-ID"></a>PersonGroup-ID

PersonGroup ID är en anges av Användarsträng som används som en identifierare för en [PersonGroup](#PersonGroup). Grupp-ID måste vara unikt i prenumerationen.

Mer information finns i följande guider: [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Utgöra (attribut)

Se [Head utgöra](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="Recognition"></a>Igenkänning

Är en populär modul för framsidan tekniker som [Sök liknande](#Find-Similar), [gruppering](#Grouping), [identifiera](#Identification),[verifierar två står samma eller inte ](#Verification).

Mer information finns i följande guider: [Ansikts - Sök liknande](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [står inför - gruppen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Rektangel (min)

Se [ansikte rektangel](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>Glada (attribut)

Le är en av de [attribut](#Attributes) används för att beskriva tillgängliga ytor le uttryck. Le-attributet är valfritt i den [identifiering](#Detection-Face-Detection) resultaten och kan kontrolleras med en [identifiering](#Detection-Face-Detection) begäran från returnFaceAttributes. Om returnFaceAttributes innehåller 'glada', har returnerade ytor le attribut.

Mer information finns i handboken [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Sökning efter liknande ansikte

Se [Sök liknande](#Find-Similar).

#### <a name="Status-Train"></a>Status (tåg)

Status är en sträng som används för att beskriva hur [utbildning LargeFaceList/LargePersonGroups/PersonGroups](#Train), inklusive notstarted, 'körs', 'har slutförts', 'misslyckade'.

Mer information finns i handboken [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Prenumerationsnyckel

Prenumerationen nyckeln är en sträng som du måste ange som en frågesträngsparameter för att anropa API: er står inför. Nyckeln för prenumerationen hittar Mina prenumerationer på sidan när du loggar in på Microsoft kognitiva Services-portalen. Det ska finnas två nycklar som är associerade med varje prenumeration: en primär nyckel och en sekundär nyckel. Båda kan användas för att anropa API identiskt. Du behöver skydda tangenterna prenumeration och du kan återskapa nycklar för prenumerationen när som helst från Mina prenumerationer-sidan.

## <a name="t"></a>T

#### <a name="Train"></a>Träna (PersonGroup-LargeFaceList/LargePersonGroup)

Detta API används för att bearbeta före den [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) så den [hitta Liknande](#Find-Similar)/[identifiering](#Identification) prestanda. Om utbildningen inte körs, eller [utbildning Status](#Status-Train) inte visas som har installerats, ID: t för den här PersonGroup leder till fel.

Mer information finns i följande guider: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [står inför – identifiera ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>UserData/användardata

Informationen är extra information som associeras med [Person](#Person) och [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Användardata anges av användare för att göra det enklare att använda, förstå och Kom ihåg data.

Mer information finns i följande guider: [LargePersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - uppdatera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - uppdatering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - skapa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [ Uppdatera PersonGroup Person -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="Verification"></a>Verifieringen

Detta API används för att kontrollera om två ytor är lika eller inte. Båda sidorna representeras som står inför ID: N i förfrågan. Verifiera resulterar i ett booleskt fält ([isIdentical](#Is-Identical)) som anger samma om värdet är true och ett tal-fält ([förtroende](#Confidence)) som anger konfidensnivån.

Mer information finns i handboken [står inför – verifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Y

## <a name="z"></a>Z
