---
title: Ansluta Azure Functions till Azure Storage med Visual Studio Code
description: Lär dig hur du ansluter Azure Functions till en Azure Storage kö genom att lägga till en utgående bindning i Visual Studio Code-projektet.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 22f7df52e90a35a3ed9a26a7672f8354efc173e3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191096"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Ansluta Azure Functions till Azure Storage med Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du använder Visual Studio Code för att ansluta den funktion som du skapade i [föregående snabb starts artikel](functions-create-first-function-vs-code.md) till Azure Storage. Den utgående bindning som du lägger till i den här funktionen skriver data från HTTP-begäran till ett meddelande i en kö för Azure Queue Storage. 

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra det enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot finns redan i en app-inställning med namnet `AzureWebJobsStorage`.  

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du startar den här artikeln måste du uppfylla följande krav:

* Installera [Azure Storage-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Installera [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer är ett verktyg som du använder för att undersöka Kömeddelanden som genereras av din utgående bindning. Storage Explorer stöds på macOS-, Windows-och Linux-baserade operativ system.

::: zone pivot="programming-language-csharp"
* Installera [.net Core CLI verktyg](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Slutför stegen i [del 1 av snabb starten för Visual Studio Code](functions-create-first-function-vs-code.md). 

Den här artikeln förutsätter att du redan har loggat in på Azure-prenumerationen från Visual Studio Code. Du kan logga in genom att köra `Azure: Sign In` från kommando paletten. 

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

I [föregående snabb starts artikel](functions-create-first-function-vs-code.md)skapade du en Function-app i Azure tillsammans med det lagrings konto som krävs. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagrings kö i samma konto. För att ansluta till ditt lagrings konto när funktionen körs lokalt måste du hämta appinställningar till filen Local. Settings. JSON. 

1. Tryck på F1-tangenten för att öppna kommando paletten och Sök sedan efter och kör kommandot `Azure Functions: Download Remote Settings....`. 

1. Välj den Function-app som du skapade i föregående artikel. Välj **Ja om** du vill skriva över de befintliga lokala inställningarna. 

    > [!IMPORTANT]  
    > Eftersom den innehåller hemligheter publiceras inte filen Local. Settings. JSON och tas inte med i käll kontrollen.

1. Kopiera värdet `AzureWebJobsStorage`, som är nyckeln för värdet för anslutnings strängen för lagrings kontot. Använd den här anslutningen för att kontrol lera att utgående bindning fungerar som förväntat.

## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Eftersom du använder en kö för lagring av utdata måste du ha installerat tillägget för lagrings bindningar innan du kör projektet. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

Med undantag för HTTP-och timer-utlösare implementeras bindningar som tilläggs paket. Kör följande [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) kommando i terminalfönstret för att lägga till lagrings tilläggs paketet i projektet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Nu kan du lägga till bindningen för Storage-utdata i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I functions kräver varje typ av bindning en `direction`, `type`och en unik `name` som ska definieras i function. JSON-filen. Hur du definierar dessa attribut beror på språket i din Function-app.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När bindningen har definierats kan du använda `name` av bindningen för att komma åt den som ett attribut i funktions under kommandot. Genom att använda en utgående bindning behöver du inte använda den Azure Storage SDK-koden för autentisering, hämta en Queue referens eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

En ny kö med namnet **arbetskö** skapas i ditt lagrings konto av Functions-körningen när den utgående bindningen används först. Du använder Storage Explorer för att kontrol lera att kön har skapats tillsammans med det nya meddelandet.

### <a name="connect-storage-explorer-to-your-account"></a>Anslut Storage Explorer till ditt konto

Hoppa över det här avsnittet om du redan har installerat Azure Storage Explorer och anslutit det till ditt Azure-konto.

1. Kör verktyget [Azure Storage Explorer], Välj ikonen Anslut till vänster och välj **Lägg till ett konto**.

    ![Lägg till ett Azure-konto i Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. I dialog rutan **Anslut** väljer du **Lägg till ett Azure-konto**, väljer din Azure- **miljö**och väljer **Logga in..** . 

    ![Logga in på ditt Azure-konto](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

När du har loggat in på ditt konto visas alla Azure-prenumerationer som är kopplade till ditt konto.

### <a name="examine-the-output-queue"></a>Granska utdatakö

1. I Visual Studio Code, trycker du på F1-tangenten för att öppna kommando paletten, sedan söker du efter och kör kommandot `Azure Storage: Open in Storage Explorer` och väljer ditt lagrings konto namn. Ditt lagrings konto öppnas i Azure Storage Explorer.  

1. Expandera noden **Köer** och välj sedan kön med namnet **outqueue**. 

   Kön innehåller meddelandet som köutdatabindningen skapade när du körde den HTTP-utlösta funktionen. Om du startade en funktion med standardvärdet `name` för *Azure* så är kömeddelandet *Name passed to the function: Azure* (Namn som skickats till funktionen: Azure).

    ![Köa meddelande visas i Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Kör funktionen igen, skicka en annan begäran och se att ett nytt meddelande visas i kön.  

Nu är det dags att publicera om den uppdaterade Function-appen till Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Distribuera om och verifiera den uppdaterade appen

1. I Visual Studio Code, trycker du på F1 för att öppna kommando paletten. I paletten kommando söker du efter och väljer `Azure Functions: Deploy to function app...`.

1. Välj den Function-app som du skapade i den första artikeln. Eftersom du omdistribuerar projektet till samma app väljer du **distribuera** för att ignorera varningen om att skriva över filer.

1. När distributionen är klar kan du använda svängen igen eller en webbläsare för att testa den omdistribuerade funktionen. Som tidigare lägger du till frågesträngen `&name=<yourname>` till URL: en, som i följande exempel:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. [Visa meddelandet i lagrings kön](#examine-the-output-queue) igen för att kontrol lera att utgående bindningen igen genererar ett nytt meddelande i kön.

## <a name="clean-up-resources"></a>Rensa resurser

*Resurser* i Azure refererar till funktionsappar, funktioner, lagringskonton och så vidare. Dessa grupperas i *resursgrupper*. Du kan ta bort allt innehåll i en grupp genom att ta bort gruppen.

Du skapade resurser för att slutföra de här snabbstarterna. Det är möjligt att du debiteras för de här resurserna beroende på din [kontostatus](https://azure.microsoft.com/account/) och dina [servicepriser](https://azure.microsoft.com/pricing/). Om du inte behöver resurserna längre så visar vi hur du tar bort dem här:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Nu kan du lära dig mer om hur du utvecklar funktioner med Visual Studio Code:

+ [Utveckla Azure Functions med Visual Studio Code](functions-develop-vs-code.md)
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
+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md).
+ [Prissättnings sida för funktioner](https://azure.microsoft.com/pricing/details/functions/)
+ [Beräknar kostnader för förbruknings planer](functions-consumption-costs.md) .
