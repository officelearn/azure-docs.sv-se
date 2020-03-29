---
title: Så här uppfyller du kommandon från en klient med Tal-SDK
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklarar vi hur du hanterar anpassade kommandon aktiviteter på en klient med Tal SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367757"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Uppfylla kommandon från en klient med Tal-SDK (förhandsversion)

Om du vill slutföra uppgifter med hjälp av ett program för anpassade kommandon kan du skicka anpassade nyttolaster till en ansluten klientenhet.

I den här artikeln ska du:

- Definiera och skicka en anpassad JSON-nyttolast från ditt program för anpassade kommandon
- Ta emot och visualisera det anpassade JSON-nyttolastens innehåll från ett C# UWP Speech SDK-klientprogram

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- En Azure-prenumerationsnyckel för Tal-tjänst
  - [Skaffa en gratis](get-started.md) eller skapa den på [Azure-portalen](https://portal.azure.com)
- En tidigare skapad app för anpassade kommandon
  - [Snabbstart: Skapa ett anpassat kommando med parametrar (förhandsgranskning)](./quickstart-custom-speech-commands-create-parameters.md)
- Ett Speech SDK-aktiverat klientprogram
  - [Snabbstart: Ansluta till ett anpassat kommandoprogram med Tal-SDK (förhandsversion)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Valfritt: Kom igång snabbt

I den här artikeln beskrivs steg för steg hur du gör ett klientprogram för att prata med ditt program för anpassade kommandon. Om du föredrar att dyka rätt i, den kompletta, färdiga att kompilera källkod som används i den här artikeln finns i [Tal SDK-exemplen](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Uppfylla med JSON nyttolast

1. Öppna ditt tidigare skapade program för anpassade kommandon från [Talstudion](https://speech.microsoft.com/)
1. Kontrollera avsnittet **Slutföranderegler** för att se till att du har den tidigare skapade regeln som svarar tillbaka till användaren
1. Om du vill skicka en nyttolast direkt till klienten skapar du en ny regel med åtgärden Skicka aktivitet

   > [!div class="mx-imgBorder"]
   > ![Regel för slutförande av aktivitet](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Inställning | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Regelnamn | UpdateDeviceState | Ett namn som beskriver syftet med regeln |
   | Villkor | Obligatorisk parameter `OnOff` - och`SubjectDevice` | Villkor som avgör när regeln kan köras |
   | Åtgärder | `SendActivity`(se nedan) | Åtgärden som ska vidtas när regelvillkoret är sant |

   > [!div class="mx-imgBorder"]
   > ![Skicka aktivitetsnyttolast](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Skapa visuella objekt för enheten på eller utanför läget

I [Snabbstart: Anslut till ett anpassat kommandoprogram med tal-SDK (förhandsversion)](./quickstart-custom-speech-commands-speech-sdk.md) skapade du `turn on the tv`ett `turn off the fan`Speech SDK-klientprogram som hanterade kommandon som , . Lägg nu till några visuella objekt så att du kan se resultatet av dessa kommandon.

Lägga till märkta rutor med text som anger **På** eller **Av** med hjälp av följande XML som lagts till i`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Hantera anpassningsbar nyttolast

Nu när du har skapat en JSON-nyttolast kan du lägga till en referens till [det JSON.NET](https://www.newtonsoft.com/json) biblioteket för att hantera avserialisering.

> [!div class="mx-imgBorder"]
> ![Skicka aktivitetsnyttolast](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

Lägg `InitializeDialogServiceConnector` till följande `ActivityReceived` i händelsehanteraren. Den extra koden kommer att extrahera nyttolasten från aktiviteten och ändra det visuella tillståndet för TV eller fläkt i enlighet med detta.

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

## <a name="try-it-out"></a>Prova det

1. Starta programmet
1. Välj Aktivera mikrofon
1. Välj knappen Samtal
1. Säga`turn on the tv`
1. Tv:ns visuella tillstånd ska ändras till "På"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här lägger du till valideringar i parametrar för anpassat kommando (förhandsgranskning)](./how-to-custom-speech-commands-validations.md)
