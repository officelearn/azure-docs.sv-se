---
title: Skapa en Azure Function-app med Java och Sol förmörkelse
description: Instruktions guide för att skapa och publicera en enkel HTTP-utlöst app utan server med Java och Sol förmörkelse till Azure Functions.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 42e9ed7c080c9274fad7eda8e4c8af3631ed41f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756478"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Skapa din första funktion med Java och Sol förmörkelse 

Den här artikeln visar hur du skapar ett [Server](https://azure.microsoft.com/solutions/serverless/) lös funktions projekt med Sol förmörkelse IDE-och Apache-maven, testar och felsöker det och distribuerar det sedan till Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill utveckla en Functions-app med Java och Sol förmörkelse måste du ha följande installerat:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8.
-  [Apache maven](https://maven.apache.org), version 3,0 eller senare.
-  [Sol förmörkelse](https://www.eclipse.org/downloads/packages/), med stöd för Java och Maven.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

Vi rekommenderar också att du installerar [Azure Functions Core tools, version 2](functions-run-local.md#v2), som tillhandahåller en lokal miljö för att köra och felsöka Azure Functions. 

## <a name="create-a-functions-project"></a>Skapa ett Functions-projekt

1. I Sol förmörkelse väljer du **Arkiv** -menyn och sedan **nytt maven&gt; -projekt**. 
1. Acceptera standardinställningarna i dialog rutan **nytt Maven-projekt** och välj **Nästa**.
1. Välj **Lägg till archetype** och Lägg till posterna för [Azure-Functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Archetype-grupp-ID: com. Microsoft. Azure
    - Archetype artefakt-ID: Azure-Functions-archetype
    - Version: kontrol lera och Använd den senaste versionen från [den centrala databasens](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Sol förmörkelse maven skapa](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Klicka på **OK** och sedan på **Nästa**.  Var noga med att fylla i värden för alla fält, inklusive `resourceGroup`, `appName`och `appRegion` (Använd ett annat APPNAME än **Fabrikam-Function-20170920120101928**) och slutligen **Avsluta**.
    ![Sol förmörkelse maven create2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven skapar projektfiler i en ny mapp med namnet från _artifactId_. Den genererade koden i projektet är en enkel [http-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som upprepar texten i den Utlös ande http-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Köra funktioner lokalt i IDE

> [!NOTE]
> [Azure Functions Core tools version 2](functions-run-local.md#v2) måste installeras för att köra och felsöka funktioner lokalt.

1. Högerklicka på det genererade projektet och välj sedan **Kör som** och **maven build**.
1. I dialog **rutan Redigera konfiguration** anger `package` du i fälten **mål** och **namn** och väljer sedan **Kör**. Då skapas och paketeras funktions koden.
1. När build-versionen är klar skapar du en annan körnings konfiguration som `azure-functions:run` ovan och använder som mål och namn. Välj **Kör** för att köra funktionen i IDE.

Avsluta körningen i konsol fönstret när du är klar med att testa din funktion. Endast en funktions värd kan vara aktiv och köras lokalt i taget.

### <a name="debug-the-function-in-eclipse"></a>Felsöka funktionen i Sol förmörkelse

I din **Kör som** -konfiguration i föregående steg ändrar `azure-functions:run` du till `azure-functions:run -DenableDebug` och kör den uppdaterade konfigurationen för att starta Function-appen i fel söknings läge.

Välj **körnings** menyn och öppna **Debug-konfigurationer**. Välj **fjärr-Java-program** och skapa ett nytt. Ge konfigurationen ett namn och fyll i inställningarna. Porten bör vara konsekvent med fel söknings porten som öppnas av funktions värden, vilket som standard `5005`är. Starta fel sökningen efter installationen `Debug` genom att klicka på på.

![Fel söknings funktioner i Sol förmörkelse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Ange Bryt punkter och granska objekt i din funktion med IDE. När du är färdig stoppar du fel söknings programmet och den kör funktions värden. Endast en funktions värd kan vara aktiv och köras lokalt i taget.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter att använda datorns kommando tolk.

```azurecli
az login
```

Distribuera din kod till en ny function-app med `azure-functions:deploy` hjälp av maven-målet i en ny **Kör som** -konfiguration.

När distributionen är klar kan du se den webbadress som används för att få åtkomst till din funktionsapp i Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Nästa steg

- Läs igenom [utvecklarguiden för Java-funktioner](functions-reference-java.md) för att få mer information om hur du utvecklar Java-funktioner.
- Lägg till fler funktioner med olika utlösare i projektet med Maven-målet `azure-functions:add`.
