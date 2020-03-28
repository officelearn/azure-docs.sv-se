---
title: Använda attributet HeadPose
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder attributet HeadPose för att automatiskt rotera ansiktsrektangeln eller identifiera huvudgester i en videofeed.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169795"
---
# <a name="use-the-headpose-attribute"></a>Använda attributet HeadPose

I den här guiden ser du hur du kan använda attributet HeadPose för ett upptäckt ansikte för att aktivera vissa viktiga scenarier.

## <a name="rotate-the-face-rectangle"></a>Rotera ansiktsrektangeln

Ansiktsrektangeln, returneras med varje upptäckt ansikte, markerar platsen och storleken på ansiktet i bilden. Som standard är rektangeln alltid justerad mot bilden (sidorna är lodräta och vågräta). Detta kan vara ineffektivt för inramning vinklade ytor. I situationer där du vill beskära ansikten i en bild programmatiskt är det bättre att kunna rotera rektangeln så att den beskärs.

Exempelappen [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) använder attributet HeadPose för att rotera de upptäckta ansiktsrektanglarna.

### <a name="explore-the-sample-code"></a>Utforska exempelkoden

Du kan programmässigt rotera ansiktsrektangeln med attributet HeadPose. Om du anger det här attributet när du upptäcker ansikten (se Så här identifierar du [ansikten)](HowtoDetectFacesinImage.md)kan du fråga det senare. Följande metod från [appen Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) tar en lista över **DetectedFace-objekt** och returnerar en lista över **[ansiktsobjekt.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** **Face** här är en anpassad klass som lagrar ansiktsdata, inklusive de uppdaterade rektangelkoordinaterna. Nya värden beräknas för **överkant,** **vänster,** **bredd**och **höjd**och ett nytt fält **FaceAngle** anger rotationen.

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

### <a name="display-the-updated-rectangle"></a>Visa den uppdaterade rektangeln

Härifrån kan du använda de **Face** returnerade Face-objekten på skärmen. Följande rader från [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) visar hur den nya rektangeln återges från dessa data:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Identifiera huvudgester

Du kan upptäcka huvudgester som nickande och huvudskakningar genom att spåra HeadPose-ändringar i realtid. Du kan använda den här funktionen som en anpassad liveness detektor.

Liveness upptäckt är uppgiften att fastställa att ett ämne är en verklig person och inte en bild eller video representation. En huvudgest detektor kan fungera som ett sätt att hjälpa till att kontrollera livlighet, särskilt i motsats till en bild representation av en person.

> [!CAUTION]
> Om du vill identifiera huvudgester i realtid måste du anropa Ansikts-API:et med hög hastighet (mer än en gång per sekund). Om du har en prenumeration på den kostnadsfria nivån (f0) är detta inte möjligt. Om du har en prenumeration på betald nivå kontrollerar du att du har beräknat kostnaderna för att ringa snabba API-anrop för identifiering av huvudgester.

Se [face headpose-exemplet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) på GitHub för ett fungerande exempel på identifiering av huvudgester.

## <a name="next-steps"></a>Nästa steg

Se [Appen Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) på GitHub för ett fungerande exempel på roterade ansiktsrektanglar. Eller se appen [Face HeadPose Sample,](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) som spårar attributet HeadPose i realtid för att upptäcka huvudrörelser.