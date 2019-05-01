---
title: Använd HeadPose för att justera ansiktsrektangeln
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder attributet HeadPose för att rotera ansiktsrektangeln automatiskt.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576508"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>Använd HeadPose attributet för att justera ansiktsrektangeln

I den här guiden använder attributet identifierade ansikte HeadPose, för att rotera rektangeln av ett ansikte-objekt. Exemplet kod i den här guiden från den [Cognitive Services ansikte WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) exempelappen använder .NET SDK.

Ansiktsrektangeln som returneras med varje identifierad ansikte markerar platsen och storleken för ansikte i bilden. Rektangeln justeras som standard alltid med bilden (dess sidorna är perfekt vertikal och horisontell). Detta kan vara ineffektiv för framing sneda ansikten. I situationer där du vill programmässigt beskära ansikten i en bild, är det bra att kunna rotera rektangeln till beskära.

## <a name="explore-the-sample-code"></a>Utforska exempelkoden

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

## <a name="display-the-updated-rectangle"></a>Visa uppdaterade rektangel

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

## <a name="next-steps"></a>Nästa steg

Se den [Cognitive Services ansikte WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) app på GitHub för ett exempel på roterad ansiktsrektanglar. Se den [Ansikts-API-HeadPose exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) appen, som spårar attributet HeadPose i realtid för att identifiera olika head-förflyttningar av typ (nodding, skaka).