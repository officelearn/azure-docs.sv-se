---
title: Python Developer-referens för Azure Functions
description: Förstå hur du utvecklar funktioner med python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, functions, Event Processing, dynamisk beräkning, Server lös arkitektur, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 16cf6704096f8c1534777ffb1958d2fa858374db
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170543"
---
# <a name="azure-functions-python-developer-guide"></a>Guide för Azure Functions python-utvecklare

Den här artikeln är en introduktion till att utveckla Azure Functions med python. Innehållet nedan förutsätter att du redan har läst [guiden Azure Functions utvecklare](functions-reference.md). 

Exempel projekt för fristående funktioner i python finns i [exempel på python-funktioner](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Programmeringsmodell

Azure Functions förväntar sig att en funktion är en tillstånds lös metod i python-skriptet som bearbetar indata och genererar utdata. Som standard förväntar sig körningen att metoden ska implementeras som en global metod `main()` som kallas `__init__.py` i filen. Du kan också [Ange en alternativ Start punkt](#alternate-entry-point).

Data från utlösare och bindningar binds till funktionen via method-attribut `name` med hjälp av egenskapen som definierats i *Function. JSON* -filen. _Funktionen. JSON_ nedan beskriver till exempel en enkel funktion som utlöses av en http-begäran med `req`namnet:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` Filen innehåller följande funktions kod:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Du kan även explicit deklarera attributtyper och retur typen i funktionen med hjälp av python-typ anteckningar. På så sätt kan du använda IntelliSense-och Autoavsluta-funktionerna från många python-kod redigerare.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Använd python-anteckningarna som ingår i [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python) -paketet för att binda indata och utdata till dina metoder.

## <a name="alternate-entry-point"></a>Alternativ Start punkt

Du kan ändra standard beteendet för en funktion genom att alternativt ange `scriptFile` och `entryPoint` -egenskaperna i *Function. JSON* -filen. _Funktionen. JSON_ nedan meddelar till exempel körningen att använda `customentry()` metoden i _main.py_ -filen som start punkt för din Azure-funktion.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappstruktur

Mappstrukturen för ett python Functions-projekt ser ut som i följande exempel:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Det finns en delad [Host. JSON](functions-host-json.md) -fil som kan användas för att konfigurera Function-appen. Varje funktion har sin egen kod fil och bindnings konfigurations fil (Function. JSON). 

Delad kod ska sparas i en separat mapp. Om du vill referera till moduler i SharedCode-mappen kan du använda följande syntax:

```
from __app__.SharedCode import myFirstHelperFunction
```

När du distribuerar ett funktions projekt till din Function-app i Azure, ska hela innehållet i *FunctionApp* -mappen inkluderas i paketet, men inte själva mappen.

## <a name="triggers-and-inputs"></a>Utlösare och indata

Indata är indelade i två kategorier i Azure Functions: Utlös indata och ytterligare indata. Även om de skiljer sig i `function.json` filen är användningen identiska i python-kod.  Anslutnings strängar eller hemligheter för utlösare och inmatade källor mappar `local.settings.json` till värden i filen när de körs lokalt och program inställningarna körs i Azure. 

Till exempel visar följande kod skillnaden mellan de två:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

När funktionen anropas skickas HTTP-begäran till funktionen som `req`. En post hämtas från Azure-Blob Storage baserat på _ID: t_ i väg-URL: en och görs tillgänglig `obj` som i funktions texten.  Här är det angivna lagrings kontot den anslutnings sträng som `AzureWebJobsStorage` finns i och som är samma lagrings konto som används av Function-appen.


## <a name="outputs"></a>outputs

Utdata kan uttryckas både i retur värde och utdataparametrar. Om det bara finns en utmatning rekommenderar vi att du använder det returnerade värdet. För flera utdata måste du använda utdataparametrar.

Om du vill använda returvärdet för en funktion som värde för en utgående bindning `name` ska egenskapen för bindningen anges till `$return` i `function.json`.

Om du vill skapa flera utdata använder du `set()` metoden som tillhandahålls [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) av gränssnittet för att tilldela ett värde till bindningen. Följande funktion kan till exempel skicka ett meddelande till en kö och även returnera ett HTTP-svar.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Loggning

Åtkomst till Azure Functions runtime-loggen är tillgänglig via en [`logging`](https://docs.python.org/3/library/logging.html#module-logging) rot hanterare i din Function-app. Den här loggen är kopplad till Application Insights och gör att du kan flagga varningar och fel som påträffas under funktions körningen.

I följande exempel loggas ett informations meddelande när funktionen anropas via en HTTP-utlösare.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Det finns ytterligare loggnings metoder som gör att du kan skriva till-konsolen på olika spårnings nivåer:

| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Skriver ett meddelande med nivå kritisk på rot loggaren.  |
| **`error(_message_)`**   | Skriver ett meddelande med nivå fel på rot loggaren.    |
| **`warning(_message_)`**    | Skriver ett meddelande med nivå varning på rot loggaren.  |
| **`info(_message_)`**    | Skriver ett meddelande med nivå information på rot loggaren.  |
| **`debug(_message_)`** | Skriver ett meddelande med nivå fel sökning på rot loggaren.  |

Mer information om loggning finns i [övervaka Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-utlösaren definieras i filen function. Jon. `name` För bindningen måste matcha den namngivna parametern i funktionen. I föregående exempel används ett bindnings namn `req` . Den här parametern är ett [HttpRequest] -objekt och ett [HttpResponse] -objekt returneras.

Du kan hämta begärandehuvuden, frågeparametrar, väg parametrar och meddelande texten från [HttpRequest] -objektet. 

Följande exempel är från [http trigger-mallen för python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

I den här funktionen hämtas värdet för `name` Frågeparametern `params` från parametern för [HttpRequest] -objektet. Den JSON-kodade meddelande texten läses med hjälp `get_json` av metoden. 

På samma sätt kan du ange `status_code` och `headers` för svarsmeddelandet i det returnerade [HttpResponse] -objektet.
                                                              
## <a name="async"></a>Async

Vi rekommenderar att du skriver din Azure-funktion som en asynkron samarbets rutin `async def` med hjälp av instruktionen.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Om Main ()-funktionen är synkron (ingen kvalificerare), kör vi automatiskt funktionen i en `asyncio` Thread-pool.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Kontext

Om du vill hämta anrops kontexten för en funktion under körningen [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) ska du inkludera argumentet i signaturen. 

Exempel:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Kontext**](/python/api/azure-functions/azure.functions.context?view=azure-python) klassen har följande metoder:

`function_directory`  
Katalogen där funktionen körs.

`function_name`  
Namnet på funktionen.

`invocation_id`  
ID för aktuellt funktions anrop.

## <a name="global-variables"></a>Globala variabler

Det garanterar inte att appens tillstånd kommer att bevaras för framtida körningar. Azure Functions körning återanvänder dock ofta samma process för flera körningar av samma app. För att cachelagra resultatet av en dyr beräkning, deklarera det som en global variabel. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [program inställningar](functions-app-settings.md), t. ex. tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här inställningarna genom `import os` att deklarera och sedan `setting = os.environ["setting-name"]`använda.

I följande exempel hämtas [program inställningen](functions-how-to-use-azure-function-app-settings.md#settings)med nyckeln med namnet `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

För lokal utveckling underhålls program inställningarna [i filen Local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version-and-package-management"></a>Python-version och paket hantering

Azure Functions stöder för närvarande endast python 3.6. x (officiell CPython-distribution).

När du utvecklar lokalt med Azure Functions Core Tools eller Visual Studio Code lägger du till namn och versioner för de nödvändiga paketen i `requirements.txt` filen och installerar dem med `pip`.

Till exempel kan följande krav fil och pip-kommando användas för att installera `requests` paketet från pypi.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicera till Azure

När du är redo att publicera ser du till att alla beroenden visas i filen *Requirements. txt* , som finns i rot katalogen i projekt katalogen. Azure Functions kan [bygga](functions-deployment-technologies.md#remote-build) dessa beroenden på distans.

Använd följande kommando för att distribuera till Azure och utföra en fjärran sluten version:

```bash
func azure functionapp publish <app name> --build remote
```

Om du inte använder fjärran sluten version och använder ett paket som kräver en kompilerare och inte stöder installationen av många Linux-kompatibla hjul från PyPI, kommer publicering till Azure utan att det går att skapa lokalt att Miss förklaras med följande fel:

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Om du vill skapa ett lokalt och konfigurera de binärfiler som krävs [installerar](https://docs.docker.com/install/) du Docker på den lokala datorn och kör följande kommando för att publicera med hjälp av [Azure Functions Core tools](functions-run-local.md#v2) (Func). Kom ihåg att `<app name>` ersätta med namnet på din Function-app i Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Under försättsblad använder kärn verktygen Docker för att köra [MCR.Microsoft.com/Azure-Functions/python](https://hub.docker.com/r/microsoft/azure-functions/) -avbildningen som en behållare på den lokala datorn. Med den här miljön skapar och installerar du de moduler som krävs från käll distribution, innan de paketeras för slutlig distribution till Azure.

[Använd Azure-pipeline](functions-how-to-azure-devops.md)för att bygga dina beroenden och publicera med ett kontinuerligt leverans system (CD). 

## <a name="unit-testing"></a>Enhets testning

Funktioner som skrivs i python kan testas som annan python-kod med standard testnings ramverk. För de flesta bindningar är det möjligt att skapa ett objekt av en modell genom att skapa en instans av en lämplig klass `azure.functions` från paketet. Eftersom paketet inte är omedelbart tillgängligt ser du till att installera det `requirements.txt` via filen enligt beskrivningen i avsnittet om [hantering av python-version och paket hantering](#python-version-and-package-management) ovan. [`azure.functions`](https://pypi.org/project/azure-functions/)

Följande är till exempel ett modell test av en HTTP-utlöst funktion:

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
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
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Här är ett annat exempel med en funktion som utlöses av en kö:

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

Alla kända problem och funktions begär Anden spåras med hjälp av listan med [GitHub-problem](https://github.com/Azure/azure-functions-python-worker/issues) . Om du stöter på ett problem och inte kan hitta problemet i GitHub, öppnar du ett nytt ärende och innehåller en detaljerad beskrivning av problemet.

### <a name="cross-origin-resource-sharing"></a>Cross-origin resource sharing (CORS)

Azure Functions stöder resurs delning mellan ursprung (CORS). CORS konfigureras [i portalen](functions-how-to-use-azure-function-app-settings.md#cors) och via [Azure CLI](/cli/azure/functionapp/cors). Listan över tillåtna ursprung för CORS-listan gäller på funktions nivån app. När CORS är aktiverat inkluderar `Access-Control-Allow-Origin` Svaren rubriken. Mer information finns i [Cross-origin resource sharing](functions-how-to-use-azure-function-app-settings.md#cors).

Listan över tillåtna ursprung [stöds för närvarande inte](https://github.com/Azure/azure-functions-python-worker/issues/444) för python Function-appar. På grund av den här begränsningen måste du uttryckligen ange `Access-Control-Allow-Origin` sidhuvudet i dina http-funktioner, som du ser i följande exempel:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Se till att du också uppdaterar function. JSON för att ge stöd för alternativ-HTTP-metoden:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Den här metoden används av Chrome-webbläsaren för att förhandla listan över tillåtna ursprung. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Dokumentation om Azure Functions Package API](/python/api/azure-functions/azure.functions?view=azure-python)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Blob Storage-bindningar](functions-bindings-storage-blob.md)
* [HTTP-och webhook-bindningar](functions-bindings-http-webhook.md)
* [Köa lagrings bindningar](functions-bindings-storage-queue.md)
* [Timerutlösare](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python