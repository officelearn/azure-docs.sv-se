---
title: Lägg till en Azure Storage Queue-bindning till python-funktionen
description: Lär dig hur du lägger till en Azure Storage utgående bindning för en python-funktion med hjälp av Azure CLI-och Functions Core-verktyg.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839182"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Lägg till en Azure Storage Queue-bindning till python-funktionen

Med Azure Functions kan du ansluta Azure-tjänster och andra resurser till funktioner utan att behöva skriva din egen integrerings kod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktions definitionen. Data från bindningar tillhandahålls till funktionen som parametrar. En *utlösare* är en särskild typ av ingående bindning. Även om en funktion bara har en utlösare, kan den ha flera indata och utdata-bindningar. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

Den här artikeln visar hur du integrerar funktionen som du skapade i [föregående snabb starts artikel](functions-create-first-function-python.md) med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra anslutningen enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot finns redan i en app-inställning med namnet `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln slutför du stegen i [del 1 av python-snabb](functions-create-first-function-python.md)starten.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

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
> Eftersom i den tidigare snabb starten som du har aktiverat tillägg i Host. JSON laddades [lagrings bindnings tillägget](functions-bindings-storage-blob.md#packages---functions-2x) ned och installerades åt dig under starten, tillsammans med de andra Microsoft binding-tilläggen.

Kopiera URL:en till din funktion `HttpTrigger` från körtidutdatan och klistra in den i webbläsarens adressfält. Lägg till frågesträngen `?name=<yourname>` till denna URL och kör begäran. Du bör se samma svar i webbläsaren som du gjorde i föregående artikel.

Den här gången skapar bindningen för utdata också en kö med namnet `outqueue` i ditt lagrings konto och lägger till ett meddelande med samma sträng.

Sedan använder du Azure CLI för att visa den nya kön och kontrol lera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Ange lagrings konto anslutningen

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Fråga lagrings kön

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Nu är det dags att publicera om den uppdaterade Function-appen till Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Igen kan du använda en sväng eller en webbläsare för att testa den distribuerade funktionen. Som tidigare lägger du till frågesträngen `&name=<yourname>` till URL: en, som i det här exemplet:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Du kan kontrol lera [lagrings köns meddelande](#query-the-storage-queue) för att kontrol lera att utgående bindningen igen genererar ett nytt meddelande i kön.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Mer information om hur du utvecklar Azure Functions med python finns i [Azure Functions python Developer Guide](functions-reference-python.md) och [Azure Functions utlösare och bindningar](functions-triggers-bindings.md). Exempel på kompletta funktions projekt i python finns i exempel på [python-funktioner](/samples/browse/?products=azure-functions&languages=python). 

Sedan bör du aktivera Application Insights övervakning för din Function-app:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/