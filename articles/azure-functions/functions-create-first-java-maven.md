---
title: Använd Java och Maven för att publicera en funktion – Azure Functions
description: Skapa och publicera en enkel HTTP-utlöst funktion i Azure med Java och Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.topic: quickstart
ms.devlang: java
ms.date: 08/10/2018
ms.author: routlaw
ms.reviewer: glenga
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 5c5a0285a827a7990a11eb6ef6445e7d84189767
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096861"
---
# <a name="quickstart-use-java-to-create-and-publish-a-function-to-azure-functions"></a>Snabbstart: Använd Java för att skapa och publicera en funktion som Azure Functions

Den här artikeln vägleder dig genom att använda kommando rads verktyget Maven för att bygga och publicera en Java-funktion för att Azure Functions. När du är klar körs funktionskoden på [förbrukningsplanen](functions-scale.md#consumption-plan) i Azure och kan utlösas med hjälp av en HTTP-begäran.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](https://aka.ms/azure-jdks), version 8
- [Apache maven](https://maven.apache.org), version 3,0 eller senare
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare

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
> Om du har problem med att köra kommandot bör du ta en titt på vilken `maven-archetype-plugin` version som används. Eftersom du kör kommandot i en tom katalog utan `.pom` fil, kan det försöka använda ett plugin-program av den äldre versionen från `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` om du har uppgraderat maven från en äldre version. I så fall kan du `maven-archetype-plugin` försöka ta bort katalogen och köra kommandot igen.

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

Maven uppmanar dig att ange de värden som behövs för att projektet ska genereras. Mer information om värdena _groupId_, _artifactId_ och _version_ finns i referensmaterialet om [namngivningskonventioner i Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Värdet _appName_ måste vara unikt i Azure, så Maven genererar som standard ett appnamn baserat på ett tidigare angivet _artifactId_. Värdet _packageName_ anger vilket Java-paket som ska användas till den genererade funktionskoden.

Identifierarna `com.fabrikam.functions` och `fabrikam-functions` nedan används som exempel och för att göra senare steg i den här snabbstarten lättare att läsa. Du bör ange dina egna värden för Maven i det här steget.

```Output
Define value for property 'groupId' (should match expression '[A-Za-z0-9_\-\.]+'): com.fabrikam.functions
Define value for property 'artifactId' (should match expression '[A-Za-z0-9_\-\.]+'): fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus: :
Define value for property 'resourceGroup' java-functions-group: :
Confirm properties configuration: Y
```

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, `fabrikam-functions` i det här exemplet. Den färdiga koden för att köra genererad kod i projektet är en [http-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som skickar ut bröd texten i begäran. Ersätt *src/main/Java/com/Fabrikam/functions. java* med följande kod: 

```java
package com.fabrikam.functions;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class Function {
    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java
     * 2. curl {your host}/api/HttpTrigger-Java?name=HTTP%20Query
     */
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}

```

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Ändra katalogen till den nyligen skapade projektmappen (den som innehåller din värd. JSON-och POM. XML-fil) och skapa och kör funktionen med maven:

```CMD
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Om du får det här undantaget: `javax.xml.bind.JAXBException` med Java 9 så hittar du en lösning på [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Du kan se dessa utdata när funktionen körs lokalt på datorn och är redo att svara på HTTP-begäranden:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/HttpTrigger-Java
```

Utlös funktionen från kommandoraden med curl i ett nytt terminalfönster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java -d LocalFunction
```

```Output
Hello LocalFunction!
```

Använd `Ctrl-C` i terminalen för att stoppa funktionskoden.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter.

```azurecli
az login
```

Distribuera koden till en ny funktionsapp med hjälp av Maven-målet `azure-functions:deploy`. Detta utför en [zip-distribution med kör från paket](functions-deployment-technologies.md#zip-deploy) läge aktiverat.

> [!NOTE]
> När du använder Visual Studio Code för att distribuera din Function-app måste du komma ihåg att välja en icke-kostnads fri prenumeration eller så får du ett fel meddelande. Du kan titta på din prenumeration på vänster sida av IDE-nätverket.

```azurecli
mvn azure-functions:deploy
```

När distributionen är klar kan du se den webbadress som används för att få åtkomst till din funktionsapp i Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Testa funktionsappen som körs på Azure med hjälp av `cURL`. Du måste ändra URL-adressen från exemplet till den distribuerade URL-adressen för din egen funktionsapp från föregående steg.

> [!NOTE]
> Se till att du ställer in **åtkomst behörighet** till `Anonymous`. När du väljer standard nivån för `Function`, måste du presentera [funktions nyckeln](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) i begär Anden för att få åtkomst till funktions slut punkten.

```azurecli
curl -w "\n" https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Göra ändringar och omdistribuera

Redigera källfilen `src/main.../Function.java` i det genererade projektet för att ändra texten som returneras av funktionsappen. Ändra den här raden:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
```

Till följande:

```java
return request.createResponseBuilder(HttpStatus.OK).body("Hi, " + name).build();
```

Spara ändringarna. Kör mvn Clean Package och distribuera om genom att `azure-functions:deploy` köra från terminalen som tidigare. Funktionsappen uppdateras och den här begäran:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Får uppdaterade utdata:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Nästa steg

Du har skapat en Java-funktionsapp med en enkel HTTP-utlösare och distribuerat den till Azure Functions.

- Läs igenom [utvecklarguiden för Java-funktioner](functions-reference-java.md) för att få mer information om hur du utvecklar Java-funktioner.
- Lägg till fler funktioner med olika utlösare i projektet med Maven-målet `azure-functions:add`.
- Skriv och felsök funktioner lokalt med [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) och [Eclipse](functions-create-maven-eclipse.md). 
- Felsök funktioner distribuerade i Azure med Visual Studio Code. Instruktioner finns i dokumentationen för [serverlösa Java-program](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) för Visual Studio Code.
