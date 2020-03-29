---
title: Identifiera ansikten i en bild - Ansikte
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du använder ansiktsigenkänning för att extrahera attribut som kön, ålder eller posering från en viss bild.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169882"
---
# <a name="get-face-detection-data"></a>Hämta ansiktsidentifieringsdata

Den här guiden visar hur du använder ansiktsigenkänning för att extrahera attribut som kön, ålder eller posering från en viss bild. Kodavsnitten i den här guiden skrivs i C# med hjälp av Azure Cognitive Services Face-klientbiblioteket. Samma funktioner är tillgängliga via [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Den här guiden visar hur du:

- Hämta platser och dimensioner av ansikten i en bild.
- Få platserna för olika ansikte landmärken, till exempel elever, näsa och mun, i en bild.
- Gissa kön, ålder, känslor och andra attribut i ett upptäckt ansikte.

## <a name="setup"></a>Installation

Den här guiden förutsätter att du redan har `faceClient`skapat ett [FaceClient-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) med en face-prenumerationsnyckel och slutpunkts-URL. Härifrån kan du använda ansiktsigenkänning funktionen genom att ringa antingen [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), som används i den här guiden, eller [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Instruktioner om hur du konfigurerar den här funktionen följer du en av snabbstarterna.

Den här guiden fokuserar på detaljerna i identifieringsanropet, till exempel vilka argument du kan skicka och vad du kan göra med returnerade data. Vi rekommenderar att du bara frågar efter de funktioner du behöver. Varje åtgärd tar extra tid att slutföra.

## <a name="get-basic-face-data"></a>Hämta grundläggande ansiktsdata

Om du vill hitta ansikten och hämta deras platser i en bild anropar du metoden med parametern _returnFaceId_ inställd på **true**. Den här inställningen är standardinställningen.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Du kan fråga de returnerade [DetectedFace-objekten](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) efter deras unika ID och en rektangel som ger pixelkoordinaterna för ansiktet.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Information om hur du tolkar sidans placering och dimensioner finns i [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Vanligtvis innehåller denna rektangel ögon, ögonbryn, näsa och mun. Toppen av huvud, öron och haka ingår inte nödvändigtvis. Om du vill använda ansiktsrektangeln för att beskära ett komplett huvud eller få ett porträtt i mitten av bilden, kanske för en bild av foto-ID-typ, kan du expandera rektangeln i varje riktning.

## <a name="get-face-landmarks"></a>Få ansikte landmärken

[Face landmärken](../concepts/face-detection.md#face-landmarks) är en uppsättning lätt att hitta punkter på ett ansikte, såsom eleverna eller spetsen på näsan. Om du vill hämta banbrytande data anger du parametern _returnFaceLandmarks_ till **true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Följande kod visar hur du kan hämta platserna för näsan och eleverna:

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

Du kan också använda ansiktslandmärken för att exakt beräkna ansiktets riktning. Du kan till exempel definiera ansiktets rotation som en vektor från mitten av munnen till mitten av ögonen. Följande kod beräknar den här vektorn:

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

När du känner till ansiktets riktning kan du rotera den rektangulära ansiktsramen så att den justeras mer korrekt. Om du vill beskära ansikten i en bild kan du programmässigt rotera bilden så att ansiktena alltid visas upprätt.

## <a name="get-face-attributes"></a>Få ansiktsattribut

Förutom ansiktsrektanglar och landmärken kan ansiktsidentifierings-API:et analysera flera konceptuella attribut för ett ansikte. En fullständig lista finns i konceptfältet [Ansiktsattribut.](../concepts/face-detection.md#attributes)

Om du vill analysera ansiktsattribut anger du parametern _returnFaceAttributes_ till en lista över [FaceAttributeType Enum-värden.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)

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

Hämta sedan referenser till returnerade data och gör fler åtgärder efter dina behov.

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

Mer information om vart och ett av attributen finns i konceptguiden [för ansiktsidentifiering och attribut.](../concepts/face-detection.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur du använder de olika funktionerna i ansiktsigenkänning. Integrera sedan dessa funktioner i appen genom att följa en djupgående självstudiekurs.

- [Självstudie: Skapa en WPF-app för att visa ansiktsinformation i en bild](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Självstudie: Skapa en Android-app för att upptäcka och rama in ansikten i en bild](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referensdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referensdokumentation (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)