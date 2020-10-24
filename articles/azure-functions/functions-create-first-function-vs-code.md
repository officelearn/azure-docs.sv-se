---
title: Skapa din första funktion i Azure med Visual Studio Code
description: Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions-tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo, devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 4e50f6b2770f9f787c92e7a9c66f72cdbb252a94
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519698"
---
# <a name="quickstart-create-a-function-in-azure-using-visual-studio-code"></a>Snabbstart: Skapa en funktion i Azure med hjälp av Visual Studio Code

::: zone pivot="programming-language-csharp"
I den här artikeln använder du Visual Studio Code för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.
::: zone-end
::: zone pivot="programming-language-javascript"
I den här artikeln använder du Visual Studio Code för att skapa en JavaScript-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.
::: zone-end
::: zone pivot="programming-language-typescript"
I den här artikeln använder du Visual Studio Code för att skapa en TypeScript-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.
::: zone-end 
::: zone pivot="programming-language-powershell"
I den här artikeln använder du Visual Studio Code för att skapa en PowerShell-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.
::: zone-end
::: zone pivot="programming-language-python"
I den här artikeln använder du Visual Studio Code för att skapa en python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.
::: zone-end
::: zone pivot="programming-language-java"
I den här artikeln använder du Visual Studio Code för att skapa en Java-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.
::: zone-end

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
Det finns också en [CLI-baserad version](functions-create-first-azure-function-azure-cli.md) av den här artikeln.
::: zone-end
::: zone pivot="programming-language-java"
> [!NOTE]
> Om Visual Studio Code inte är ett fördelaktigt utvecklingsverktyg kan du titta på våra liknande själv studie kurser för Java-utvecklare med [maven](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java), [Gradle](./functions-create-first-java-gradle.md) och [IntelliJ-idé](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"
- [Node.js](https://nodejs.org/)som krävs av Windows för NPM. Endast [aktiva LTS-och underhålls LTS-versioner](https://nodejs.org/about/releases/). Använd `node --version` kommandot för att kontrol lera din version.
    Krävs inte för lokal utveckling på macOS och Linux. 
::: zone-end 
::: zone pivot="programming-language-javascript,programming-language-typescript"
- [Node.js](https://nodejs.org/), aktiva LTS och UNDERHÅLLs LTS-versioner (10.14.1 rekommenderas). Använd `node --version` kommandot för att kontrol lera din version.
::: zone-end
::: zone pivot="programming-language-python"
- [Python 3,8](https://www.python.org/downloads/release/python-381/), [python 3,7](https://www.python.org/downloads/release/python-375/), [python 3,6](https://www.python.org/downloads/release/python-368/) stöds av Azure Functions (x64).
::: zone-end 
::: zone pivot="programming-language-powershell"
- [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

- Både [.net core 3,1](https://www.microsoft.com/net/download) och [.net Core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.2)
::: zone-end
::: zone pivot="programming-language-java"
- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11.

- [Apache maven](https://maven.apache.org), version 3,0 eller senare.
::: zone-end
- [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"
- [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-python"
- [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
- [PowerShell-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).
::: zone-end
::: zone pivot="programming-language-java"
- [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end

- [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt på ditt valda språk. Senare i den här artikeln ska du publicera funktions koden till Azure. 

1. Välj Azure-ikonen i aktivitets fältet och välj sedan ikonen **Skapa nytt projekt** i avsnittet **Azure: Functions** .

    ![Välj Skapa ett nytt projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalog plats för projekt arbets ytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i prompten:

    ::: zone pivot="programming-language-csharp"
    - **Välj ett språk för ditt funktions projekt**: Välj `C#` .
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - **Välj ett språk för ditt funktions projekt**: Välj `JavaScript` .
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - **Välj ett språk för ditt funktions projekt**: Välj `TypeScript` .
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - **Välj ett språk för ditt funktions projekt**: Välj `PowerShell` .
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - **Välj ett språk för ditt funktions projekt**: Välj `Python` .

    - **Välj ett python-alias för att skapa en virtuell miljö**: Välj platsen för din python-tolk. Om platsen inte visas anger du den fullständiga sökvägen till din python-binärfil.
    ::: zone-end

    ::: zone pivot="programming-language-java"
    - **Välj ett språk för ditt funktions projekt**: Välj `Java` .

    - **Välj en version av Java**: Välj `Java 8` eller `Java 11` , där dina funktioner körs i Azure. Välj en Java-version som du har verifierat lokalt.

    - **Ange ett grupp-ID**: Välj `com.function` .

    - **Ange ett artefakt-ID**: Välj `myFunction` .

    - **Ange en version**: Välj `1.0-SNAPSHOT` .

    - **Ange ett paket namn**: Välj `com.function` .

    - **Ange ett namn på appen**: Välj `myFunction-12345` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    - **Välj en mall för projektets första funktion**: Välj `HTTP trigger` .
  
    - **Ange ett funktions namn**: typ `HttpExample` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    - **Ange ett namn område**: typ `My.Functions` .
    ::: zone-end
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    - **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om autentiseringsnivå finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).
    ::: zone-end
    - **Välj hur du vill öppna projektet**: Välj `Add to workspace` .

1. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [genererade projektfiler](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

1. Gå tillbaka till avsnittet **Azure: Functions** i sido fältet och expandera den nya Function-appen under din prenumeration. Expandera **funktioner**, högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på **HttpExample**och välj sedan **Kopiera funktions webb adress**.

    ![Kopiera funktions webb adressen för den nya HTTP-utlösaren](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Klistra in URL: en för HTTP-begäran i webbläsarens Adress fält, Lägg till `name` frågesträngen som `?name=Functions` i slutet av den här URL: en och kör sedan begäran. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

    ```http
    http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions
    ```

    I följande exempel visas svaret i webbläsaren till den fjärranslutna GET-begäran som returnerades av funktionen: 

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
