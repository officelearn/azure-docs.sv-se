---
title: Självstudie om Ansikts-API C# | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: I den här självstudien skapar du en Windows-app som använder Ansiktstjänsten för Cognitive Services för att identifiera och rama in ansikten i en bild.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: e4f2192c40f0b650b31ed59642dee89e42eca703
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125957"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Självstudie: Skapa en WPF-app för att upptäcka och rama in ansikten i en bild

I den här självstudien skapar du ett WPF-program (Windows Presentation Framework) som använder Ansiktstjänsten via dess .NET-klientbibliotek. Appen identifierar ansikten i en bild, ritar en ram runt varje ansikte och visar en beskrivning av ansiktet i statusfältet. Den fullständiga exempelkoden finns på GitHub på [Identifiera och rama in ansikten i en bild på Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample).

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Skapa ett WPF-program
> - Installera Ansiktstjänstens klientbibliotek
> - Använda klientbiblioteket för att identifiera ansikten i en bild
> - Rita en ram runt varje identifierat ansikte
> - Visa en beskrivning av ansiktet i statusfältet

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du behöver en prenumerationsnyckel för att köra exemplet. Du kan hämta nycklar för kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/). För Visual Studio 2017 krävs arbetsbelastningen för .NET Desktop-programutveckling. I den här självstudien används Visual Studio 2017 Community Edition.
- NuGet-paketet för klientbiblioteket [Microsoft.Azure.CognitiveServices.Vision.Face 2.0.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.0.0-preview). Du behöver inte ladda ned paketet. Installationsinstruktioner finns nedan.

## <a name="create-the-visual-studio-solution"></a>Skapa Visual Studio-lösningen

Följ dessa steg för att skapa ett Windows WPF-programprojekt.

1. Öppna Visual Studio och öppna menyn **Arkiv**. Klicka på **Nytt** och sedan på **Projekt**.
   - I Visual Studio 2017 expanderar du **Installerat** och sedan **Andra språk**. Välj **Visual C#** och sedan **WPF App (.NET Framework)**.
   - In Visual Studio 2015 expanderar du **Installerat** och sedan **Mallar**. Välj **Visual C#** och sedan **WPF Application** (WPF-program).
1. Ge programmet namnet **FaceTutorial** och klicka på **OK**.

## <a name="install-the-face-service-client-library"></a>Installera Ansiktstjänstens klientbibliotek

Följ dessa instruktioner för att installera klientbiblioteket.

1. Öppna menyn **Verktyg**. Välj **NuGet-pakethanterare** och sedan **Package Manager-konsolen**.
1. I **Package Manager-konsolen** klistrar du in följande och trycker sedan på **Retur**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.0.0-preview`

## <a name="add-the-initial-code"></a>Lägg till den första koden

### <a name="mainwindowxaml"></a>MainWindow.xaml

Öppna *MainWindow.xaml* (tips: växla fönster med hjälp av **uppåt-/nedåtpilikonen**) och ersätt innehållet med följande kod. Den här xaml-koden används för att skapa gränssnittsfönstret. Observera händelsehanterarna `FacePhoto_MouseMove` och `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Expandera *MainWindow.xaml*, öppna sedan *MainWindow.xaml.cs* och ersätt innehållet med följande kod. Ignorera de röda vågiga understrykningarna; de försvinner efter den första versionen.

De två första raderna importerar klientbibliotekets namnrymder. Därefter skapas `FaceClient` med prenumerationsnyckeln som argument, medan Azure-regionen anges i `MainWindow`-konstruktorn. De två metoderna, `BrowseButton_Click` och `FacePhoto_MouseMove`, motsvarar de händelsehanterare som deklarerats i *MainWindow.xaml*.

`BrowseButton_Click` skapar en `OpenFileDialog`, vilket gör att användaren kan välja en jpg-bild. Bilden läses in och visas i huvudfönstret. Den återstående koden för `BrowseButton_Click` och koden för `FacePhoto_MouseMove` infogas i efterföljande steg.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string baseUri =
            "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(baseUri, UriKind.Absolute))
            {
                faceClient.BaseUri = new Uri(baseUri);
            }
            else
            {
                MessageBox.Show(baseUri,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Infoga din prenumerationsnyckel och bekräfta eller ändra regionen

- Lägg till följande rad i *MainWindow.xaml.cs* och ersätt `<Subscription Key>` med din prenumerationsnyckel för Ansikts-API:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Lägg till följande rad i *MainWindow.xaml.cs* och ersätt eller verifiera den Azure-region som är associerad med din prenumerationsnyckel:

    ```csharp
    private const string baseUri =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
    ```

    Se till att platsen är samma som där du skaffade din prenumerationsnycklar. Om du till exempel skaffade prenumerationsnycklarna från regionen **westus** (USA, västra) ersätter du `Westcentralus` med `Westus`.

    Om du fick prenumerationsnycklarna med den kostnadsfria utvärderingsversionen är regionen för dina nycklar **westcentralus** (USA, västra centrala), så det krävs ingen ändring.

### <a name="test-the-app"></a>Testa appen

Tryck på **Start** (Starta) på menyn för att testa din app. När fönstret öppnas klickar du på **Browse** (Bläddra) i det nedre vänstra hörnet. Dialogrutan **File Open** (Öppna fil) visas där du kan bläddra och välja ett foto, som sedan visas i fönstret.

![Skärmbild som visar oförändrad bild på ansikten](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Ladda upp en bild för att identifiera ansikten

Det enklaste sättet att spåra ansikten är genom att anropa metoden `FaceClient.Face.DetectWithStreamAsync`, som omsluter [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-API-metoden för att ladda upp den lokala bilden.

Infoga följande metod i `MainWindow`-klassen nedanför `FacePhoto_MouseMove`-metoden.

En lista över ansiktsattribut som ska analyseras skapas, och den skickade bildfilen läses in en `Stream`. Båda skickas som argument till `DetectWithStreamAsync`-anropet.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Rita rektanglar runt varje ansikte

Lägg till koden för att rita en rektangel runt varje identifierat ansikte i bilden.

I *MainWindow.xaml.cs* lägger du till `async`-modifieraren till metoden `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Infoga följande kod i slutet av `BrowseButton_Click`-metoden, efter raden `FacePhoto.Source = bitmapSource`.

Listan över identifierade ansikten fylls via anropet till `UploadAndDetectFaces`. Därefter ritas en rektangel runt varje ansikte, och den ändrade bilden visas i huvudfönstret.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Beskriv ansiktena i bilden

Lägg till följande metod i `MainWindow`-klassen nedanför `UploadAndDetectFaces`-metoden.

Metoden konverterar ansiktsattribut till en sträng som beskriver ansiktet. Strängen visas när du håller muspekaren över ansiktsrektangeln.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Visa ansiktsbeskrivning

Ersätt metoden `FacePhoto_MouseMove` med följande kod.

Den här händelsehanteraren visar strängen för ansiktsbeskrivning när du håller muspekaren över ansiktsrektangeln.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Kör appen

Kör programmet och bläddra efter en bild som innehåller ett ansikte. Vänta några sekunder på att Ansiktstjänsten ska svara. Efter det visas en röd rektangel på ansiktena i bilden. När du flyttar musen över en ansiktsrektangel visas beskrivningen av det ansiktet i statusfältet.

![Skärmbild som visar identifierade ansikten inramade med rektanglar](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Sammanfattning

I den här självstudien har du lärt dig den grundläggande processen för att använda Ansiktstjänstens klientbibliotek och skapat ett program för att visa och rama in ansikten i en bild.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du identifierar och använder ansiktsattribut.

> [!div class="nextstepaction"]
> [Så identifierar du ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
