---
title: 'Självstudie: Identifiera och visa ansiktsinformation i en bild med .NET SDK'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en Windows-app som använder ansiktstjänsten för att identifiera och rama in ansikten i en avbildning.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a5cf3c59c94134e1d0751c1467cd324a95c366eb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78898808"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Självstudiekurs: Skapa en WPF-app (Windows Presentation Framework) för att visa ansiktsdata i en bild

I den här självstudien får du lära dig hur du använder Azure Face-tjänsten, via .NET-klienten SDK, för att identifiera ansikten i en avbildning och sedan presentera dessa data i användargränssnittet. Du ska skapa ett WPF-program som identifierar ansikten, ritar en ram runt varje yta och visar en beskrivning av ansiktet i statusfältet. 

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Skapa ett WPF-program
> - Installera face-klientbiblioteket
> - Använda klientbiblioteket för att identifiera ansikten i en bild
> - Rita en ram runt varje identifierat ansikte
> - Visa en beskrivning av det markerade ansikten i statusfältet

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)

Den fullständiga exempelkoden är tillgänglig på [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)-lagringsplatsen på GitHub.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. 


## <a name="prerequisites"></a>Krav

- En Face-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Du kan också följa instruktionerna i [Skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Ansiktstjänsten och hämta din nyckel. Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och `FACE_SUBSCRIPTION_KEY` tjänstslutpunktssträngen, med namnet respektive `FACE_ENDPOINT`.
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

Följ dessa steg för att skapa ett nytt WPF-programprojekt.

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installed** (Installerat), sedan **Visual C#** och välj **WPF App (.NET Framework)**.
1. Ge programmet namnet **FaceTutorial** och klicka på **OK**.
1. Hämta de NuGet-paket som behövs. Högerklicka på projektet i Solution Explorer och välj **Hantera NuGet-paket**. Sök efter och installera följande paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.5.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1)

## <a name="add-the-initial-code"></a>Lägg till den första koden

I det här avsnittet lägger du till appens grundläggande ramverk utan dess ansiktsspecifika funktioner.

### <a name="create-the-ui"></a>Skapa användargränssnittet

Öppna *MainWindow.xaml* och ersätta innehållet&mdash;med följande kod som den här koden skapar gränssnittsfönstret. `FacePhoto_MouseMove` Metoderna `BrowseButton_Click` och är händelsehanterare som du kommer att definiera senare.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Skapa klassen main

Öppna *MainWindow.xaml.cs* och lägg till klientbibliotekets namnområden, tillsammans med andra nödvändiga namnrymder. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Infoga därefter följande kod i klassen **MainWindow**. Den här koden skapar en **FaceClient-instans** med hjälp av prenumerationsnyckeln och slutpunkten.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Lägg sedan till **MainWindow-konstruktorn.** Den kontrollerar url-strängen för slutpunkten och associeras sedan med klientobjektet.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Lägg slutligen till metoderna **BrowseButton_Click** och **FacePhoto_MouseMove** i klassen. Dessa metoder motsvarar de händelsehanterare som deklarerats i *MainWindow.xaml*. Metoden **BrowseButton_Click** skapar en **OpenFileDialog**, där användaren kan välja en .jpg-bild. Sedan visas bilden i huvudfönstret. Du infogar de återstående koden för **BrowseButton_Click** och **FacePhoto_MouseMove** i senare steg. Observera även `faceList`-referensen&mdash;en lista över **DetectedFace**-objekt. Den här referensen är där din app kommer att lagra och anropa de faktiska ansiktsdata.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Prova appen

Tryck på **Start** (Starta) på menyn för att testa din app. När appfönstret öppnas klickar du på **Browse** (Bläddra) i det nedre vänstra hörnet. Dialogrutan **File Open** (Öppna fil) bör visas. Välj en bild i filsystemet och verifiera att den visas i fönstret. Stäng sedan appen och gå till nästa steg.

![Skärmbild som visar oförändrad bild på ansikten](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Ladda upp bilden och identifiera ansikten

Appen identifierar ansikten genom att anropa metoden **FaceClient.Face.DetectWithStreamAsync**, som omsluter REST API för [identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) för uppladdning av en lokal bild.

Infoga följande metod i klassen **MainWindow**, under metoden **FacePhoto_MouseMove**. Den här metoden definierar en lista över ansiktsattribut för att hämta och läser den inskickade bildfilen i en **stream**. Sedan skickar den båda objekten till metodanropet **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Rita rektanglar runt ansikten

Därefter lägger du till koden för att rita en rektangel runt varje identifierat ansikte i bilden. I klassen **MainWindow** infogar du följande kod i slutet av metoden **BrowseButton_Click**, efter `FacePhoto.Source = bitmapSource`-raden. Den här koden fyller i en lista över identifierade ansikten från anropet till **UploadAndDetectFaces**. Sedan ritar den en rektangel runt varje ansikte och visar den ändrade bilden i huvudfönstret.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Beskriv ansiktena

Lägg till följande metod i klassen **MainWindow**, under metoden **UploadAndDetectFaces**. Med den här metoden konverteras de hämtade ansiktsattributen till en sträng som beskriver ansiktet.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Visa ansiktsbeskrivning

Lägg till följande kod i metoden **FacePhoto_MouseMove**. Den här händelsehanteraren visar ansiktsbeskrivningssträngen i `faceDescriptionStatusBar` när markören hovrar över en identifierad ansiktsrektangel.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Kör appen

Kör programmet och bläddra efter en bild som innehåller ett ansikte. Vänta några sekunder på att Ansiktstjänsten ska svara. Du bör se en röd rektangel kring varje ansikte i bilden. Om du för muspekaren över en ansiktsrektangel bör beskrivningen av ansiktet visas i statusfältet.

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig den grundläggande processen för att använda Ansiktstjänstens .NET SDK och skapat ett program för att visa och rama in ansikten i en bild. Härnäst får du mer information om ansiktsigenkänning.

> [!div class="nextstepaction"]
> [Så identifierar du ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
