---
title: Skapa en funktionsapp i Azure med Java och IntelliJ | Microsoft Docs
description: Anvisningar för att skapa och publicera en enkel HTTP utlöses app utan server med Java och IntelliJ till Azure Functions.
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
ms.openlocfilehash: d641688b5e2da7bf571b536aa9cc340e9a5b2ace
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115428"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Skapa din första funktion med Java och IntelliJ (förhandsversion)

> [!NOTE] 
> Java för Azure Functions finns för närvarande som förhandsversion.

Den här artikeln visar hur du skapar en [serverlös](https://azure.microsoft.com/solutions/serverless/) function-projekt med IntelliJ IDEA och Apache Maven, testa och felsöka det på din dator från IDE och distribuera den till Azure Functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö

Om du vill utveckla en functions-app med Java och IntelliJ, måste du ha följande installerat:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), version 8.
-  [Apache Maven](https://maven.apache.org), version 3.0 eller senare.
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community eller Ultimate med Maven verktyg.
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

Det mycket är rekommenderat att även installera [Azure Functions Core Tools version 2](functions-run-local.md#v2), vilket ger en lokal utvecklingsmiljö för att skriva, köra och felsöka Azure Functions. 


## <a name="create-a-functions-project"></a>Skapa ett funktionsprojekt

1. I IntelliJ IDEA klickar du på att **Skapa nytt projekt**.  
1. Välj för att skapa från **Maven**
1. Markera kryssrutan för att **skapa från archetype** och **lägga till Archetype** för den [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype).
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - Version: Använd senaste versionen från [centralt](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![IntelliJ Maven skapa](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. Klicka på **nästa** och ange information om aktuella project och så småningom **Slutför**.

Maven skapar projektfiler i en ny mapp med namnet från _artifactId_. Den genererade koden i projektet är en enkel [HTTP-utlöst](/azure/azure-functions/functions-bindings-http-webhook) funktion som utgörs av utlösande HTTP-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Köra funktioner lokalt i IDE

> [!NOTE]
> [Azure Functions Core Tools, version 2](functions-run-local.md#v2) måste installeras om du vill köra och felsöka funktioner lokalt.

1. Välj att importera ändringar eller se till att [automatiskt importera](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html) är installerat.
1. Öppna den **Maven-projekt** verktygsfältet
1. Under livscykeln, dubbelklickar du på **paketet** att paketera och bygg lösningen och skapa en målkatalog.
1. Under plugin-program -> azure-functions dubbelklicka **azure-funktioner: kör** att starta den lokala azure functions-körningen.  
  ![Maven-verktygsfältet för Azure Functions](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

Stäng dialogrutan Kör när du är klar testa din funktion. Enda funktion värden kan vara aktiv och körs lokalt i taget.

### <a name="debug-the-function-in-intellij"></a>Felsöka funktionen i IntelliJ
Starta funktionen värden i felsökningsläge genom att lägga till **- DenableDebug** som argumentet när du kör din funktion. Du kan köra nedan kommandoraden i terminal eller konfigurera den i [maven mål](https://www.jetbrains.com/help/idea/maven-support.html#run_goal). Funktionen värden öppna sedan en felsökningsport vid 5005. 

```
mvn azure-functions:run -DenableDebug
```

Felsöka i IntelliJ, i den **kör** väljer du menyn **redigera konfigurationer**. Klicka på **+** att lägga till en **Remote**. Fyll i **namn** och **inställningar**, och klicka sedan på **OK** att spara konfigurationen. Efter installationen klickar du på **felsöka** ”din fjärranslutna Configuration namn” eller trycker på **SKIFT + F9** att starta felsökningen.

![Felsökningsfunktioner i IntelliJ](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

När du är klar stoppa felsökningen och processen. Enda funktion värden kan vara aktiv och körs lokalt i taget.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) innan du fortsätter med hjälp av datorns kommandotolk.

```azurecli
az login
```

Distribuera koden till ett nytt funktionen med hjälp av den `azure-functions:deploy` Maven-målet eller välj azure-funktioner: distribuera alternativ i fönstret Maven-projekt.

```
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

## <a name="next-steps"></a>Nästa steg

- Läs igenom [utvecklarguiden för Java-funktioner](functions-reference-java.md) för att få mer information om hur du utvecklar Java-funktioner.
- Lägg till fler funktioner med olika utlösare i projektet med Maven-målet `azure-functions:add`.
