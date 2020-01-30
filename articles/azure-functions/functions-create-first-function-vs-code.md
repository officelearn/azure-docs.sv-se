---
title: Skapa din första funktion i Azure med Visual Studio Code
description: Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions-tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842264"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Snabb start: skapa ett Azure Functions projekt med Visual Studio Code

I den här artikeln använder du Visual Studio Code för att skapa en funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto. 

Det finns också en [CLI-baserad version](functions-create-first-azure-function-azure-cli.md) av den här artikeln.

## <a name="prerequisites"></a>Krav

+ [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ Tillägget för Visual Studio Code. [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), aktiva LTS och underhåll LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) eller [python 3,6](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. Python 3,8 stöds inte ännu. 

+ [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell-kärna](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.Net Core SDK 2.2 +](https://www.microsoft.com/net/download)

+ [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code. 

+ Ett [Azure-konto](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) med en aktiv prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt 

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt på ditt valda språk. Senare i den här artikeln ska du publicera funktions koden till Azure. 

1. Välj Azure-ikonen i aktivitets fältet och välj sedan ikonen **Skapa nytt projekt** i avsnittet **Azure: Functions** .

    ![Välj Skapa ett nytt projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalog plats för projekt arbets ytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i prompten:

    ::: zone pivot="programming-language-csharp"

    | Uppmaning | Värde | 
    | ------ | ----- | 
    | Välj ett språk för ditt funktions projekt | C# |
    | Välj en version | Azure Functions v2 | 
    | Välj en mall för projektets första funktion | HTTP-utlösare | 
    | Ange ett funktions namn | HttpExample | 
    | Ange ett namn område | Mina. Functions | 
    | Auktorisationsnivå | Anonym | 
    | Välj hur du vill öppna projektet | Lägg till i arbets ytan |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Uppmaning | Värde | 
    | ------ | ----- | 
    | Välj ett språk för ditt funktions projekt | JavaScript | 
    | Välj en version | Azure Functions v2 | 
    | Välj en mall för projektets första funktion | HTTP-utlösare | 
    | Ange ett funktions namn | HttpExample | 
    | Auktorisationsnivå | Anonym | 
    | Välj hur du vill öppna projektet | Lägg till i arbets ytan |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Uppmaning | Värde | 
    | ------ | ----- | 
    | Välj ett språk för ditt funktions projekt | TypeScript | 
    | Välj en version | Azure Functions v2 | 
    | Välj en mall för projektets första funktion | HTTP-utlösare | 
    | Ange ett funktions namn | HttpExample | 
    | Auktorisationsnivå | Anonym | 
    | Välj hur du vill öppna projektet | Lägg till i arbets ytan |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Uppmaning | Värde | 
    | ------ | ----- | 
    | Välj ett språk för ditt funktions projekt | PowerShell | 
    | Välj en version | Azure Functions v2 | 
    | Välj en mall för projektets första funktion | HTTP-utlösare | 
    | Ange ett funktions namn | HttpExample | 
    | Auktorisationsnivå | Anonym | 
    | Välj hur du vill öppna projektet | Lägg till i arbets ytan |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Uppmaning | Värde | 
    | ------ | ----- | 
    | Välj ett språk för ditt funktions projekt | Python | 
    | Välj en version | Azure Functions v2 | 
    | Välj ett python-alias för att skapa en virtuell miljö | Python-alias | 
    | Välj en mall för projektets första funktion | HTTP-utlösare | 
    | Ange ett funktions namn | HttpExample | 
    | Auktorisationsnivå | Anonym | 
    | Välj hur du vill öppna projektet | Lägg till i arbets ytan | 

    ::: zone-end

1. Visual Studio Code gör följande:

    + Skapar ett Azure Functions-projekt i en ny arbets yta som innehåller konfigurationsfilerna [Host. JSON](functions-host-json.md) och [Local. Settings. JSON](functions-run-local.md#local-settings-file) . 

    ::: zone pivot="programming-language-csharp"

    + Skapar en [HttpExample.cs klass biblioteks fil](functions-dotnet-class-library.md#functions-class-library-project) som implementerar funktionen.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Skapar en Package. JSON-fil i rotmappen.

    + Skapar en HttpExample-mapp som innehåller [definitions filen function. JSON](functions-reference-node.md#folder-structure) och [filen index. js](functions-reference-node.md#exporting-a-function), en Node. js-fil som innehåller funktions koden.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Skapar en Package. JSON-fil och en tsconfig. JSON-fil i rotmappen.

    + Skapar en HttpExample-mapp som innehåller [definitions filen function. JSON](functions-reference-node.md#folder-structure) och [index. TS-filen](functions-reference-node.md#typescript), en typescript-fil som innehåller funktions koden.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Skapar en HttpExample-mapp som innehåller [definitions filen function. JSON](functions-reference-python.md#programming-model) och filen Run. ps1, som innehåller funktions koden.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Skapar en fil med kraven. txt på projekt nivå som visar paket som krävs av functions.
    
    + Skapar en HttpExample-mapp som innehåller [definitions filen function. JSON](functions-reference-python.md#programming-model) och den \_\_init\_\_. py-fil som innehåller funktions koden.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

1. Kopiera URL:en för HTTP-utlösaren från panelen **Utdata**. Lägg återigen till `name` frågesträngen som `?name=<yourname>` i slutet av den här URL: en och kör begäran.

    Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Klistra in den nya URL:en för HTTP-begäran i webbläsarens adressfält. I följande exempel visas svaret i webbläsaren till den fjärranslutna GET-begäran som returnerades av funktionen: 

    ![Funktionssvar i webbläsaren](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du fortsätter till nästa steg, [lägger till en Azure Storage Queue-bindning till din funktion](functions-add-output-binding-storage-queue-vs-code.md), måste du hålla alla dina resurser på plats för att bygga vidare på det du redan har gjort.

Annars kan du använda följande steg för att ta bort Function-appen och dess relaterade resurser för att undvika ytterligare kostnader.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Mer information om funktions kostnader finns i [uppskatta förbruknings plan kostnader](functions-consumption-costs.md).

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. I nästa artikel expanderar du den funktionen genom att lägga till en utgående bindning. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. 

> [!div class="nextstepaction"]
> [Lägg till en Azure Storage Queue-bindning till din funktion](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
