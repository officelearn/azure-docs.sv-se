---
title: 'Självstudie: Identifiera och visa ansiktsinformation i en bild med .NET SDK'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en Windows-app som använder Ansiktsigenkänning till att identifiera och rama in ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: pafarley
ms.openlocfilehash: 5319584d892c261dcc6290703e9ca6518640cc94
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685488"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>Självstudie: Skapa en WPF-app för att visa ansiktsinformation i en bild

I den här självstudien lär du dig hur du använder API:et för ansiktsigenkänning i Azure, via .NET-klient-SDK, för att identifiera ansikten i en bild och sedan visa dessa data i användargränssnittet. Du skapar en enkel WPF-app (Windows Presentation Framework) som identifierar ansikten, ritar en ram runt varje ansikte och visar en beskrivning av ansiktet i statusfältet. 

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


## <a name="prerequisites"></a>Nödvändiga komponenter

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Följ dessa steg för att skapa ett nytt WPF-programprojekt.

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installed** (Installerat), sedan **Visual C#** och välj **WPF App (.NET Framework)**.
1. Ge programmet namnet **FaceTutorial** och klicka på **OK**.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview

## <a name="add-the-initial-code"></a>Lägg till den första koden

I det här avsnittet lägger du till appens grundläggande ramverk utan dess ansiktsspecifika funktioner.

### <a name="create-the-ui"></a>Skapa användargränssnittet

Öppna *MainWindow.xaml* och ersätt innehållet med följande kod&mdash;detta skapar användargränssnittsfönstret. Observera att `FacePhoto_MouseMove` och `BrowseButton_Click` är händelsehanterare som du definierar senare.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>Skapa klassen main

Öppna *MainWindow.xaml.cs* och lägg till klientbibliotekets namnområden, tillsammans med andra nödvändiga namnrymder. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

Infoga därefter följande kod i klassen **MainWindow**. Det skapar en **FaceClient**-instans med hjälp av prenumerationsnyckeln, som du måste ange själv. Du måste även ange regionssträngen i `faceEndpoint` till rätt region för prenumerationen.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

Klistra sedan in följande kod i metoden **MainWindow**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

Lägg slutligen till metoderna **BrowseButton_Click** och **FacePhoto_MouseMove** i klassen. Dessa motsvarar de händelsehanterare som deklarerats i *MainWindow.xaml*. Metoden **BrowseButton_Click** skapar en **OpenFileDialog**, där användaren kan välja en .jpg-bild. Sedan visas bilden i huvudfönstret. Du infogar de återstående koden för **BrowseButton_Click** och **FacePhoto_MouseMove** i senare steg. Observera även `faceList`-referensen&mdash;en lista över **DetectedFace**-objekt. Här lagrar och anropar appen faktiska ansiktsdata.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>Prova appen

Tryck på **Start** (Starta) på menyn för att testa din app. När appfönstret öppnas klickar du på **Browse** (Bläddra) i det nedre vänstra hörnet. Dialogrutan **File Open** (Öppna fil) bör visas. Välj en bild i filsystemet och verifiera att den visas i fönstret. Stäng sedan appen och gå till nästa steg.

![Skärmbild som visar oförändrad bild på ansikten](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Ladda upp bilden och identifiera ansikten

Appen identifierar ansikten genom att anropa metoden **FaceClient.Face.DetectWithStreamAsync**, som omsluter REST API för [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för uppladdning av en lokal bild.

Infoga följande metod i klassen **MainWindow**, under metoden **FacePhoto_MouseMove**. Det här definierar en lista över ansiktsattribut som ska hämtas och läser den skickade bildfilen i en **Stream** (Ström). Sedan skickar den båda objekten till metodanropet **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>Rita rektanglar runt ansikten

Därefter lägger du till koden för att rita en rektangel runt varje identifierat ansikte i bilden. I klassen **MainWindow** infogar du följande kod i slutet av metoden **BrowseButton_Click**, efter `FacePhoto.Source = bitmapSource`-raden. Det här fyller i en lista med identifierade ansikten från anropet i **UploadAndDetectFaces**. Sedan ritar den en rektangel runt varje ansikte och visar den ändrade bilden i huvudfönstret.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>Beskriv ansiktena

Lägg till följande metod i klassen **MainWindow**, under metoden **UploadAndDetectFaces**. Det här konverterar hämtade ansiktsattribut till en sträng som beskriver ansiktet.

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
