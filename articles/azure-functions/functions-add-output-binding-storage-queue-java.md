---
title: Ansluta java-funktionen till Azure Storage
description: Lär dig hur du ansluter en HTTP-utlöst Java-funktion till Azure Storage med hjälp av en kölagringsutdatabindning.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673262"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Ansluta java-funktionen till Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du integrerar funktionen som du skapade i den [tidigare snabbstartsartikeln](functions-create-first-java-maven.md) med en Azure Storage-kö. Utdatabindningen som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutningssträng som Funktioner använder för att komma åt den bundna tjänsten. Om du vill göra den här anslutningen enklare använder du det lagringskonto som du skapade med funktionsappen. Anslutningen till det här kontot lagras redan `AzureWebJobsStorage`i appinställningen .  

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln, slutföra stegen i [del 1 av Java snabbstart](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Ladda ned funktionsappinställningarna

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggspaket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kan du lägga till storage-utdatabindningen i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Du är nu redo att prova den nya utdatabindningen lokalt.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Som tidigare använder du följande kommando för att skapa projektet och starta funktionerskörningen lokalt:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Eftersom du har aktiverat tilläggspaket i host.json hämtades och installerades [tillägget Lagringsbindning](functions-bindings-storage-blob.md#add-to-your-functions-app) åt dig under start, tillsammans med de andra Microsoft-bindningstilläggen.

Precis som tidigare utlöser du funktionen från kommandoraden med cURL i ett nytt terminalfönster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Den här gången skapar utdatabindningen också en kö som namnges `outqueue` i ditt Lagringskonto och lägger till ett meddelande med samma sträng.

Därefter använder du Azure CLI för att visa den nya kön och verifiera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure-portalen](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Omfördela projektet 

Om du vill uppdatera den publicerade appen kör du följande kommando igen:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Återigen kan du använda cURL för att testa den distribuerade funktionen. Precis som tidigare `AzureFunctions` skickar du värdet i brödtexten i POST-begäran till webbadressen, som i det här exemplet:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Du kan [granska meddelandet Lagringskö](#query-the-storage-queue) igen för att kontrollera att utdatabindningen genererar ett nytt meddelande i kön, som förväntat.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat funktionen HTTP för att skriva data till en lagringskö. Mer information om hur du utvecklar Azure Functions med Java finns i [Azure Functions Java-utvecklarguiden](functions-reference-java.md) och [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md). Exempel på kompletta funktionsprojekt i Java finns i [javafunktionsexempelen](/samples/browse/?products=azure-functions&languages=Java). 

Därefter bör du aktivera Application Insights-övervakning för din funktionsapp:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
