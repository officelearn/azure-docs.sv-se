---
title: Skapa en Azure-funktion med Java och IntelliJ
description: Lär dig hur du skapar och publicerar en enkel HTTP-utlöst, serverlös app på Azure med Java och IntelliJ.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7003dc19a7bfc405809de91534028aba8e0416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136858"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Skapa din första Azure-funktion med Java och IntelliJ

Den här artikeln:
- Hur man skapar ett [serverlöst](https://azure.microsoft.com/overview/serverless-computing/) funktionsprojekt med IntelliJ IDEA och Apache Maven
- Steg för att testa och felsöka funktionen i den integrerade utvecklingsmiljön (IDE) på din egen dator
- Instruktioner för distribution av funktionsprojektet till Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Så här utvecklar du en funktion med Java och IntelliJ genom att installera följande programvara:

- [Java Developer Kit](https://www.azul.com/downloads/zulu/) (JDK), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Gemenskapen eller Ultimate versioner med Maven
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> Variabeln JAVA_HOME miljö måste ställas in på installationsplatsen för JDK för att kunna slutföra stegen i den här artikeln.

 Vi rekommenderar att du installerar [Azure Functions Core Tools, version 2](functions-run-local.md#v2). Det ger en lokal utvecklingsmiljö för att skriva, köra och felsöka Azure Functions.

## <a name="create-a-functions-project"></a>Skapa ett functions-projekt

1. Välj **Skapa nytt projekt**i IntelliJ IDEA .  
1. Välj **Maven** i fönstret Nytt projekt i fönstret **Nytt projekt.**
1. Markera kryssrutan **Skapa från arketyp** och välj sedan **Lägg till arketyp** för [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
1. Fyll i fälten enligt följande i fönstret **Lägg till arketyp:**
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-archetype
    - _Version_: Kontrollera och använda den senaste versionen från [den centrala databasen](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Skapa ett Maven-projekt från arketyp i IntelliJ IDEA](media/functions-create-first-java-intellij/functions-create-intellij.png)  
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
  ![Verktygsfält maven för Azure-funktioner](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

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

- Läs igenom [utvecklarguiden för Java-funktioner](functions-reference-java.md) för att få mer information om hur du utvecklar Java-funktioner.
- Lägg till ytterligare funktioner med olika utlösare i projektet med hjälp av `azure-functions:add` Maven-målet.
