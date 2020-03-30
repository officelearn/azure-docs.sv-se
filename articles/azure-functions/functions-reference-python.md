---
title: Python-utvecklarreferens för Azure Functions
description: Förstå hur du utvecklar funktioner med Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276691"
---
# <a name="azure-functions-python-developer-guide"></a>Utvecklarhandboken för Azure Functions Python

Den här artikeln är en introduktion till att utveckla Azure-funktioner med Python. Innehållet nedan förutsätter att du redan har läst [Azure Functions-utvecklarguiden](functions-reference.md). 

För fristående funktionsexempelprojekt i Python finns i [python functions-exemplen](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Programmeringsmodell

Azure Functions förväntar sig att en funktion är en tillståndslös metod i python-skriptet som bearbetar indata och producerar utdata. Som standard förväntar sig körningen att metoden ska implementeras `__init__.py` som en global metod som anropas `main()` i filen. Du kan också [ange en alternativ startpunkt](#alternate-entry-point).

Data från utlösare och bindningar är bundna till `name` funktionen via metodattribut med hjälp av egenskapen som definieras i *filen function.json.* _Funktionen.json_ nedan beskriver till exempel en enkel funktion som `req`utlöses av en HTTP-begäran med namnet :

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Baserat på den `__init__.py` här definitionen kan filen som innehåller funktionskoden se ut som följande exempel:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Du kan också uttryckligen deklarera attributtyper och returtyp i funktionen med hjälp av Python-typanteckningar. Detta hjälper dig att använda de funktioner som intellisense och komplettera automatiskt som tillhandahålls av många Python-kodredigerare.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Använd Python-anteckningarna som ingår i [paketet azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) för att binda indata och utdata till dina metoder.

## <a name="alternate-entry-point"></a>Alternativ startpunkt

Du kan ändra standardbeteendet för en funktion `scriptFile` `entryPoint` genom att ange egenskaperna och i *filen function.json.* _Funktionen.json_ nedan talar till exempel om för `customentry()` körningen att använda metoden i _main.py_ filen, som startpunkt för din Azure-funktion.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mappstrukturen

Den rekommenderade mappstrukturen för ett Python Functions-projekt ser ut som följande exempel:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 tests
```
Huvudprojektmappen\_\_(\_\_appen ) kan innehålla följande filer:

* *local.settings.json*: Används för att lagra appinställningar och anslutningssträngar när du kör lokalt. Den här filen publiceras inte i Azure. Mer information finns i [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: Innehåller en lista över paket som systemet installerar när det publiceras till Azure.
* *host.json*: Innehåller globala konfigurationsalternativ som påverkar alla funktioner i en funktionsapp. Den här filen publiceras till Azure. Alla alternativ stöds inte när du kör lokalt. Mer information finns i [host.json](functions-host-json.md).
* *.funcignore*: (Valfritt) deklarerar filer som inte ska publiceras i Azure.
* *.gitignore*: (Valfritt) deklarerar filer som är undantagna från en git-repo, till exempel local.settings.json.

Varje funktion har sin egen kodfil och bindning konfigurationsfil (function.json). 

När du distribuerar projektet till en funktionsapp i Azure bör hela innehållet i huvudprojektets*\_\_(app)\_* mappen inkluderas i paketet, men inte själva mappen. Vi rekommenderar att du underhåller dina tester i en `tests`mapp separat från projektmappen i det här exemplet . Detta hindrar dig från att distribuera testkod med din app. Mer information finns i [Enhetstestning](#unit-testing).

## <a name="import-behavior"></a>Importbeteende

Du kan importera moduler i funktionskoden med både explicita relativa och absoluta referenser. Baserat på mappstrukturen som visas ovan fungerar följande importer inifrån * \_ \_funktionsfilappen\_\_\_\min\_första funktion\\_\_init\_\_.py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

Följande importer *fungerar inte* inifrån samma fil:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Delad kod ska förvaras i * \_ \_en\_* separat mapp i appen . Om du vill referera till moduler i den *\_delade kodmappen* kan du använda följande syntax:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Utlösare och indata

Indata är indelade i två kategorier i Azure Functions: utlösa indata och ytterligare indata. Även om de `function.json` är olika i filen är användningen identisk i Python-kod.  Anslutningssträngar eller hemligheter för utlösare och `local.settings.json` indatakällor mappas till värden i filen när de körs lokalt och programinställningarna när de körs i Azure. 

Följande kod visar till exempel skillnaden mellan de två:

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

När funktionen anropas skickas HTTP-begäran till funktionen `req`som . En post hämtas från Azure Blob Storage baserat på _ID_ i `obj` flödes-URL:en och görs tillgänglig som i funktionstexten.  Här är det angivna lagringskontot den anslutningssträng som finns i appinställningen AzureWebJobsStorage, vilket är samma lagringskonto som används av funktionsappen.


## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i returvärde och utdataparametrar. Om det bara finns en utdata rekommenderar vi att du använder returvärdet. För flera utdata måste du använda utdataparametrar.

Om du vill använda en funktions returvärde som `name` värdet för en utdatabindning ska egenskapen för bindningen anges till `$return` i `function.json`.

Om du vill producera `set()` flera utdata [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) använder du den metod som tillhandahålls av gränssnittet för att tilldela bindningen ett värde. Följande funktion kan till exempel skicka ett meddelande till en kö och även returnera ett HTTP-svar.

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

Åtkomst till Azure Functions runtime logger [`logging`](https://docs.python.org/3/library/logging.html#module-logging) är tillgänglig via en rothanterare i din funktionsapp. Den här loggern är knuten till Application Insights och låter dig flagga varningar och fel som påträffats under funktionskörningen.

I följande exempel loggas ett informationsmeddelande när funktionen anropas via en HTTP-utlösare.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Det finns ytterligare loggningsmetoder som gör att du kan skriva till konsolen på olika spårningsnivåer:

| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Skriver ett meddelande med nivå KRITISK på rotloggern.  |
| **`error(_message_)`**   | Skriver ett meddelande med nivåfel på rotloggern.    |
| **`warning(_message_)`**    | Skriver ett meddelande med nivå VARNING på rotloggern.  |
| **`info(_message_)`**    | Skriver ett meddelande med nivå INFO på rotloggern.  |
| **`debug(_message_)`** | Skriver ett meddelande med nivå DEBUG på rotloggern.  |

Mer information om loggning finns i [Övervaka Azure-funktioner](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP-utlösare och bindningar

HTTP-utlösaren definieras i filen function.jon. Bindningen `name` måste matcha den namngivna parametern i funktionen. I de föregående exemplen `req` används ett bindningsnamn. Den här parametern är ett [HttpRequest-objekt] och ett [HttpResponse-objekt] returneras.

Från [HttpRequest-objektet] kan du hämta begäranden, frågeparametrar, vägparametrar och meddelandetexten. 

Följande exempel kommer från [HTTP-utlösarmallen för Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

I den här funktionen `name` hämtas värdet för `params` frågeparametern från parametern [för HttpRequest-objektet.] Den JSON-kodade meddelandetexten `get_json` läses med metoden. 

På samma sätt `status_code` kan `headers` du ställa in och för svarsmeddelandet i det returnerade [HttpResponse-objektet.]

## <a name="scaling-and-concurrency"></a>Skalning och samtidighet

Som standard övervakar Azure Functions automatiskt belastningen på ditt program och skapar ytterligare värdinstanser för Python efter behov. Funktioner använder inbyggda (inte användarkonfigurerbara) tröskelvärden för olika utlösartyper för att bestämma när instanser ska läggas till, till exempel meddelandeålder och köstorlek för QueueTrigger. Mer information finns i [Så här fungerar förbruknings- och Premium-abonnemangen](functions-scale.md#how-the-consumption-and-premium-plans-work).

Det här skalningsbeteendet är tillräckligt för många program. Tillämpningar med någon av följande egenskaper får dock inte skalas lika effektivt:

- Programmet måste hantera många samtidiga anrop.
- Programmet bearbetar ett stort antal I/O-händelser.
- Programmet är I/O-bundet.

I sådana fall kan du förbättra prestanda ytterligare genom att använda asynkronmönster och använda flera språkarbetarprocesser.

### <a name="async"></a>Asynkrona

Eftersom Python är en runtime med en tråd kan en värdinstans för Python bara bearbeta en funktionsyrop i taget. För program som bearbetar ett stort antal I/O-händelser och/eller är I/O-bundna kan du förbättra prestanda genom att köra funktioner asynkront.

Om du vill köra en funktion asynkront använder du uttrycket, `async def` som kör funktionen med [asyncio](https://docs.python.org/3/library/asyncio.html) direkt:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

En funktion `async` utan nyckelordet körs automatiskt i en asyncio trådpool:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Använda flera språkarbetsprocesser

Som standard har varje värdinstans för Funktioner en enda språkarbetsprocess. Du kan öka antalet arbetsprocesser per värd (upp till 10) med hjälp av [programinställningen för FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) Azure Functions försöker sedan distribuera samtidiga funktionsarop mellan dessa arbetare. 

Den FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som Functions skapar när du skalar ut ditt program för att möta efterfrågan. 

## <a name="context"></a>Kontext

Om du vill hämta åkaropskontexten [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) för en funktion under körningen tar du med argumentet i dess signatur. 

Ett exempel:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Klassen [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) har följande strängattribut:

`function_directory`  
Katalogen där funktionen körs.

`function_name`  
Namnet på funktionen.

`invocation_id`  
ID för den aktuella funktionstallelsen.

## <a name="global-variables"></a>Globala variabler

Det är inte garanterat att tillståndet för din app kommer att bevaras för framtida körningar. Azure Functions-körningen återanvänder dock ofta samma process för flera körningar av samma app. För att cachelagra resultaten av en dyr beräkning, deklarera det som en global variabel. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Miljövariabler

I Funktioner visas [programinställningar](functions-app-settings.md), till exempel tjänstanslutningssträngar, som miljövariabler under körningen. Du kan komma åt `import os` dessa inställningar `setting = os.environ["setting-name"]`genom att deklarera och sedan använda .

I följande exempel får [programinställningen](functions-how-to-use-azure-function-app-settings.md#settings) `myAppSetting`med nyckeln :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

För lokal utveckling underhålls programinställningarna [i filen local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Python-version 

Azure Functions stöder följande Python-versioner:

| Version av funktioner | Python-versioner<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Officiella CPython-distributioner

Om du vill begära en specifik Python-version när `--runtime-version` du [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) skapar din funktionsapp i Azure använder du alternativet kommandot. Den funktionskörningsversion är `--functions-version` inställd med alternativet. Python-versionen ställs in när funktionsappen skapas och kan inte ändras.  

När du kör lokalt använder körningen den tillgängliga Python-versionen. 

## <a name="package-management"></a>Pakethantering

När du utvecklar lokalt med hjälp av Azure Functions Core Tools eller Visual Studio `requirements.txt` Code lägger `pip`du till namn och versioner av de paket som krävs i filen och installerar dem med . 

Följande krav fil- och pip-kommando kan till `requests` exempel användas för att installera paketet från PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicera i Azure

När du är redo att publicera kontrollerar du att alla dina allmänt tillgängliga beroenden visas i filen requirements.txt, som finns i roten till projektkatalogen. 

Projektfiler och mappar som inte är undantagna från publicering, inklusive den virtuella miljömappen, visas i .funcignore-filen.

Det finns tre byggåtgärder som stöds för att publicera ditt Python-projekt i Azure:

+ Fjärrversion: Beroenden hämtas på distans baserat på innehållet i filen requirements.txt. [Fjärrbygge](functions-deployment-technologies.md#remote-build) är den rekommenderade byggmetoden. Remote är också standardbyggalternativet för Azure-verktyg. 
+ Lokal version: Beroenden hämtas lokalt baserat på innehållet i filen requirements.txt. 
+ Anpassade beroenden: Ditt projekt använder paket som inte är allmänt tillgängliga för våra verktyg. (Kräver Docker.)

Om du vill skapa dina beroenden och publicera med hjälp av ett CD-system (continuous delivery) [använder du Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Fjärrbygge

Som standard begär Azure Functions Core Tools en fjärrversion när du använder följande [func azure functionapp-publiceringskommando](functions-run-local.md#publish) för att publicera ditt Python-projekt till Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Kom ihåg `<APP_NAME>` att ersätta med namnet på din funktionsapp i Azure.

[Azure Functions Extension för Visual Studio-kod](functions-create-first-function-vs-code.md#publish-the-project-to-azure) begär också en fjärrversion som standard. 

### <a name="local-build"></a>Lokalt bygge

Du kan förhindra att du gör en fjärrversion med hjälp av följande [func azure functionapp publicera](functions-run-local.md#publish) kommando för att publicera med en lokal version. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Kom ihåg `<APP_NAME>` att ersätta med namnet på din funktionsapp i Azure. 

Med `--build local` alternativet läss projektberoendet från filen requirements.txt och de beroende paketen hämtas och installeras lokalt. Projektfiler och beroenden distribueras från den lokala datorn till Azure. Detta resulterar i att ett större distributionspaket överförs till Azure. Om beroenden i filen requirements.txt av någon anledning inte kan förvärvas av Core Tools måste du använda alternativet anpassade beroenden för publicering. 

### <a name="custom-dependencies"></a>Anpassade beroenden

Om ditt projekt använder paket som inte är allmänt tillgängliga för våra verktyg \_ \_kan\_\_du göra dem tillgängliga för din app genom att placera dem i appen /.python_packages-katalogen. Innan du publicerar kör du följande kommando för att installera beroenden lokalt:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

När du använder anpassade beroenden `--no-build` bör du använda publiceringsalternativet eftersom du redan har installerat beroendena.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Kom ihåg `<APP_NAME>` att ersätta med namnet på din funktionsapp i Azure.

## <a name="unit-testing"></a>Enhetstestning

Funktioner skrivna i Python kan testas som andra Python-kod med hjälp av standardtestramverk. För de flesta bindningar är det möjligt att skapa ett utkast indataobjekt genom att skapa en instans av en lämplig klass från `azure.functions` paketet. Eftersom [`azure.functions`](https://pypi.org/project/azure-functions/) paketet inte är omedelbart tillgängligt, se `requirements.txt` till att installera det via filen enligt beskrivningen i [pakethanteringsavsnittet](#package-management) ovan. 

Följande följer ett modelltest av en HTTP-utlöst funktion:

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

Här är ett annat exempel, med en kö utlöst funktion:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>Tillfälliga filer

Metoden `tempfile.gettempdir()` returnerar en tillfällig mapp, `/tmp`som på Linux är . Ditt program kan använda den här katalogen för att lagra temporära filer som genereras och används av dina funktioner under körningen. 

> [!IMPORTANT]
> Filer som skrivits till den tillfälliga katalogen är inte garanterade att finnas kvar över anrop. Under utskalning delas inte temporära filer mellan instanser. 

I följande exempel skapas en namngiven`/tmp`temporär fil i den tillfälliga katalogen ( ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Vi rekommenderar att du underhåller dina tester i en mapp som är separat från projektmappen. Detta hindrar dig från att distribuera testkod med din app. 

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor

Alla kända problem och funktionsbegäranden spåras med hjälp av [GitHub-ärendelistan.](https://github.com/Azure/azure-functions-python-worker/issues) Om du stöter på ett problem och inte kan hitta problemet i GitHub öppnar du ett nytt problem och innehåller en detaljerad beskrivning av problemet.

### <a name="cross-origin-resource-sharing"></a>Cross-origin resource sharing (CORS)

Azure Functions stöder resursdelning över ursprung (CORS). CORS konfigureras [i portalen](functions-how-to-use-azure-function-app-settings.md#cors) och via [Azure CLI](/cli/azure/functionapp/cors). Listan med tillåtna ursprung på CORS-listan gäller på funktionsappnivå. Med CORS aktiverat inkluderar `Access-Control-Allow-Origin` svaren huvudet. Mer information finns i [Cross-origin resource sharing](functions-how-to-use-azure-function-app-settings.md#cors).

Listan över tillåtna ursprung [stöds för närvarande inte](https://github.com/Azure/azure-functions-python-worker/issues/444) för Python-funktionsappar. På grund av den här begränsningen `Access-Control-Allow-Origin` måste du uttryckligen ange huvudet i HTTP-funktionerna, vilket visas i följande exempel:

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

Kontrollera att du även uppdaterar funktionen.json för att stödja HTTP-metoden OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Den här HTTP-metoden används av webbläsare för att förhandla om listan över tillåtna ursprung. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [API-dokumentation för Azure Functions-paketet](/python/api/azure-functions/azure.functions?view=azure-python)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Blob lagring bindningar](functions-bindings-storage-blob.md)
* [HTTP- och Webhook-bindningar](functions-bindings-http-webhook.md)
* [Kölagringsbindningar](functions-bindings-storage-queue.md)
* [Timerutlösare](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
