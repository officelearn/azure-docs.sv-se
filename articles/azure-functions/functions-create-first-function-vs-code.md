---
title: Skapa din första funktion i Azure med Visual Studio Code
description: Skapa och publicera en enkel HTTP-utlöst funktion till Azure med Azure Functions-tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 3e1cf95d3c6ac8918e9e7e5593d687ee2d398810
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886627"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Snabbstart: Skapa ett Azure Functions-projekt med Visual Studio-kod

I den här artikeln använder du Visual Studio Code för att skapa en funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa miljön i Azure Functions. Om du slutför den här snabbstarten medför en liten kostnad på några USD-cent eller mindre i ditt Azure-konto. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Det finns också en [CLI-baserad version](functions-create-first-azure-function-azure-cli.md) av den här artikeln.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Om VS Code inte är din föredragna utvecklingsverktyg, kolla in våra liknande tutorials för Java devlopers med [Maven,](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java) [Gradle](/azure/azure-functions/functions-create-first-java-gradle) och [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions).
::: zone-end  

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar måste du se till att du har följande krav på plats:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Nod.js](https://nodejs.org/), krävs av Windows för npm. Endast [LTS- och underhålls-LTS-versioner](https://nodejs.org/about/releases/). Använd `node --version` kommandot för att kontrollera din version.
    Krävs inte för lokal utveckling på macOS och Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js,](https://nodejs.org/)Active LTS och Underhåll LTS-versioner (10.14.1 rekommenderas). Använd `node --version` kommandot för att kontrollera din version.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) stöds av Azure Functions (x64).
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), version 8.

+ [Apache Maven](https://maven.apache.org), version 3.0 eller högre.
::: zone-end  
+ [Visual Studio-kod](https://code.visualstudio.com/) på en av plattformarna [som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio-kod.  
::: zone-end  
::: zone pivot="programming-language-python"
+ [Python-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-python.python) för Visual Studio-kod.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ [PowerShell-tillägget för Visual Studio-kod](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java-förlängningspaketet](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio-kod. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt 

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions-projekt på ditt valda språk. Senare i den här artikeln publicerar du din funktionskod till Azure. 

1. Välj Azure-ikonen i aktivitetsfältet och välj sedan ikonen **Skapa nytt projekt i** azure: functions i området **Azure: Functions.**

    ![Välj Skapa ett nytt projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > Dessa steg har utformats för att slutföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information vid uppmaningarna:

    ::: zone pivot="programming-language-csharp"
    + **Välj ett språk för funktionsprojektet**: Välj `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Välj ett språk för funktionsprojektet**: Välj `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Välj ett språk för funktionsprojektet**: Välj `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Välj ett språk för funktionsprojektet**: Välj `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Välj ett språk för funktionsprojektet**: Välj `Python`.

    + **Välj ett Python-alias för att skapa en virtuell miljö:** Välj platsen för din Python-tolk. Om platsen inte visas skriver du in den fullständiga sökvägen till din Python-binär.  
    ::: zone-end

    ::: zone pivot="programming-language-java"  
    + **Välj ett språk för funktionsprojektet**: Välj `Java`.

    + **Ange ett grupp-ID:** Välj `com.function`.

    + **Ange ett artefakt-ID:** Välj `myFunction`.

    + **Ange en**version `1.0-SNAPSHOT`: Välj .

    + **Ange ett paketnamn**: Välj `com.function`.

    + **Ange ett appnamn**: Välj `myFunction-12345`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Välj en mall för projektets**första `HTTP trigger`funktion : Välj .
    
    + **Ange ett funktionsnamn**: Type `HttpExample`.
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Ange ett namnområde:** Skriv `My.Functions`. 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Auktoriseringsnivå**: Välj `Anonymous`, vilket gör att vem som helst kan anropa din funktionsslutpunkt. Mer information om auktoriseringsnivå finns i [Auktoriseringsnycklar](functions-bindings-http-webhook-trigger.md#authorization-keys).
    ::: zone-end  
    + **Välj hur du vill öppna**projektet `Add to workspace`: Välj .

1. Med den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [Genererade projektfiler](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

När du har verifierat att funktionen körs korrekt på din lokala dator är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

1. Tillbaka i **området Azure: Funktioner** i sidofältet expanderar du den nya funktionsappen under din prenumeration. Expandera **funktioner**, högerklicka (Windows) eller Ctrl + klicka (macOS) på **HttpExample**och välj sedan **Url för kopieringsfunktionen**.

    ![Kopiera funktions-URL:en för den nya HTTP-utlösaren](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Klistra in den här URL:en för HTTP-begäran `name` i `?name=Functions` webbläsarens adressfält, lägg till frågesträngen i slutet av den här URL:en och kör sedan begäran. Den URL som anropar den HTTP-utlösta funktionen ska ha följande format:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    I följande exempel visas svaret i webbläsaren på den fjärrfråkomsbegäran som returneras av funktionen: 

    ![Funktionssvar i webbläsaren](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du fortsätter till nästa steg, [Lägg till en Azure Storage kö bindning till din funktion](functions-add-output-binding-storage-queue-vs-code.md), måste du hålla alla dina resurser på plats för att bygga vidare på vad du redan har gjort.

Annars kan du använda följande steg för att ta bort funktionsappen och dess relaterade resurser för att undvika att ådra dig ytterligare kostnader.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Mer information om Funktionerskostnader finns i [Uppskatta kostnader för förbrukningsplan](functions-consumption-costs.md).

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. I nästa artikel expanderar du den funktionen genom att lägga till en utdatabindning. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. 

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage-köbindning i din funktion](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
