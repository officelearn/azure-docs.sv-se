---
title: 'Snabbstart: QnA Maker-klientbibliotek för .NET'
description: Den här snabbstarten visar hur du kommer igång med QnA Maker-klientbiblioteket för .NET. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 2911c74226c3b682b75e8d10b0b4b7617a48ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946343"
---
Använd QnA Maker-klientbiblioteket för .NET för att:

* Skapa en kunskapsbas
* Hantera en kunskapsbas
* Publicera en kunskapsbas
* Generera ett svar från kunskapsbasen

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# Exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator.

När du har hämtat en nyckel och en slutpunkt `QNAMAKER_SUBSCRIPTION_KEY`för resursen skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln med namnet . Resursnamnet används som en del av slutpunkts-URL:en.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE.

I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du `qna-maker-quickstart`kommandot för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```dotnetcli
dotnet build
```

Byggutdata bör inte innehålla några varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Installera SDK:n

Installera QnA Maker-klientbiblioteket för .NET i programkatalogen med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.


## <a name="object-model"></a>Objektmodell

QnA Maker-klienten är ett [QnAMakerClient-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) som autentiserar till Azure med Microsoft.Rest.ServiceClientCredentials, som innehåller din nyckel.

När klienten har skapats använder du egenskapen [Kunskapsbas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) för att skapa, hantera och publicera kunskapsbasen.

Hantera din kunskapsbas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligtvis ett JSON-objekt som anger status. För tidskrävande åtgärder är svaret operations-ID. Ring [klienten. Operations.GetDetailsAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) med åtgärds-ID för att fastställa [status för begäran](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).


## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med QnA Maker-klientbiblioteket för .NET:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Ladda ner en kunskapsbas](#download-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)
* [Generera ett svar från kunskapsbasen](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna den **Program.cs** filen i önskad redigerare eller IDE från projektkatalogen. Ersätt den `using` befintliga koden `using` med följande direktiv:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autentisera klienten för att skapa kunskapsbasen

I **huvudmetoden** skapar du en variabel för resursens Azure-nyckel som hämtas från en miljövariabel med namnet `QNAMAKER_SUBSCRIPTION_KEY`. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE: t eller shell som kör den stängas och laddas om för att komma åt variabeln. Metoderna skapas senare.

Skapa sedan ett [ApiKeyServiceClientCredentials-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) med nyckeln och använd det med slutpunkten för att skapa ett [QnAMakerClient-objekt.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)

|Miljövariabel|Variabel|Exempel|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Nyckeln är en 32 teckensträng och är tillgänglig i Azure-portalen, på QnA Maker-resursen, på snabbstartssidan. Detta är inte samma sak som förutsägelseslutpunktsnyckeln.|
|`QNAMAKER_HOST`|`Endpoint`| Din redigeringsslutpunkt, i `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formatet , innehåller ditt **resursnamn**. Det här är inte samma URL som används för att fråga ut förutsägelseslutpunkten.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autentisera körningen för att generera ett svar

I **huvudmetoden** skapar du en variabel för resursens autentisering som hämtas från en miljövariabler med namnet `QNAMAKER_ENDPOINT_HOSTNAME` och `QNAMAKER_ENDPOINT_KEY`. När du publicerar kunskapsbasen returneras dessa värden. När du har publicerat hittar du de här inställningarna på sidan **Inställningar** på QnA Maker-portalen.

Skapa en [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) för att fråga kunskapsbasen för att generera ett svar eller träna från aktiv inlärning.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

I en kunskapsbas lagras fråge- och svarspar för [CreateKbDTO-objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) från tre källor:

* För **redaktionellt innehåll**använder du [QnADTO-objektet.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)
* Använd [FileDTO-objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) för **filer.**
* För **webbadresser**använder du en lista med strängar.

Anropa [metoden CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) och skicka sedan det returnerade åtgärds-ID:et till metoden [MonitorOperation](#get-status-of-an-operation) för att avsöka status.

Den sista raden i följande kod returnerar kunskapsbas-ID från svaret från MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Se till att [`MonitorOperation`](#get-status-of-an-operation) funktionen inkluderas, som refereras i koden ovan, för att kunna skapa en kunskapsbas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskapsbas genom att skicka in knowledge base-ID och ett [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) som innehåller [add,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet) [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)och [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO-objekt i [metoden UpdateAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) Använd metoden [MonitorOperation](#get-status-of-an-operation) för att avgöra om uppdateringen lyckades.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Se till att [`MonitorOperation`](#get-status-of-an-operation) funktionen inkluderas, som refereras i ovanstående kod, för att uppdatera en kunskapsbas.

## <a name="download-a-knowledge-base"></a>Ladda ner en kunskapsbas

Använd metoden [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) för att hämta databasen som en lista över [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Detta motsvarar _inte_ QnA Maker-portalens export från sidan **Inställningar** eftersom resultatet av den här metoden inte är en TSV-fil.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskapsbasen med metoden [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) Detta tar den aktuella sparade och tränade modellen, refererad av kunskapsbas-ID, och publicerar den vid en slutpunkt.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generera ett svar från kunskapsbasen

Generera ett svar från en publicerad kunskapsbas med [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync-metoden.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) Den här metoden accepterar knowledge base-ID och [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Få tillgång till ytterligare egenskaper för QueryDTO, till exempel [överkant](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) och [kontext](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) som ska användas i chattroboten.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskapsbasen med metoden [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) med en parameter i kunskapsbas-ID.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt med tid att en [åtgärd](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) returneras i stället för att vänta på att processen ska slutföras. Använd [åtgärds-ID:t](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) från åtgärden för att avsöka (med logik för återförsök) för att fastställa status för den ursprungliga metoden.

_Loop och_ _Task.Delay_ i följande kodblock används för att simulera logik för återförsök. Dessa bör ersättas med din egen logik för återförsök.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `dotnet run` kommandot från programkatalogen.

Alla kodavsnitt i den här artikeln är [tillgängliga](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) och kan köras som en enda fil.

```dotnetcli
dotnet run
```

[Källkoden för den här snabbstarten](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) är tillgänglig i GitHub-arkivet QnA Maker C#.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)