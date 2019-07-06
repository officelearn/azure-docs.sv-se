---
title: 'Självstudier: Identifiera och visa ansiktsinformation i en bild med .NET SDK'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en Windows-app som använder Ansiktsigenkänning till att identifiera och rama in ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 54069fbaa8ad06d257ab835ed3b170fecb76d800
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603346"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Självstudier: Skapa en WPF-app för att visa ansiktsinformation i en bild

I de här självstudierna lär du dig att använda Ansikts-API i Azure, via .NET-klient SDK, för att identifiera ansikten i en bild och sedan presentera dessa data i Användargränssnittet. Du ska skapa ett program för Windows Presentation Framework (WPF) som identifierar ansikten ritar en ram runt varje ansikte och visar en beskrivning av de står inför i statusfältet. 

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Skapa ett WPF-program
> - Installera ansikts-API-klientbiblioteket
> - Använda klientbiblioteket för att identifiera ansikten i en bild
> - Rita en ram runt varje identifierat ansikte
> - Visa en beskrivning av det markerade ansikten i statusfältet

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)

Den fullständiga exempelkoden är tillgänglig på [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)-lagringsplatsen på GitHub.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 


## <a name="prerequisites"></a>Förutsättningar

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Följ dessa steg för att skapa ett nytt WPF-programprojekt.

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installed** (Installerat), sedan **Visual C#** och välj **WPF App (.NET Framework)** .
1. Ge programmet namnet **FaceTutorial** och klicka på **OK**.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Lägg till den första koden

I det här avsnittet lägger du till appens grundläggande ramverk utan dess ansiktsspecifika funktioner.

### <a name="create-the-ui"></a>Skapa användargränssnittet

Öppna *MainWindow.xaml* och Ersätt innehållet med följande kod&mdash;den här koden skapar fönstret Användargränssnittet. Den `FacePhoto_MouseMove` och `BrowseButton_Click` metoder är händelsehanterare som definierar du vid ett senare tillfälle.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Skapa klassen main

Öppna *MainWindow.xaml.cs* och lägg till klientbibliotekets namnområden, tillsammans med andra nödvändiga namnrymder. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Infoga därefter följande kod i klassen **MainWindow**. Den här koden skapar en **FaceClient** instans med prenumerationsnyckel som du måste ange dig själv. Du måste ange den region-strängen `faceEndpoint` till rätt region för din prenumeration (finns i den [Ansikts-API-dokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en lista över alla slutpunkter i regionen).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Klistra sedan in följande kod i metoden **MainWindow**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Lägg slutligen till metoderna **BrowseButton_Click** och **FacePhoto_MouseMove** i klassen. Dessa metoder som motsvarar de händelsehanterare som deklarerats i *MainWindow.xaml*. Metoden **BrowseButton_Click** skapar en **OpenFileDialog**, där användaren kan välja en .jpg-bild. Sedan visas bilden i huvudfönstret. Du infogar de återstående koden för **BrowseButton_Click** och **FacePhoto_MouseMove** i senare steg. Observera även `faceList`-referensen&mdash;en lista över **DetectedFace**-objekt. Den här referensen är där appen sparar och anropa faktiska ansikts-data.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Prova appen

Tryck på **Start** (Starta) på menyn för att testa din app. När appfönstret öppnas klickar du på **Browse** (Bläddra) i det nedre vänstra hörnet. Dialogrutan **File Open** (Öppna fil) bör visas. Välj en bild i filsystemet och verifiera att den visas i fönstret. Stäng sedan appen och gå till nästa steg.

![Skärmbild som visar oförändrad bild på ansikten](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Ladda upp bilden och identifiera ansikten

Appen identifierar ansikten genom att anropa metoden **FaceClient.Face.DetectWithStreamAsync**, som omsluter REST API för [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för uppladdning av en lokal bild.

Infoga följande metod i klassen **MainWindow**, under metoden **FacePhoto_MouseMove**. Den här metoden definierar en lista över ansiktsattribut att hämta och läser skickade image-filen till en **Stream**. Sedan skickar den båda objekten till metodanropet **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Rita rektanglar runt ansikten

Därefter lägger du till koden för att rita en rektangel runt varje identifierat ansikte i bilden. I klassen **MainWindow** infogar du följande kod i slutet av metoden **BrowseButton_Click**, efter `FacePhoto.Source = bitmapSource`-raden. Den här koden lägger en lista över identifierade ansikten vid anrop till **UploadAndDetectFaces**. Sedan ritar den en rektangel runt varje ansikte och visar den ändrade bilden i huvudfönstret.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Beskriv ansiktena

Lägg till följande metod i klassen **MainWindow**, under metoden **UploadAndDetectFaces**. Den här metoden konverterar de hämtade ansiktsattribut till en sträng som beskriver de står inför.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>Visa ansiktsbeskrivning

Lägg till följande kod i metoden **FacePhoto_MouseMove**. Den här händelsehanteraren visar ansiktsbeskrivningssträngen i `faceDescriptionStatusBar` när markören hovrar över en identifierad ansiktsrektangel.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>Kör appen

Kör programmet och bläddra efter en bild som innehåller ett ansikte. Vänta några sekunder på att Ansiktstjänsten ska svara. Du bör se en röd rektangel kring varje ansikte i bilden. Om du för muspekaren över en ansiktsrektangel bör beskrivningen av ansiktet visas i statusfältet.

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig den grundläggande processen för att använda Ansiktstjänstens .NET SDK och skapat ett program för att visa och rama in ansikten i en bild. Härnäst får du mer information om ansiktsigenkänning.

> [!div class="nextstepaction"]
> [Så identifierar du ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
