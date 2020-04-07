---
title: Ansluta Azure-funktioner till Azure Storage med Visual Studio-kod
description: Lär dig hur du ansluter Azure Functions till en Azure Storage-kö genom att lägga till en utdatabindning i visual studio-kodprojektet.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: c32f98fc1b3de98592f8e7ceb43c17aa8a9049f7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673458"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Ansluta Azure-funktioner till Azure Storage med Visual Studio-kod

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du använder Visual Studio Code för att ansluta funktionen som du skapade i föregående [snabbstartsartikel](functions-create-first-function-vs-code.md) till Azure Storage. Utdatabindningen som du lägger till i den här funktionen skriver data från HTTP-begäran till ett meddelande i en Azure Queue storage-kö. 

De flesta bindningar kräver en lagrad anslutningssträng som Funktioner använder för att komma åt den bundna tjänsten. För att göra det enklare använder du det lagringskonto som du skapade med funktionsappen. Anslutningen till det här kontot lagras redan `AzureWebJobsStorage`i appinställningen .  

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar den här artikeln måste du uppfylla följande krav:

* Installera [Azure Storage-tillägget för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).

* Installera [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer är ett verktyg som du använder för att undersöka kömeddelanden som genereras av utdatabindningen. Storage Explorer stöds på macOS-, Windows- och Linux-baserade operativsystem.

::: zone pivot="programming-language-csharp"
* Installera [.NET Core CLI-verktyg](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x).
::: zone-end

* Slutför stegen i [del 1 i snabbstarten för Visual Studio-kod](functions-create-first-function-vs-code.md). 

Den här artikeln förutsätter att du redan är inloggad på din Azure-prenumeration från Visual Studio Code. Du kan logga `Azure: Sign In` in genom att köra från kommandopaletten. 

## <a name="download-the-function-app-settings"></a>Ladda ned funktionsappinställningarna

I den [föregående snabbstartsartikeln](functions-create-first-function-vs-code.md)skapade du en funktionsapp i Azure tillsammans med det nödvändiga lagringskontot. Anslutningssträngen för det här kontot lagras säkert i appinställningar i Azure. I den här artikeln skriver du meddelanden till en lagringskö i samma konto. Om du vill ansluta till ditt Lagringskonto när du kör funktionen lokalt måste du hämta appinställningarna till filen local.settings.json. 

1. Tryck på F1 för att öppna kommandopaletten och `Azure Functions: Download Remote Settings....`sök sedan efter och kör kommandot . 

1. Välj den funktionsapp som du skapade i föregående artikel. Välj **Ja om** du vill skriva över de befintliga lokala inställningarna. 

    > [!IMPORTANT]  
    > Eftersom den innehåller hemligheter publiceras aldrig filen local.settings.json och utesluts inte från källkontrollen.

1. Kopiera värdet `AzureWebJobsStorage`, som är nyckeln för anslutningssträngvärdet För lagringskonto. Du använder den här anslutningen för att kontrollera att utdatabindningen fungerar som förväntat.

## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Eftersom du använder en kölagringsbelödning måste du ha tillägget Lagringsbindningar installerat innan du kör projektet. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Projektet har konfigurerats för att använda [tilläggspaket](functions-bindings-register.md#extension-bundles), som automatiskt installerar en fördefinierad uppsättning tilläggspaket. 

Tilläggspaket är aktiverade i filen host.json i projektets rot, som ser ut så här:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

Med undantag för HTTP- och timerutlösare implementeras bindningar som tilläggspaket. Kör följande [dotnet add-paketkommando](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att lägga till paketet för lagringstillägg i projektet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

::: zone-end

Nu kan du lägga till lagringsutdatabindningen i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I Funktioner kräver varje typ `direction` `type`av bindning `name` en , och en unik som ska definieras i filen function.json. Hur du definierar dessa attribut beror på språket i din funktionsapp.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När bindningen har definierats `name` kan du använda bindningen för att komma åt den som ett attribut i funktionssignaturen. Genom att använda en utdatabindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en köreferens eller skriva data. Funktionskörnings- och köutdatabindningen utför dessa uppgifter åt dig.

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

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

<!--- Local testing section --->

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

En ny kö med namnet **utkö** skapas i ditt lagringskonto av funktionskörningen när utdatabindningen först används. Du ska använda Storage Explorer för att kontrollera att kön har skapats tillsammans med det nya meddelandet.

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Anslut Storage Explorer till ditt konto

Hoppa över det här avsnittet om du redan har installerat Azure Storage Explorer och anslutit den till ditt Azure-konto.

1. Kör verktyget [Azure Storage Explorer], välj anslutningsikonen till vänster och välj **Lägg till ett konto**.

    ![Lägga till ett Azure-konto i Microsoft Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. I dialogrutan **Anslut** väljer du **Lägg till ett Azure-konto,** väljer din **Azure-miljö**och väljer **Logga in...**. 

    ![Logga in på ditt Azure-konto](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

När du har loggat in på ditt konto ser du alla Azure-prenumerationer som är kopplade till ditt konto.

### <a name="examine-the-output-queue"></a>Granska utdatakö

1. I Visual Studio-kod trycker du på F1 för att öppna kommandopaletten och söker sedan efter och kör kommandot `Azure Storage: Open in Storage Explorer` och väljer ditt lagringskontonamn. Ditt lagringskonto öppnas i Azure Storage Explorer.  

1. Expandera noden **Köer** och välj sedan kön med namnet **outqueue**. 

   Kön innehåller meddelandet som köutdatabindningen skapade när du körde den HTTP-utlösta funktionen. Om du startade en funktion med standardvärdet `name` för *Azure* så är kömeddelandet *Name passed to the function: Azure* (Namn som skickats till funktionen: Azure).

    ![Kömeddelande som visas i Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Kör funktionen igen, skicka en ny begäran och du ser ett nytt meddelande visas i kön.  

Nu är det dags att publicera om den uppdaterade funktionsappen till Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Distribuera om och verifiera den uppdaterade appen

1. Öppna kommandopaletten genom att trycka på F1 i Visual Studio Code. Sök efter och välj `Azure Functions: Deploy to function app...`i kommandopaletten .

1. Välj den funktionsapp som du skapade i den första artikeln. Eftersom du distribuerar om projektet till samma app väljer du **Distribuera** för att avvisa varningen om att skriva över filer.

1. När distributionen är klar kan du återigen använda cURL eller en webbläsare för att testa funktionen omdelat. Precis som tidigare lade `&name=<yourname>` du till frågesträngen i URL:en, som i följande exempel:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Visa [meddelandet i lagringskön](#examine-the-output-queue) igen för att kontrollera att utdatabindningen igen genererar ett nytt meddelande i kön.

## <a name="clean-up-resources"></a>Rensa resurser

*Resurser* i Azure refererar till funktionsappar, funktioner, lagringskonton och så vidare. Dessa grupperas i *resursgrupper*. Du kan ta bort allt innehåll i en grupp genom att ta bort gruppen.

Du skapade resurser för att slutföra de här snabbstarterna. Det är möjligt att du debiteras för de här resurserna beroende på din [kontostatus](https://azure.microsoft.com/account/) och dina [servicepriser](https://azure.microsoft.com/pricing/). Om du inte behöver resurserna längre så visar vi hur du tar bort dem här:

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat http-utlöst funktion för att skriva data till en lagringskö. Nu kan du läsa mer om hur du utvecklar Funktioner med Visual Studio-kod:

+ [Utveckla Azure-funktioner med Visual Studio-kod](functions-develop-vs-code.md)
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
+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md).
+ [Sidan Funktioner prissättning](https://azure.microsoft.com/pricing/details/functions/)
+ [Uppskattning Förbrukningsplan kostnader](functions-consumption-costs.md) artikel.
