---
title: "Skapa din första funktion i Azure med Java och Maven | Microsoft Docs"
description: "Skapa och publicera en enkel HTTP aktiveras funktionen för Azure med Java och Maven."
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 50fd59c288312c7aa5ffe6abf1318a5ec2f406e6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Skapa din första funktion med Java och Maven (förhandsgranskning)

Den här snabbstarten hjälper att skapa en [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) funktionen projekt med Maven lokal testning och distribuera den till Azure-funktioner. När du är klar har du en funktion som utlöses av HTTP-app som körs i Azure.

 ![Använda funktionen Hello World från kommandoraden med cURL](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav
För att utveckla funktioner app med Java, måste du ha följande installerat:

-  [.NET core](https://www.microsoft.com/net/core), senaste versionen.
-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 1.8.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)
-  [Apache Maven](https://maven.apache.org), version 3.0 eller senare.
-  [Node.js](https://nodejs.org/download/), version 8,6 eller senare.

> [!IMPORTANT] 
> Miljövariabeln JAVA_HOME måste anges i JDK-installationsplatsen för att slutföra den här snabbstarten.

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core-verktyg

Den [Azure Functions Core verktyg 2.0](https://www.npmjs.com/package/azure-functions-core-tools) tillhandahålla en lokal utvecklingsmiljö för att skriva, köra och felsökning Azure Functions. Installera verktyg med [npm](https://www.npmjs.com/), som ingår i [Node.js](https://nodejs.org/).

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> Om du har problem med att installera Azure Functions grundläggande verktyg version 2.0, se [Version 2.x runtime](/azure/azure-functions/functions-run-local#version-2x-runtime).

## <a name="generate-a-new-functions-project"></a>Generera ett nytt projekt funktioner

I en tom mapp, kör du följande kommando för att skapa projektet funktioner från en [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven uppmanas du att värden som behövs för att skapa projektet är klar. För _groupId_, _artefakt-ID_, och _version_ värden, finns det [Maven namnkonventioner](https://maven.apache.org/guides/mini/guide-naming-conventions.html) referens. Den _appName_ värdet måste vara unikt i Azure, så Maven genererar ett programnamn baserat på den tidigare angivna _artefakt-ID_ som standard. Den _packageName_ värdet avgör Java-paketet för genererade funktionskoden.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven skapar projektfiler i en ny mapp med namnet _artefakt-ID_. Den genererade koden i projektet är ett enkelt [HTTP utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som ekon brödtexten i begäran:

```java
public class Function {
    @FunctionName("hello")
    public String hello(@HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req,
                        ExecutionContext context) {
        return String.format("Hello, %s!", req);
    }
}
```

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Ändra katalogen till mappen nyligen skapade projekt och skapa och köra funktionen med Maven:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Du kan se dessa utdata när funktionen körs:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Aktivera funktionen från kommandoraden med curl i en ny terminal:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Använd `Ctrl-C` i terminalen för att stoppa funktionskoden.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Azure Functions distribuera processen använder autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) och distribuera din kod till en ny funktion appen med hjälp av `azure-functions:deploy` Maven mål.

```
az login
mvn azure-functions:deploy
```

När distribuera är klar kan du se den URL som du kan använda för att få åtkomst till din funktionsapp i Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Testa funktionen appen körs på Azure med hjälp av curl:

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Nästa steg

Du har skapat en Java-funktionsapp med en enkel HTTP-utlösare och distribueras till Azure Functions.

- Granska de [Java funktioner Utvecklarhandbok](functions-reference-java.md) för mer information om hur du utvecklar Java-funktioner.
- Lägga till ytterligare funktioner med olika utlösare i projektet med den `azure-functions:add` Maven mål.
- Felsöka funktioner lokalt med Visual Studio-koden. Med den [Java tillägget pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) installerat och med projektet funktioner som är öppna i Visual Studio Code [koppla felsökaren](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) till port 5005. Sedan ange en brytpunkt i redigeraren och utlösa funktionen medan den körs lokalt: ![felsökningsfunktioner i Visual Studio Code](media/functions-create-java-maven/vscode-debug.png)



