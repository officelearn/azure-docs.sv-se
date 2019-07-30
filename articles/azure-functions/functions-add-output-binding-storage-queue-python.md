---
title: Lägg till en Azure Storage Queue-bindning till python-funktionen
description: Lär dig hur du lägger till en Azure Storage utgående bindning för en python-funktion med hjälp av Azure CLI-och Functions Core-verktyg.
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
ms.openlocfilehash: 34ec7c678410b2e0814f8dbb7a69257886cb891d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639152"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Lägg till en Azure Storage Queue-bindning till python-funktionen

Med Azure Functions kan du ansluta Azure-tjänster och andra resurser till funktioner utan att behöva skriva din egen integrerings kod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktions definitionen. Data från bindningar tillhandahålls till funktionen som parametrar. En utlösare är en särskild typ av ingående bindning. Även om en funktion bara har en utlösare, kan den ha flera indata och utdata-bindningar. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

Den här artikeln visar hur du integrerar funktionen som du skapade i [föregående snabb starts artikel](functions-create-first-function-python.md) med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra anslutningen enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot är redan lagrad i en app- `AzureWebJobsStorage`inställning med namnet.  

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln slutför du stegen i [del 1 av python-snabb](functions-create-first-function-python.md)starten.

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

I föregående snabb starts artikel skapade du en Function-app i Azure, tillsammans med det nödvändiga lagrings kontot. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagrings kö i samma konto. För att ansluta till ditt lagrings konto när funktionen körs lokalt måste du hämta appinställningar till filen Local. Settings. JSON. Kör följande Azure Functions Core tools-kommando för att ladda ned inställningar till Local. Settings. `<APP_NAME>` JSON och Ersätt med namnet på din Function-app från föregående artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Du kan behöva logga in på ditt Azure-konto.

> [!IMPORTANT]  
> Eftersom den innehåller hemligheter publiceras inte filen Local. Settings. JSON och den bör undantas från käll kontrollen.

Du behöver värdet `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Använd den här anslutningen för att kontrol lera att utgående bindning fungerar som förväntat.

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kan du lägga till bindningen för Storage-utdata i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I functions kräver varje typ av bindning att a `direction`, a `type`, och en unik `name` definieras i function. JSON-filen. Beroende på bindnings typen kan ytterligare egenskaper krävas. I [kös Ekö konfigurationen](functions-bindings-storage-queue.md#output---configuration) beskrivs de fält som krävs för en Azure Storage Queue-bindning.

Om du vill skapa en bindning lägger du till ett bindnings konfigurations objekt i function. JSON-filen. Redigera filen function. json i mappen HttpTrigger för att lägga till ett objekt i `bindings` matrisen som har följande egenskaper:

| Egenskap | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Namnet som identifierar bindnings parametern som refereras i din kod. |
| **`type`** | `queue` | Bindningen är en Azure Storage Queue-bindning. |
| **`direction`** | `out` | Bindningen är en utgående bindning. |
| **`queueName`** | `outqueue` | Namnet på kön som bindningen skriver till. `queueName` När inte finns skapar bindningen den när den används första gången. |
| **`connection`** | `AzureWebJobsStorage` | Namnet på en app-inställning som innehåller anslutnings strängen för lagrings kontot. `AzureWebJobsStorage` Inställningen innehåller anslutnings strängen för det lagrings konto som du skapade med Function-appen. |

Din function. JSON-fil bör nu se ut som i det här exemplet:

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

`name` När har kon figurer ATS kan du börja använda den för att få åtkomst till bindningen som ett method-attribut i Function-signaturen. I följande exempel `msg` är en instans [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)av.

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

När du använder en utgående bindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en referens till kön eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Som tidigare använder du följande kommando för att starta Functions-körningen lokalt:

```bash
func host start
```

> [!NOTE]  
> Eftersom i den tidigare snabb starten som du har aktiverat tillägg i Host. JSON laddades [lagrings bindnings tillägget](functions-bindings-storage-blob.md#packages---functions-2x) ned och installerades åt dig under starten, tillsammans med de andra Microsoft binding-tilläggen.

Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` till denna URL och kör begäran. Du bör se samma svar i webbläsaren som du gjorde i föregående artikel.

Den här gången skapar bindningen för utdata också en kö `outqueue` med namnet i ditt lagrings konto och lägger till ett meddelande med samma sträng.

Sedan använder du Azure CLI för att visa den nya kön och kontrol lera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Ange lagrings konto anslutningen

Öppna filen Local. Settings. JSON och kopiera värdet för `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. `AZURE_STORAGE_CONNECTION_STRING` Ställ in miljövariabeln på anslutnings strängen genom att använda det här bash-kommandot:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

När du anger anslutnings strängen i `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln, kan du komma åt ditt lagrings konto utan att behöva tillhandahålla autentisering varje gång.

### <a name="query-the-storage-queue"></a>Fråga lagrings kön

Du kan använda [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att Visa lagrings köer i ditt konto, som i följande exempel:

```azurecli-interactive
az storage queue list --output tsv
```

Utdata från det här kommandot innehåller en kö med `outqueue`namnet, som är kön som skapades när funktionen kördes.

Använd [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) sedan kommandot för att visa meddelanden i den här kön, som i det här exemplet:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Den returnerade strängen ska vara samma som det meddelande som du skickade för att testa funktionen.

> [!NOTE]  
> I föregående exempel avkodas den returnerade strängen från base64. Detta beror på att Queue Storage-bindningarna skriver till och läser från Azure Storage som [base64-strängar](functions-bindings-storage-queue.md#encoding).

Nu är det dags att publicera om den uppdaterade Function-appen till Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Igen kan du använda en sväng eller en webbläsare för att testa den distribuerade funktionen. Som tidigare lägger du till frågesträngen `&name=<yourname>` i URL: en, som i det här exemplet:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Du kan kontrol lera [lagrings köns meddelande](#query-the-storage-queue) för att kontrol lera att utgående bindningen igen genererar ett nytt meddelande i kön.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Mer information om hur du utvecklar Azure Functions med python finns i [Azure Functions python Developer Guide](functions-reference-python.md) och [Azure Functions utlösare och bindningar](functions-triggers-bindings.md).

Sedan bör du aktivera Application Insights övervakning för din Function-app:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/