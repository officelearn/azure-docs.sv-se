---
title: Python Developer-referens för Azure Functions
description: Förstå hur du utvecklar funktioner med python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 6c625c050652ffac568ac45b06af7a853c75c8c2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358061"
---
# <a name="azure-functions-python-developer-guide"></a>Guide för Azure Functions python-utvecklare

Den här artikeln är en introduktion till att utveckla Azure Functions med python. Innehållet nedan förutsätter att du redan har läst [guiden Azure Functions utvecklare](functions-reference.md). 

Exempel projekt för fristående funktioner i python finns i [exempel på python-funktioner](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Programmeringsmodell

Azure Functions förväntar sig att en funktion är en tillstånds lös metod i python-skriptet som bearbetar indata och genererar utdata. Som standard förväntar sig körningen att metoden ska implementeras som en global metod som kallas `main()` i `__init__.py`s filen. Du kan också [Ange en alternativ Start punkt](#alternate-entry-point).

Data från utlösare och bindningar är kopplade till funktionen via attribut med hjälp av egenskapen `name` som definierats i *Function. JSON* -filen. _Funktionen. JSON_ nedan beskriver till exempel en enkel funktion som utlöses av en http-begäran med namnet `req`:

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Baserat på den här definitionen kan `__init__.py`-filen som innehåller funktions koden se ut som i följande exempel:

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

Du kan ändra standard beteendet för en funktion genom att alternativt ange `scriptFile` och `entryPoint` egenskaper i filen *Function. JSON* . _Funktionen. JSON_ nedan meddelar till exempel körningen att använda metoden `customentry()` i filen _main.py_ , som start punkt för din Azure-funktion.

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

Den rekommenderade mappstrukturen för ett python Functions-projekt ser ut som i följande exempel:

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
Huvudprojektmappen (\_\_app\_\_) kan innehålla följande filer:

* *Local. Settings. JSON*: används för att lagra appinställningar och anslutnings strängar när de körs lokalt. Den här filen publiceras inte i Azure. Mer information finns i [Local. Settings. File](functions-run-local.md#local-settings-file).
* *Requirements. txt*: innehåller listan över paket som systemet installerar vid publicering till Azure.
* *Host. JSON*: innehåller globala konfigurations alternativ som påverkar alla funktioner i en Function-app. Den här filen publiceras i Azure. Alla alternativ stöds inte när du kör lokalt. Läs mer i [Host. JSON](functions-host-json.md).
* *. funcignore*: (valfritt) deklarerar filer som inte ska publiceras i Azure.
* *. gitignore*: (valfritt) deklarerar filer som är exkluderade från en git-lagrings platsen, t. ex. local. Settings. JSON.

Varje funktion har sin egen kod fil och bindnings konfigurations fil (Function. JSON). 

När du distribuerar projektet till en Function-app i Azure ska hela innehållet i huvudprojektet ( *\_\_app\_\_* ) inkluderas i paketet, men inte själva mappen. Vi rekommenderar att du underhåller dina tester i en mapp separat från projektmappen, i det här exemplet `tests`. Detta gör att du inte distribuerar test koden med din app. Mer information finns i [enhets testning](#unit-testing).

## <a name="import-behavior"></a>Import beteende

Du kan importera moduler i funktions koden med både explicita relativa och absoluta referenser. Utifrån mappstrukturen som visas ovan fungerar följande importer inifrån Function-filen *\_\_app\_\_\mina\_första\_funktion\\_\_init\_\_. py*:

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

Följande importer *fungerar inte* från samma fil:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Delad kod ska lagras i en separat mapp i *\_\_app\_\_* . Om du vill referera till moduler i mappen *delade\_-kod* kan du använda följande syntax:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Utlösare och indata

Indata är indelade i två kategorier i Azure Functions: Utlös indata och ytterligare indata. Även om de skiljer sig i `function.json`-filen är användningen identiska i python-kod.  Anslutnings strängar eller hemligheter för utlösare och inmatade källor mappar till värden i `local.settings.json`-filen när de körs lokalt och program inställningarna körs i Azure. 

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

När funktionen anropas skickas HTTP-begäran till funktionen som `req`. En post hämtas från Azure-Blob Storage baserat på _ID: t_ i väg-URL: en och görs tillgänglig som `obj` i funktions texten.  Här är det angivna lagrings kontot den anslutnings sträng som hittades i AzureWebJobsStorage-appens inställning, som är samma lagrings konto som används av Function-appen.


## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i retur värde och utdataparametrar. Om det bara finns en utmatning rekommenderar vi att du använder det returnerade värdet. För flera utdata måste du använda utdataparametrar.

Om du vill använda returvärdet för en funktion som värde för en utgående bindning ska bindningens `name` egenskap anges till `$return` i `function.json`.

Om du vill skapa flera utdata använder du metoden `set()` som tillhandahålls av [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) -gränssnittet för att tilldela ett värde till bindningen. Följande funktion kan till exempel skicka ett meddelande till en kö och även returnera ett HTTP-svar.

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

Åtkomst till Azure Functions runtime-loggen är tillgänglig via en rot [`logging`](https://docs.python.org/3/library/logging.html#module-logging) hanterare i din Function-app. Den här loggen är kopplad till Application Insights och gör att du kan flagga varningar och fel som påträffas under funktions körningen.

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

HTTP-utlösaren definieras i filen function. Jon. Bindningens `name` måste matcha den namngivna parametern i funktionen. I föregående exempel används ett bindnings namn `req`. Den här parametern är ett [HttpRequest] -objekt och ett [HttpResponse] -objekt returneras.

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

I den här funktionen hämtas värdet för parametern `name` Query från parametern `params` i [HttpRequest] -objektet. Den JSON-kodade meddelande texten läses med hjälp av metoden `get_json`. 

På samma sätt kan du ange `status_code` och `headers` för svarsmeddelandet i det returnerade [HttpResponse] -objektet.

## <a name="scaling-and-concurrency"></a>Skalning och samtidighet

Som standard övervakar Azure Functions automatiskt belastningen på ditt program och skapar ytterligare värd instanser för python vid behov. Funktionerna använder inbyggda (inte användar konfigurerbara) tröskelvärden för olika utlösare för att bestämma när du ska lägga till instanser, till exempel ålder för meddelanden och kös Tor lek för QueueTrigger. Mer information finns i [så här fungerar förbruknings-och Premium planerna](functions-scale.md#how-the-consumption-and-premium-plans-work).

Det här skalnings beteendet räcker för många program. Program med någon av följande egenskaper kan dock inte skalas så effektivt:

- Programmet måste hantera många samtidiga anrop.
- Programmet bearbetar ett stort antal I/O-händelser.
- Programmet är I/O-bindning.

I sådana fall kan du förbättra prestanda ytterligare genom att använda asynkrona mönster och använda flera språk arbets processer.

### <a name="async"></a>Async

Eftersom python är en enkel tråds körning kan en värd instans för python endast bearbeta ett funktions anrop åt gången. För program som bearbetar ett stort antal I/O-händelser och/eller är I/O-bundit kan du förbättra prestanda genom att köra funktioner asynkront.

Om du vill köra en funktion asynkront använder du `async def`-instruktionen som kör funktionen med [asyncio](https://docs.python.org/3/library/asyncio.html) direkt:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

En funktion utan nyckelordet `async` körs automatiskt i en asyncio tråd-pool:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Använd flera språk arbets processer

Som standard har varje Functions Host-instans en enda språk arbets process. Du kan öka antalet arbets processer per värd (upp till 10) med hjälp av inställningen [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) program. Azure Functions försöker sedan jämnt distribuera samtidiga funktions anrop över dessa arbetare. 

FUNCTIONS_WORKER_PROCESS_COUNT gäller för varje värd som fungerar när du skalar ditt program för att möta efter frågan. 

## <a name="context"></a>Kontext

Om du vill hämta anrops kontexten för en funktion under körningen inkluderar du argumentet [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) i signaturen. 

Exempel:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Kontext**](/python/api/azure-functions/azure.functions.context?view=azure-python) klassen har följande String-attribut:

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

I funktioner visas [program inställningar](functions-app-settings.md), t. ex. tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här inställningarna genom att deklarera `import os` och sedan använda `setting = os.environ["setting-name"]`.

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

## <a name="python-version"></a>Python-version 

Azure Functions stöder för närvarande både python 3.6. x och 3.7. x (officiella CPython-distributioner). När den körs lokalt använder körningen den tillgängliga python-versionen. Om du vill begära en speciell python-version när du skapar din Function-app i Azure, använder du alternativet `--runtime-version` i kommandot [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) . Versions ändring är endast tillåten vid skapande av Funktionsapp.  

## <a name="package-management"></a>Pakethantering

När du utvecklar lokalt med Azure Functions Core Tools eller Visual Studio Code lägger du till namn och versioner för de nödvändiga paketen i `requirements.txt`-filen och installerar dem med hjälp av `pip`. 

Till exempel kan följande krav fil och pip-kommando användas för att installera `requests`-paketet från PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicera till Azure

När du är redo att publicera ser du till att alla dina offentligt tillgängliga beroenden visas i filen Requirements. txt, som finns i roten i projekt katalogen. 

Projektfiler och mappar som undantas från publicering, inklusive mappen för virtuella miljöer, visas i. funcignore-filen.

Det finns tre Bygg åtgärder som stöds för att publicera ditt python-projekt till Azure:

+ Fjärran sluten version: beroenden hämtas via fjärr anslutning baserat på innehållet i filen Requirements. txt. [Fjärran slutet](functions-deployment-technologies.md#remote-build) är den rekommenderade build-metoden. Remote är också standard alternativet build för Azure-verktyg. 
+ Lokal version: beroenden hämtas lokalt baserat på innehållet i filen Requirements. txt. 
+ Anpassade beroenden: ditt projekt använder paket som inte är offentligt tillgängliga för våra verktyg. (Kräver Docker.)

[Använd Azure-pipeline](functions-how-to-azure-devops.md)för att bygga dina beroenden och publicera med ett kontinuerligt leverans system (CD).

### <a name="remote-build"></a>Fjärrversion

Som standard begär Azure Functions Core Tools en fjärr anslutning när du använder följande [FUNC Azure functionapp Publish](functions-run-local.md#publish) -kommando för att publicera ditt python-projekt till Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Kom ihåg att ersätta `<APP_NAME>` med namnet på din Function-app i Azure.

[Azure Functions-tillägget för Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) begär också en fjärran sluten version som standard. 

### <a name="local-build"></a>Lokal version

Du kan förhindra en fjärran sluten version genom att använda följande [FUNC Azure functionapp Publish](functions-run-local.md#publish) -kommando för att publicera med en lokal version. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Kom ihåg att ersätta `<APP_NAME>` med namnet på din Function-app i Azure. 

Med alternativet `--build local` läses projekt beroenden från filen Requirements. txt och de beroende paketen hämtas och installeras lokalt. Projektfiler och beroenden distribueras från den lokala datorn till Azure. Detta innebär att ett större distributions paket överförs till Azure. Om det av någon anledning inte går att hämta beroenden i din Requirements. txt-fil med kärn verktyg, måste du använda alternativet anpassade beroenden för att publicera. 

### <a name="custom-dependencies"></a>Anpassade beroenden

Om ditt projekt använder paket som inte är offentligt tillgängliga för våra verktyg kan du göra dem tillgängliga för din app genom att placera dem i \_\_-appen\_\_/. python_packages Directory. Innan du publicerar kör du följande kommando för att installera beroendena lokalt:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

När du använder anpassade beroenden bör du använda alternativet för `--no-build` publicering, eftersom du redan har installerat beroendena.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Kom ihåg att ersätta `<APP_NAME>` med namnet på din Function-app i Azure.

## <a name="unit-testing"></a>Enhets testning

Funktioner som skrivs i python kan testas som annan python-kod med standard testnings ramverk. För de flesta bindningar är det möjligt att skapa ett objekt av en modell genom att skapa en instans av en lämplig klass från `azure.functions`-paketet. Eftersom [`azure.functions`](https://pypi.org/project/azure-functions/) -paketet inte är omedelbart tillgängligt, måste du installera det via din `requirements.txt`-fil enligt beskrivningen i avsnittet [paket hantering](#package-management) ovan. 

Följande är till exempel ett modell test av en HTTP-utlöst funktion:

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

Här är ett annat exempel med en funktion som utlöses av en kö:

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
## <a name="temporary-files"></a>Temporära filer

Metoden `tempfile.gettempdir()` returnerar en tillfällig mapp, som på Linux är `/tmp`. Ditt program kan använda den här katalogen för att lagra temporära filer som skapas och används av funktionerna under körningen. 

> [!IMPORTANT]
> Filer som skrivs till den temporära katalogen garanterar inte att de behålls i ett anrop. Temporära filer delas inte mellan instanser under utskalning. 

I följande exempel skapas en namngiven temporär fil i den tillfälliga katalogen (`/tmp`):

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

Vi rekommenderar att du underhåller dina tester i en mapp separat från projektmappen. Detta gör att du inte distribuerar test koden med din app. 

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

Alla kända problem och funktions begär Anden spåras med hjälp av listan med [GitHub-problem](https://github.com/Azure/azure-functions-python-worker/issues) . Om du stöter på ett problem och inte kan hitta problemet i GitHub, öppnar du ett nytt ärende och innehåller en detaljerad beskrivning av problemet.

### <a name="cross-origin-resource-sharing"></a>Cross-origin resource sharing (CORS)

Azure Functions stöder resurs delning mellan ursprung (CORS). CORS konfigureras [i portalen](functions-how-to-use-azure-function-app-settings.md#cors) och via [Azure CLI](/cli/azure/functionapp/cors). Listan över tillåtna ursprung för CORS-listan gäller på funktions nivån app. När CORS är aktiverat inkluderar Svaren `Access-Control-Allow-Origin`-huvudet. Mer information finns i [Cross-origin resource sharing](functions-how-to-use-azure-function-app-settings.md#cors).

Listan över tillåtna ursprung [stöds för närvarande inte](https://github.com/Azure/azure-functions-python-worker/issues/444) för python Function-appar. På grund av den här begränsningen måste du uttryckligen ange `Access-Control-Allow-Origin`s huvudet i dina HTTP-funktioner, som du ser i följande exempel:

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

Den här HTTP-metoden används av webbläsare för att förhandla listan över tillåtna ursprung. 

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
