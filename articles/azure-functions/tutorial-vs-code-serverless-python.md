---
title: Skapa och Distribuera Azure Functions i python med Visual Studio Code
description: Använda Visual Studio Code-tillägget för Azure Functions för att skapa server lös funktioner i python och distribuera dem till Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 587d8040039080e15c61292279c5e5637d296b08
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68351342"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Distribuera python till Azure Functions med Visual Studio Code

I den här självstudien använder du Visual Studio Code och Azure Functions-tillägget för att skapa en server lös HTTP-slutpunkt med python och för att även lägga till en anslutning (eller "bindning") till lagringen. Azure Functions kör koden i en miljö utan server utan att behöva etablera en virtuell dator eller publicera en webbapp. Azure Functions-tillägget för Visual Studio Code fören klar processen att använda funktioner automatiskt genom att automatiskt hantera många konfigurations problem.

Om du stöter på problem med något av stegen i den här självstudien ska vi gärna höra om detaljerna. Använd knappen **jag körde i ett ärende** i slutet av varje avsnitt för att skicka detaljerad feedback.

## <a name="prerequisites"></a>Förutsättningar

- En [Azure-prenumeration](#azure-subscription).
- [Visual Studio Code med Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension) -tillägget.
- [Azure Functions Core tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Azure-prenumeration

Om du inte har en Azure-prenumeration kan du [Registrera dig nu](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) för ett kostnads fritt 30-dagars konto med $200 i Azure-krediter för att testa alla typer av tjänster.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, python och Azure Functions-tillägget

Installera följande program vara:

- Python 3.6. x som krävs av Azure Functions. [Python-3.6.8](https://www.python.org/downloads/release/python-368/) är den senaste versionen 3.6. x.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) som beskrivs i [själv studie kursen om Visual Studio Code python-krav](https://code.visualstudio.com/docs/python/python-tutorial).
- [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Allmän information finns på [VSCode-Azurefunctions GitHub-lagringsplatsen](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Följ instruktionerna för ditt operativ system på [arbetet med Azure Functions Core tools](functions-run-local.md#v2). Själva verktygen är skrivna i .NET Core och Core Tools-paketet är bäst installerat med hjälp av Package Manager i Node. js, NPM, vilket är anledningen till att du måste installera .NET Core och Node. js, även för python-kod. Du kan dock kringgå kraven för .NET Core med "tilläggs paket" enligt beskrivningen i den tidigare dokumentationen. Oavsett vilket är fallet behöver du bara installera dessa komponenter en gång, efter vilken Visual Studio Code automatiskt efterfrågar att du installerar eventuella uppdateringar.

### <a name="sign-in-to-azure"></a>Logga in på Azure

När Functions-tillägget har installerats loggar du in på ditt Azure-konto genom att **gå till Azure: Funktions** Utforskaren, Välj **Logga in på Azure**och följ anvisningarna.

![Logga in på Azure via Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

När du har loggat in kontrollerar du att e-postkontot i din Azure-prenumeration visas i statusfältet:

![Statusfältet i Visual Studio Code visar Azure-konto](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Namnet som du har tilldelat din prenumeration visas också i **Azure: Funktions** Utforskaren ("primär" på bilden nedan):

![Visual Studio Code Azure App Service Explorer som visar prenumerationer](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Om du stöter på fel meddelandet **"det går inte att hitta prenumerationen med namnet [prenumerations-ID]"** kan det bero på att du ligger bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` och`HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Kontrollera förutsättningar

Kontrol lera att alla Azure Functions-verktyg har installerats genom att öppna Visual Studio Code Command-paletten (F1), **Välj terminalen: Skapa ett nytt integrerat** Terminal-kommando och när terminalen öppnas kör du kommandot `func`:

![Kontrollerar Azure Functions Core tools-krav](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Utdata som börjar med Azure Functions-logotypen (du måste rulla utdata uppåt) anger att Azure Functions Core Tools finns.

`func` Om kommandot inte är känt kontrollerar du att mappen där du installerade Azure Functions Core tools ingår i miljövariabeln PATH.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>Skapa funktionen

1. Kod för Azure Functions hanteras i ett Functions- _projekt_som du skapar först innan du skapar koden. I **Azure: Function** Explorer (öppnas med Azure-ikonen till vänster), Välj kommando ikonen **nytt projekt** eller öppna Command-paletten (F1) och välj **Azure Functions: Skapa nytt projekt**.

    ![Knappen Skapa nytt projekt i funktions Utforskaren](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. I prompterna som följer:

    | Uppmaning | Value | Beskrivning | 
    | --- | --- | --- |
    | Ange en mapp för projektet | Aktuell öppen mapp | Den mapp där projektet ska skapas. Du kanske vill skapa projektet i en undermapp. |
    | Välj ett språk för ditt projekt för Function-appen | **Python** | Språket som ska användas för funktionen, vilket avgör vilken mall som används för koden. |
    | Välj en mall för projektets första funktion | **HTTP-utlösare** | En funktion som använder en HTTP-Utlösare körs när en HTTP-förfrågan har gjorts till funktionens slut punkt. (Det finns flera andra utlösare för Azure Functions. Mer information finns i [vad kan jag göra med Functions?](functions-overview.md#what-can-i-do-with-functions).) |
    | Ange ett funktions namn | HttpExample | Namnet används för en undermapp som innehåller funktionens kod tillsammans med konfigurations data och som även definierar namnet på HTTP-slutpunkten. Använd "HttpExample" i stället för att acceptera standardvärdet "HTTPTrigger" för att särskilja funktionen från utlösaren. |
    | Auktorisationsnivå | **Antal** | Anonym auktorisering gör funktionen offentligt tillgänglig för alla. |
    | Välj hur du vill öppna projektet | **Öppna i aktuellt fönster** | Öppnar projektet i det aktuella Visual Studio Code-fönstret. |

1. Efter en kort stund visas ett meddelande som anger att det nya projektet har skapats. I **Utforskaren**finns undermappen som skapas för funktionen och Visual Studio Code öppnar  *\_ \_filen\_init\_. py* som innehåller standard funktions koden:

    [![Resultat av att skapa ett nytt python Functions-projekt](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Om Visual Studio Code visar att du inte har en python-tolk markerad när  *\_ \_init\_\_.* **py öppnas, öppnar du kommando-paletten (F1) och väljer python: Välj tolks** kommando och välj sedan den virtuella miljön i den lokala `.env` mappen (som skapades som en del av projektet). Miljön måste vara baserad på python 3.6 x särskilt, som tidigare nämnts under [krav](#prerequisites).
    >
    > ![Välja den virtuella miljön som skapats med projektet](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> När du vill skapa en annan funktion i samma projekt använder du kommandot **skapa funktion** i **Azure: Funktions** Utforskaren eller öppna kommando fältet (F1) och **Välj Azure Functions: Create Function** (Skapa funktion). Båda kommandona efterfrågar ett funktions namn (som är namnet på slut punkten) och skapar sedan en undermapp med standardfilerna.
>
> ![Nytt funktions kommando i Azure: Funktions Utforskaren](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>Undersök filerna i koden

I undermappen för den nyligen skapade funktionen finns tre filer  *\_:\_ \_\_init. py* innehåller funktionens kod, *Function. JSON* beskriver funktionen för att Azure Functions och *Sample. dat* är en exempel data fil. Du kan ta bort *Sample. dat* om du vill, eftersom det bara finns för att visa att du kan lägga till andra filer i undermappen.

Nu ska vi titta på *Function. JSON* först och sedan koden i  *\_ \_init\_\_. py*.

### <a name="functionjson"></a>function.json

Filen function. JSON innehåller nödvändig konfigurations information för Azure Functions slut punkten:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
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

Egenskapen identifierar start filen för koden och koden måste innehålla en python-funktion med namnet `main`. `scriptFile` Du kan faktor din kod i flera filer så länge filen som anges här innehåller en `main` funktion.

`bindings` Elementet innehåller två objekt, ett för att beskriva inkommande begär Anden och det andra för att beskriva http-svaret. För inkommande begär Anden`"direction": "in"`() svarar funktionen på http Get-eller post-begär Anden och kräver inte autentisering. Response (`"direction": "out"`) är ett HTTP-svar som returnerar det värde som returneras `main` från python-funktionen.

### <a name="initpy"></a>\_\_init.py\_\_

När du skapar en ny funktion tillhandahåller Azure Functions standard python-kod i  *\_ \_init\_\_. py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
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
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

De viktiga delarna i koden är följande:

- Du måste importera `func` från `azure.functions`. om du vill importera loggningsmodulen är det valfritt men rekommenderas.
- Den önskade `main` python-funktionen tar `func.request` emot ett `req`objekt med namnet och returnerar ett värde `func.HttpResponse`av typen. Du kan lära dig mer om funktionerna för dessa objekt i [funcet. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) och [Func. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) -referenser.
- Texten i `main` bearbetar sedan begäran och genererar ett svar. I det här fallet söker koden efter en parameter `name` i URL: en. Det går inte att kontrol lera om begär ande texten innehåller JSON (med `func.HttpRequest.get_json`) och att JSON innehåller ett `name` värde (med hjälp `get` av metoden för JSON-objektet som returnerades av `get_json`).
- Om ett namn hittas, returnerar koden strängen "Hello" med namnet tillagt; annars returnerar den ett fel meddelande.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Felsök lokalt

1. När du skapar Functions-projektet skapar Visual Studio Code-tillägget även en start konfiguration i `.vscode/launch.json` som innehåller en enda konfiguration med namnet **Attach to python Functions**. Den här konfigurationen innebär att du bara kan trycka på F5 eller använda fel söknings Utforskaren för att starta projektet:

    ![Fel söknings Utforskaren visar konfiguration av Functions-start](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. När du startar fel söknings programmet öppnas en Terminal som visar utdata från Azure Functions, inklusive en sammanfattning av tillgängliga slut punkter. URL: en kan vara en annan om du använde ett annat namn än "HttpExample":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Använd **CTRL + klicka** eller **cmd + Klicka** på URL: en i Visual Studio Code **output** -fönstret för att öppna en webbläsare till den adressen eller starta en webbläsare och klistra in den i samma URL. I båda fallen är `api/<function_name>`slut punkten i det här fallet. `api/HttpExample` Men eftersom URL: en inte innehåller någon namn parameter, ska webbläsarfönstret bara visa, "Skicka ett namn på frågesträngen eller i begär ande texten" efter vad som är lämpligt för den sökvägen i koden.

1. Nu kan du försöka lägga till en namn parameter för användning, `http://localhost:7071/api/HttpExample?name=VS%20Code`till exempel och webbläsarfönstret ska visa meddelandet, "Hej Visual Studio Code!", som visar att du har kört kod Sök vägen.

1. Om du vill skicka name-värdet i en JSON-begäran, kan du använda ett verktyg som spiral med JSON-infogat:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Du kan också skapa en fil som *data. JSON* som innehåller `{"name":"Visual Studio Code"}` och använder kommandot. `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`

1. Testa att felsöka funktionen genom att ange en Bryt punkt på raden som läser `name = req.params.get('name')` och gör en begäran till URL: en igen. Visual Studio Code-felsökaren bör stoppas på den raden, så att du kan granska variabler och gå igenom koden. (En kort genom gång av grundläggande fel sökning finns i [Visual Studio Code-självstudie – konfigurera och kör fel söknings programmet](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).)

1. När du är nöjd med att du har testat funktionen lokalt, stoppar du fel söknings verktyget (med kommandona **Felsök** > ,**stoppa fel sökning** eller kommandot **Koppla från** i verktygsfältet fel sökning).

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Distribuera till Azure Functions

I de här stegen använder du tillägget Functions för att skapa en Function-app i Azure, tillsammans med andra nödvändiga Azure-resurser. I en funktionsapp kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Det kräver också ett Azure Storage-konto för data och en [värd plan](functions-scale.md#hosting-plan-support). Alla dessa resurser är ordnade i en enda resurs grupp.

1. **I Azure: Funktions** Utforskaren, Välj kommandot **distribuera till Funktionsapp** eller öppna Command-paletten (F1 **) och välj Azure Functions: Distribuera till Funktionsapp** -kommando. Återigen är Function-appen där ditt python-projekt körs i Azure.

    ![Distribuera till Funktionsapp kommando](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. När du uppmanas väljer du **Skapa ny Funktionsapp i Azure**och anger ett namn som är unikt för Azure (vanligt vis med hjälp av ditt personliga eller företagets namn tillsammans med andra unika identifierare. du kan använda bokstäver, siffror och bindestreck). Om du skapade en Funktionsapp tidigare visas namnet i listan med alternativ.

1. Tillägget utför följande åtgärder, som du kan studera i Visual Studio Code-popup-meddelanden och **utdatafönstret** (processen tar några minuter):

    - Skapa en resurs grupp med det namn du angav (tar bort bindestreck).
    - I den resurs gruppen skapar du lagrings kontot, värd planen och Function-appen. Som standard skapas en [förbruknings plan](functions-scale.md#consumption-plan) . Om du vill köra dina funktioner i en dedikerad plan måste du [aktivera publicering med avancerade alternativ](functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options)för att skapa.
    - Distribuera din kod till Function-appen.

    **Azure: Funktions** Utforskaren visar också förloppet:

    ![Distributions förlopps indikator i Azure: Funktions Utforskaren](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. När distributionen är klar visar Azure Functions-tillägget ett meddelande med knappar för tre ytterligare åtgärder:

    ![Meddelande som anger att distributionen har slutförts med ytterligare åtgärder](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Information om **strömnings loggar** och **överförings inställningar**finns i nästa avsnitt. För att **Visa utdata**, se steg 5 nedan.

1. Efter distributionen visar fönstret **utdata** även den offentliga slut punkten på Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Använd den här slut punkten för att köra samma tester som du har gjort lokalt, med URL-parametrar och/eller begär Anden med JSON-data i begär ande texten. Resultatet av den offentliga slut punkten ska matcha resultatet från den lokala slut punkten som du testade tidigare.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Strömningsloggar

Stöd för logg strömning är för närvarande under utveckling, enligt beskrivningen i [utfärdar 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) för Azure Functions tillägget. Knappen **Stream-loggar** i popup-meddelandet för distributions meddelandet kommer slutligen att ansluta loggens utdata från Azure till Visual Studio Code. Du kommer också att kunna starta och stoppa logg strömmen i **Azure Functions** Explorer genom att högerklicka på Functions-projektet och välja **starta direkt uppspelnings loggar** eller **stoppa strömnings loggar**.

Men de här kommandona fungerar inte ännu. Logg strömning är i stället tillgängligt i en webbläsare genom att köra följande kommando och `<app_name>` ersätta med namnet på din functions-app i Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Synkronisera lokala inställningar till Azure

Knappen **överför inställningar** i popup-meddelandet för distributions meddelandet använder alla ändringar som du har gjort i din *lokala. Settings. JSON* -fil till Azure. Du kan också anropa kommandot i **Azure Functions** Explorer genom att expandera noden funktioner, högerklicka på **program inställningar**och välja **Ladda upp lokala inställningar...** . Du kan också använda kommando-paletten för att **välja Azure Functions: Kommandot ladda upp** lokala inställningar.

Genom att ladda upp inställningar uppdaterar du alla befintliga inställningar och lägger till nya inställningar som definierats i *Local. Settings. JSON*. När du laddar upp tas inte alla inställningar från Azure som inte listas i den lokala filen bort. Om du vill ta bort de här inställningarna expanderar du noden **program inställningar** i **Azure Functions** Explorer, högerklickar på inställningen och väljer **ta bort inställning...** . Du kan också redigera inställningarna direkt på Azure Portal.

Om du vill tillämpa ändringar som du gör via portalen eller via **Azure Explorer** till den *lokala. Settings. JSON* -filen högerklickar du på noden **program inställningar** och väljer kommandot **Ladda ned Fjärrinställningar...** . Du kan också använda kommando-paletten för att **välja Azure Functions: Hämta Fjärrinställningar** , kommando.

## <a name="add-a-second-function"></a>Lägg till en andra funktion

Efter din första distribution kan du göra ändringar i koden, till exempel lägga till ytterligare funktioner och distribuera om till samma Functions-app.

1. **I Azure: Funktions** Utforskaren, Välj kommandot **skapa funktion** eller Använd **Azure Functions: Skapa funktion** från kommando paletten. Ange följande information för funktionen:

    - Mall: HTTP-utlösare
    - Namn: "DigitsOfPi"
    - Autentiseringsnivå: Anonymt

1. I Visual Studio Code-Utforskaren är en undermapp med funktions namnet som igen innehåller filer med namnet  *\_ \_\_init\_. py*, *Function. JSON*och *Sample. dat*.

1. Ersätt innehållet i  *\_ \_init.pyför\_att matcha följande kod, som genererar en sträng som innehåller värdet Pi till ett antal siffror som anges i URL (den här koden använder bara en URL-parameter)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Eftersom koden endast stöder HTTP Get, ändra *Function. JSON* så att `"methods"` samlingen endast `"get"` innehåller (det vill säga ta bort `"post"`). Hela filen bör se ut så här:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
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

1. Starta fel söknings programmet genom att trycka på F5 eller Välj Meny kommandot **fel sökning** > **Starta fel sökning** . I fönstret **utdata** visas nu båda slut punkterna i projektet:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. I en webbläsare, eller från en sväng, gör du en `http://localhost:7071/api/DigitsOfPi?digits=125` begäran till och ser resultatet. (Du kanske märker att kodnavigatören inte är helt korrekt, men vi lämnar förbättringarna för dig!) Stoppa fel sökningen när du är klar.

1. Distribuera om koden med hjälp av **distribuera till Funktionsapp** i **Azure: Functions** Explorer. Om du uppmanas väljer du den Funktionsapp som du skapade tidigare.

1. När distributionen är klar (det tar några minuter!) visar fönstret **utdata** de offentliga slut punkter som du kan använda för att upprepa dina tester.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Lägg till en bindning för att skriva meddelanden till Azure Storage

Med en _bindning_ kan du ansluta funktions koden till resurser, till exempel Azure Storage, utan att skriva någon kod för data åtkomst. En bindning definieras i *Function. JSON* -filen och kan representera både indata och utdata. En funktion kan använda flera in-och utdata-bindningar, men endast en utlösare. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

I det här avsnittet lägger du till en lagrings bindning till HttpExample-funktionen som skapades tidigare i den här självstudien. Funktionen använder denna bindning för att skriva meddelanden till lagrings utrymmet med varje begäran. Lagringen i fråga använder samma standard lagrings konto som används av Function-appen. Om du planerar att använda lagrings utrymme bör du dock överväga att skapa ett separat konto.

1. Synkronisera fjärrinställningarna för ditt Azure Functions-projekt i din *lokala. Settings. JSON* -fil genom att öppna kommando **-paletten och välja Azure Functions: Hämta Fjärrinställningar**. Öppna *Local. Settings. JSON* och kontrol lera att det innehåller ett `AzureWebJobsStorage`värde för. Värdet är anslutnings strängen för lagrings kontot.

1. Högerklicka på *Function. JSON*i mappen,VäljLäggtillbindning:`HttpExample`

    ![Lägg till bindnings kommando i Visual Studio Code Explorer](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. I prompterna som följer i Visual Studio Code väljer eller anger du följande värden:

    | Uppmaning | Värde att ange |
    | --- | --- |
    | Ange bindnings riktning | genomför |
    | Välj bindning med riktning ut | Azure Queue Storage |
    | Namnet som identifierar den här bindningen i koden | msg |
    | Kön som meddelandet ska skickas till | utkö |
    | Välj inställningen från *Local. Settings. JSON* (fråga efter lagrings anslutningen) | AzureWebJobsStorage |

1. När du har gjort dessa val kontrollerar du att följande bindning har lagts till i din *Function. JSON* -fil:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Nu när du har konfigurerat bindningen kan du använda den i funktions koden. Återigen visas den nyligen definierade bindningen i koden som ett argument `main` för funktionen i  *\_ \_\_init\_. py*. Du kan till exempel `msg` ändra  *\_ \_filen\_init\_. py* i HttpExample så att den matchar följande, som visar hur du använder argumentet för att skriva ett tidsstämplat meddelande med namnet som används i anmoda. Kommentarerna förklarar de olika ändringarna:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
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
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Om du vill testa ändringarna lokalt startar du fel söknings programmet igen i Visual Studio Code genom att trycka på F5 eller välja kommandot **fel sökning** > **Starta** fel söknings meny. Som innan fönstret **utdata** ska Visa slut punkterna i projektet.

1. I en webbläsare går du till URL `http://localhost:7071/api/HttpExample?name=VS%20Code` : en för att skapa en begäran till HttpExample-slutpunkten, som även ska skriva ett meddelande till kön.

1. För att verifiera att meddelandet skrevs till kön "kön" (som namngetts i bindningen) kan du använda en av tre metoder:

    1. Logga in på [Azure Portal](https://portal.azure.com)och navigera till resurs gruppen som innehåller ditt Functions-projekt. I den resurs gruppen, lokalt och navigera till lagrings kontot för projektet, går du till **köer**. På sidan navigerar du till "kön", som visar alla loggade meddelanden.

    1. Navigera och granska kön med antingen Azure Storage Explorer, som integreras med Visual Studio, enligt beskrivningen i [Connect Functions för att Azure Storage med hjälp av Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md), i synnerhet avsnittet [Granska kön utdata](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Använd Azure CLI för att fråga lagrings kön, enligt beskrivningen i [fråga lagrings kön](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. Om du vill testa i molnet distribuerar du om koden med hjälp av **distribuera till Funktionsapp** i **Azure: Functions** Explorer. Om du uppmanas väljer du den Funktionsapp som du skapade tidigare. När distributionen är klar (det tar några minuter!) visas de offentliga slut punkter som du kan använda för att upprepa dina tester i fönstret **utdata** .

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Rensa resurser

Funktionsapp som du har skapat inkluderar resurser som kan medföra minimala kostnader (se prissättningen för [funktioner](https://azure.microsoft.com/pricing/details/functions/)). Om du vill rensa resurserna högerklickar du på Funktionsapp i **Azure: Funktions** Utforskaren och välj **ta bort Funktionsapp**. Du kan också besöka [Azure Portal](https://portal.azure.com), välja **resurs grupper** från det vänstra navigerings fönstret, välja resurs gruppen som skapades i processen för den här själv studie kursen och sedan använda kommandot **ta bort resurs grupp** .

## <a name="next-steps"></a>Nästa steg

Grattis till att slutföra den här genom gången av att distribuera python-kod till Azure Functions! Nu är du redo att skapa många fler funktioner utan server.

Som tidigare nämnts kan du lära dig mer om funktions tillägget genom att gå till dess GitHub [-lagringsplats, VSCode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Problem och bidrag är också välkommen.

Läs [Azure Functions översikt](functions-overview.md) för att utforska de olika utlösare som du kan använda.

Om du vill veta mer om Azure-tjänster som du kan använda från python, inklusive data lagring tillsammans med AI-och Machine Learning-tjänster, går du till [Azure python Developer Center](/azure/python/?view=azure-python).

Det finns även andra Azure-tillägg för Visual Studio-kod som du kan ha nytta av. Sök bara på "Azure" i tillägg Utforskaren:

![Azure-tillägg för Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Några populära tillägg är:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
