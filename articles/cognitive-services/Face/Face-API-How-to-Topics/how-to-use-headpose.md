---
title: Använda attributet HeadPose
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder attributet HeadPose att rotera automatiskt ansiktsrektangeln eller identifiera head gester på en video feed.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058574"
---
# <a name="use-the-headpose-attribute"></a>Använda attributet HeadPose

I den här handboken visas hur du kan använda attributet HeadPose för ett identifierat ansikte för att aktivera vissa viktiga scenarier.

## <a name="rotate-the-face-rectangle"></a>Rotera ansiktsrektangeln

Ansiktsrektangeln som returneras med varje identifierad ansikte markerar platsen och storleken för ansikte i bilden. Rektangeln justeras som standard alltid med bilden (dess sidorna är vertikal och horisontell). Detta kan vara ineffektiv för framing sneda ansikten. I situationer där du vill programmässigt beskära ansikten i en bild, är det bättre att kunna rotera rektangeln till beskära.

Den [Cognitive Services ansikte WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) exempelappen använder HeadPose-attribut för att rotera dess identifierade ansiktsrektanglar.

### <a name="explore-the-sample-code"></a>Utforska exempelkoden

Du kan rotera ansiktsrektangeln programmässigt med hjälp av attributet HeadPose. Om du anger det här attributet när du söker efter ansikten (se [hur du identifierar ansikten](HowtoDetectFacesinImage.md)), du kommer att kunna frågar den senare. Följande metod från den [Cognitive Services ansikte WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) app tar en lista med **DetectedFace** objekt och returnerar en lista över **[ansikte](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** objekt. **Ansikte** här är en anpassad klass som lagrar står inför data, inklusive uppdaterade rektangel koordinaterna. Nya värden beräknas för **upp**, **vänstra**, **bredd**, och **höjd**, och ett nytt fält **FaceAngle**anger rotationen.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Visa uppdaterade rektangel

Härifrån kan du använda den returnerade **ansikte** objekt i din skärm. Följande rader från [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) visar hur den nya rektangeln återges från dessa data:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Identifiera head gester

Du kan identifiera head gester som nodding och head skaka genom att spåra HeadPose ändringar i realtid. Du kan använda den här funktionen som en anpassad liveness detektor.

Liveness identifiering är uppgiften att bestämma att ett ämne är en verklig person och inte en bild eller video-representation. En head gest detektor kan fungera som ett sätt att kontrollera liveness, särskilt i stället för en bild-representation av en person.

> [!CAUTION]
> Om du vill identifiera head gester i realtid, måste du anropa Ansikts-API med en hög hastighet (mer än en gång per sekund). Om du har en prenumeration på kostnadsfri nivå (f0) kan vara det inte möjligt. Om du har en betald nivå-prenumeration kan du se till att du har beräknade kostnaderna för att göra snabba API anrop för head rörelser för identifiering.

Se den [Ansikts-API-HeadPose exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) på GitHub för ett exempel på head rörelser för identifiering.

## <a name="next-steps"></a>Nästa steg

Se den [Cognitive Services ansikte WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) app på GitHub för ett exempel på roterad ansiktsrektanglar. Se den [Ansikts-API-HeadPose exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) appen, som spårar attributet HeadPose i realtid för att identifiera head förflyttningar.