---
title: Använda Java och Gradle för att publicera en funktion i Azure
description: Skapa och publicera en HTTP-utlöst funktion till Azure med Java och Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 34aab24bf39e387715cfa5783b801d45ed488750
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732727"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Använda Java och Gradle för att skapa och publicera en funktion i Azure

Den här artikeln visar hur du skapar och publicerar ett Java-funktionsprojekt till Azure Functions med kommandoradsverktyget Gradle. När du är klar körs din funktionskod i Azure i en [serverlös värdplan](functions-scale.md#consumption-plan) och utlöses av en HTTP-begäran. 

> [!NOTE]
> Om Gradle inte är din föredragna utvecklingsverktyg, kolla in våra liknande tutorials för Java-utvecklare som använder [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Krav

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](https://aka.ms/azure-jdks), version 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 eller senare
- [Gradle](https://gradle.org/), version 4.10 och högre

Du behöver också en aktiv Azure-prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="prepare-a-functions-project"></a>Förbereda ett functions-projekt

Använd följande kommando för att klona exempelprojektet:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Öppna `build.gradle` och `appName` ändra i följande avsnitt till ett unikt namn för att undvika domännamnskonflikt när du distribuerar till Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Öppna den nya filen Function.java från *sökvägen src/main/java* i en textredigerare och granska den genererade koden. Den här koden är en [HTTP-utlöst](functions-bindings-http-webhook.md) funktion som ekar brödtexten i begäran. 

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör följande kommando för att bygga och kör sedan funktionsprojektet:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Du ser utdata som följande från Azure Functions Core Tools när du kör projektet lokalt:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Utlösa funktionen från kommandoraden med följande cURL-kommando i ett nytt terminalfönster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Den förväntade produktionen är följande:

<pre>
Hello AzureFunctions!
</pre>

[Funktionsnyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) krävs inte när du kör lokalt.  
Använd `Ctrl+C` i terminalen för att stoppa funktionskoden.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

En funktionsapp och relaterade resurser skapas i Azure när du först distribuerar din funktionsapp. Innan du kan distribuera använder du azure [CLI-kommandot az login](/cli/azure/authenticate-azure-cli) för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

> [!TIP]
> Om ditt konto kan komma åt flera prenumerationer använder du [az-kontouppsättningen](/cli/azure/account#az-account-set) för att ange standardprenumerationen för den här sessionen. 

Använd följande kommando för att distribuera projektet till en ny funktionsapp. 

```bash
gradle azureFunctionsDeploy
```

Detta skapar följande resurser i Azure, baserat på värdena i filen build.gradle:

+ Resursgrupp. Namngiven med den _resursGrupp_ du angav.
+ Lagringskonto. Krävs av Functions. Namnet genereras slumpmässigt baserat på krav på lagringskontonamn.
+ App Service plan. Serverlös förbrukningsplan som är värd för din funktionsapp i den angivna _appenRegion_. Namnet genereras slumpmässigt.
+ Funktionsapp. En funktionsapp är distributions- och körningsenheten för dina funktioner. Namnet är ditt _appName_, läggs till med ett slumpmässigt genererat nummer. 

Distributionen paketerar också projektfilerna och distribuerar dem till den nya funktionsappen med [zip-distribution](functions-deployment-technologies.md#zip-deploy), med körning från paketläge aktiverat.

Eftersom HTTP-utlösaren `authLevel = AuthorizationLevel.FUNCTION`som vi publicerade använder måste du hämta funktionsnyckeln för att anropa funktionsslutpunkten via HTTP. Det enklaste sättet att hämta funktionsnyckeln är från [Azure-portalen].

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Hämta URL:en för HTTP-utlösare

Du kan få den URL som krävs för att utlösa din funktion, med funktionsnyckeln, från Azure-portalen. 

1. Bläddra till [Azure-portalen,]logga in, skriv _appenName_ för din funktionsapp i **Sök** högst upp på sidan och tryck på retur.
 
1. I funktionsappen expanderar du **Funktioner (Skrivskyddade)**, väljer din funktion och väljer sedan **</> Hämta funktions-URL** längst upp till höger. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Välj **standard (funktionstangent)** och välj **Kopiera**. 

Du kan nu använda den kopierade WEBBADRESSEN för att komma åt din funktion.

## <a name="verify-the-function-in-azure"></a>Verifiera funktionen i Azure

Om du vill verifiera funktionsappen som körs på Azure med `cURL`ersätter du URL:en från exemplet nedan med webbadressen som du kopierade från portalen.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Detta skickar en POST-begäran till `AzureFunctions` funktionsslutpunkten med i brödtexten för begäran. Du ser följande svar.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java-funktionsprojekt med en HTTP-utlöst funktion, kört det på din lokala dator och distribuerat det till Azure. Utöka din funktion genom att...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage-köutdatabindning](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
