---
title: Så här uppfyller du anpassade kommandon på klienten med talet SDK
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du hanterar anpassade kommando aktiviteter på klienten med talet SDK.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 687ae8fb30f7e81f0e35e4d4d9281b9500fd4923
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770754"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Gör så här: utföra kommandon på klienten med talet SDK (för hands version)

För att slutföra uppgifter med hjälp av ett anpassat kommando program kan du skicka anpassade nytto laster till en ansluten klient enhet.

I den här artikeln får du:

- Definiera och skicka en anpassad JSON-nyttolast från programmet för anpassade kommandon
- Ta emot och visualisera det anpassade JSON-nyttolasten C# från ett UWP-program för tal-SDK

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- En Azure-prenumerations nyckel för tal service
  - [Hämta ett kostnads fritt](get-started.md) eller skapa det på [Azure Portal](https://portal.azure.com)
- En tidigare skapad app med anpassade kommandon
  - [Snabb start: skapa ett anpassat kommando med parametrar (förhands granskning)](./quickstart-custom-speech-commands-create-parameters.md)
- Ett klient program som är kompatibla med Speech SDK
  - [Snabb start: ansluta till ett anpassat kommando program med talet SDK (för hands version)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Valfritt: kom igång snabbt

I den här artikeln beskrivs steg för steg hur du gör ett klient program att prata med dina anpassade kommandon. Om du föredrar att använda den fullständiga käll koden som är färdig att kompilera som används i den här artikeln finns i exempel på tal- [SDK](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Uppfylla med JSON-nyttolast

1. Öppna ditt tidigare skapade program för anpassade kommandon från [tal Studio](https://speech.microsoft.com/)
1. Kontrol lera att du har den tidigare skapade regeln som svarar på användaren genom att titta i avsnittet **regler för slut för ande** .
1. Om du vill skicka en nytto Last direkt till klienten skapar du en ny regel med åtgärden skicka aktivitet

   > [!div class="mx-imgBorder"]
   > ![skicka regel för slut för ande av aktivitet](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Regelnamn | UpdateDeviceState | Ett namn som beskriver syftet med regeln |
   | Villkor | Obligatorisk parameter – `OnOff` och `SubjectDevice` | Villkor som avgör när regeln kan köras |
   | Åtgärder | `SendActivity` (se nedan) | Den åtgärd som ska vidtas när regel villkoret är sant |

   > [!div class="mx-imgBorder"]
   > ![skicka](media/custom-speech-commands/fulfill-sdk-send-activity-action.png) för aktivitets nytto Last

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Skapa visuella objekt för enhet på eller av-tillstånd

I [snabb start: Anslut till ett anpassat kommando program med talet SDK (för hands version) du har](./quickstart-custom-speech-commands-speech-sdk.md) skapat ett program för tal-SDK-klient som hanterade kommandon, till exempel `turn on the tv`, `turn off the fan`. Nu kan du lägga till visuella objekt så att du kan se resultatet av dessa kommandon.

Lägg till etiketterade rutor med text som visar **på** eller **av** med hjälp av följande XML tillagt i `MainPage.xaml.cs`

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

Nu när du har skapat en JSON-nyttolast kan du lägga till en referens i [JSON.net](https://www.newtonsoft.com/json) -biblioteket för att hantera deserialisering.

> [!div class="mx-imgBorder"]
> ![skicka](media/custom-speech-commands/fulfill-sdk-json-nuget.png) för aktivitets nytto Last

I `InitializeDialogServiceConnector` lägger du till följande i `ActivityReceived` händelse hanteraren. Den ytterligare koden extraherar nytto lasten från aktiviteten och ändrar det visuella läget för TV: n eller fläkten på motsvarande sätt.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Prova

1. Starta programmet
1. Välj Aktivera mikrofon
1. Välj knappen prata
1. Säg `turn on the tv`
1. TV-apparatens visuella tillstånd ska ändras till "på"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: Lägg till valideringar i anpassade kommando parametrar (för hands version)](./how-to-custom-speech-commands-validations.md)
