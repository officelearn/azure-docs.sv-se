---
title: Python-utvecklare för Azure Functions
description: Förstå hur du utvecklar funktioner med Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur och python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 14594e95efe94fe38502dc6269627158c42a04be
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622357"
---
# <a name="azure-functions-python-developer-guide"></a>Utvecklarguide för Azure Functions Python

Den här artikeln är en introduktion till utvecklar Azure Functions med hjälp av Python. Innehållet nedan förutsätter att du redan har läst den [utvecklarguide för Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Programmeringsmodell

En Azure-funktion ska vara en tillståndslös metod i Python-skriptet som bearbetar indata och utdata. Som standard körningen förväntar sig att metoden implementeras som en global metod som kallas `main()` i den `__init__.py` filen.

Du kan ändra standardkonfigurationen genom att ange den `scriptFile` och `entryPoint` egenskaper i den *function.json* fil. Till exempel den _function.json_ nedan berättar körning för att använda den `customentry()` -metod i den _main.py_ som startpunkt för din Azure-funktion.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Data från utlösare och bindningar är bunden till funktionen via metoden attribut med hjälp av den `name` egenskapen som definierats i den *function.json* fil. Till exempel den _function.json_ nedan beskriver en enkel funktion som utlöses av en HTTP-begäran med namnet `req`:

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

Den `__init__.py` filen innehåller följande funktionskod:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Du kan också om du vill använda intellisense och komplettera funktionerna som tillhandahålls av ditt kodredigeringsprogram, kan du också deklarera attributtyperna och returtyp i funktionen med hjälp av Python typanteckningar. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Använda Python-anteckningar som ingår i den [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) paket att binda indata och utdata till din metoder.

## <a name="folder-structure"></a>mappstruktur

Mappstrukturen för en Python-Functions-projekt som ser ut som följande:

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

Det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har sina egna kodfilen och konfigurationsfilen för bindning (function.json). 

Delad kod ska behållas i en separat mapp. Om du vill referera moduler i mappen SharedCode kan du använda följande syntax:

```
from __app__.SharedCode import myFirstHelperFunction
```

När du distribuerar en Function-projekt till funktionsappen i Azure, hela innehållet i den *FunctionApp* mappen ska inkluderas i paketet, men inte själva mappen.

## <a name="triggers-and-inputs"></a>Utlösare och indata

Indata är indelade i två kategorier i Azure Functions: utlösarens indata och ytterligare indata. Även om de skiljer sig i den `function.json` fil, användningen är identiska i Python-kod.  Anslutningssträngar eller hemligheter för utlösare och indata källor som mappas till värdena i den `local.settings.json` filen när den körs lokalt och programinställningar när du kör i Azure. 

Till exempel visar följande kod skillnaden mellan två:

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

När funktionen anropas HTTP-begäran skickas till funktionen som `req`. En post som ska hämtas från Azure Blob-lagringen utifrån den _ID_ i URL: en väg och blir tillgängliga som `obj` i själva funktionen.  Storage-konto anges här är hitta anslutningssträngen i `AzureWebJobsStorage` som är samma lagringskonto som används av funktionsappen.


## <a name="outputs"></a>outputs

Resultatet kan uttryckas både i returvärdet och utdataparametrar. Om det finns bara en utdata, bör du använda det returnera värdet. För flera utdata kommer du behöva använda utdataparametrar.

Du använder det returnera värdet för en funktion som värde för en utdatabindning i `name` egenskapen om bindningen ska vara inställd på `$return` i `function.json`.

För att skapa flera utdata, använda den `set()` metod som tillhandahålls av den `azure.functions.Out` gränssnittet för att tilldela ett värde till bindningen. Följande funktion kan till exempel skickar ett meddelande till en kö och också returnera ett HTTP-svar.

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

Åtkomst till Azure Functions runtime loggaren är tillgängligt via en rot [ `logging` ](https://docs.python.org/3/library/logging.html#module-logging) hanteraren i din funktionsapp. Den här loggaren är knuten till Application Insights och du kan ange flaggan varningar och fel påträffades under körning funktion.

I följande exempel loggar ett informationsmeddelande med när funktionen anropas via en HTTP-utlösare.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Ytterligare loggningsmetoder är tillgängliga som låter dig skriva till konsolen vid olika spårningsnivåer:

| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| loggning. **kritiska (_meddelande_)**   | Skriver ett meddelande med nivån kritiskt på rot-loggaren.  |
| loggning. **fel (_meddelande_)**   | Skriver ett meddelande med på TOPPNIVÅ på rot-loggaren.    |
| logging.**warning(_message_)**    | Skriver ett meddelande med nivån varning på rot-loggaren.  |
| loggning. **info (_meddelande_)**    | Skriver ett meddelande med nivån information på rot-loggaren.  |
| logging.**debug(_message_)** | Skriver ett meddelande på felsökning på rot-loggaren.  |

## <a name="async"></a>Async

Vi rekommenderar att du skriver din Azure-funktion som en asynkron coroutine med hjälp av den `async def` instruktionen.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Om funktionen main() är synkron (inga `async` kvalificerare) vi automatiskt att köra funktionen en `asyncio` trådpoolen.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Kontext

För att få anrop kontexten för en funktion under körning kan inkludera den `context` argumentet i dess signatur. 

Exempel:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Den **kontext** klassen har följande metoder:

`function_directory`  
Den katalog där funktionen körs.

`function_name`  
Namnet på funktionen.

`invocation_id`  
ID för den aktuella funktionsanrop.

## <a name="global-variables"></a>Globala variabler

Det är inte säkert att tillståndet för din app kommer att bevaras för framtida körningar. Azure Functions-runtime återanvänder dock ofta samma process för flera körningar av samma app. Deklarera den för att cachelagra resultatet av en dyr beräkning, som en global variabel. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Hantering av Python version och paket

För närvarande, Azure Functions stöder Python bara 3.6.x (officiella CPython distribution).

När du utvecklar lokalt med hjälp av Azure Functions Core Tools eller Visual Studio Code, lägger du till namn och versioner av de nödvändiga paketen till den `requirements.txt` filen och installera dem med hjälp av `pip`.

Till exempel följande krav för fil- och pip kommando kan användas för att installera den `requests` paket från PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicering till Azure

När du är redo att publicera, se till att alla beroenden är angiven i den *requirements.txt* -fil som finns i roten av din projektkatalog. Om du använder ett paket som kräver en kompilator och stöder inte installationen av manylinux-kompatibla hjul från PyPI misslyckas publicering till Azure med följande fel: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Att automatiskt skapa och konfigurera nödvändiga binärfiler [installera Docker](https://docs.docker.com/install/) på din lokala dator och kör sedan följande kommando för att publicera med hjälp av den [Azure Functions Core Tools](functions-run-local.md#v2) (func). Kom ihåg att ersätta `<app name>` med namnet på din funktionsapp i Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Under försättsbladen, Core Tools ska använda docker för att köra den [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) avbildning som en behållare på den lokala datorn. Med den här miljön kan det därefter skapar och installera de nödvändiga modulerna från källdistribution innan du paketerar dem för slutlig distribution till Azure.

Och skapa dina beroenden som du kan publicera med hjälp av ett system för kontinuerlig leverans (CD), [använder Azure DevOps Pipelines](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Enhetstestning

Python-funktioner kan testas som andra Python-kod med hjälp av standard testning ramverk. För de flesta bindningar, är det möjligt att skapa en fingerad indataobjektet genom att skapa en instans av en lämplig klass ändrats från den `azure.functions` paketet. Eftersom den [ `azure.functions` ](https://pypi.org/project/azure-functions/) paketet är inte omedelbart tillgängligt, måste du installera den via din `requirements.txt` filen enligt beskrivningen i [Python version och paketet management](#python-version-and-package-management) ovan.

Följande är till exempel en fingerad test av en HTTP-utlöst funktion:

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

Här är ett annat exempel med en funktion som utlöses av lagringskön:

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

Alla kända problem och funktionsförfrågningar spåras med hjälp av [GitHub-ärenden](https://github.com/Azure/azure-functions-python-worker/issues) lista. Om du stöter på problem och kan inte hitta problemet i GitHub, öppnas ett nytt ärende och innehåller en detaljerad beskrivning av problemet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [BLOB storage-bindningar](functions-bindings-storage-blob.md)
* [HTTP och Webhook-bindningar](functions-bindings-http-webhook.md)
* [Queue storage-bindningar](functions-bindings-storage-queue.md)
* [Timerutlösare](functions-bindings-timer.md)
