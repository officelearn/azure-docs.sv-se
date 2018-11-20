---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure .NET SDK för ansiktsigenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure SDK för ansiktsigenkänning med C# för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: pafarley
ms.openlocfilehash: 4fbbde167a8c895a71ab3614e8c3ecbce26604a9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578169"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av .NET SDK för ansiktsigenkänning

I den här snabbstarten ska du använda SDK för ansiktsigenkänningstjänsten med C# för att identifiera ansikten i en bild. Ett exempel på koden i den här snabbstarten finns i ansiktsigenkänningsprojektet i databasen [Cognitive Services Vision csharp quickstarts](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) på GitHub.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. Skapa ett nytt projekt för **Konsolprogram (.NET Framework)** i Visual Studio och ge det namnet **FaceDetection**. 
1. Om det finns andra projekt i din lösning väljer du den här kopian som det enda startprojektet.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Klicka på fliken **Bläddra** och välj **Inkludera förhandsversion**. Leta sedan reda på och installera följande paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-face-detection-code"></a>Lägga till kod för ansiktsigenkänning

Öppna det nya projektets *Program.cs*-fil. Här lägger du till den kod som behövs för att läsa in bilder och identifiera ansikten.

### <a name="include-namespaces"></a>Inkludera namnområden

Lägg till följande `using`-instruktioner överst i *Program.cs*-filen.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>Lägga till grundläggande fält

Lägg till följande fält i klassen **Program**. Dessa data anger hur du ansluter till ansiktsigenkänningstjänsten och var du hämtar indata. Du måste uppdatera `subscriptionKey`-fältet med värdet för din prenumerationsnyckel, och du kanske måste ändra `faceEndpoint`-strängen så att den innehåller rätt regionsidentifierare. Du behöver även ange `localImagePath`- och/eller `remoteImageUrl`-värden till sökvägar som pekar på faktiska bildfiler.

Fältet `faceAttributes` är helt enkelt en matris med vissa typer av attribut. Det anger vilken information som ska hämtas om de identifierade ansiktena.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>Skapa och använda ansikts-API-klienten

Lägg nu till följande kod i metoden **Main** i klassen **Program**. Då konfigureras ansikts-API-klienten.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

I metoden **Main** lägger du även till följande kod för att använda den nya ansikts-API-klienten för att identifiera ansikten i en fjärransluten eller lokal bild. Identifieringsmetoden definieras härnäst. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>Identifiera ansikten

Lägg till följande metod i klassen **Program**. Den använder klienten för ansiktsigenkänningstjänsten för att identifiera ansikten i en fjärransluten bild, som refereras till av en URL. Observera att den använder fältet `faceAttributes` &mdash; **DetectedFace**-objekt som lagts till i `faceList` har de angivna attributen (i det här fallet ålder och kön).

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

Lägg också till metoden **DetectLocalAsync**. Den använder klienten för ansiktsigenkänningstjänsten för att identifiera ansikten i en lokal bild, som refereras till av en sökväg.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>Hämta och visa ansiktsattribut

Definiera sedan metoden **GetFaceAttributes**. Den returnerar en sträng med relevant attributinformation.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

Definiera slutligen metoden **DisplayAttributes** för att skriva ansiktsattributdata till konsolutdata.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>Kör appen

Ett lyckat svar visar kön och ålder för varje ansikte i bilden. Exempel:

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett enkelt .NET-konsolprogram som kan använda tjänsten Ansikts-API till att identifiera ansikten i både lokala och fjärranslutna bilder. Följ sedan en mer djupgående självstudie för att se hur du kan visa ansiktsinformation för användaren på ett intuitivt sätt.

> [!div class="nextstepaction"]
> [Självstudie: Skapa en WPF-app för att upptäcka och analysera ansikten i en bild](../Tutorials/FaceAPIinCSharpTutorial.md)
