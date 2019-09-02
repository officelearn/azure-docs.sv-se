---
title: 'Snabbstart: QnA Maker klient bibliotek för .NET'
titleSuffix: Azure Cognitive Services
description: Kom igång med QnA Maker klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 1724e2d81f1a18da5a200637cd25636166d5cb37
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70206748"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Snabbstart: QnA Maker klient bibliotek för .NET

Kom igång med QnA Maker klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker. 

Använd QnA Maker klient bibliotek för .NET för att:

* Skapa en kunskapsbas 
* Hantera en kunskaps bas
* Publicera en kunskapsbas
* Generera ett svar från kunskaps basen

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ exempelC# ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. 

När du har fått en nyckel från resursen [skapar du en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln, med `QNAMAKER_SUBSCRIPTION_KEY`namnet.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C# program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du kommandot dotNet `new` för att skapa en ny konsol app med namnet. `qna-maker-quickstart` Det här kommandot skapar ett enkelt "Hello World C# "-projekt med en enda käll `Program.cs`fil:. 

```console
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
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

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.


## <a name="object-model"></a>Objekt modell

QnA Maker-klienten är ett [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

När klienten har skapats använder du kunskaps [bas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) egenskapen skapa, hantera och publicera din kunskaps bas. 

Hantera din kunskaps bas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligt vis ett JSON-objekt som indikerar status. För långvariga åtgärder är svaret åtgärds-ID. Anropa [klienten. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) -metoden med åtgärds-ID för att fastställa [status för begäran](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande med QnA Maker klient biblioteket för .NET:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Hämta en kunskaps bas](#download-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskaps bas](#delete-a-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)
* [Generera ett svar från kunskaps basen](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **program.cs** -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autentisera klienten för att redigera kunskaps basen

I **main** -metoden skapar du en variabel för resursens Azure-nyckel som hämtas från en miljö variabel `QNAMAKER_SUBSCRIPTION_KEY`med namnet. Om du har skapat miljövariabeln när programmet har startats måste redigeraren, IDE eller gränssnittet som kör den stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare.

Skapa sedan ett [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -objekt.

Om nyckeln inte finns i `westus` regionen, som exempel koden visar, ändrar du plats för **slut punkts** variabeln. Den här platsen finns på **översikts** sidan för din QNA Maker-resurs i Azure Portal.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autentisera körningen för att skapa ett svar

I **main** -metoden skapar du en variabel för resursens Azure-nyckel som hämtas från en miljövariabler `QNAMAKER_ENDPOINT_HOSTNAME` med `QNAMAKER_ENDPOINT_KEY`namnet och. När du publicerar din kunskaps bas returneras dessa värden. När du har publicerat kan du hitta de här inställningarna på sidan **Inställningar** på QNA Maker-portalen. 

Skapa en [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) för att söka i kunskaps basen för att skapa ett svar eller träna från aktiv inlärning.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par för [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) -objektet från tre källor:

* För **redaktionellt innehåll**använder du [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) -objektet.
* För **filer**använder du [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) -objektet. 
* För **URL: er**använder du en lista med strängar.

Anropa metoden [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) och skicka sedan det returnerade åtgärds-ID: t till [MonitorOperation](#get-status-of-an-operation) -metoden för att avsöka efter status. 

Den sista raden i följande kod returnerar kunskaps bas-ID: t från svaret från MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskaps bas genom att skicka i kunskaps bas-ID: t och en [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) som innehåller objekt för att [lägga till](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [Uppdatera](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)och [ta bort](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO till [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) -metoden. Använd [MonitorOperation](#get-status-of-an-operation) -metoden för att avgöra om uppdateringen har slutförts.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Hämta en kunskaps bas

Använd metoden [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) för att ladda ned databasen som en lista över [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Detta motsvarar _inte_ QNA Maker portalens export från inställnings sidan eftersom resultatet av den här metoden inte är en TSV-fil.

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

## <a name="run-the-application"></a>Köra programmet

Kör programmet med kommandot dotNet `run` från program katalogen.

```console
dotnet run
```

[Käll koden för den här snabb](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) starten är tillgänglig i QNA Maker C# samples GitHub-lagringsplatsen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Självstudier: Skapa och svara på en KB](../tutorials/create-publish-query-in-portal.md)

* [Vad är API för QnA Maker?](../Overview/overview.md)
* [Redigera en kunskapsbas](../how-to/edit-knowledge-base.md)
* [Hämta användnings analys](../how-to/get-analytics-knowledge-base.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).
