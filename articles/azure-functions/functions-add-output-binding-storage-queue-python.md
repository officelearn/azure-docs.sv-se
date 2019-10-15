---
title: Lägg till en Azure Storage Queue-bindning till python-funktionen
description: Lär dig hur du lägger till en Azure Storage utgående bindning för en python-funktion.
author: ggailey777
ms.author: glenga
ms.date: 10/02/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 2307a296453247a5deee082aadb474f3641cce88
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329739"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Lägg till en Azure Storage Queue-bindning till python-funktionen

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du integrerar funktionen som du skapade i [föregående snabb starts artikel](functions-create-first-function-python.md) med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra anslutningen enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot finns redan i en app-inställning med namnet `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln slutför du stegen i [del 1 av python-snabb](functions-create-first-function-python.md)starten.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kan du lägga till bindningen för Storage-utdata i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I funktioner kräver varje typ av bindning en `direction`, `type` och en unik `name` som ska definieras i function. JSON-filen. Hur du definierar dessa attribut beror på språket i din Function-app.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

När du använder en utgående bindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en referens till kön eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Som tidigare använder du följande kommando för att starta Functions-körningen lokalt:

```bash
func host start
```

> [!NOTE]  
> Eftersom du har aktiverat tilläggs paket i Host. JSON laddades [lagrings bindnings tillägget](functions-bindings-storage-blob.md#packages---functions-2x) ned och installerades åt dig under starten, tillsammans med de andra Microsoft binding-tilläggen.

Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` till denna URL och kör begäran. Du bör se samma svar i webbläsaren som du gjorde i föregående artikel.

Den här gången skapar bindningen för utdata också en kö med namnet `outqueue` i ditt lagrings konto och lägger till ett meddelande med samma sträng.

Sedan använder du Azure CLI för att visa den nya kön och kontrol lera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Distribuera om projektet 

Om du vill uppdatera din publicerade app använder du kommandot [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools för att distribuera projekt koden till Azure. I det här exemplet ersätter du `<APP_NAME>` med namnet på din app.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Igen kan du använda en sväng eller en webbläsare för att testa den distribuerade funktionen. Som tidigare lägger du till frågesträngen `&name=<yourname>` till URL: en, som i det här exemplet:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Du kan [Granska lagrings köns meddelande](#query-the-storage-queue) igen för att kontrol lera att utmatnings bindningen genererar ett nytt meddelande i kön som förväntat.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Mer information om hur du utvecklar Azure Functions med python finns i [Azure Functions python Developer Guide](functions-reference-python.md) och [Azure Functions utlösare och bindningar](functions-triggers-bindings.md). Exempel på kompletta funktions projekt i python finns i exempel på [python-funktioner](/samples/browse/?products=azure-functions&languages=python). 

Sedan bör du aktivera Application Insights övervakning för din Function-app:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/