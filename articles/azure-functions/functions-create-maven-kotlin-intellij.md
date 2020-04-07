---
title: Skapa en Azure-funktion med Kotlin och IntelliJ
description: Lär dig hur du skapar och publicerar en enkel HTTP-utlöst, serverlös app på Azure med Kotlin och IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674099"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Snabbstart: Skapa din första HTTP-utlöst funktion med Kotlin och IntelliJ

Den här artikeln visar hur du skapar ett [serverlöst](https://azure.microsoft.com/overview/serverless-computing/) funktionsprojekt med IntelliJ IDEA och Apache Maven. Den visar också hur du lokalt felsöker din funktionskod i den integrerade utvecklingsmiljön (IDE) och sedan distribuerar funktionsprojektet till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

För att utveckla en funktion med Kotlin och IntelliJ, installera följande programvara:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Gemenskapen eller Ultimate versioner med Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Version 2.x](functions-run-local.md#v2) av Azure Functions Core Tools. Det ger en lokal utvecklingsmiljö för att skriva, köra och felsöka Azure Functions.

> [!IMPORTANT]
> Variabeln JAVA_HOME miljö måste ställas in på installationsplatsen för JDK för att kunna slutföra stegen i den här artikeln.

## <a name="create-a-functions-project"></a>Skapa ett functions-projekt

1. Välj **Skapa nytt projekt**i IntelliJ IDEA .  
1. Välj **Maven** i fönstret Nytt projekt i fönstret **Nytt projekt.**
1. Markera kryssrutan **Skapa från arketyp** och välj sedan **Lägg till arketyp** för [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Fyll i fälten enligt följande i fönstret **Lägg till arketyp:**
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-arketyp
    - _Version_: Använd den senaste versionen från [den centrala databasen](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Skapa ett Maven-projekt från arketyp i IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Välj **OK**och välj sedan **Nästa**.
1. Ange dina uppgifter för det aktuella projektet och välj **Slutför**.

Maven skapar projektfilerna i en ny mapp med samma namn som _ArtifactId-värdet._ Projektets genererade kod är en enkel [HTTP-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som ekar brödtexten i den utlösande HTTP-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Kör funktioner lokalt i IDE

> [!NOTE]
> Om du vill köra och felsöka funktioner lokalt kontrollerar du att du har installerat [Azure Functions Core Tools, version 2](functions-run-local.md#v2).

1. Importera ändringar manuellt eller aktivera [automatisk import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Öppna verktygsfältet **Maven-projekt.**
1. Expandera **livscykel och**öppna sedan **paketet**. Lösningen är byggd och paketerad i en nyskapad målkatalog.
1. Expandera > **Azure-funktioner** och öppna **azure-funktioner:kör** för att starta den lokala körningen azure functions. **Plugins**  
  ![Verktygsfält maven för Azure-funktioner](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Stäng kördialogrutan när du är klar med testningen av funktionen. Endast en funktionsvärd kan vara aktiv och köras lokalt åt gången.

## <a name="debug-the-function-in-intellij"></a>Felsök funktionen i IntelliJ

1. Om du vill starta funktionsvärden i felsökningsläge lägger du till **-DenableDebug** som argument när du kör funktionen. Du kan antingen ändra konfigurationen i [mavenmål](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) eller köra följande kommando i ett terminalfönster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Det här kommandot gör att funktionsvärden öppnar en felsökningsport vid 5005.

1. Välj **Redigera konfigurationer på** **Kör-menyn** .
1. Välj **(+)** om du vill lägga till en **fjärrkontroll**.
1. Fyll i fälten _Namn_ _och Inställningar_ och välj sedan **OK** för att spara konfigurationen.
1. När du har konfigurerat väljer du **Felsökning < Fjärrkonfigurationsnamn >** eller trycker på Skift+F9 på tangentbordet för att starta felsökningen.

   ![Felsökningsfunktioner i IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. När du är klar stoppar du felsökningssökningen och körningsprocessen. Endast en funktionsvärd kan vara aktiv och köras lokalt åt gången.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

1. Innan du kan distribuera din funktion till Azure måste du [logga in med hjälp av Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Distribuera koden till en ny `azure-functions:deploy` funktion med hjälp av Maven-målet. Du kan också välja **alternativet azure-functions:deploy** i fönstret Maven-projekt.

   ```
   mvn azure-functions:deploy
   ```

1. Hitta URL:en för din funktion i Azure CLI-utdata när funktionen har distribuerats.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat din första Kotlin-funktion till Azure kan du läsa [utvecklarhandboken](functions-reference-java.md) för Java Functions för mer information om hur du utvecklar Java- och Kotlin-funktioner.
- Lägg till ytterligare funktioner med olika utlösare i projektet med hjälp av `azure-functions:add` Maven-målet.
