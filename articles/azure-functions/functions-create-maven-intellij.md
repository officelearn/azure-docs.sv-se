---
title: Skapa en Azure-funktion med Java och IntelliJ | Microsoft Docs
description: Lär dig hur du skapar och publicerar en enkel HTTP-utlöst, serverlös app på Azure med Java och IntelliJ.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure functions, funktioner, händelsebearbetning, beräkning, serverlös arkitektur, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: da93c60b52edf509900adf89fb688a0596d9763b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342247"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Skapa din första Azure-funktion med Java och IntelliJ

Den här artikeln visar:
- Så här skapar du en [serverlös](https://azure.microsoft.com/overview/serverless-computing/) function-projekt med IntelliJ IDEA och Apache Maven
- Steg för testning och felsökning av funktionen i den integrerade utvecklingsmiljön (IDE) på din dator
- Instruktioner för att distribuera function-projekt till Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill utveckla en funktion med Java och IntelliJ, installera följande programvara:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community eller Ultimate-versioner med Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste anges till installationsplatsen för JDK-Paketet för att slutföra stegen i den här artikeln.

 Vi rekommenderar att du installerar [Azure Functions Core Tools version 2](functions-run-local.md#v2). Det ger en lokal utvecklingsmiljö för att skriva, köra och felsöka Azure Functions.

## <a name="create-a-functions-project"></a>Skapa ett funktionsprojekt

1. Välj i IntelliJ IDEA **Skapa nytt projekt**.  
1. I den **nytt projekt** väljer **Maven** i den vänstra rutan.
1. Välj den **skapa från archetype** kryssrutan och välj sedan **lägga till Archetype** för den [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. I den **lägga till Archetype** fönstret fyller du i fälten på följande sätt:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Version_: Använd den senaste versionen från [centralt](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![skapa ett Maven-projekt från archetype i IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Välj **OK**, och välj sedan **nästa**.
1. Ange din information för aktuella projektet och välj **Slutför**.

Maven skapar projektfiler i en ny mapp med samma namn som den _ArtifactId_ värde. Genererade koden i projektets är en enkel [HTTP-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som utgörs av utlösande HTTP-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Köra funktioner lokalt i IDE

> [!NOTE]
> Om du vill köra och felsöka funktioner lokalt, kontrollera att du har installerat [Azure Functions Core Tools version 2](functions-run-local.md#v2).

1. Importera ändringar manuellt eller aktivera [automatiskt importera](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Öppna den **Maven-projekt** verktygsfältet.
1. Expandera **livscykel**, och öppna sedan **paketet**. Lösningen byggs och paketeras i en nyligen skapade målkatalogen.
1. Expandera **plugin-program** > **azure functions** och öppna **azure-funktioner: kör** att starta den lokala Azure Functions-körningen.  
  ![Maven-verktygsfältet för Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. Stäng dialogrutan Kör när du är klar testa din funktion. Enda funktion värden kan vara aktiv och körs lokalt i taget.

## <a name="debug-the-function-in-intellij"></a>Felsöka funktionen i IntelliJ

1. Starta funktionen värden i felsökningsläge genom att lägga till **- DenableDebug** som argumentet när du kör din funktion. Du kan antingen ändra konfigurationen i [maven mål](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) eller kör följande kommando i ett terminalfönster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Det här kommandot gör funktionen värden för att öppna en felsökningsport vid 5005.

1. På den **kör** menyn och välj **redigera konfigurationer**.
1. Välj **(+)** att lägga till en **Remote**.
1. Slutför den _namn_ och _inställningar_ fält och välj sedan **OK** att spara konfigurationen.
1. Efter installationen, Välj **felsöka < Remote namn >** eller tryck på SKIFT + F9 på tangentbordet för att starta felsökningen.

   ![Felsökningsfunktioner i IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

1. Stoppa felsökningen och processen när du är klar. Enda funktion värden kan vara aktiv och körs lokalt i taget.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

1. Innan du kan distribuera funktionen till Azure, måste du [logga in med hjälp av Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Distribuera koden till en ny funktion med hjälp av den `azure-functions:deploy` Maven-målet. Du kan också välja den **azure functions: distribuera** alternativ i fönstret Maven-projekt.

   ```
   mvn azure-functions:deploy
   ```

1. Hitta URL: en för din funktion i Azure CLI-utdata när funktionen har distribuerats.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Nästa steg

- Läs igenom [utvecklarguiden för Java-funktioner](functions-reference-java.md) för att få mer information om hur du utvecklar Java-funktioner.
- Lägg till ytterligare funktioner med olika utlösare i projektet genom att använda den `azure-functions:add` Maven-målet.
