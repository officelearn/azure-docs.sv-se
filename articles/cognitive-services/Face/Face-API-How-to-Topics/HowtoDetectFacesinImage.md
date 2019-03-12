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
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588779"
---
# <a name="get-face-detection-data"></a>Hämta data för identifiering av ansikte

Den här guiden visar hur du använder ansiktsigenkänning för att extrahera attribut som kön, ålder eller attityd från en viss avbildning. Kodsnuttarna i den här guiden är skrivna i C# med hjälp av klientbiblioteket Ansikts-API, men samma funktion är tillgänglig via den [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Den här guiden visar hur du:

- Hämta platser och dimensioner för ansikten i en bild.
- Hämta platserna för olika ansiktslandmärken (elever, näsa, munnen och så vidare) i en bild.
- Gissa kön, ålder, och känslor och andra attribut för ett identifierat ansikte.

## <a name="setup"></a>Konfiguration

Den här handboken förutsätts att du redan har skapat en **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objekt, med namnet `faceClient`, med ett ansikte prenumeration nyckel och slutpunkt-URL. Härifrån kan du använda funktionen för identifiering av ansikte genom att anropa antingen **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (används i den här guiden) eller **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Se den [identifiera ansikten Snabbstart för C# ](../quickstarts/csharp-detect-sdk.md) för anvisningar om hur du konfigurerar detta.

Den här guiden fokuserar på ärendets natur identifiera anropet&mdash;vilka argument som du kan skicka och vad du kan göra med data som returneras. Vi rekommenderar att endast fråga efter för de funktioner du behöver, eftersom varje åtgärd tar extra tid att slutföra.

## <a name="get-basic-face-data"></a>Hämta data för grundläggande ansikte

Om du vill hitta ansikten och få deras platser i en bild, anropar du metoden med den _returnFaceId_ parameteruppsättning till **SANT** (standard).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Den returnerade **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objekt kan frågas för deras unika ID: N och en rektangel som ger pixel koordinaterna för ansiktet.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Se **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** information om hur du Parsar plats och mått på ansiktet. Den här rektangeln innehåller vanligtvis ögon, eyebrows, näsa och munnen; överst head, öronen och hakan ingår inte nödvändigtvis. Om du tänker använda ansiktsrektangeln beskära en fullständig huvud- eller mitten som visar stående (en foto-ID typ av avbildning) måste du expandera rektangeln med en viss marginal i varje riktning.

## <a name="get-face-landmarks"></a>Hämta ansiktslandmärken

Ansiktslandmärken är en uppsättning enkelt att hitta punkter på ett ansikte, till exempel eleverna eller visarens näsa. Du kan hämta ansikte landmärken data genom att ange den _returnFaceLandmarks_ parameter **SANT**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Som standard finns 27 fördefinierade landmärkespunkter. Följande bild visar alla 27 punkter:

![Ett ansikte diagram med alla 27 ansiktslandmärken som en etikett](../Images/landmarks.1.jpg)

Det är punkter returneras i antal bildpunkter, precis som ansikte rektangel ramen. Följande kod visar hur du kan hämta platserna för näsa och elever:

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

Ansiktslandmärken data kan också användas för att korrekt kunna beräkna ansiktet riktning. Vi kan till exempel definiera rotationen av de står inför som en vector från mitten av munnen till mitten av ögonen. Koden nedan beräknar den här vektor:

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

Att känna till riktningen för ansiktet, kan du sedan rotera ramen rektangulär ansikte för att justera den mer korrekt. Om du vill beskära ansikten i en bild kan du programmässigt rotera bilden så att ansiktena visas alltid stående.

## <a name="get-face-attributes"></a>Hämta ansiktsattribut

Förutom ansiktsrektanglar och landmärken kan kan ansiktsigenkännings-API analysera flera konceptuella attribut av ett ansikte. Exempel på dessa är:

- Ålder
- Kön
- Leendets intensitet
- Ansiktsbehåring
- Glasögon
- 3D-head attityd
- Känsla

> [!IMPORTANT]
> Dessa attribut förutse med statistisk algoritmerna och kanske inte alltid är korrekt. Var försiktig när du fattar beslut utifrån attributdata.
>

Om du vill analysera ansiktsattribut, ange den _returnFaceAttributes_ parametern till en lista över **[FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** värden.

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

Sedan hämta referenser till data som returneras och utföra ytterligare åtgärder efter dina behov.

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

Om du vill veta mer om alla attribut kan referera till den [ordlista](../Glossary.md).

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du använder olika funktioner för ansiktsigenkänning. Sedan kan du se den [ordlista](../Glossary.md) för en mer detaljerad information om ansikts-data som du har hämtats.

## <a name="related-topics"></a>Relaterade ämnen

- [Referensdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referensdokumentation (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
