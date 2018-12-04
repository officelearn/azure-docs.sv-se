---
title: Python-utvecklare för Azure Functions
description: Förstå hur du utvecklar funktioner med Pythong
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur och python
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 078a8995dc6f8ce9792f1d18661407f7c6d90029
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856898"
---
# <a name="azure-functions-python-developer-guide"></a>Utvecklarguide för Azure Functions Python

Den här artikeln är en introduktion till utvecklar Azure Functions med hjälp av Python. Innehållet nedan förutsätter att du redan har läst den [utvecklarguide för Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Programmeringsmodell

En Azure-funktion ska vara en tillståndslös metod i Python-skriptet som bearbetar indata och utdata. Som standard körningen förväntar sig att detta ska implementeras som en global metod som kallas `main()` i den `__init__.py` filen.

Du kan ändra standardkonfigurationen genom att ange den `scriptFile` och `entryPoint` egenskaper i den `function.json` filen. Till exempel den _function.json_ nedan berättar körning för att använda den _customentry()_ -metod i den _main.py_ som startpunkt för din Azure-funktion.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Data från utlösare och bindningar är bunden till funktionen via metoden attribut med hjälp av den `name` egenskapen som definierats i den `function.json` konfigurationsfilen. Till exempel den _function.json_ nedan beskriver en enkel funktion som utlöses av en HTTP-begäran med namnet `req`:

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

Du kan också deklarera parametertyper och returtyp i funktionen med hjälp av Python typanteckningar. Exempelvis kan kan samma funktion skrivas med anteckningar, enligt följande:

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
 | - extensions.csproj
 | - bin
```

Det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har sina egna kodfilen och konfigurationsfilen för bindning (function.json). 

Delad kod ska behållas i en separat mapp. Om du vill referera moduler i mappen SharedCode kan du använda följande syntax:

```
from ..SharedCode import myFirstHelperFunction
```

Tillägg av bindning används av Functions-körning har definierats i den `extensions.csproj` -fil med faktiska library-filer i den `bin` mapp. När du utvecklar lokalt, måste du [registrera tillägg av bindning](functions-triggers-bindings.md#local-development-azure-functions-core-tools) med hjälp av Azure Functions Core Tools. 

När du distribuerar en Functions-projekt till funktionsappen i Azure, ska hela innehållet i mappen FunctionApp tas med i paketet, men inte själva mappen.

## <a name="inputs"></a>Indata

Indata är indelade i två kategorier i Azure Functions: utlösarens indata och ytterligare indata. Även om de skiljer sig i `function.json`, användningen är identiska i Python-kod. Låt oss ta ett kodfragment som exempel:

```json
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

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

När funktionen anropas HTTP-begäran skickas till funktionen som `req`. En post som ska hämtas från Azure Blob-lagringen utifrån den _id_ i URL: en väg och blir tillgängliga som `obj` i själva funktionen.

## <a name="outputs"></a>Utdata

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
| loggning. **varning (_meddelande_)**    | Skriver ett meddelande med nivån varning på rot-loggaren.  |
| loggning. **info (_meddelande_)**    | Skriver ett meddelande med nivån information på rot-loggaren.  |
| loggning. **felsöka (_meddelande_)** | Skriver ett meddelande på felsökning på rot-loggaren.  |

## <a name="importing-shared-code-into-a-function-module"></a>Importera delad kod till en funktionsmodul

Python-moduler som publicerats tillsammans med funktionen moduler måste importeras med hjälp av relativa import-syntax:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Du kan också placera delad kod i ett fristående paket, publicera den till en offentlig eller privat PyPI-instans och ange den som ett reguljärt beroende.

## <a name="async"></a>Async

Eftersom bara en enda process Python får finnas per funktionsapp, rekommenderar vi att implementera din Azure-funktion som en asynkron coroutine med hjälp av den `async def` instruktionen.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Om funktionen main() är synkron (inga `async` kvalificerare) vi kör det automatiskt en `asyncio` trådpoolen.

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

## <a name="python-version-and-package-management"></a>Hantering av Python version och paket

För närvarande, Azure Functions stöder Python bara 3.6.x (officiella CPython distribution).

När du utvecklar lokalt med hjälp av Azure Functions Core Tools eller Visual Studio Code, lägger du till namn och versioner av de nödvändiga paketen till den `requirements.txt` filen och installera dem med hjälp av `pip`.

Till exempel följande krav för fil- och pip kommando kan användas för att installera den `requests` paket från PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

När du är redo för publicering, se till att alla beroenden finns med i den `requirements.txt` filen i roten av din projektkatalog. För att kunna köra dina Azure-funktioner, ska krav filen innehålla minst följande paket:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Publicering till Azure

Om du använder ett paket som kräver en kompilator och stöder inte installationen av manylinux-kompatibla hjul från PyPI misslyckas publicering till Azure med följande fel: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Att automatiskt skapa och konfigurera nödvändiga binärfiler [installera Docker](https://docs.docker.com/install/) på din lokala dator och kör sedan följande kommando för att publicera med hjälp av den [Azure Functions Core Tools](functions-run-local.md#v2) (func). Kom ihåg att ersätta `<app name>` med namnet på din funktionsapp i Azure. 

```bash
func azure functionapp <app name> --build-native-deps
```

Under försättsbladen, Core Tools ska använda docker för att köra den [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) avbildning som en behållare på den lokala datorn. Med den här miljön kan det därefter skapar och installera de nödvändiga modulerna från källdistribution innan du paketerar dem för slutlig distribution till Azure.

> [!NOTE]
> Core Tools (func) använder PyInstaller-programmet för att låsa användarens kod och beroenden i en enda fristående körbara fil som körs i Azure. Den här funktionen är för närvarande en förhandsversion och kan inte utöka till alla typer av Python-paket. Om det inte går att importera dina moduler, försök publicera igen med den `--no-bundler` alternativet. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Om du fortfarande har problem med att kontakta oss gärna av [att öppna ett ärende](https://github.com/Azure/azure-functions-core-tools/issues/new) och en beskrivning av problemet. 


Om du vill distribuera en Python-Funktionsapp till Azure måste du använda en [Travis CI anpassat skript](https://docs.travis-ci.com/user/deployment/script/) med GitHub-lagringsplatsen. Nedan visas ett exempel `.travis.yaml` skript för att skapa och publicera.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

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
