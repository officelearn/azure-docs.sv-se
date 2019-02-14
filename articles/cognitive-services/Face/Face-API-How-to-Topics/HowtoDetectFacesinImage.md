---
title: 'Exempel: Identifiera ansikten i bilder – Ansikts-API'
titleSuffix: Azure Cognitive Services
description: Använda Ansikts-API för att identifiera ansikten i bilder.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 30d5294defe02ca6c8cfd588648429859bdf19ad
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856402"
---
# <a name="example-how-to-detect-faces-in-image"></a>Exempel: Känna igen ansikten i en bild

Den här guiden visar hur du känner igen ansikten från en bild där ansiktsattribut som kön, ålder eller attityd extraheras. Exemplen är skrivna i C#- med Ansikts-API-klientbiblioteket. 

## <a name="concepts"></a>Begrepp

Om du inte är bekant med följande begrepp i den här guiden kan du söka efter definitioner i vår [ordlista](../Glossary.md) när som helst: 

- Ansiktsspårning
- Ansiktslandmärken
- Huvudattityd
- Ansiktsattribut

## <a name="preparation"></a>Förberedelse

I det här exemplet visar vi följande funktioner: 

- Identifiera ansikten från en bild och markera dem med hjälp av rektangulära synkroniseringstecken
- Analysera platserna för pupilller, näsa eller mun och markera dem i bilden
- Analysera huvudattityd, kön och ålder på ansiktet

För att köra dessa funktioner behöver du förbereda en bild med minst ett tydligt ansikt. 

## <a name="step-1-authorize-the-api-call"></a>Steg 1: Auktorisera API-anropet

Varje anrop till ett ansikts-API för visuellt innehåll kräver en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud. Om du vill skicka prenumerationsnyckeln via frågesträngen ska du använda URL:en för [Ansiktsigenkänning– identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) som exempel:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Prenumerationsnyckeln kan även anges i HTTP-frågehuvudet: **ocp-apim-subscription-key: &lt;Prenumerationsnyckel&gt;** När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för klassen FaceServiceClient. Exempel:
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>Steg 2: Ladda upp en bild till tjänsten och kör ansiktsigenkänning

Det enklaste sättet att utföra ansiktsigenkänning är genom att ladda upp en bild direkt. Detta sker genom att skicka en ”POST”-begäran med innehållstypen program-/oktettflöde tillsammans med de data som läses från JPEG-bilden. Bildens maxstorlek är 4MB.

Med hjälp av klientbiblioteket sker ansiktsigenkänning med överföring genom att skicka in ett Streamobjekt. Se exemplet nedan:

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

Observera att metoden DetectAsync i FaceServiceClient är asynkron. Anropsmetoden bör också markeras som asynkron för att använda väntesatsen.
Om bilden redan finns på webben och har en URL kan ansiktsigenkänning ske med URL:en. I det här exemplet är begärandetexten en JSON-sträng som innehåller URL:en.
Med hjälp av klientbiblioteket, kan ansiktsigenkänning med hjälp av en URL utföras enkelt med hjälp av en annan överlagring för metoden DetectAsync.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Egenskapen FaceRectangle som returneras med identifierade ansikten är i stort sett platser på ansiktet i bildpunkter. Den här rektangeln innehåller vanligtvis ögon, ögonbryn, näsa och mun – hjässan, öronen och hakan ingår inte. Om du beskär ett fullständigt huvud eller ett porträtt (av typen som används för legitimationer) kan du behöva expandera ytan för ansiktsramen eftersom ansiktet kan vara för litet för vissa program. Använd ansiktslandmärken (hitta ansiktsdrag eller ansiktsriktningar) som beskrivs i nästa avsnitt för att hitta ett ansikte med större precision.

## <a name="step-3-understanding-and-using-face-landmarks"></a>Steg 3: Förstå och använda ansiktslandmärken

Ansiktslandmärken är ett antal detaljerade punkter på ett ansikte, till exempel pupiller, canthus eller näsa. Ansiktslandmärken är valfria attribut som kan analyseras under ansiktsigenkänning. Du kan antingen skicka ”SANT” som ett booleskt värde för frågeparametern returnFaceLandmarks när du anropar [Ansiktsigenkänning – Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) eller använda den valfria parametern returnFaceLandmarks för klassmetoden FaceServiceClient DetectAsync för att inkludera ansiktslandmärken i igenkänningsresultaten.

Som standard finns 27 fördefinierade landmärkespunkter. Följande bild visar hur alla 27 punkter definieras:

![HowToDetectFace](../Images/landmarks.1.jpg)

Punkterna returneras i antal bildpunkter, precis som den rektangulära ansiktsramen. Därmed är det enklare att markera specifika platser av intresse i bilden. Följande kod visar hämtning av platserna för näsa och pupiller:

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

Förutom markeringen av ansiktsdrag i en bild kan ansiktslandmärken även användas för att noggrant beräkna ansiktets riktning. Vi kan till exempel definiera ansiktets riktning som en vektor från mitten av munnen till mitten av ögonen. Nedanstående kod förklarar detta i detalj:

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

Genom att känna till ansiktets riktning kan du rotera den rektangulära ansiktsramen för att rikta in den mot ansiktet. Det är tydligt att ansiktslandmärken ger mer detaljrikedom och användbarhet.

## <a name="step-4-using-other-face-attributes"></a>Steg 4: Använda andra ansiktsattribut

Förutom ansiktslandmärken kan API:et Ansiktsigenkänning – Identifiera också analysera flera andra attribut på ett ansikte. Dessa attribut är:

- Ålder
- Kön
- Leendets intensitet
- Ansiktsbehåring
- Huvudattityd i 3D

Dessa attribut beräknas med hjälp av statistiska algoritmer och är kanske inte alltid 100 % exakta. De är fortfarande användbara när du vill klassificera ansikten med dessa attribut. Mer information om varje attribut finns i [Ordlistan](../Glossary.md).

Nedan visas ett enkelt exempel på extrahering av ansiktsattribut under ansiktsigenkänning:

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

## <a name="summary"></a>Sammanfattning

I den här guiden har du lärt dig funktionerna i [Ansiktsigenkänning – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API, identifiering av ansikten för lokala överförda bilder eller bild-URL:er på webben, identifiering av ansikten genom att returnera en rektangulär ansiktsram samt analys av ansiktslandmärken, huvudattityd i 3D och andra ansiktsattribut.

Mer information om API-information finns i API-referensguide för [Ansiktsigenkänning – Identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Relaterade ämnen

[Identifiera ansikten i en bild](HowtoIdentifyFacesinImage.md)
