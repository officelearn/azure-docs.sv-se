---
title: Ansluta Azure-funktioner till Azure Storage med kommandoradsverktyg
description: Lär dig hur du ansluter Azure Functions till en Azure Storage-kö genom att lägga till en utdatabindning till ditt kommandoradsprojekt.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473395"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Ansluta Azure-funktioner till Azure Storage med kommandoradsverktyg

I den här artikeln integrerar du en Azure Storage-kö med det funktions- och lagringskonto som du skapade i [föregående snabbstart](functions-create-first-azure-function-azure-cli.md). Du uppnår den här integreringen med hjälp av en *utdatabindning* som skriver data från en HTTP-begäran till ett meddelande i kön. Slutföra denna artikel medför inga extra kostnader utöver de få USD cent av den tidigare snabbstart. Mer information om bindningar finns i [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)begrepp .

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du slutföra artikeln [Snabbstart: Skapa ett Azure Functions-projekt från kommandoraden](functions-create-first-azure-function-azure-cli.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa funktionsappen och relaterade resurser i Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Hämta anslutningssträngen för Azure Storage

När du skapade en funktionsapp i Azure i föregående snabbstart har du också skapat ett lagringskonto. Anslutningssträngen för det här kontot lagras säkert i appinställningar i Azure. Genom att hämta inställningen till filen *local.settings.json* kan du använda den anslutningsskrivningen till en lagringskö i samma konto när du kör funktionen lokalt. 

1. Från projektets rot kör du följande `<APP_NAME>` kommando och ersätter med namnet på funktionsappen från föregående snabbstart. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Öppna *local.settings.json* och leta `AzureWebJobsStorage`reda på värdet med namnet , som är anslutningssträngen för lagringskonto. Du använder `AzureWebJobsStorage` namnet och anslutningssträngen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *local.settings.json* innehåller hemligheter som hämtats från Azure, utesluter du alltid den här filen från källkontrollen. *Gitignore-filen* som skapats med ett lokalt funktionsprojekt utesluter filen som standard.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Lägga till en utdatabindningsdefinition i funktionen

Även om en funktion bara kan ha en utlösare kan den ha flera indata- och utdatabindningar, vilket gör att du kan ansluta till andra Azure-tjänster och resurser utan att skriva anpassad integrationskod. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Du deklarerar dessa bindningar i *filen function.json* i funktionsmappen. Från föregående snabbstart innehåller *filen function.json* i mappen *HttpExample* två `bindings` bindningar i samlingen:  
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
Varje bindning har minst en typ, en riktning och ett namn. I exemplet ovan är den första `httpTrigger` bindningen `in`av typen med riktningen . För `in` riktningen `name` anger namnet på en indataparameter som skickas till funktionen när den anropas av utlösaren.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Den andra bindningen i `res`samlingen heter . Den `http` här bindningen`out`är en utdatabindning ( ) som används för att skriva HTTP-svaret. 

Om du vill skriva till en Azure `out` Storage-kö från den här funktionen lägger du till en bindning av typen `queue` med namnet `msg`, som visas i koden nedan:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Den andra bindningen i `http` samlingen är `out`av typen riktning `name` `$return` , i vilket fall specialvärdet anger att denna bindning använder funktionens returvärde i stället för att tillhandahålla en indataparameter.

Om du vill skriva till en Azure `out` Storage-kö från den här funktionen lägger du till en bindning av typen `queue` med namnet `msg`, som visas i koden nedan:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Den andra bindningen i `res`samlingen heter . Den `http` här bindningen`out`är en utdatabindning ( ) som används för att skriva HTTP-svaret. 

Om du vill skriva till en Azure `out` Storage-kö från den här funktionen lägger du till en bindning av typen `queue` med namnet `msg`, som visas i koden nedan:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
I det `msg` här fallet ges till funktionen som ett utdataargument. För `queue` en typ måste du också ange `queueName` namnet på kön i och ange *namnet* på Azure Storage-anslutningen (från *local.settings.json)* i `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Mer information om information om bindningar finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md) och [köutdatakonfiguration](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Lägga till kod för att använda utdatabindningen

Med köbindningen angiven i *function.json*kan du `msg` nu uppdatera funktionen för att ta emot utdataparametern och skriva meddelanden till kön.

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

Observera att du *inte* behöver skriva någon kod för autentisering, hämta en köreferens eller skriva data. Alla dessa integrationsuppgifter hanteras bekvämt i Azure Functions runtime och köutdatabindning.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage-kön

Du kan visa kön i [Azure-portalen](../storage/queues/storage-quickstart-queues-portal.md) eller i [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI, enligt beskrivningen i följande steg:

1. Öppna funktionsprojektets *local.setting.json-fil* och kopiera anslutningssträngvärdet. I ett terminal- eller kommandofönster kör du följande `AZURE_STORAGE_CONNECTION_STRING`kommando för att skapa en `<MY_CONNECTION_STRING>`miljövariabel med namnet , klistra in din specifika anslutningssträng i stället för . (Den här miljövariabeln innebär att du inte behöver ange `--connection-string` anslutningssträngen till varje efterföljande kommando med argumentet.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Valfritt) Använd [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att visa lagringsköerna i ditt konto. Utdata från det här kommandot `outqueue`ska innehålla en kö med namnet , som skapades när funktionen skrev sitt första meddelande till den kön.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Använd [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) kommandot för att läsa meddelandet från den här kön, vilket bör vara det förnamn som du använde när du testade funktionen tidigare. Kommandot läser och tar bort det första meddelandet från kön. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Det här skriptet använder certutil för att avkoda den base64-kodade meddelandesamlingen från en lokal temp-fil. Om det inte finns några `> NUL` utdata kan du prova att ta bort från skriptet för att sluta undertrycka certutil-utdata om det finns ett fel. 
    
    ---
    
    Eftersom meddelandetexten lagras [base64-kodad](functions-bindings-storage-queue-trigger.md#encoding)måste meddelandet avkodas innan det visas. När du `az storage message get`har kör tas meddelandet bort från kön. Om det bara fanns `outqueue`ett meddelande i hämtas inte ett meddelande när du kör det här kommandot en andra gång och får i stället ett felmeddelande.

## <a name="redeploy-the-project-to-azure"></a>Distribuera om projektet till Azure

Nu när du har verifierat lokalt att funktionen skrev ett meddelande till Azure Storage-kön kan du distribuera om projektet för att uppdatera slutpunkten som körs på Azure.

1. I mappen *LocalFunctionsProj* använder [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) du kommandot för att distribuera`<APP_NAME>` om projektet och ersätter med namnet på appen.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Precis som i föregående snabbstart använder du en webbläsare eller CURL för att testa den omdepilerade funktionen.

    # <a name="browser"></a>[Webbläsare](#tab/browser)
    
    Kopiera den fullständiga **anropa-URL:en** som visas i utdata från publiceringskommandot till ett webbläsaradressfält, vilket lägger till frågeparametern `&name=Functions`. Webbläsaren ska visa liknande utdata som när du körde funktionen lokalt.

    ![Utdata för funktionen körs på Azure i en webbläsare](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Kör [`curl`](https://curl.haxx.se/) med **url:en anropa**och `&name=Functions`lägga till parametern . Utdata för kommandot ska vara texten "Hej funktioner".
    
    ![Utdata för funktionen körs på Azure med CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Undersök lagringskön igen, enligt beskrivningen i föregående avsnitt, för att kontrollera att den innehåller det nya meddelandet som skrivits till kön.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika att ådra sig ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat http-utlöst funktion för att skriva data till en lagringskö. Nu kan du läsa mer om att utveckla funktioner från kommandoraden med hjälp av Core Tools och Azure CLI:

+ [Arbeta med Azure Functions core-verktyg](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Exempel på kompletta funktionsprojekt i C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exempel på kompletta funktionsprojekt i JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Utvecklarhandboken för Azure Functions JavaScript](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Exempel på kompletta funktionsprojekt i TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Utvecklarhandboken för Azure Functions TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Exempel på kompletta funktionsprojekt i Python](/samples/browse/?products=azure-functions&languages=python).

+ [Utvecklarhandboken för Azure Functions Python](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Exempel på kompletta funktionsprojekt i PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Utvecklarhandboken för Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)

+ [Sidan Funktioner prissättning](https://azure.microsoft.com/pricing/details/functions/)

+ [Uppskatta kostnader för förbrukningsplan](functions-consumption-costs.md) 
