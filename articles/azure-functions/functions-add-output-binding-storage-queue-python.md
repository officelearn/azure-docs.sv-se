---
title: Lägg till en Azure Storage-kö-bindning till din Python-funktion
description: Lär dig hur du lägger till ett Azure Storage-köutdata bindning till din Python-funktion med Azure CLI och Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870042"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>Lägg till en Azure Storage-kö-bindning till din funktion

Azure Functions kan du ansluta Azure-tjänster och andra resurser på funktioner utan att skriva egen kod för integrering. Dessa *bindningar*, som representerar både indata och utdata, har deklarerats i definitionen. Data från bindningar har angetts för funktionen som parametrar. En utlösare är en särskild typ av indatabindning. En funktion har bara en utlösare, kan den ha flera indatafiler och utdatabindningar. Mer information finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md).

Den här artikeln visar hur du integrerar funktionen som du skapade i den [tidigare snabbstartsartikel](functions-create-first-function-python.md) med ett Azure Storage-kö. Utdatabindningen som du lägger till den här funktionen skriver data från HTTP-begäran till ett meddelande i kön. 

De flesta bindningar kräver en lagrade anslutningssträng som Functions använder för att komma åt bundna tjänsten. Om du vill göra det enklare, kan du använda det lagringskonto som du skapade med din funktionsapp. Anslutningen till det här kontot lagras redan i en app som inställning med namnet `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här artikeln bör du slutföra stegen i [del 1 av Python-quickstart](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Ladda ned funktionsappinställningarna

I tidigare i snabbstartsartikeln skapat du en funktionsapp i Azure tillsammans med ett Storage-konto. Anslutningssträngen för det här kontot lagras på ett säkert sätt i appen inställningar i Azure. I den här artikeln får skriva du meddelanden till en lagringskö i samma konto. För att ansluta till ditt Storage-konto när du kör funktionen lokalt, måste du hämta app-inställningar i filen local.settings.json. Kör följande Azure Functions Core Tools-kommando för att hämta inställningar i local.settings.json, ersätta `<APP_NAME>` med namnet på din funktionsapp från föregående artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Du kan behöva logga in på ditt Azure-konto.

> [!IMPORTANT]  
> Eftersom den innehåller hemligheter filen local.settings.json publiceras aldrig och den bör undantas från källkontroll.

Du behöver värdet `AzureWebJobsStorage`, vilket är anslutningssträngen för Lagringskonto. Du kan använda den här anslutningen för att kontrollera att utdatabindningen fungerar som förväntat.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I funktioner, varje typ av bindning kräver en `direction`, `type`, och ett unikt `name` definieras i filen function.json. Beroende på bindningstyp av kan eventuellt ytterligare egenskaper som krävs. Den [kö utdata configuration](functions-bindings-storage-queue.md#output---configuration) beskriver de fält som krävs för en bindning för Azure Storage-kö.

För att skapa en bindning måste du lägga till ett konfigurationsobjekt för bindningen till den `function.json` filen. Redigera filen function.json i mappen HttpTrigger att lägga till ett objekt för att den `bindings` matris som har följande egenskaper:

| Egenskap  | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Namn som identifierar bindningsparametern som refereras till i din kod. |
| **`type`** | `queue` | Bindningen är en bindning för Azure Storage-kö. |
| **`direction`** | `out` | Bindningen är en utdatabindning. |
| **`queueName`** | `outqueue` | Namnet på kön som bindningen skriver till. När den *queueName* inte finns några bindningen skapar den första användningen. |
| **`connection`** | `AzureWebJobsStorage` | Namnet på en appinställning som innehåller anslutningssträngen för lagringskontot. Den `AzureWebJobsStorage` inställningen innehåller anslutningssträngen för lagringskontot som du skapade med appen. |

Filen function.json bör nu se ut som i följande exempel:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När den har konfigurerats kan du börja använda den `name` om bindningen till åtkomst till den som ett metodattribut i funktionssignaturen. I följande exempel `msg` är en instans av den [ `azure.functions.InputStream class` ](/python/api/azure-functions/azure.functions.httprequest).

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Genom att använda en utdatabindning kan du inte använder Azure Storage SDK-koden för autentisering, lägga till en referens för kön eller skriva data. Functions-körningen och utdata bindning utföra dessa uppgifter åt dig.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Som tidigare använder du följande kommando för att starta funktionskörningen lokalt:

```bash
func host start
```

> [!NOTE]  
> Eftersom föregående artikel hade du aktivera tillägget paket i host.json, den [Storage bindningstillägget](functions-bindings-storage-blob.md#packages---functions-2x) har hämtats och installerats för dig under starten.

Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` i webbadressen och kör din begäran. Du bör se samma svaret i webbläsaren som du gjorde i föregående artikel.

Den här gången utdatabindningen skapar även en kö med namnet `outqueue` i ditt Storage-kontot och lägger till ett meddelande med det här samma sträng.

Därefter använder Azure CLI för att visa den nya kön och kontrollera att meddelandet har lagts till. Du kan också visa din kö med hjälp av den [Microsoft Azure Lagringsutforskaren] [ Azure Storage Explorer] eller i den [Azure-portalen](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Ange lagringskontoanslutning

Öppna filen local.settings.json och kopiera värdet för `AzureWebJobsStorage`, vilket är anslutningssträngen för Lagringskonto. Ange den `AZURE_STORAGE_CONNECTION_STRING` miljövariabel på den anslutningssträng som hjälp av följande Bash-kommando:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Med anslutningssträngen som angetts i den `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln, du kan komma åt ditt lagringskonto utan att behöva ange autentisering varje gång.

### <a name="query-the-storage-queue"></a>Fråga Storage-kö

Du kan använda den [ `az storage queue list` ](/cli/azure/storage/queue#az-storage-queue-list) kommando för att visa Storage-köer i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här kommandot innehåller en kö med namnet `outqueue`, vilket är den kö som skapades när funktionen kördes.

Använd sedan den [ `az storage message peek` ](/cli/azure/storage/message#az-storage-message-peek) kommando för att visa meddelanden i kön, som i följande exempel.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Strängen som returneras ska vara samma som meddelandet du skickat till att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodar den returnerade strängen från base64. Queue storage-bindningar skriva till och läsa från Azure Storage som är [base64 strängar](functions-bindings-storage-queue.md#encoding).

Nu är det dags att publicera uppdaterade function-app till Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Igen, kan du använda cURL eller en webbläsare för att testa den distribuerade funktionen. Lägg till frågesträngen som tidigare `&name=<yourname>` till URL: en, som i följande exempel:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Du kan [undersöka lagringskömeddelande](#query-the-storage-queue) att verifiera att utdatabindning igen genererar ett nytt meddelande i kön.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagringskö. Mer information om hur du utvecklar Azure Functions med Python finns i [utvecklarguide för Azure Functions Python](functions-reference-python.md) och [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md).

Därefter bör du aktivera Application Insights-övervakning för din funktionsapp:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/