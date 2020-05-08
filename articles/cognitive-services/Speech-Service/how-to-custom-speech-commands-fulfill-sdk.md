---
title: Så här uppfyller du kommandon från en klient med talet SDK
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du hanterar anpassade kommando aktiviteter på en klient med tal-SDK.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871787"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Utföra kommandon från en klient med talet SDK (för hands version)

För att slutföra uppgifter med hjälp av ett anpassat kommando program kan du skicka anpassade nytto laster till en ansluten klient enhet.

I den här artikeln får du:

- Definiera och skicka en anpassad JSON-nyttolast från programmet för anpassade kommandon
- Ta emot och visualisera det anpassade JSON-nyttolasten från ett C# UWP-program för tal-SDK

## <a name="prerequisites"></a>Krav
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * En Azure-prenumerations nyckel för tal service: [Hämta en kostnads fri](get-started.md) eller skapa den på [Azure Portal](https://portal.azure.com)
> * En tidigare skapad app för anpassade kommandon: [snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)
> * Ett aktiverat klient program för tal-SDK: [snabb start: Anslut till ett anpassat kommando program med talet SDK (för hands version)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Valfritt: kom igång snabbt

I den här artikeln beskrivs steg för steg hur du gör ett klient program att prata med dina anpassade kommandon. Om du föredrar att använda den fullständiga käll koden som är färdig att kompilera som används i den här artikeln finns i exempel på tal- [SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Uppfylla med JSON-nyttolast

1. Öppna programmet anpassade kommandon som du skapade tidigare från [snabb starter: skapa ett anpassat kommando med parametrar](./quickstart-custom-speech-commands-create-parameters.md)
1. Kontrol lera att du har den tidigare skapade regeln som svarar på användaren genom att titta i avsnittet **regler för slut för ande** .
1. Om du vill skicka en nytto Last direkt till klienten skapar du en ny regel med åtgärden skicka aktivitet

   > [!div class="mx-imgBorder"]
   > ![Skicka regel för slut för ande av aktivitet](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Inställningen | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Regelnamn | UpdateDeviceState | Ett namn som beskriver syftet med regeln |
   | Villkor | Obligatorisk parameter- `OnOff` och`SubjectDevice` | Villkor som avgör när regeln kan köras |
   | Åtgärder | `SendActivity`(se nedan) | Den åtgärd som ska vidtas när regel villkoret är sant |

1. Kopiera JSON nedan till **aktivitets innehåll**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Skicka aktivitetens nytto Last](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Skapa visuella objekt för enhet på eller av-tillstånd

I [snabb start: Anslut till ett anpassat kommando program med talet SDK](./quickstart-custom-speech-commands-speech-sdk.md)har du skapat ett program för tal-SDK-klient som hanterade `turn on the tv`kommandon `turn off the fan`, till exempel,. Med vissa visuella objekt tillagda kan du se resultatet av dessa kommandon.

Lägg till etiketterade rutor med text som visar **på** eller **av** med hjälp av följande XML tillagt i`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Hantera anpassningsbar nytto Last
### <a name="add-reference-libraries"></a>Lägg till referens bibliotek

Eftersom du har skapat en JSON-nyttolast måste du lägga till en referens i [JSON.net](https://www.newtonsoft.com/json) -biblioteket för att hantera deserialisering.
- Rätt-klient din lösning.
- Välj **Hantera NuGet-paket för lösningen**, Välj **Installera** 
- Sök efter **Newtonsoft. JSON** i listan uppdatera, uppdatera **Microsoft. NetCore. UniversalWindowsPlatform** till senaste versionen

> [!div class="mx-imgBorder"]
> ![Skicka aktivitetens nytto Last](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

I MainPage. XAML. CS, lägger du till
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Referens för mottagna nytto Last

I `InitializeDialogServiceConnector`ersätter du `ActivityReceived` händelse hanteraren med följande kod. Den modifierade `ActivityReceived` händelse hanteraren extraherar nytto lasten från aktiviteten och ändrar det visuella läget för TV: n eller fläkten på motsvarande sätt.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Prova nu

1. Starta programmet
1. Välj Aktivera mikrofon
1. Välj knappen prata
1. Berätta`turn on the tv`
1. TV-apparatens visuella tillstånd ska ändras till "på"
   > [!div class="mx-imgBorder"]
   > ![Skicka aktivitetens nytto Last](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till valideringar i anpassade kommando parametrar (för hands version)](./how-to-custom-speech-commands-validations.md)
