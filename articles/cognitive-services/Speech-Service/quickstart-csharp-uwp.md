---
title: 'Snabbstart: Känna igen tal i C# i en UWP-app med hjälp av Cognitive Services Speech SDK'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i en UWP-app med hjälp av Cognitive Services Speech SDK
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 2eb6bda7066a01e5532fe7c0e20b0ee13f4289b6
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432995"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i en UWP-app med hjälp av Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln ska du skapa ett C#-baserat UWP-program (Universal Windows Platform) med hjälp av Cognitive Services [Speech SDK](speech-sdk.md). Du transkriberar tal till text i realtid från enhetens mikrofon. Programmet skapas med [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (valfri version).

> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en prenumerationsnyckel för Speech-tjänsten för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Mer information finns i [Prova Speech-tjänsten kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Starta Visual Studio 2017.

1. Kontrollera att arbetsbelastningen **Universal Windows Platform-utveckling** är tillgänglig. Välj **Verktyg** > **Get Tools and Features** (Hämta verktyg och funktioner) på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan. 

    ![Skärmbild av Visual Studio-installationsprogrammet med fliken Arbetsbelastningar markerad](media/sdk/vs-enable-uwp-workload.png)

    Annars markerar du rutan bredvid **.NET cross-platform development** (Plattformsoberoende .NET-utveckling) och väljer **Ändra** i det nedre högra hörnet av dialogrutan. Installationen av den nya funktionen tar en stund.

1. Skapa en tom Visual C#-baserad Universal Windows-app. Börja med att välja **Arkiv** > **Nytt** > **Projekt** på menyn. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C#** > **Windows Universal** i den vänstra rutan. Välj sedan **Tom app (Universal Windows)**. För projektnamnet anger du *helloworld*.

    ![Skärmbild av dialogrutan Nytt projekt](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speed SDK kräver att ditt program skapas för Windows 10 Fall Creators Update eller senare. I fönstret **New Universal Windows Platform Project** (Nytt Universal Windows Platform-projekt) som visas väljer du **Windows 10 Fall Creators Update (10.0; Build 16299)** som **minimiversion**. I rutan **Målversion** väljer du den här eller en senare version och klickar sedan på **OK**.

    ![Skärmbild av fönstret New Universal Windows Platform Project (Nytt Universal Windows Platform-projekt)](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Om du använder 64-bitars Windows kan du byta utvecklingsplattform till `x64` med hjälp av den nedrullningsbara menyn i Visual Studio-verktygsfältet. (64-bitars Windows kan köra 32-bitarsprogram, så du kan lämna inställningen som `x86` om du föredrar det.)

   ![Skärmbild av Visual Studio-verktygsfältet med x64 markerat](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Speech SDK stöder endast Intel-kompatibla processorer. ARM stöds inte för närvarande.

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket för lösningen**.

    ![Skärmbild av Solution Explorer, med alternativet Hantera NuGet-paket för lösningen markerat](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. I det övre högra hörnet väljer du **nuget.org** i fältet **Package Source** (Paketkälla). Sök efter `Microsoft.CognitiveServices.Speech`-paketet och installera det i **helloworld**-projektet.

    ![Skärmbild av dialogrutan Hantera paket för lösningen](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

    ![Skärmbild av dialogrutan för att acceptera licensvillkoren](media/sdk/qs-csharp-uwp-06-nuget-license.png "Acceptera licensen")

1. Följande utdatarad visas i Package Manager-konsolen.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.0' to helloworld
   ```

1. Eftersom programmet använder mikrofonen för talindata lägger du till funktionen **Mikrofon** i projektet. I Solution Explorer dubbelklickar du på **Package.appxmanifest** för att redigera programmanifestet. Sedan växlar du till fliken **Funktioner**, markerar kryssrutan för funktionen **Mikrofon** och sparar dina ändringar.

   ![Skärmbild av programmanifestet i Visual Studio med Funktioner och Mikrofon markerat](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Lägg till exempelkod

1. Programmets användargränssnitt definieras med hjälp av XAML. Öppna `MainPage.xaml` i Solution Explorer. I designerns XAML-vy infogar du följande XAML-kodavsnitt i Grid-taggen (mellan `<Grid>` och `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Öppna källfilen för den bakomliggande koden, `MainPage.xaml.cs` (du hittar den grupperad under `MainPage.xaml`). Ersätt all kod i den med nedanstående.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. I `SpeechRecognitionFromMicrophone_ButtonClicked`-hanteraren i den här filen ersätter du strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. I `SpeechRecognitionFromMicrophone_ButtonClicked`-hanteraren ersätter du även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

1. Spara alla ändringar i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-uwp-08-build.png "Slutförd byggprocess")

1. Starta programmet. På menyraden väljer du **Felsök** > **Starta felsökning**, eller så trycker du på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-uwp-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett fönster öppnas. Välj **Aktivera mikrofon** och bekräfta behörighetsbegäran som visas.

    ![Skärmbild av behörighetsbegäran](media/sdk/qs-csharp-uwp-10-access-prompt.png "Starta appen i felsökningsläge")

1. Välj **Speech recognition with microphone input** (Taligenkänning med mikrofonindata) och tala in en fras eller mening på engelska i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

    ![Skärmbild av användargränssnittet för taligenkänning](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter i tal med hjälp av Speech SDK för C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
