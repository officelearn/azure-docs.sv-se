---
title: Ansluta Azure-funktioner till Azure Storage med kommandoradsverktyg
description: Lär dig hur du ansluter Azure Functions till en Azure Storage-kö genom att lägga till en utdatabindning till ditt kommandoradsprojekt.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673336"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Ansluta Azure-funktioner till Azure Storage med kommandoradsverktyg

I den här artikeln integrerar du en Azure Storage-kö med det funktions- och lagringskonto som du skapade i [föregående snabbstart](functions-create-first-azure-function-azure-cli.md). Du uppnår den här integreringen med hjälp av en *utdatabindning* som skriver data från en HTTP-begäran till ett meddelande i kön. Slutföra denna artikel medför inga extra kostnader utöver de få USD cent av den tidigare snabbstart. Mer information om bindningar finns i [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)begrepp .

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du slutföra artikeln [Snabbstart: Skapa ett Azure Functions-projekt från kommandoraden](functions-create-first-azure-function-azure-cli.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa funktionsappen och relaterade resurser i Azure.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Mer information om information om bindningar finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md) och [köutdatakonfiguration](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Lägga till kod för att använda utdatabindningen

Med köbindningen definierad kan du nu `msg` uppdatera funktionen för att ta emot utdataparametern och skriva meddelanden till kön.

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

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Observera att du *inte* behöver skriva någon kod för autentisering, hämta en köreferens eller skriva data. Alla dessa integrationsuppgifter hanteras bekvämt i Azure Functions runtime och köutdatabindning.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage-kön

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Distribuera om projektet till Azure

Nu när du har verifierat lokalt att funktionen skrev ett meddelande till Azure Storage-kön kan du distribuera om projektet för att uppdatera slutpunkten som körs på Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
I mappen *LocalFunctionsProj* använder [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) du kommandot för att distribuera`<APP_NAME>` om projektet och ersätter med namnet på appen.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

I den lokala projektmappen använder du följande Maven-kommando för att publicera om projektet:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Verifiera i Azure

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
