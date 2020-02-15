---
title: Använd Java och Maven för att publicera en funktion i Azure
description: Skapa och publicera en HTTP-utlöst funktion i Azure med Java och Maven.
author: rloutlaw
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: f226736050319d57cd0bc123fdb2211e0faeae11
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208854"
---
# <a name="quickstart-use-java-and-maven-to-create-and-publish-a-function-to-azure"></a>Snabb start: Använd Java och Maven för att skapa och publicera en funktion i Azure

Den här artikeln visar hur du skapar och publicerar en Java-funktion för att Azure Functions med kommando rads verktyget Maven. När du är klar körs funktions koden i Azure i en [värd plan utan server](functions-scale.md#consumption-plan) och utlöses av en http-begäran.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Förutsättningar

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](https://aka.ms/azure-jdks), version 8
- [Apache maven](https://maven.apache.org), version 3,0 eller senare
- [Azure CLI]
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare

Du behöver också en aktiv Azure-prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="generate-a-new-functions-project"></a>Generera ett nytt funktionsprojekt

Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/macOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Om du har problem med att köra kommandot bör du ta en titt på vilken `maven-archetype-plugin` version som används. Eftersom du kör kommandot i en tom katalog utan `.pom` fil, kanske det försöker använda ett plugin-program av den äldre versionen från `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` om du har uppgraderat maven från en äldre version. I så fall kan du försöka ta bort `maven-archetype-plugin` katalogen och köra kommandot igen.

### <a name="windows"></a>Windows

```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-archetype"
```

```cmd
mvn archetype:generate ^
    "-DarchetypeGroupId=com.microsoft.azure" ^
    "-DarchetypeArtifactId=azure-functions-archetype"
```

Maven ber dig om värden som behövs för att slutföra genereringen av projektet vid distribution. Ange följande värden när du uppmanas till det:

| Värde | Beskrivning |
| ----- | ----------- |
| **groupId** | Ett värde som unikt identifierar ditt projekt i alla projekt, efter [paket namngivnings regler](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) för Java. I exemplen i den här snabb starten används `com.fabrikam.functions`. |
| **artifactId** | Ett värde som är jar-namn, utan versions nummer. I exemplen i den här snabb starten används `fabrikam-functions`. |
| **2.0.1** | Välj standardvärdet för `1.0-SNAPSHOT`. |
| **paketfilerna** | Ett värde som är Java-paketet för den genererade funktions koden. Använd standardvärdet. I exemplen i den här snabb starten används `com.fabrikam.functions`. |
| **Program** | Globalt unikt namn som identifierar din nya Function-app i Azure. Använd standard, vilket är _artifactId_ som läggs till med ett slumpmässigt nummer. Anteckna det här värdet, du behöver det senare. |
| **appRegion** | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. Standardvärdet är `westus`. Kör det här [Azure CLI] -kommandot för att hämta en lista över alla regioner:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Namnet på den nya [resurs gruppen](../azure-resource-manager/management/overview.md) där du vill skapa din Function-app. Använd `myResourceGroup`som används i exemplen i den här snabb starten. En resurs grupp måste vara unik för din Azure-prenumeration.|

Skriv `Y` eller tryck på RETUR för att bekräfta.

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions`. 

Öppna den nya function. java-filen från sökvägen *src/main/Java* i en text redigerare och granska den genererade koden. Den här koden är en [http-utlöst](functions-bindings-http-webhook.md) funktion som upprepar bröd texten i begäran. 

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Kör följande kommando, som ändrar katalogen till den nyss skapade projektmappen, och sedan skapar och kör-funktions projektet:

```console
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Du ser utdata som följande från Azure Functions Core Tools när du kör projektet lokalt:

```Output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
...
```

Utlös funktionen från kommando raden med hjälp av sväng i ett nytt terminalfönster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

```Output
Hello AzureFunctions!
```
[Funktions nyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) är inte obligatorisk när den körs lokalt. Använd `Ctrl+C` i terminalen för att stoppa funktionskoden.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

En Function-app och relaterade resurser skapas i Azure när du först distribuerar din Function-app. Innan du kan distribuera använder du kommandot [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

> [!TIP]
> Om ditt konto har åtkomst till flera prenumerationer använder du [AZ-konto uppsättning](/cli/azure/account#az-account-set) för att ange standard prenumerationen för den här sessionen. 

Använd följande maven-kommando för att distribuera projektet till en ny function-app. 

```azurecli
mvn azure-functions:deploy
```

Det här `azure-functions:deploy` maven-målet skapar följande resurser i Azure:

+ Resurs grupp. Med namnet med _resourceGroup_ du angav.
+ Lagrings konto. Krävs av functions. Namnet genereras slumpmässigt baserat på lagrings kontots namn krav.
+ App Service-plan. Server lös värd för din Function-app i den angivna _appRegion_. Namnet genereras slumpmässigt.
+ Function-app. En Function-app är distributions-och körnings enheten för dina funktioner. Namnet är ditt _APPNAME_och läggs till med ett slumpmässigt genererat nummer. 

Distributionen paketerar också projektfilerna och distribuerar dem till den nya Function-appen med hjälp av [zip-distribution](functions-deployment-technologies.md#zip-deploy), där läget Kör-från-paket är aktiverat.

När distributionen har slutförts visas URL: en som du kan använda för att få åtkomst till dina funktions program slut punkter. Eftersom den HTTP-utlösare som vi publicerade använder `authLevel = AuthorizationLevel.FUNCTION`måste du hämta funktions nyckeln för att anropa funktions slut punkten över HTTP. Det enklaste sättet att hämta funktions nyckeln är från [Azure Portal].

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Hämta URL: en för HTTP-utlösaren

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

Du kan hämta den URL som krävs för att utlösa din funktion med funktions tangenten från Azure Portal. 

1. Bläddra till [Azure Portal], logga in, ange _APPNAME_ för din Function-app i **Sök** överst på sidan och tryck på RETUR.
 
1. I din Function-app expanderar du Functions **(skrivskyddad)** , väljer din funktion och väljer sedan **</> Hämta funktions webb adress** längst upp till höger. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-java-maven/get-function-url-portal.png)

1. Välj **Standard (funktions nyckel)** och välj **Kopiera**. 

Nu kan du använda den kopierade URL: en för att få åtkomst till din funktion.

## <a name="verify-the-function-in-azure"></a>Verifiera funktionen i Azure

Om du vill kontrol lera att Function-appen körs på Azure med hjälp av `cURL`ersätter du URL: en från exemplet nedan med den URL som du kopierade från portalen.

```azurecli
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Detta skickar en POST-begäran till funktions slut punkten med `AzureFunctions` i bröd texten i begäran. Du ser följande svar.

```Output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java Functions-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
