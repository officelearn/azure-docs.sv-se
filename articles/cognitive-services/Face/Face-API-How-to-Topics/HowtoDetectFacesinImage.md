---
title: Identifiera ansikten i en bild - Ansikts-API
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder olika data som returneras av funktionen för identifiering av ansikte.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124553"
---
# <a name="get-face-detection-data"></a>Hämta data för identifiering av ansikte

Den här guiden visar hur du använder ansiktsigenkänning för att extrahera attribut som kön, ålder eller attityd från en viss avbildning. Kodsnuttarna i den här guiden är skrivna i C# med hjälp av klientbiblioteket för Azure Cognitive Services Ansikts-API. Samma funktion är tillgänglig via den [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Den här guiden visar hur du:

- Hämta platser och dimensioner för ansikten i en bild.
- Hämta platserna för olika ansiktslandmärken, till exempel elever och näsa munnen i en bild.
- Gissa kön, ålder, känslor och andra attribut för ett identifierat ansikte.

## <a name="setup"></a>Konfiguration

Den här handboken förutsätts att du redan skapat en [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) objekt, med namnet `faceClient`, med ett ansikte prenumeration nyckel och slutpunkt-URL. Härifrån kan du använda funktionen för identifiering av ansikte genom att anropa antingen [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), som används i den här guiden eller [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Anvisningar för hur du ställer in den här funktionen finns i den [identifiera ansikten Snabbstart för C# ](../quickstarts/csharp-detect-sdk.md).

Den här guiden fokuserar på ärendets natur identifiera-anrop, till exempel vilka argument som du kan skicka och vad du kan göra med data som returneras. Vi rekommenderar att du fråga efter endast de funktioner som du behöver. Varje åtgärd tar extra tid att slutföra.

## <a name="get-basic-face-data"></a>Hämta data för grundläggande ansikte

Om du vill hitta ansikten och få deras platser i en bild, anropar du metoden med den _returnFaceId_ parameteruppsättning till **SANT**. Den här inställningen är standardinställningen.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Du kan fråga den returnerade [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objekt för deras unika ID: N och en rektangel som ger pixel koordinaterna för ansiktet.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Information om hur du Parsar plats och mått av de står inför finns [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Den här rektangeln innehåller vanligtvis ögon, eyebrows, näsa och munnen. Överst head, öronen och hakan är inte nödvändigtvis ingår. Om du vill använda ansiktsrektangeln beskära en fullständig head eller hämta en mitten som visar stående kanske i ett foto-ID-typen image, kan du expandera rektangeln i varje riktning.

## <a name="get-face-landmarks"></a>Hämta ansiktslandmärken

[Står inför landmärken](../concepts/face-detection.md#face-landmarks) består av ett ansikte, till exempel eleverna eller tips främre enkelt att hitta punkter. Om du vill hämta ansikte landmärken data, ange den _returnFaceLandmarks_ parameter **SANT**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Följande kod visar hur du kan hämta platserna för näsa och elever:

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

Du kan också använda ansiktslandmärken data att korrekt kunna beräkna ansiktet riktning. Du kan exempelvis definiera rotationen av de står inför som en vector från mitten av munnen till mitten av ögonen. Följande kod beräknar den här vektor:

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

Du kan rotera ramen rektangulär ansikte för att justera den mer korrekt när du vet att de står inför riktning. Beskär ansikten i en bild, kan du programmässigt rotera bilden så att ansiktena visas alltid stående.

## <a name="get-face-attributes"></a>Hämta ansiktsattribut

Förutom ansiktsrektanglar och landmärken kan kan ansiktsigenkännings-API analysera flera konceptuella attribut av ett ansikte. En fullständig lista finns i den [Ansiktsattribut](../concepts/face-detection.md#attributes) konceptuella avsnittet.

Om du vill analysera ansiktsattribut, ange den _returnFaceAttributes_ parametern till en lista över [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) värden.

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

Sedan hämta referenser till data som returneras och utföra flera åtgärder efter dina behov.

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

Läs mer om alla attribut i den [Ansiktsigenkänning och attribut](../concepts/face-detection.md) konceptuella guide.

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du använder olika funktioner för ansiktsigenkänning. Integrera de här funktionerna i din app genom att följa detaljerade självstudier.

- [Självstudie: Skapa en WPF-app för att visa ansikts-data i en bild](../Tutorials/FaceAPIinCSharpTutorial.md)
- [Självstudie: Skapa en Android-app för att upptäcka och RAM ansikten i en bild](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Referensdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referensdokumentation (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)