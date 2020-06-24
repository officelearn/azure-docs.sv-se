---
title: Konfigurera webb slut punkter (förhands granskning)
titleSuffix: Azure Cognitive Services
description: konfigurera webb slut punkter för anpassade kommandon
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5bdb77d27b01f576ca06aa5b6d3df0572b3b1ea6
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307927"
---
# <a name="set-up-web-endpoints"></a>Konfigurera webb slut punkter

I den här artikeln får du lära dig hur du ställer in webb slut punkter i ett program med anpassade kommandon som gör det möjligt att göra HTTP-förfrågningar från ett klient program. Du utför följande aktiviteter:

- Konfigurera webb slut punkter i program för anpassade kommandon
- Anropa webb slut punkter i program med anpassade kommandon
- Ta emot svar på webb slut punkter 
- Integrera webbslutpunkternas svar i en anpassad JSON-nyttolast, skicka och visualisera det från ett C# UWP-program för tal-SDK

## <a name="prerequisites"></a>Krav
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * En Azure-prenumerations nyckel för tal service: [Hämta en kostnads fri](get-started.md) eller skapa den på [Azure Portal](https://portal.azure.com)
> * En tidigare [skapad app med anpassade kommandon](quickstart-custom-commands-application.md)
> * En aktive rad klient app för tal SDK: [instruktioner: slut aktivitet till klient program](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Konfigurera webb slut punkter

1. Öppna programmet anpassade kommandon som du skapade tidigare. 
1. Gå till "webb slut punkter" och klicka på "ny webb slut punkt".

   > [!div class="mx-imgBorder"]
   > ![Ny webb slut punkt](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Inställningen | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Name | UpdateDeviceState | Namn för webb slut punkten. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | URL-adressen till den slut punkt som du vill att din anpassade kommando-app ska kommunicera med. |
   | Metod | POST | Tillåtna interaktioner (till exempel GET, POST) med din slut punkt.|
   | Sidhuvuden | Nyckel: app, värde: ett unikt namn för din app | De rubrik parametrar som ska ingå i begär ande huvudet.|

    > [!NOTE]
    > - Exempel webb slut punkten som skapades med [Azure Function](https://docs.microsoft.com/azure/azure-functions/), som kopplar samman med databasen som sparar TV-enhetens enhets tillstånd
    > - Den föreslagna rubriken behövs bara för exempel slut punkten
    > - I verkligheten kan webb slut punkten vara slut punkten till den IoT- [hubb](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) som hanterar dina enheter

1. Klicka på **Spara**.

## <a name="call-web-endpoints"></a>Anropa webb slut punkter

1. Gå till kommandot **TurnOnOff** , Välj **ConfirmationResponse** under regel för slut för ande och välj sedan **Lägg till en åtgärd**.
1. Under **ny åtgärd-typ**väljer du **anropa webb slut punkt**
1. I **Redigera åtgärd – slut punkter**väljer du **UpdateDeviceState**, som är webb slut punkten som vi skapade.  
1. I **konfiguration**anger du följande värden: 
   > [!div class="mx-imgBorder"]
   > ![Anropa åtgärds parametrar för webb slut punkter](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Inställningen | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Slutpunkter | UpdateDeviceState | Den webb slut punkt som du vill anropa i den här åtgärden. |
   | Frågeparametrar | objekt = {SubjectDevice} &&värde = {mikrofonen} | Frågeparametrar som ska läggas till i URL: en för webb slut punkten.  |
   | Innehåll i brödtext | Ej tillämpligt | Bröd innehållet i begäran. |

    > [!NOTE]
    > - Föreslagna frågeparametrar behövs bara för exempel slut punkten

1. Under **åtgärden lyckades-åtgärd att köra**väljer du **Skicka tal svar**.
   
   > [!div class="mx-imgBorder"]
   > ![Anropa webb slut punkts åtgärd vid lyckad](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Inställningen | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Åtgärd att köra | Skicka tal svar | Åtgärd som ska utföras om begäran till webb slut punkten lyckas |
   
   > [!NOTE]
   > - Du kan också få direkt åtkomst till fälten i http-svaret med hjälp av `{YourWebEndpointName.FieldName}` . Exempelvis: `{UpdateDeviceState.TV}`

1. Välj **Skicka tal svar** på grund **av haveri åtgärd**
   > [!div class="mx-imgBorder"]
   > ![Anropa webb slut punkts åtgärd vid misslyckande](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Inställningen | Föreslaget värde | Beskrivning |
   | ------- | --------------- | ----------- |
   | Åtgärd att köra | Skicka tal svar | Åtgärd som ska utföras om begäran till webb slut punkten Miss lyckas |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` är valfritt. Du är kostnads fri att ta bort det om du inte vill visa något fel meddelande.
   > - I vår exempel slut punkt skickar vi tillbaka http-svar med detaljerade fel meddelanden för vanliga fel som saknade huvud parametrar. 

### <a name="try-it-out-in-test-portal"></a>Testa i test portalen
- Vid lyckad svar \
Spara, träna och testa
   > [!div class="mx-imgBorder"]
   > ![Anropa webb slut punkts åtgärd vid lyckad](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Vid misslyckande svar \
Ta bort en av frågeparametrar, spara, omträna och testa
   > [!div class="mx-imgBorder"]
   > ![Anropa webb slut punkts åtgärd vid lyckad](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrera med klient program

I [anvisningar: skicka aktivitet till klient program (för hands version)](./how-to-custom-commands-send-activity-to-client.md)har du lagt till en **Skicka aktivitet till klient** åtgärden. Aktiviteten skickas till klient programmet oavsett om åtgärden **anropar webb slut punkten** lyckas eller inte.
I de flesta fall vill du dock bara skicka aktivitet till klient programmet när anropet till webb slut punkten lyckas. I det här exemplet är det när enhetens status har uppdaterats.

1. Ta bort åtgärden **Skicka aktivitet till klient** som du har lagt till tidigare.
1. Redigera anrops webb slut punkt: 
    1. I **konfigurationen**kontrollerar du att **frågeparametrar** är`item={SubjectDevice}&&value={OnOff}`
    1. Vid **lyckad**åtgärd, ändra **åtgärd att köra** för att **Skicka aktivitet till klienten**
    1. Kopiera JSON-filen nedan till **aktivitets innehållet**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Skicka aktivitet vid lyckad](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Nu ska du bara skicka aktivitet till klienten när begäran till webb slut punkten har slutförts.

### <a name="create-visuals-for-syncing-device-state"></a>Skapa visuella objekt för synkronisering av enhets status
Lägg till följande XML till `MainPage.xaml` ovanför `"EnableMicrophoneButton"` blocket.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Synkronisera enhets tillstånd 

I `MainPage.xaml.cs` lägger du till referensen `using Windows.Web.Http;` . Lägg till följande kod i- `MainPage` klassen. Den här metoden skickar en GET-begäran till exempel slut punkten och extraherar det aktuella enhets läget för appen. Se till att ändra `<your_app_name>` till det du använde i **sidhuvudet** i den anpassade kommando webb slut punkten

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Prova nu

1. Starta programmet
1. Klicka på synkronisera enhets tillstånd. \
Om du har testat ut appen med `turn on tv` i föregående avsnitt ser du att TV-programmen visas som "på".
    > [!div class="mx-imgBorder"]
    > ![Synkronisera enhets tillstånd](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Välj Aktivera mikrofon
1. Välj knappen prata
1. Berätta`turn on the fan`
1. Fläktens visuella tillstånd ska ändras till "på"
    > [!div class="mx-imgBorder"]
    > ![Aktivera fläkten](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera en CI/CD-process för anpassade kommandon-program](./how-to-custom-commands-deploy-cicd.md)