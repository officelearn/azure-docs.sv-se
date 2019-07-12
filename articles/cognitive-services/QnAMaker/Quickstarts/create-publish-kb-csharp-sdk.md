---
title: 'Snabbstart: QnA Maker-klientbiblioteket för .NET'
titlesuffix: Azure Cognitive Services
description: Kom igång med QnA Maker-klientbiblioteket för .NET. Följ dessa steg för att installera paketet och prova att använda kodexempel för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: f38cbfa0efd3b9be9ca091942dca7ffcd91b6019
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828122"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Snabbstart: QnA Maker-klientbiblioteket för .NET

Kom igång med QnA Maker-klientbiblioteket för .NET. Följ dessa steg för att installera paketet och prova att använda kodexempel för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker. 

Använd QnA Maker-klientbiblioteket för .NET för att:

* Skapa en kunskapsbas 
* Hantera en kunskapsbas
* Publicera en kunskapsbas

[Referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [paketet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ C# exempel](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa ett kostnadsfritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Ställa in

### <a name="create-a-qna-maker-azure-resource"></a>Skapa en QnA Maker Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för QnA Maker med hjälp av den [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. 

När en nyckel från din resurs [skapar en miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) nyckeln, namnet `QNAMAKER_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Skapa en ny C# program

Skapa ett nytt .NET Core-program i önskat redigeringsprogram eller IDE. 

I ett konsolfönster (till exempel cmd, PowerShell eller Bash), använder du dotnet `new` kommando för att skapa en ny konsolapp med namnet `qna-maker-quickstart`. Det här kommandot skapar en enkel ”Hello World” C# projekt med en enskild källfil: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till den nyligen skapade app-mappen. Du kan skapa program med:

```console
dotnet build
```

Resultatet bör innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```


### <a name="install-the-sdk"></a>Installera SDK:n

I programkatalogen, installerar du QnA Maker-klientbiblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Om du använder Visual Studio IDE är-klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.


## <a name="object-model"></a>Objektmodell

QnA Maker-klienten är en [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objekt som autentiserar till Azure med hjälp av Microsoft.Rest.ServiceClientCredentials som innehåller din nyckel.

När klienten har skapats kan du använda den [kunskapsbas](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) egenskapen skapa, hantera och publicera din kunskapsbas. 

Hantera din kunskapsbas genom att skicka ett JSON-objekt. En metoden returnerar vanligtvis ett JSON-objekt som anger status för omedelbara åtgärder. För långvariga åtgärder är svaret åtgärden-ID. Anropa den [klienten. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metoden med åtgärds-ID för att fastställa den [status för begäran](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Kodexempel

Följande kodfragment visar hur du gör följande med QnA Maker-klientbibliotek för .NET:

* [Skapa en kunskapsbas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Ladda ned en kunskapsbas](#download-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Lägga till beroenden

Öppna från projektkatalogen den **Program.cs** filen i önskat redigeringsprogram eller IDE. Ersätt de befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autentisera klienten

I den **huvudsakliga** metod, skapa en variabel för din resurs Azure nyckeln hämtas från miljövariabeln `QNAMAKER_SUBSCRIPTION_KEY`. Om du har skapat miljövariabeln när appen startas behöver i redigeraren, IDE eller shell köra det vara stängd och in igen för att få åtkomst till variabeln. Metoderna som skapas senare.

Skapa sedan en [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) objekt med din nyckel och använda den med din slutpunkt för att skapa en [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objekt.

Om din nyckel inte finns i den `westus` region, som den här exempelkoden visar, ändrar du platsen för den **Endpoint** variabeln. Den här platsen finns på den **översikt** för din QnA Maker-resurs i Azure-portalen.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskapsbas lagrar par för frågor och svar för den [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objekt från tre källor:

* För **tryckt innehåll**, använda den [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objekt.
* För **filer**, använda den [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objekt. 
* För **URL: er**, använda en lista med strängar.

Anropa den [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metoden så släpps returnerade åtgärds-ID till den [MonitorOperation](#get-status-of-an-operation) metod för att söka efter status. 

Den sista raden i följande kod returnerar kunskapsbas-ID från svaret från MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskapsbas genom att skicka in kunskapsbas-ID och en [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) som innehåller [lägga till](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [uppdatera](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet), och [ta bort](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet)DTO objekt till den [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metod. Använd den [MonitorOperation](#get-status-of-an-operation) metod för att fastställa om uppdateringen lyckades.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Ladda ned en kunskapsbas

Använd den [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metod för att hämta databasen som en lista över [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Det här är _inte_ motsvarar QnA Maker-portalen export från den **inställningar** sidan eftersom resultatet av den här metoden inte är en TSV-fil.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera en kunskapsbas med hjälp av den [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metod. Detta tar den aktuella sparade och tränade modellen, refereras till av kunskapsbas-ID och publicerar som i en slutpunkt. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort en kunskapsbas med hjälp av den [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metod med en parameter i knowledge base-ID. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel som skapar och uppdaterar, kan ta tillräckligt med tid som i stället för att vänta tills processen är klar kan en [åtgärden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) returneras. Använd den [åtgärds-ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) från åtgärden att avsöka (med logik för omprövning) för att avgöra statusen för den ursprungliga metoden. 

Den _loop_ och _Task.Delay_ i följande kodblock för att simulera logik för omprövning. Dessa ska ersättas med din egen logik för omprövning. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Köra programmet

Köra programmet med dotnet `run` från programkatalogen.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Ta bort resursgruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Självstudier: Skapa och svarar på ett KB](../tutorials/create-publish-query-in-portal.md)

* [Vad är QnA Maker API?](../Overview/overview.md)
* [Redigera en kunskapsbas](../how-to/edit-knowledge-base.md)
* [Hämta användningsanalys](../how-to/get-analytics-knowledge-base.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).