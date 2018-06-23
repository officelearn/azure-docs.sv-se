---
title: Identifiera ytor i bilder med Ansikts-API | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Använd Ansikts-API i kognitiva Services för att identifiera ytor i bilder.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351729"
---
# <a name="how-to-detect-faces-in-image"></a>Hur du identifierar står i avbildningen

Den här guiden visar hur du identifierar ytor från en avbildning med framsidan attribut som kön, ålder eller attityd extraheras. Exemplen är skrivna i C# med hjälp av klientbiblioteket Ansikts-API. 

## <a name="concepts"></a> Begrepp

Om du inte är bekant med någon av följande begrepp i den här guiden hittar du definitioner i vår [ordlista](../Glossary.md) när som helst: 

- Ansiktsspårning
- Framsidan landmärken
- HEAD utgöra
- Ansikts-attribut

## <a name="preparation"></a> Förberedelse

I det här exemplet visar vi följande funktioner: 

- Identifiera ytor från en avbildning och markera dem med rektangulär synkroniseringstecken
- Analysera platserna för elever, näsa eller munnen och markera dem i avbildningen
- Analysera head attityd, kön och ålder på ytan

För att kunna utföra dessa funktioner måste du förbereda en bild med minst en tydlig yta. 

## <a name="step1"></a> Steg 1: Ge API-anrop

Varje anrop till Ansikts-API kräver en nyckel för prenumerationen. Den här nyckeln måste vara antingen skickas via en frågesträngsparameter eller angetts i huvudet i begäran. Om du vill överföra prenumerationen nyckeln via frågesträng, referera till URL: en för den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som exempel:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Alternativt kan nyckeln prenumeration kan också ange i HTTP-frågehuvudet: **ocp-apim-prenumeration-nyckel: &lt;prenumeration nyckeln&gt;**  när du använder ett klientbibliotek nyckeln prenumeration skickas via konstruktorn för klassen FaceServiceClient. Exempel:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> Steg 2: Ladda upp en bild till tjänsten och kör ansikte identifiering

Det enklaste sättet att utföra ansikte identifiering är genom att ladda upp en bild direkt. Detta görs genom att skicka en begäran om ”POST” med typen för program/oktett-ström innehåll med data läses från en JPEG-bild. Den maximala storleken för bilden är 4 MB.

Använder klientbiblioteket görs ansikte identifiering med hjälp av överföring genom att passera i en Stream-objektet. Se exemplet nedan:
```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Observera att asynkrona metoden DetectAsync i FaceServiceClient. Anropa metoden måste markeras som asynkron, samt för att kunna använda await-satsen.
Om bilden finns redan på webben och har en URL, kan står inför identifiering utföras av tillhandahåller även URL: en. I det här exemplet är begärandetexten en JSON-sträng som innehåller URL: en.
Använder klientbiblioteket ansikte identifiering med hjälp av en URL som kan köra enkelt med hjälp av en annan överlagring för metoden DetectAsync.
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Egenskapen FaceRectangle som returneras med identifierade ytor är i stort sett platser på framsidan i bildpunkter. Den här rektangeln innehåller vanligtvis ögonen, eyebrows, nosen och munnen – överkant head, öronen och hakan ingår inte i listan. Om du beskär en fullständig head eller halva som visar stående (en foto-ID typ avbildning), kan du vill expandera området rektangulär yta ramens eftersom område av de står inför är för litet för vissa program. Att hitta ett ansikte mer exakt med framsidan landmärken (hitta ansikte funktioner eller står inför riktning mekanismer) beskrivs i nästa avsnitt kommer visar sig vara användbara.

## <a name="step3"></a> Steg 3: Förstå och använda ansikte landmärken

Framsidan landmärken är en serie av detaljerad pekar på ett ansikte; punkter ansikte komponenter som vanligtvis elever, canthus eller näsa. Framsidan landmärken är valfria attribut som kan analyseras vid identifiering av yta. Du kan antingen skicka 'true' som ett booleskt värde för parametern returnFaceLandmarks frågan när du anropar den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), eller Använd parametern returnFaceLandmarks för klassen FaceServiceClient DetectAsync metod i ordning att inkludera ansikte landmärken i resultaten för identifiering.

Som standard finns 27 fördefinierade Landmärke punkter. Följande bild visar hur alla 27 poäng definieras:

![HowToDetectFace](../Images/landmarks.1.jpg)

Det är punkter returneras i antal bildpunkter, precis som står inför rektangulär ram. Vilket gör det lättare att markera specifika punkter av intresse för bilden. Följande kod visar hämtar näsa och elever:
```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Förutom märkning står inför funktioner i en bild, användas står inför landmärken också korrekt beräkna riktningen för de står inför. Vi kan till exempel definiera de står inför riktning som en vector från mitten av munnen till mitten av ögonen. Koden nedan förklaras i detalj:

```CSharp
var landmarks = face.FaceLandmarks;
 
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

Du kan sedan rotera rektangulär yta ram om du vill justera med de står inför genom att veta vilken riktning som de står inför. Det är tydligt att framsidan landmärken ger mer information och verktyg.

## <a name="step4"></a> Steg 4: Använda andra ansikts-attribut

Förutom ansikte Orienteringspunkter ansikte – kan identifiera API även analysera flera attribut på en sida. Dessa attribut inkluderar:

- Ålder
- Kön
- Glada intensiteten
- Ansikte hår
- En 3D-head attityd

Dessa attribut beräknas med hjälp av statistiska algoritmer och kanske inte alltid 100% exakt. De är fortfarande användbara när du vill klassificera ytor av dessa attribut. Mer information om alla attribut, referera till den [ordlista](../Glossary.md).

Nedan visas ett enkelt exempel på extraherar ansikte attribut vid identifiering av ansikte:
```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 
## <a name="summary"></a> Sammanfattning

I den här guiden har du lärt dig funktionerna i [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API, hur det kan upptäcka ytor för lokal upp bilder eller bild-URL: er på webben; hur ytor kan identifieras genom att returnera rektangulär yta ramar, och hur det kan också analysera står inför landmärken, 3D head utgör och andra ansikts-attribut.

Mer information om API-information finns i guiden för API-referens för [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Närliggande information

[Så här identifierar du står i avbildningen](HowtoIdentifyFacesinImage.md)
