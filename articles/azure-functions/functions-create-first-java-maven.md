---
title: Använda Java och Maven/Gradle för att publicera en funktion i Azure
description: Skapa och publicera en HTTP-utlöst funktion till Azure med Java och Maven eller Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136875"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Snabbstart: Använda Java och Maven/Gradle för att skapa och publicera en funktion i Azure

Den här artikeln visar hur du skapar och publicerar en Java-funktion till Azure Functions med kommandoradsverktyget Maven/Gradle. När du är klar körs din funktionskod i Azure i en [serverlös värdplan](functions-scale.md#consumption-plan) och utlöses av en HTTP-begäran.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Krav

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](https://aka.ms/azure-jdks), version 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 eller senare
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/), version 4.10 och högre
::: zone-end 

Du behöver också en aktiv Azure-prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="prepare-a-functions-project"></a>Förbereda ett functions-projekt

::: zone pivot="java-build-tools-maven" 
Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Om du använder Powershell, kom ihåg att lägga till "" runt parametrar.

> [!NOTE]
> Om du har problem med att köra kommandot kan `maven-archetype-plugin` du ta en titt på vilken version som används. Eftersom du kör kommandot i en `.pom` tom katalog utan fil, kan det vara `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` att försöka använda ett plugin-program av den äldre versionen från om du uppgraderade din Maven från en äldre version. Försök i så `maven-archetype-plugin` fall att ta bort katalogen och köra kommandot igen.

Maven ber dig om värden som behövs för att slutföra generera projektet på distributionen. Ange följande värden när du uppmanas att göra det:

| Värde | Beskrivning |
| ----- | ----------- |
| **groupId** | Ett värde som unikt identifierar ditt projekt i alla projekt, enligt [paketnamngivningsreglerna](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) för Java. Exemplen i den `com.fabrikam.functions`här snabbstarten används . |
| **artifactId (artefaktId)** | Ett värde som är namnet på burken, utan versionsnummer. Exemplen i den `fabrikam-functions`här snabbstarten används . |
| **Version** | Välj standardvärdet `1.0-SNAPSHOT`för . |
| **Paket** | Ett värde som är Java-paketet för den genererade funktionskoden. Använd standardvärdet. Exemplen i den `com.fabrikam.functions`här snabbstarten används . |
| **Appname** | Globalt unikt namn som identifierar din nya funktionsapp i Azure. Använd standard, som är _artifactId_ läggas med ett slumptal. Anteckna det här värdet, du behöver det senare. |
| **appRegion** | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. Standardvärdet är `westus`. Kör det här [Azure CLI-kommandot] för att få en lista över alla regioner:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resursGrupp** | Namn på den nya [resursgruppen](../azure-resource-manager/management/overview.md) där funktionsappen ska skapas. Använd `myResourceGroup`, som används av exempel i den här snabbstarten. En resursgrupp måste vara unik för din Azure-prenumeration.|

Skriv `Y` eller tryck på Retur för att bekräfta.

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions`. Kör följande kommando om du vill ändra katalogen till den skapade projektmappen.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
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
::: zone-end

Öppna den nya filen Function.java från *sökvägen src/main/java* i en textredigerare och granska den genererade koden. Den här koden är en [HTTP-utlöst](functions-bindings-http-webhook.md) funktion som ekar brödtexten i begäran. 

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör följande kommando för att bygga och kör sedan funktionsprojektet:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

Du kommer att se utdata som följande från Azure Functions Core Tools när du kör projektet lokalt:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Utlösa funktionen från kommandoraden med cURL i ett nytt terminalfönster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
[Funktionsnyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) krävs inte när du kör lokalt. Använd `Ctrl+C` i terminalen för att stoppa funktionskoden.

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


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Detta skapar följande resurser i Azure:

+ Resursgrupp. Namngiven med den _resursGrupp_ du angav.
+ Lagringskonto. Krävs av Functions. Namnet genereras slumpmässigt baserat på krav på lagringskontonamn.
+ App serviceplan. Serverlös hosting för din funktionsapp i den angivna _appenRegion_. Namnet genereras slumpmässigt.
+ Funktionsapp. En funktionsapp är distributions- och körningsenheten för dina funktioner. Namnet är ditt _appName_, läggs till med ett slumpmässigt genererat nummer. 

Distributionen paketerar också projektfilerna och distribuerar dem till den nya funktionsappen med [zip-distribution](functions-deployment-technologies.md#zip-deploy), med körning från paketläge aktiverat.

När distributionen är klar visas webbadressen som du kan använda för att komma åt slutpunkterna för funktionsappen. Eftersom HTTP-utlösaren `authLevel = AuthorizationLevel.FUNCTION`som vi publicerade använder måste du hämta funktionsnyckeln för att anropa funktionsslutpunkten via HTTP. Det enklaste sättet att hämta funktionsnyckeln är från [Azure-portalen].

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Hämta URL:en för HTTP-utlösare

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

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

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java-funktionsprojekt med en HTTP-utlöst funktion, kört det på din lokala dator och distribuerat det till Azure. Utöka din funktion genom att...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage-köutdatabindning](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure-portal]: https://portal.azure.com
