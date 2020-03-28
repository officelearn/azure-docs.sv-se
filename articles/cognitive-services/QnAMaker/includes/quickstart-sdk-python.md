---
ms.openlocfilehash: a77041c0e53c2f0f8b6d0891a0f755e8ca474923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946350"
---

Använd QnA Maker-klientbiblioteket för python för att:

* Skapa en kunskapsbas
* Hantera en kunskapsbas
* Publicera en kunskapsbas

[Referensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [BibliotekskällkodPaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [(pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python-exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Inrätta

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator.

När du har hämtat en nyckel från resursen `QNAMAKER_KEY` `QNAMAKER_HOST`skapar [du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för resursen med namnet och . Använd nyckel- och slutpunktsvärdena som finns i Azure-portalen.

### <a name="install-the-python-library-for-qna-maker"></a>Installera pythonbiblioteket för QnA Maker

När du har installerat Python kan du installera klientbiblioteket med:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Objektmodell

Skapa ett [CognitiveServicesCredentials-objekt](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) med nyckeln och använd det med slutpunkten för att skapa ett [QnAMakerClient-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python)

När klienten har skapats använder du [kunskapsbasen](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) för att skapa, hantera och publicera din kunskapsbas.

För omedelbara åtgärder returnerar en metod vanligtvis ett JSON-objekt som anger status. För tidskrävande åtgärder är svaret operations-ID. Ring [klienten. Metoden Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.operations%28class%29?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) med åtgärds-ID för att fastställa [status för begäran](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python).


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med QnA Maker-klientbiblioteket för python:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ladda ner en kunskapsbas](#download-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Skapa ett nytt pythonprogram

Skapa ett nytt Python-program i önskad redigerare eller IDE. Importera sedan följande bibliotek.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Skapa variabler för resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

|Miljövariabel|Variabel|Exempel|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Nyckeln är en 32 teckensträng och är tillgänglig i Azure-portalen, på QnA Maker-resursen, på snabbstartssidan. Detta är inte samma sak som förutsägelseslutpunktsnyckeln.|
|`QNAMAKER_HOST`|`host`| Din redigeringsslutpunkt, i `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formatet , innehåller ditt **resursnamn**. Det här är inte samma URL som används för att fråga ut förutsägelseslutpunkten.|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa sedan ett CognitiveServicesCredentials-objekt med nyckeln och använd det med slutpunkten för att skapa ett [QnAMakerClient-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python)


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

 Använd klientobjektet för att hämta ett [verksamhetsobjekt för kunskapsbas.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python)

I en kunskapsbas lagras fråge- och svarspar för [CreateKbDTO-objektet](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) från tre källor:

* För **redaktionellt innehåll**använder du [QnADTO-objektet.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python)
* Använd [FileDTO-objektet](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python) för **filer.**
* För **webbadresser**använder du en lista med strängar.

Anropa [metoden create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) och skicka sedan det returnerade åtgärds-ID:et till metoden [Operations.getDetails](#get-status-of-an-operation) för att avsöka status.

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Se till att [`_monitor_operation`](#get-status-of-an-operation) funktionen inkluderas, som refereras i koden ovan, för att kunna skapa en kunskapsbas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskapsbas genom att skicka in knowledge base-ID och ett [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) som innehåller [lägg till,](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python) [uppdatera](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python)och [ta bort](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) DTO-objekt till [uppdateringsmetoden.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) Använd metoden [Operation.getDetail](#get-status-of-an-operation) för att avgöra om uppdateringen lyckades.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Se till att [`_monitor_operation`](#get-status-of-an-operation) funktionen inkluderas, som refereras i ovanstående kod, för att uppdatera en kunskapsbas.

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskapsbasen [publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) med publiceringsmetoden. Detta tar den aktuella sparade och tränade modellen, refererad av kunskapsbas-ID, och publicerar den vid en slutpunkt.

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Ladda ner en kunskapsbas

Använd [hämtningsmetoden](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) för att hämta databasen som en lista över [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Detta motsvarar _inte_ QnA Maker-portalens export från sidan **Inställningar** eftersom resultatet av den här metoden inte är en TSV-fil.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskapsbasen med [borttagningsmetoden](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) med en parameter i kunskapsbas-ID: et.

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt med tid att en [åtgärd](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation%28class%29?view=azure-python) returneras i stället för att vänta på att processen ska slutföras. Använd åtgärds-ID:t från åtgärden för att avsöka (med logik för återförsök) för att fastställa status för den ursprungliga metoden.

_SetTimeout-anropet_ i följande kodblock används för att simulera asynkron kod. Ersätt detta med logik för återförsök.

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Köra appen

Kör programmet `python knowledgebase_quickstart.py` med kommando från programkatalogen.

Alla kodavsnitt i den här artikeln är [tillgängliga](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) och kan köras som en enda fil.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
