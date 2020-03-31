---
title: Skapa en Azure-funktionsapp med Java och Eclipse
description: Så här kan du skapa och publicera en enkel HTTP-utlöst serverlös app med Java och Eclipse till Azure-funktioner.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: c82432a3d76a84eba1ad921d936b2f3ba064e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136841"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Skapa din första funktion med Java och Eclipse 

Den här artikeln visar hur du skapar ett [serverlöst](https://azure.microsoft.com/solutions/serverless/) funktionsprojekt med Eclipse IDE och Apache Maven, testar och felsöker det och distribuerar det sedan till Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill utveckla en funktionsapp med Java och Eclipse måste du ha följande installerat:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8.
-  [Apache Maven](https://maven.apache.org), version 3.0 eller högre.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), med Java och Maven stöd.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

Vi rekommenderar starkt att du även installerar [Azure Functions Core Tools, version 2,](functions-run-local.md#v2)som tillhandahåller en lokal miljö för att köra och felsöka Azure Functions. 

## <a name="create-a-functions-project"></a>Skapa ett functions-projekt

1. Välj **Arkiv-menyn** i Eclipse och välj sedan **Nytt -&gt; Maven-projekt**. 
1. Acceptera standardinställningarna i den **nya mavenprojektdialogen** och välj **Nästa**.
1. Välj **Lägg till arketyp** och lägg till posterna för [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Grupp-ID för arketyp: com.microsoft.azure
    - Arketyp Artefakt-ID: azure-functions-archetype
    - Version: Kontrollera och använda den senaste versionen från [den centrala databasen](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven skapa](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Klicka på **OK** och sedan på **Nästa**.  Var noga med att fylla i `resourceGroup`värden `appName`för `appRegion` alla fält, inklusive , och (använd ett annat appName annat än **fabrikam-function-20170920120101928**), och så småningom **Avsluta**.
    ![Eclipse Maven create2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven skapar projektfiler i en ny mapp med namnet från _artifactId_. Den genererade koden i projektet är en enkel [HTTP-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som ekar brödtexten i den utlösande HTTP-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Kör funktioner lokalt i IDE

> [!NOTE]
> [Azure Functions Core Tools, version 2](functions-run-local.md#v2) måste installeras för att köra och felsöka funktioner lokalt.

1. Högerklicka på det genererade projektet och välj sedan **Kör som** och **Maven bygga**.
1. Ange `package` i fälten **Mål** och **namn** i dialogrutan **Redigera konfiguration** och välj sedan **Kör**. Detta kommer att bygga och paketera funktionskoden.
1. När build är klar, skapa en annan `azure-functions:run` Kör konfiguration som ovan, med som mål och namn. Välj **Kör** om du vill köra funktionen i IDE.

Avsluta körningen i konsolfönstret när du är klar med testningen av funktionen. Endast en funktionsvärd kan vara aktiv och köras lokalt åt gången.

### <a name="debug-the-function-in-eclipse"></a>Felsök funktionen i Eclipse

I konfigurationen **Kör som** i föregående `azure-functions:run` steg `azure-functions:run -DenableDebug` ändrar du till och kör den uppdaterade konfigurationen för att starta funktionsappen i felsökningsläge.

Välj **menyn Kör** och öppna **Felsökningskonfigurationer**. Välj **Fjärr-Java-program** och skapa ett nytt. Ge din konfiguration ett namn och fyll i inställningarna. Porten bör vara förenligt med felsökningsporten som öppnas `5005`av funktionsvärd, som som standard är . Efter installationen, `Debug` klicka på för att börja felsökning.

![Felsökningsfunktioner i Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Ange brytpunkter och inspektera objekt i din funktion med hjälp av IDE. När du är klar stoppar du felsöknings- och löparfunktionsvärden. Endast en funktionsvärd kan vara aktiv och köras lokalt åt gången.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter använda datorns kommandotolk.

```azurecli
az login
```

Distribuera koden till en ny `azure-functions:deploy` funktionsapp med Maven-målet i en ny **Run As-konfiguration.**

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
