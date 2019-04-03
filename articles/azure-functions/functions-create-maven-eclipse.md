---
title: Skapa en funktionsapp i Azure med Java och Eclipse | Microsoft Docs
description: Anvisningar för att skapa och publicera en enkel HTTP utlöses app utan server med Java och Eclipse till Azure Functions.
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
ms.openlocfilehash: 123a24eb13de584d8e3b70d0d8b1173f583867c1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881434"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Skapa din första funktion med Java och Eclipse 

Den här artikeln visar hur du skapar en [serverlös](https://azure.microsoft.com/solutions/serverless/) function-projekt med Eclipse IDE och Apache Maven, testa och felsöka det och sedan distribuera den till Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill utveckla en functions-app med Java och Eclipse, måste du ha följande installerat:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8.
-  [Apache Maven](https://maven.apache.org), version 3.0 eller senare.
-  [Eclipse](https://www.eclipse.org/downloads/packages/)med Java och Maven stöd.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

Vi rekommenderar starkt att även installera [Azure Functions Core Tools version 2](functions-run-local.md#v2), vilket ger en lokal miljö för att köra och felsöka Azure Functions. 

## <a name="create-a-functions-project"></a>Skapa ett funktionsprojekt

1. I Eclipse väljer den **filen** menyn och välj sedan **projekt**. 
1. Öppna den **Java-projekt** mapp i den **nytt projekt** och välj **Maven-projekt**och välj sedan **nästa**.
1. Acceptera standardinställningarna i den **nytt Maven-projekt** dialog och välj **nästa**.
1. Välj **lägga till Archetype** och lägga till poster för den [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - Archetype grupp-ID: com.microsoft.azure
    - Archetype artefakt-ID: azure-functions-archetype
    - Version: Använd den senaste versionen från [centralt](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![Eclipse Maven skapa](media/functions-create-first-java-eclipse/functions-create-eclipse.png)  
1. Klicka på **OK** och ange information om aktuella project och så småningom **Slutför**.

Maven skapar projektfiler i en ny mapp med namnet från _artifactId_. Den genererade koden i projektet är en enkel [HTTP-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som utgörs av utlösande HTTP-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Köra funktioner lokalt i IDE

> [!NOTE]
> [Azure Functions Core Tools, version 2](functions-run-local.md#v2) måste installeras om du vill köra och felsöka funktioner lokalt.

1. Högerklicka på det genererade projektet och välj sedan **kör som** och **Maven build**.
1. I den **redigera konfiguration** dialogrutan RETUR `package` i den **mål** och **namn** fält, välj sedan **kör**. Detta kommer att bygga och paketera funktionskoden.
1. När versionen har slutförts, skapar du en annan kör konfiguration som ovan, med hjälp av `azure-functions:run` som mål och namn. Välj **kör** att köra funktionen i IDE.

Avsluta körningen på konsolfönstret när du är klar testa din funktion. Enda funktion värden kan vara aktiv och körs lokalt i taget.

### <a name="debug-the-function-in-eclipse"></a>Felsöka funktionen i Eclipse

I din **kör som** konfiguration som angetts i föregående steg, ändra `azure-functions:run` till `mvn azure-functions:run -DenableDebug` och kör den uppdaterade konfigurationen för att starta appen i felsökningsläge.

Välj den **kör** menyn och öppna **felsöka konfigurationer**. Välj **fjärranslutna Java-program** och skapa en ny. Namnge din konfiguration och Fyll i inställningarna. Porten bör överensstämma med debug-port som öppnas av funktionen värden, vilket som standard är `5005`. Efter installationen klickar du på `Debug` att starta felsökningen.

![Felsökningsfunktioner i Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Ange brytpunkter och granska objekt i din funktion med hjälp av IDE. Stoppa felsökningsprogrammet och köra funktionen värden när du är klar. Enda funktion värden kan vara aktiv och körs lokalt i taget.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter med hjälp av datorns kommandotolk.

```azurecli
az login
```

Distribuera koden till ett nytt funktionen med hjälp av den `azure-functions:deploy` Maven-målet i en ny **kör som** konfiguration.

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
