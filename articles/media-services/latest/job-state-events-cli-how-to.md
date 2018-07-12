---
title: Dirigera Azure Media Services-händelser till en anpassad webbslutpunkt | Microsoft Docs
description: Använd Azure Event Grid prenumererar du på Media Services jobbet statusändringshändelsen.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e9df0cd24ef890765b78c25a073d671889be10a7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724066"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Dirigera Azure Media Services-händelser till en anpassad webbslutpunkt med CLI

Azure Event Grid är en händelsetjänst för molnet. I den här artikeln använder Azure CLI för att prenumerera på Azure Media Services jobbet tillståndsändringshändelser och utlösa händelsen för att visa resultatet. 

Normalt kan du skicka händelser till en slutpunkt som svarar på händelsen, exempelvis en webhook eller Azure Function. Den här självstudien visar hur du skapar och ställer in en webhook.

När du slutför stegen som beskrivs i den här artikeln ser du att händelsedata har skickats till en slutpunkt.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in i [Azure Portal](http://portal.azure.com) och starta **CloudShell** för att köra CLI-kommandon som visas i nästa steg.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI](/cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Se till att komma ihåg de värden som du använde för den Media Services-kontonamn och namnet på storage-resursnamnet.

## <a name="enable-event-grid-resource-provider"></a>Aktivera Event Grid-resursprovider

Första du behöver göra är se till att du har aktiverat på din prenumeration Event Grid-resursprovidern. 

I den **Azure** portalen gör du följande:

1. Gå till prenumerationer.
2. Välj din prenumeration.
3. Välj Resursprovidrar under inställningar.
4. Sök efter ”EventGrid”.
5. Kontrollera att Event Grid är registrerad. Om inte, trycker du på den **registrera** knappen.  

## <a name="create-a-generic-azure-function-webhook"></a>Skapa en allmän webhook i Azure-funktion 

### <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Skapa en slutpunkt som samlar in meddelandena så att du kan visa dem innan du prenumererar på Event Grid-artikeln.

Skapa en funktion som utlöses av en allmän webhook enligt beskrivningen i den [allmän webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) artikeln. I de här självstudierna i **C#** koden används.

När webhooken har skapats kan du kopiera URL: en genom att klicka på den *hämta Funktionswebbadress* länken högst upp på den **Azure** portalfönstret. Du behöver inte den sista delen av URL: en (*& clientID = standard*).

![Skapa en webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Verifiera webhooken

När du registrerar din egen webhook-slutpunkt med Event Grid skickar det du en POST-begäran med en enkel valideringskod att bevisa ägarskapet för slutpunkten. Din app behöver ska svara på eko tillbaka verifieringskoden. Event Grid leverera inte händelser till webHook-slutpunkter som inte har validerades. Mer information finns i [Event Grid säkerhet och autentisering](https://docs.microsoft.com/azure/event-grid/security-authentication). Det här avsnittet definierar två delar som måste definieras för verifiering för att skicka.

#### <a name="update-the-source-code"></a>Uppdatera källkoden

När du har skapat din webhook i **run.csx** filen visas i webbläsaren. Ersätt standardkoden med följande kod. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Uppdatera begärandetexten för testning

Till höger om den **Azure** portalfönstret som du ser två flikar: **visa filer** och **Test**. Välj fliken **Test**. I den **Begärandetext**, klistra in följande json. Du kan klistra in den skick behöver inte ändra alla värden.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Tryck på **spara och kör** överst i fönstret.

![Begärandetext](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Registrera dig för Event Grid-prenumeration 

Du prenumererar på en artikel som talar om Event Grid vilka händelser som du vill spåra. I följande exempel prenumererar vi på Media Services-konto du just skapat, och URL: en från Azure Function-webhook som du har skapat som slutpunkt för händelseavisering. 

Ersätt `<event_subscription_name>` med ett unikt namn för din händelseprenumeration. För `<resource_group_name>` och `<ams_account_name>` använder du det värde du skapade tidigare.  För den `<endpoint_URL>` klistra in din slutpunkts-URL. Ta bort *& clientID = standard* från URL: en. Genom att ange en slutpunkt när du prenumererar kan Event Grid hantera omdirigeringen av händelser till denna slutpunkt. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Media Services-konto resource ID-värdet ser ut ungefär så här:

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Testa händelser

Kör ett kodningsjobb. Till exempel som beskrivs i den [Stream videofiler](stream-files-dotnet-quickstart.md) Snabbstart.

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt du konfigurerade när du startade prenumerationen. Bläddra till webhooken som du skapade tidigare. Klicka på **övervakaren** och **uppdatera**. Du ser ändras tillståndet för jobbets händelser: ”i kö”, ”schemalagd”, ”bearbetnings”, ”klar”, ”Error”, ”har avbrutits”, ”avbryter”.  Mer information finns i [Media Services Händelsescheman](media-services-event-schemas.md).

Exempel:

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Testa händelser](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med det här lagringskontot och händelseprenumerationen ska du inte rensa upp bland de resurser som skapades i den här artikeln. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

Ersätt `<resource_group_name>` med resursgruppen du skapade ovan.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nästa steg

[Reagera på händelser](reacting-to-media-services-events.md)

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
