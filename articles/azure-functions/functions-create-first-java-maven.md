---
title: "Skapa din första funktion i Azure med Java och Maven | Microsoft Docs"
description: "Skapa och publicera en enkel HTTP-utlöst funktion i Azure med Java och Maven."
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
ms.date: 11/07/2017
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 3762a6e267540ef79577c3bf94ce27b648bd3534
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Skapa din första funktion med Java och Maven (förhandsversion)

> [!NOTE] 
> Java för Azure Functions finns för närvarande som förhandsversion.

I den här snabbstarten vägleds du genom processen att skapa ett [serverfritt](https://azure.microsoft.com/overview/serverless-computing/) funktionsprojekt med Maven, testa det lokalt och distribuera det till Azure Functions. När du är klar har du en HTTP-utlöst funktionsapp som körs i Azure.

![Använda funktionen Hello World från kommandoraden med cURL](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav
För att kunna utveckla funktionsappar med Java måste du ha följande installerat:

-  [.NET Core](https://www.microsoft.com/net/core), senaste versionen.
-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)
-  [Apache Maven](https://maven.apache.org), version 3.0 eller senare.
-  [Node.js](https://nodejs.org/download/), version 8.6 eller högre.

> [!IMPORTANT] 
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="install-the-azure-functions-core-tools"></a>Installera Azure Functions Core Tools

[Azure Functions Core Tools 2.0](https://www.npmjs.com/package/azure-functions-core-tools) tillhandahåller en lokal utvecklingsmiljö för att skriva, köra och felsöka Azure Functions. Installera verktygen med [npm](https://www.npmjs.com/) som ingår i [Node.js](https://nodejs.org/).

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> Om du har problem med att installera Azure Functions Core Tools version 2.0, se informationen om [körmiljön i version 2.x](/azure/azure-functions/functions-run-local#version-2x-runtime).

## <a name="generate-a-new-functions-project"></a>Generera ett nytt funktionsprojekt

Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

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

Maven uppmanar dig att uppge de värden som behövs för att slutföra skapandet av projektet. Mer information om värdena _groupId_, _artifactId_ och _version_ finns i referensmaterialet om [namngivningskonventioner i Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Värdet _appName_ måste vara unikt i Azure, så Maven genererar som standard ett appnamn baserat på ett tidigare angivet _artifactId_. Värdet _packageName_ anger vilket Java-paket som ska användas till den genererade funktionskoden.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven skapar projektfiler i en ny mapp med namnet från _artifactId_. Den genererade koden i projektet utgörs av en enkel [HTTP-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som återspeglar en begäran:

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

Byt katalog till den nyskapade projektmappen. Skapa och kör sedan funktionen med Maven:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

Följande utdata visas när funktionen körs:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Utlös funktionen från kommandoraden med curl i en ny terminal:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Använd `Ctrl-C` i terminalen för att stoppa funktionskoden.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) och distribuera koden till en ny funktionsapp med hjälp av Maven-målet `azure-functions:deploy`.

```
az login
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

Testa funktionsappen som körs på Azure med hjälp av curl:

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Nästa steg

Du har skapat en Java-funktionsapp med en enkel HTTP-utlösare och distribuerat den till Azure Functions.

- Läs igenom [utvecklarguiden för Java-funktioner](functions-reference-java.md) för att få mer information om hur du utvecklar Java-funktioner.
- Lägg till fler funktioner med olika utlösare i projektet med Maven-målet `azure-functions:add`.
- Felsök funktioner lokalt med Visual Studio Code. När du har installerat [Java-tilläggspaketet](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) och öppnat funktionsprojektet i Visual Studio Code ska du [koppla felsökaren](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) till port 5005. Ange sedan en brytpunkt i redigeringsprogrammet och utlös funktionen medan den körs lokalt: ![Felsökningsfunktioner i Visual Studio Code](media/functions-create-java-maven/vscode-debug.png)



