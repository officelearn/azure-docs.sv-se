---
title: Lägg till en Azure Storage Queue-bindning till python-funktionen
description: Integrera en Azure Storage kö med en python-funktion med hjälp av en utgående bindning.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 6cea44dca666bbf002de6e2b7dd283f49ac7bd5a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485173"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Lägg till en Azure Storage Queue-bindning till python-funktionen

I den här artikeln integrerar du en Azure Storage kö med den funktion och det lagrings konto som du skapade i [skapa en HTTP-utlöst python-funktion](functions-create-first-function-python.md). Du uppnår denna integrering genom att använda en *utgående bindning* som skriver data från en http-begäran till ett meddelande i kön. Om du slutför den här artikeln debiteras inga ytterligare kostnader utöver de få USD cent i föregående snabb start.

## <a name="prerequisites"></a>Förutsättningar

- Slutför snabb starten och [skapa en HTTP-utlöst python-funktion](functions-create-first-function-python.md). Om du redan har rensat resurser i slutet av artikeln går du igenom stegen igen och återskapar appen Functions i Azure, men låter resurserna vara på plats.

## <a name="retrieve-the-azure-storage-connection-string"></a>Hämta Azure Storage anslutnings sträng

När du skapade en Function-app i Azure i den tidigare snabb starten skapade du även ett lagrings konto. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. Genom att Hämta inställningen till den *lokala. Settings. JSON* -filen kan du använda den för att skriva till en lagrings kö i samma konto när funktionen körs lokalt. 

1. Kör följande kommando från roten av projektet och ersätt `<app_name>` med namnet på din Function-app från föregående snabb start. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Öppna *Local. Settings. JSON* och leta upp värdet med namnet `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Du använder namnet `AzureWebJobsStorage` och anslutnings strängen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *Local. Settings. JSON* innehåller hemligheter som hämtats från Azure, ska du alltid utesluta den här filen från käll kontroll. Filen *. gitignore* som skapas med ett lokalt Functions-projekt utesluter filen som standard.

## <a name="add-an-output-binding-to-functionjson"></a>Lägg till en utgående bindning till function. JSON

Även om en funktion bara kan ha en utlösare, kan den ha flera indata-och utgående bindningar, vilket gör att du kan ansluta till andra Azure-tjänster och-resurser utan att skriva anpassad integrerings kod. Du deklarerar dessa bindningar i *Function. JSON* -filen i mappen funktion efter vad som är lämpligt för det språk som du använder för funktionen.

I den tidigare snabb starten innehåller din *Function. JSON* -fil i mappen *HttpExample* två bindningar i samlingen `bindings`:

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
    }
  ]
}
```

Varje bindning har minst en typ, en riktning och ett namn. I exemplet ovan är den första bindningen av typen `httpTrigger` med riktningen `in`. För `in` riktning anger `name` namnet på en indataparameter som skickas till funktionen när den anropas av utlösaren.

Den andra bindningen är av typen `http` med riktningen `out`, i vilket fall den särskilda `name` av `$return` anger att den här bindningen använder funktionens retur värde i stället för att ange en indataparameter.

Om du vill skriva till en Azure Storage kö från den här funktionen lägger du till en `out`-bindning av typen `queue` med namnet `msg`, som du ser i koden nedan:

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

I det här fallet tilldelas `msg` funktionen som ett argument för utdata. För en `queue` typ måste du också ange namnet på kön i `queueName` och ange *namnet* på Azure Storage anslutningen (från *Local. Settings. json*) i `connection`.

Mer information om bindningar finns i [Azure Functions utlösare och bindning av koncept](functions-triggers-bindings.md) och utdata från [kö](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Lägg till kod för att använda utgående bindning

Med den Queue-bindning som anges i *Function. JSON*kan du nu uppdatera din funktion för att ta emot `msg` utdataparameter och skriva meddelanden till kön.

Uppdatera *HttpExample\\\_\_init\_\_. py* för att matcha följande kod, lägga till `msg`-parametern i funktions definitionen och `msg.set(name)` under `if name:`-instruktionen.

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

Parametern `msg` är en instans av [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Dess `set` metod skriver ett sträng meddelande till kön, i det här fallet det namn som skickades till funktionen i URL-frågesträngen.

Observera att du *inte* behöver skriva någon kod för autentisering, få en Queue-referens eller skriva data. Alla dessa integrations aktiviteter hanteras bekvämt i Azure Functions Runtime-och Queue-bindning.

## <a name="run-and-test-the-function-locally"></a>Kör och testa funktionen lokalt

1. I en terminal-eller kommando tolk navigerar du till din Function Project-mapp, *LocalFunctionProj*.

1. Starta Azure Functions körnings värd genom att använda följande kommando.

    ```
    func host start
    ```

1. När starten är klar och du ser URL: en för `HttpExample` slut punkten kopierar du dess URL till en webbläsare och lägger till frågesträngen `?name=<your-name>`, vilket gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Guido`. Webbläsaren ska visa ett meddelande som `Hello Guido` som i föregående artikel.

    Om du inte ser `HttpExample` slut punkten visas stoppar du värden med **Ctrl**+**C** och kontrollerar utdata för fel. Värden aktiverar till exempel inte slut punkten om det finns ett fel i *Function. JSON*. Kontrol lera också att du kör `func host start` från mappen Functions-projekt och inte mappen *HttpExample* .

1. När du är klar stoppar du värden med **Ctrl**+**C**.

> [!TIP]
> Under starten hämtar värden och installerar [lagrings bindnings tillägget](functions-bindings-storage-blob.md#add-to-your-functions-app) och andra tillägg för Microsoft-bindning. Den här installationen sker eftersom bindnings tillägg är aktiverade som standard i *Host. JSON* -filen med följande egenskaper:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Om du stöter på fel som rör bindnings tillägg kontrollerar du att egenskaperna ovan finns i *Host. JSON*.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage kön

När din funktion genererar ett HTTP-svar för webbläsaren, anropar den också `msg.set(name)`, som skriver ett meddelande till en Azure Storage kö med namnet `outqueue`, enligt vad som anges i Queue-bindningen. Du kan visa kön i [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) eller i [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI enligt beskrivningen i följande steg:

1. Öppna funktions projektets *lokala. Ange. JSON* -fil och kopiera värdet för anslutnings strängen. Kör följande kommando i ett terminalfönster-eller kommando fönster för att skapa en miljö variabel med namnet `AZURE_STORAGE_CONNECTION_STRING`, och klistra in din speciella anslutnings sträng i stället för `<connection_string>`. (Denna miljö variabel innebär att du inte behöver ange anslutnings strängen för varje efterföljande kommando med hjälp av argumentet `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Valfritt Använd kommandot [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) för att Visa lagrings köerna i ditt konto. Utdata från det här kommandot ska innehålla en kö med namnet `outqueue`, som skapades när funktionen skrev sitt första meddelande till kön.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Använd kommandot [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) för att visa meddelanden i den här kön, vilket bör vara det första namnet du använde när du testar funktionen tidigare. Kommandot hämtar det första meddelandet i kön i [base64-kodning](functions-bindings-storage-queue-trigger.md#encoding), så du måste också avkoda meddelandet för att visa som text.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    Eftersom du måste referera till meddelande samlingen och avkoda från base64 kör du PowerShell och använder PowerShell-kommandot.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Distribuera om projektet till Azure

Nu när du har testat funktionen lokalt och verifierat att den skrev ett meddelande till Azure Storage kön kan du distribuera om projektet för att uppdatera slut punkten som körs på Azure.

1. I mappen *LocalFunctionsProj* använder du kommandot [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) för att distribuera om projektet och ersätter`<app_name>` med namnet på din app.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Som i föregående snabb start använder du en webbläsare eller en sväng för att testa den omdistribuerade funktionen.

    # <a name="browser"></a>[Webbläsare](#tab/browser)
    
    Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webb adress fält för webbläsare, och lägga till Frågeparametern `&name=Azure`. Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

    ![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[klammerparentes](#tab/curl)
    
    Kör en [sväng](https://curl.haxx.se/) med **anrops-URL: en**och Lägg till parametern `&name=Azure`. Kommandots utdata ska vara texten "Hello Azure".
    
    ![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Granska lagrings kön igen, enligt beskrivningen i föregående avsnitt, för att kontrol lera att den innehåller det nya meddelandet som skrivits till kön.

    Om du använder Azure CLI för att undersöka kön visar kommandot `az storage message peek` bara det första meddelandet i kön. Använd `az storage message get` i stället för samma argument för att simulera bearbetning av meddelanden. Kommandot `get` returnerar meddelandet och tar bort det från kön. Du kan sedan upprepa samma kommando tills kön är tom (och kommandot ger ett fel).

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg ska du [aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource), se till att alla resurser är på plats när du bygger på vad du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera Application Insights integrering med en Azure Functions-app](functions-monitoring.md#manually-connect-an-app-insights-resource)

Andra resurser:

- [Exempel på kompletta funktions projekt i python](/samples/browse/?products=azure-functions&languages=python).
- [Guide för Azure Functions python-utvecklare](functions-reference-python.md)
- [Azure Functions utlösare och bindningar](functions-triggers-bindings.md).
- [Prissättnings sida för funktioner](https://azure.microsoft.com/pricing/details/functions/)
- [Beräknar kostnader för förbruknings planer](functions-consumption-costs.md) .
