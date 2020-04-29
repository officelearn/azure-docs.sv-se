---
title: Använd Java och Gradle för att publicera en funktion i Azure
description: Skapa och publicera en HTTP-utlöst funktion i Azure med Java och Gradle.
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 34aab24bf39e387715cfa5783b801d45ed488750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732727"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Använd Java och Gradle för att skapa och publicera en funktion i Azure

Den här artikeln visar hur du skapar och publicerar ett Java-Function-projekt för att Azure Functions med kommando rads verktyget Gradle. När du är klar körs funktions koden i Azure i en [värd plan utan server](functions-scale.md#consumption-plan) och utlöses av en http-begäran. 

> [!NOTE]
> Om Gradle inte är ditt fördelaktiga utvecklingsverktyg kan du ta en titt på våra liknande själv studie kurser för Java-utvecklare med [maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [IntelliJ-idé](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och vs- [kod](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).

## <a name="prerequisites"></a>Krav

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](https://aka.ms/azure-jdks), version 8
- [Azure CLI]
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare
- [Gradle](https://gradle.org/), version 4,10 och senare

Du behöver också en aktiv Azure-prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="prepare-a-functions-project"></a>Förbereda ett Functions-projekt

Använd följande kommando för att klona exempelprojektet:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Öppna `build.gradle` och ändra `appName` i följande avsnitt till ett unikt namn för att undvika domän namns konflikt vid distribution till Azure. 

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

Öppna den nya function. java-filen från sökvägen *src/main/Java* i en text redigerare och granska den genererade koden. Den här koden är en [http-utlöst](functions-bindings-http-webhook.md) funktion som upprepar bröd texten i begäran. 

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör följande kommando för att skapa och sedan köra-funktions projektet:

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

Utlös funktionen från kommando raden med hjälp av följande spiral kommando i ett nytt terminalfönster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Förväntade utdata är följande:

<pre>
Hello AzureFunctions!
</pre>

[Funktions nyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) är inte obligatorisk när den körs lokalt.  
Använd `Ctrl+C` i terminalen för att stoppa funktionskoden.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

En Function-app och relaterade resurser skapas i Azure när du först distribuerar din Function-app. Innan du kan distribuera använder du kommandot [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

> [!TIP]
> Om ditt konto har åtkomst till flera prenumerationer använder du [AZ-konto uppsättning](/cli/azure/account#az-account-set) för att ange standard prenumerationen för den här sessionen. 

Använd följande kommando för att distribuera projektet till en ny function-app. 

```bash
gradle azureFunctionsDeploy
```

Detta skapar följande resurser i Azure, baserat på värdena i filen build. gradle:

+ Resurs grupp. Med namnet med _resourceGroup_ du angav.
+ Lagrings konto. Krävs av functions. Namnet genereras slumpmässigt baserat på lagrings kontots namn krav.
+ App Service plan. Server lös förbruknings plan som är värd för din Function-app i den angivna _appRegion_. Namnet genereras slumpmässigt.
+ Function-app. En Function-app är distributions-och körnings enheten för dina funktioner. Namnet är ditt _APPNAME_och läggs till med ett slumpmässigt genererat nummer. 

Distributionen paketerar också projektfilerna och distribuerar dem till den nya Function-appen med hjälp av [zip-distribution](functions-deployment-technologies.md#zip-deploy), där läget Kör-från-paket är aktiverat.

Eftersom HTTP-utlösaren som `authLevel = AuthorizationLevel.FUNCTION`vi publicerade använder måste du hämta funktions nyckeln för att anropa funktions slut punkten över http. Det enklaste sättet att hämta funktions nyckeln är från [Azure Portal].

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Hämta URL: en för HTTP-utlösaren

Du kan hämta den URL som krävs för att utlösa din funktion med funktions tangenten från Azure Portal. 

1. Bläddra till [Azure Portal], logga in, ange _APPNAME_ för din Function-app i **Sök** överst på sidan och tryck på RETUR.
 
1. I din Function-app expanderar du Functions **(skrivskyddad)**, väljer din funktion och väljer sedan **</> Hämta funktions webb adress** längst upp till höger. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Välj **Standard (funktions nyckel)** och välj **Kopiera**. 

Nu kan du använda den kopierade URL: en för att få åtkomst till din funktion.

## <a name="verify-the-function-in-azure"></a>Verifiera funktionen i Azure

För att kontrol lera att funktionen App körs på `cURL`Azure med, ersätter du URL: en från exemplet nedan med den URL som du kopierade från portalen.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Detta skickar en POST-begäran till funktions slut punkten `AzureFunctions` med i bröd texten i begäran. Du ser följande svar.

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java Functions-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
