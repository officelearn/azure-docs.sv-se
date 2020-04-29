---
title: Identifiera ansikten i en bild – ansikte
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du använder ansikts igenkänning för att extrahera attribut som kön, ålder eller attityd från en specifik bild.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76169882"
---
# <a name="get-face-detection-data"></a>Hämta ansikts identifierings data

Den här guiden visar hur du använder ansikts igenkänning för att extrahera attribut som kön, ålder eller attityd från en specifik bild. Kodfragmenten i den här hand boken skrivs i C# med hjälp av klient biblioteket för Azure Cognitive Services Face. Samma funktioner är tillgängliga via [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Den här guiden visar hur du:

- Hämta platser och dimensioner för ansikten i en bild.
- Få plats för olika ansikts landmärken, till exempel elever, näsa och mun, i en bild.
- Gissa kön, ålder, känslo och andra attribut för ett identifierat ansikte.

## <a name="setup"></a>Installation

Den här guiden förutsätter att du redan [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) har skapat ett FaceClient `faceClient`-objekt med namnet, med en ansikts prenumerations nyckel och en slut punkts-URL. Härifrån kan du använda funktionen för ansikts igenkänning genom att anropa antingen [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), som används i den här hand boken eller [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Instruktioner för hur du konfigurerar den här funktionen finns i en av snabb starterna.

Den här guiden fokuserar på de olika uppgifterna i identifierings anropet, till exempel vilka argument du kan skicka och vad du kan göra med returnerade data. Vi rekommenderar att du frågar efter enbart de funktioner du behöver. Varje åtgärd tar ytterligare tid att slutföra.

## <a name="get-basic-face-data"></a>Hämta grundläggande ansikts data

Om du vill hitta ansikten och hämta sina platser i en bild anropar du metoden med parametern _returnFaceId_ inställd på **True**. Den här inställningen är standardinställningen.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Du kan fråga de returnerade [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) -objekten för sina unika ID: n och en rektangel som ger facets pixel koordinater.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Information om hur du analyserar facets plats och dimensioner finns i [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Den här rektangeln innehåller vanligt vis ögonen, eyebrows, näsa och munnen. Huvud-, öron-och Chin är inte nödvändigt vis inkluderade. Om du vill använda ansikts rektangeln för att beskära en hel rubrik eller få ett mellanliggande porträtt, kanske för en bild av bild-ID-typ, kan du expandera rektangeln i varje riktning.

## <a name="get-face-landmarks"></a>Hämta ansikts landmärken

[Ansikts landmärken](../concepts/face-detection.md#face-landmarks) är en uppsättning enkla punkter på ett ansikte, till exempel elever eller spets. Om du vill hämta ansikts landmärkes data anger du parametern _returnFaceLandmarks_ till **True**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Följande kod visar hur du kan hämta platserna för näsan och elever:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Du kan också använda ansikts landmärkes data för att korrekt beräkna riktningen på ytan. Du kan till exempel definiera rotationen för ansiktst som en vektor från munnen i mitten till centrum för ögonen. Följande kod beräknar den här vektorn:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

När du känner till ansikts riktningen kan du rotera den rektangulära ytan för att justera den på ett mer korrekt sätt. Om du vill beskära ansikten i en bild kan du program mässigt rotera bilden så att ansikten alltid visas på rätt sätt.

## <a name="get-face-attributes"></a>Hämta ansikts attribut

Förutom ansikts rektanglar och landmärken kan ansikts igenkännings-API: et analysera flera konceptuella attribut för en ansikte. En fullständig lista finns i avsnittet koncept för [ansikts attribut](../concepts/face-detection.md#attributes) .

Om du vill analysera ansikts attribut ställer du in parametern _returnFaceAttributes_ på en lista med [uppräknings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) värden för FaceAttributeType.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Hämta sedan referenser till de data som returnerades och utför fler åtgärder utifrån dina behov.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Mer information om varje attribut finns i konceptuell guide för [ansikts igenkänning och attribut](../concepts/face-detection.md) .

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder olika funktioner för ansikts igenkänning. Sedan integrerar du dessa funktioner i din app genom att följa en djupgående själv studie kurs.

- [Självstudie: Skapa en WPF-app för att visa ansiktsinformation i en bild](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Självstudie: Skapa en Android-app för att upptäcka och rama in ansikten i en bild](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referens dokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referens dokumentation (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)