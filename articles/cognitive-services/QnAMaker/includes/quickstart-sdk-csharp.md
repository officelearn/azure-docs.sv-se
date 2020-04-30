---
title: 'Snabb start: QnA Maker klient bibliotek för .NET'
description: Den här snabb starten visar hur du kommer igång med QnA Maker-klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: ce12b0d5739f3c17a324a663a777b70e61f167d1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204054"
---
Använd QnA Maker klient bibliotek för .NET för att:

* Skapa en kunskapsbas
* Uppdatera en kunskapsbas
* Publicera en kunskapsbas
* Hämta publicerad slut punkts nyckel
* Vänta på aktivitet som körs
* Ta bort kunskaps bas

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [paket (NuGet) C#-](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har en Azure-prenumeration kan du skapa en [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) i Azure Portal för att hämta din redigerings nyckel och slut punkt. När den har distribuerats väljer **du gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för QnA Maker. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån`F0`() för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn.

När du har fått en nyckel och slut punkt för resursen [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, `QNAMAKER_SUBSCRIPTION_KEY`med namnet. Resurs namnet används som en anpassad under domän för slut punktens URL.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE.

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet. `qna-maker-quickstart` Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Installera SDK:n

I program katalogen installerar du QnA Maker klient biblioteket för .NET med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.


## <a name="object-model"></a>Objekt modell

QnA Maker-klienten är ett [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

När klienten har skapats använder du [kunskaps bas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) egenskapen för att skapa, hantera och publicera din kunskaps bas.

Hantera din kunskaps bas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligt vis ett JSON-objekt som indikerar status. För långvariga åtgärder är svaret åtgärds-ID. Anropa [klienten. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) -metoden med åtgärds-ID för att fastställa [status för begäran](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).


## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med QnA Maker klient biblioteket för .NET:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Hämta en kunskaps bas](#download-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)
* [Generera ett svar från kunskaps basen](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **program.cs** -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autentisera klienten för att redigera kunskaps basen

I **main** -metoden skapar du en variabel för resursens Azure-nyckel som hämtas från en miljö variabel `QNAMAKER_SUBSCRIPTION_KEY`med namnet. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE eller gränssnittet som kör den stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare.

Skapa sedan ett [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -objekt.

|Miljövariabel|variabel|Exempel|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Nyckeln är en 32 tecken sträng och är tillgänglig i Azure Portal på den QnA Maker resursen på snabb starts sidan. Detta är inte samma som för förutsägelse slut punkts nyckel.|
|`QNAMAKER_HOST`|`Endpoint`| Din redigerings slut punkt, i formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, innehåller ditt **resurs namn**. Detta är inte samma URL som används för att fråga efter förutsägelse slut punkten.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autentisera körningen för att skapa ett svar

I **main** -metoden skapar du en variabel för resursens autentisering som hämtas från en miljövariabler med `QNAMAKER_ENDPOINT_HOSTNAME` namnet `QNAMAKER_ENDPOINT_KEY`och. När du publicerar din kunskaps bas returneras dessa värden. När du har publicerat kan du hitta de här inställningarna på sidan **Inställningar** på QNA Maker-portalen.

Skapa en [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) för att söka i kunskaps basen för att skapa ett svar eller träna från aktiv inlärning.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey&highlight=3)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par för [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) -objektet från tre källor:

* För **redaktionellt innehåll**använder du [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) -objektet.
* För **filer**använder du [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) -objektet.
* För **URL: er**använder du en lista med strängar.

Anropa metoden [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) och skicka sedan det returnerade åtgärds-ID: t till [MonitorOperation](#get-status-of-an-operation) -metoden för att avsöka efter status.

Den sista raden i följande kod returnerar kunskaps bas-ID: t från svaret från MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=30)]

Se till att inkludera [`MonitorOperation`](#get-status-of-an-operation) funktionen, som refereras till i ovanstående kod, för att kunna skapa en kunskaps bas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskaps bas genom att skicka i kunskaps bas-ID: t och en [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) som innehåller objekt för att [lägga till](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [Uppdatera](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)och [ta bort](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO till [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) -metoden. Använd [MonitorOperation](#get-status-of-an-operation) -metoden för att avgöra om uppdateringen har slutförts.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Se till att inkludera [`MonitorOperation`](#get-status-of-an-operation) funktionen, som refereras till i ovanstående kod, för att kunna uppdatera en kunskaps bas.

## <a name="download-a-knowledge-base"></a>Hämta en kunskaps bas

Använd metoden [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) för att ladda ned databasen som en lista över [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Detta motsvarar _inte_ QNA Maker portalens export från **inställnings** sidan eftersom resultatet av den här metoden inte är en TSV-fil.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskaps basen med [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) -metoden. Detta tar den aktuella sparade och utbildade modellen som refereras till av kunskaps bas-ID: t och publicerar den på en slut punkt.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generera ett svar från kunskaps basen

Generera ett svar från en publicerad kunskaps bas med hjälp av [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) -metod. Den här metoden godkänner kunskaps bas-ID: t och [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Få åtkomst till ytterligare egenskaper för QueryDTO, till exempel [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) och [context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) , som ska användas i din Chat-robot.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskaps basen med [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) -metoden med en parameter i kunskaps bas-ID: t.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt lång tid i stället för att vänta på att processen ska slutföras, returneras en [åtgärd](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) . Använd [Åtgärds-ID: t](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) från åtgärden att söka (med logiken för omprövning) för att fastställa statusen för den ursprungliga metoden.

_Loop_ och _Task. Delay_ i följande kodblock används för att simulera omprövnings logik. De bör ersättas med din egen omprövnings logik.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `dotnet run` kommandot från program katalogen.

Alla kodfragment i den här artikeln är [tillgängliga](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) och kan köras som en enda fil.

```dotnetcli
dotnet run
```

[Käll koden för den här snabb](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) starten är tillgänglig i QNA Maker C#-exempel GitHub-lagringsplatsen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)