---
title: 'Snabbstart: Klient bibliotek för en egen klient för .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Kom igång med personanpassa klient biblioteket för .NET med hjälp av en inlärnings slinga.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: e9dd01a58309a6b65538b19b25df70e3d18866a9
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207353"
---
# <a name="quickstart-personalize-client-library-for-net"></a>Snabbstart: Anpassa klient bibliotek för .NET

Visa personligt innehåll i den C# här snabb starten med tjänsten personanpassa.

Kom igång med personanpassa klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

 * Rangordna en lista med åtgärder för anpassning.
 * Rapportera belönings Poäng som indikerar att det främsta antalet rankade åtgärder lyckades.

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-personalizer-azure-resource"></a>Skapa en personanpassa Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Personanpassare med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

<!-- rename TBD_KEY to something meaningful for your service, like TEXT_ANALYTICS_KEY -->
När du har skaffat en nyckel från din utvärderings prenumeration eller resurs skapar du två [miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY`för resurs nyckeln.
* `PERSONALIZER_RESOURCE_ENDPOINT`för resurs slut punkten.

### <a name="change-the-model-update-frequency"></a>Ändra modell uppdaterings frekvensen

Ändra uppdaterings frekvensen för **modellen** till 10 sekunder i den personliga resursen i Azure Portal. Detta kommer att träna tjänsten snabbt, så att du kan se hur de viktigaste åtgärderna ändras för varje iteration.

![Ändra modell uppdaterings frekvens](./media/settings/configure-model-update-frequency-settings.png)

När en säkerhetsslinga först instansieras finns det ingen modell eftersom det inte har skett några belönings-API-anrop att träna från. Ranknings anrop returnerar lika många sannolikheter för varje objekt. Ditt program borde fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.

### <a name="create-a-new-c-application"></a>Skapa ett nytt C# program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du kommandot dotNet `new` för att skapa en ny konsol app med namnet. `personalizer-quickstart` Det här kommandot skapar ett enkelt "Hello World C# "-projekt med en enda käll `Program.cs`fil:. 

```console
dotnet new console -n personalizer-quickstart
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

I program katalogen installerar du ett installations program för personanpassa klient bibliotek för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om innehållets rangordning skapar du en [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)och skickar den sedan till [klienten. Rangordnings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metod. Rangordnings metoden returnerar en RankResponse som innehåller det rankade innehållet. 

Om du vill skicka en belöning till Personanpassan skapar du en [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)och skickar den till [klienten. Belönings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) metod. 

Att fastställa belöningen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Detta bör vara ett av de primära design besluten i din anpassnings arkitektur. 

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande med personanpassa klient biblioteket för .NET:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Begär en rang](#request-a-rank)
* [Skicka en belöning](#send-a-reward)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **program.cs** -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

I **program** -klassen skapar du variabler för din resurs Azure-nyckel och slut punkt från miljövariablerna, med `PERSONALIZER_RESOURCE_KEY` namnet `PERSONALIZER_RESOURCE_ENDPOINT`och. Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE eller gränssnittet som kör det stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare i den här snabb starten.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till-metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey `PERSONALIZER_RESOURCE_KEY`är.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-content-choices-represented-as-actions"></a>Hämta innehålls val som visas som åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska rangordna. Lägg till följande metoder i program-klassen för att få en användares indata från kommando raden för tid på dag och aktuell kost preferens.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Båda metoderna använder `GetKey` metoden för att läsa användarens val från kommando raden. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av rang-och belönings samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten rangordnade innehållet. 

Följande kod i `main` -metoden i programmet loopar genom en cykel som ber användaren att ange sina inställningar på kommando raden, vilket skickar informationen till en person som ska rangordnas, och presentera det rankade valet för kunden att välja bland de och skicka en belöning till personligare signalera hur väl tjänsten gjorde en rangordning av valet.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Initialize Personalizer client.
    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        // <rank>
        // Get context information from the user.
        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        // Create current context from user specified data.
        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        // Exclude an action for personalizer ranking. This action will be held at its current position.
        // This simulates a business rule to force the action "juice" to be ignored in the ranking.
        // As juice is excluded, the return of the API will always be with a probability of 0.
        IList<string> excludeActions = new List<string> { "juice" };

        // Generate an ID to associate with the request.
        string eventId = Guid.NewGuid().ToString();

        // Rank the actions
        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);
        // </rank>

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        // <reward>
        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        // Send the reward for the action based on user response.
        client.Reward(response.EventId, new RewardRequest(reward));
        // </reward>

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

## <a name="request-a-rank"></a>Begär en rang

För att slutföra ranknings förfrågan ställer programmet till användarens inställningar för att skapa ett `currentContent` av innehålls valen. Processen kan skapa innehåll som ska undantas från rangordningen, som `excludeActions`visas som. Ranknings förfrågan behöver åtgärderna, currentContext, excludeActions och ett unikt ID för ranknings händelse (som GUID) för att ta emot det rankade svaret. 

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](concept-feature-evaluation.md) [åtgärder och funktioner](concepts-features.md) .  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning

För att slutföra belönings förfrågan hämtar programmet användarens val från kommando raden, tilldelar ett numeriskt värde till varje val och skickar sedan det unika ID: t för rangordning och det numeriska värdet till belönings metoden.

Den här snabb starten tilldelar ett enkelt tal som en belöning, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas [](concept-rewards.md) till belönings anropet som en icke-trivial fråga, beroende på dina behov. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med kommandot dotNet `run` från program katalogen.

```console
dotnet run
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Käll koden för den här snabb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) starten är tillgänglig i GitHub-lagringsplatsen för personanpassa exempel.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Så här fungerar Personanpassaren](how-personalizer-works.md)

* [Vad är Personanpassare?](what-is-personalizer.md)
* [Var kan du använda Personanpassare?](where-can-you-use-personalizer.md)
* [Felsökning](troubleshooting.md)

