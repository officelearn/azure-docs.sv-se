---
title: Skicka anpassade kommandon-aktivitet till klient program
titleSuffix: Azure Cognitive Services
description: 'I den här artikeln får du lära dig hur du skickar en aktivitet från ett program med anpassade kommandon till ett klient program som kör tal-SDK: n.'
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0a3e3455615006c0e93cf32eebcdaedac9960a79
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307923"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Skicka anpassade kommandon-aktivitet till klient program

I den här artikeln får du lära dig hur du skickar en aktivitet från ett program med anpassade kommandon till ett klient program som kör tal-SDK: n.

Du utför följande aktiviteter:

- Definiera och skicka en anpassad JSON-nyttolast från programmet för anpassade kommandon
- Ta emot och visualisera det anpassade JSON-nyttolasten från ett C# UWP-program för tal-SDK

## <a name="prerequisites"></a>Krav
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * En Azure-prenumerations nyckel för tal service: [Hämta en kostnads fri](get-started.md) eller skapa den på [Azure Portal](https://portal.azure.com)
> * En tidigare [skapad app med anpassade kommandon](quickstart-custom-commands-application.md)
> * En klient app för tal-SDK som är aktive rad: [så här integrerar du med ett klient program med hjälp av tal-SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Konfigurera skicka aktivitet till klient 
1. Öppna programmet anpassade kommandon som du skapade tidigare
1. Välj kommandot **TurnOnOff** , Välj **ConfirmationResponse** under regel för slut för ande och välj sedan **Lägg till en åtgärd**
1. Under **ny åtgärd-typ**väljer **du skicka aktivitet till klient**
1. Kopiera JSON nedan till **aktivitets innehåll**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. Klicka på **Spara** för att skapa en ny regel med åtgärden skicka aktivitet

   > [!div class="mx-imgBorder"]
   > ![Skicka regel för slut för ande av aktivitet](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrera med klient program

I [anvisningar: Konfigurera klient program med Speech SDK (för hands version)](./how-to-custom-commands-setup-speech-sdk.md)har du skapat ett UWP-klientprogram med tal-SDK som hanterade kommandon som `turn on the tv` , `turn off the fan` . Med vissa visuella objekt tillagda kan du se resultatet av dessa kommandon.

Lägg till etiketterade rutor med text som visar **på** eller **av** med hjälp av följande XML tillagt i`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

### <a name="add-reference-libraries"></a>Lägg till referens bibliotek

Eftersom du har skapat en JSON-nyttolast måste du lägga till en referens i [JSON.net](https://www.newtonsoft.com/json) -biblioteket för att hantera deserialisering.

1. Rätt-klient din lösning.
1. Välj **Hantera NuGet-paket för lösningen**, Välj **Installera** 
1. Sök efter **Newtonsoft.js** i listan uppdatera, uppdatera **Microsoft. NetCore. UniversalWindowsPlatform** till senaste versionen

> [!div class="mx-imgBorder"]
> ![Skicka aktivitetens nytto Last](media/custom-commands/send-activity-to-client-json-nuget.png)

I MainPage. XAML. CS, lägger du till
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-the-received-payload"></a>Hantera mottagna nytto Last

I `InitializeDialogServiceConnector` ersätter du `ActivityReceived` händelse hanteraren med följande kod. Den modifierade `ActivityReceived` händelse hanteraren kommer att extrahera nytto lasten från aktiviteten och ändra TV-eller fläktens visuella status.

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
   > ![Skicka aktivitetens nytto Last](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: konfigurera webb slut punkter (förhands granskning)](./how-to-custom-commands-setup-web-endpoints.md)