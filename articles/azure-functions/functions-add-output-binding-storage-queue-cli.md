---
title: Ansluta Azure Functions till Azure Storage med hjälp av kommando rads verktyg
description: Lär dig hur du ansluter Azure Functions till en Azure Storage kö genom att lägga till en utgående bindning till ditt kommando rads projekt.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201929"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Ansluta Azure Functions till Azure Storage med hjälp av kommando rads verktyg

I den här artikeln integrerar du en Azure Storage kö med den funktion och det lagrings konto som du skapade i [föregående snabb start](functions-create-first-azure-function-azure-cli.md). Du uppnår denna integrering genom att använda en *utgående bindning* som skriver data från en http-begäran till ett meddelande i kön. Om du slutför den här artikeln debiteras inga ytterligare kostnader utöver de få USD cent i föregående snabb start. Mer information om bindningar finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du slutföra artikeln, [snabb start: skapa ett Azure Functions-projekt från kommando raden](functions-create-first-azure-function-azure-cli.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Hämta Azure Storage anslutnings sträng

När du skapade en Function-app i Azure i den tidigare snabb starten skapade du även ett lagrings konto. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. Genom att Hämta inställningen till den *lokala. Settings. JSON* -filen kan du använda den för att skriva till en lagrings kö i samma konto när funktionen körs lokalt. 

1. Kör följande kommando från roten av projektet och ersätt `<APP_NAME>` med namnet på din Function-app från föregående snabb start. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Öppna *Local. Settings. JSON* och leta upp värdet med namnet `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Du använder namnet `AzureWebJobsStorage` och anslutnings strängen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *Local. Settings. JSON* innehåller hemligheter som hämtats från Azure, ska du alltid utesluta den här filen från käll kontroll. Filen *. gitignore* som skapas med ett lokalt Functions-projekt utesluter filen som standard.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Lägg till en definition för utgående bindning i funktionen

Även om en funktion bara kan ha en utlösare, kan den ha flera indata-och utgående bindningar, vilket gör att du kan ansluta till andra Azure-tjänster och-resurser utan att skriva anpassad integrerings kod. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Du deklarerar dessa bindningar i *Function. JSON* -filen i mappen function. I den tidigare snabb starten innehåller din *Function. JSON* -fil i mappen *HttpExample* två bindningar i samlingen `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Varje bindning har minst en typ, en riktning och ett namn. I exemplet ovan är den första bindningen av typen `httpTrigger` med riktningen `in`. För `in` riktning anger `name` namnet på en indataparameter som skickas till funktionen när den anropas av utlösaren.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Den andra bindningen i samlingen heter `res`. Den här `http` bindningen är en utgående bindning (`out`) som används för att skriva HTTP-svaret. 

Om du vill skriva till en Azure Storage kö från den här funktionen lägger du till en `out`-bindning av typen `queue` med namnet `msg`, som du ser i koden nedan:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Den andra bindningen i samlingen är av typen `http` med riktningen `out`, i vilket fall den särskilda `name` för `$return` anger att den här bindningen använder funktionens retur värde istället för att ange en indataparameter.

Om du vill skriva till en Azure Storage kö från den här funktionen lägger du till en `out`-bindning av typen `queue` med namnet `msg`, som du ser i koden nedan:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Den andra bindningen i samlingen heter `res`. Den här `http` bindningen är en utgående bindning (`out`) som används för att skriva HTTP-svaret. 

Om du vill skriva till en Azure Storage kö från den här funktionen lägger du till en `out`-bindning av typen `queue` med namnet `msg`, som du ser i koden nedan:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
I det här fallet tilldelas `msg` funktionen som ett argument för utdata. För en `queue` typ måste du också ange namnet på kön i `queueName` och ange *namnet* på Azure Storage anslutningen (från *Local. Settings. json*) i `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Mer information om bindningar finns i [Azure Functions utlösare och bindning av koncept](functions-triggers-bindings.md) och utdata från [kö](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Lägg till kod för att använda utgående bindning

Med den Queue-bindning som anges i *Function. JSON*kan du nu uppdatera din funktion för att ta emot `msg` utdataparameter och skriva meddelanden till kön.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

Observera att du *inte* behöver skriva någon kod för autentisering, få en Queue-referens eller skriva data. Alla dessa integrations aktiviteter hanteras bekvämt i Azure Functions Runtime-och Queue-bindning.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage kön

Du kan visa kön i [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) eller i [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI enligt beskrivningen i följande steg:

1. Öppna funktions projektets *lokala. Ange. JSON* -fil och kopiera värdet för anslutnings strängen. Kör följande kommando i ett terminalfönster-eller kommando fönster för att skapa en miljö variabel med namnet `AZURE_STORAGE_CONNECTION_STRING`, och klistra in din speciella anslutnings sträng i stället för `<MY_CONNECTION_STRING>`. (Denna miljö variabel innebär att du inte behöver ange anslutnings strängen för varje efterföljande kommando med hjälp av argumentet `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Valfritt Använd kommandot [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) för att Visa lagrings köerna i ditt konto. Utdata från det här kommandot ska innehålla en kö med namnet `outqueue`, som skapades när funktionen skrev sitt första meddelande till kön.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. Använd kommandot [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) för att läsa meddelandet från den här kön, vilket bör vara det första namnet du använde när du testar funktionen tidigare. Kommandot läser och tar bort det första meddelandet från kön. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Det här skriptet använder certutil för att avkoda den base64-kodade meddelande samlingen från en lokal Temp-fil. Om det inte finns några utdata kan du försöka ta bort `> NUL` från skriptet för att sluta ignorera certutil-utdata om det uppstår ett fel. 
    
    ---
    
    Eftersom meddelande texten lagras base64- [kodat](functions-bindings-storage-queue-trigger.md#encoding)måste meddelandet avkodas innan det visas. När du har kört `az storage message get`tas meddelandet bort från kön. Om det bara fanns ett meddelande i `outqueue`, kommer du inte att få ett meddelande när du kör det här kommandot en gång i stället för att få ett fel.

## <a name="redeploy-the-project-to-azure"></a>Distribuera om projektet till Azure

Nu när du har verifierat lokalt att funktionen skrev ett meddelande till Azure Storage kön kan du distribuera om projektet för att uppdatera slut punkten som körs på Azure.

1. I mappen *LocalFunctionsProj* använder du kommandot [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) för att distribuera om projektet och ersätter`<APP_NAME>` med namnet på din app.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Som i föregående snabb start använder du en webbläsare eller en sväng för att testa den omdistribuerade funktionen.

    # <a name="browser"></a>[Webbläsare](#tab/browser)
    
    Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webb adress fält för webbläsare, och lägga till Frågeparametern `&name=Functions`. Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

    ![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[klammerparentes](#tab/curl)
    
    Kör [`curl`](https://curl.haxx.se/) med **ANROPs-URL: en**och Lägg till parametern `&name=Functions`. Kommandots utdata ska vara texten, "Hello Functions".
    
    ![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Granska lagrings kön igen, enligt beskrivningen i föregående avsnitt, för att kontrol lera att den innehåller det nya meddelandet som skrivits till kön.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Nu kan du lära dig mer om att utveckla funktioner från kommando raden med hjälp av kärn verktyg och Azure CLI:

+ [Arbeta med Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Exempel på kompletta funktions projekt i C# ](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referens C# för Azure Functions-utvecklare](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exempel på kompletta funktions projekt i Java Script](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript-guide för utvecklare](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exempel på kompletta funktions projekt i typescript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Guide för Azure Functions TypeScript-utvecklare](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exempel på kompletta funktions projekt i python](/samples/browse/?products=azure-functions&languages=python).

+ [Guide för Azure Functions python-utvecklare](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exempel på kompletta funktions projekt i PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions PowerShell-guide för utvecklare](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

+ [Prissättnings sida för funktioner](https://azure.microsoft.com/pricing/details/functions/)

+ [Uppskatta förbruknings Plans kostnader](functions-consumption-costs.md) 
