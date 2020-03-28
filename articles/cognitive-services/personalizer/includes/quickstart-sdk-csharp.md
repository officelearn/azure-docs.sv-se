---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122889"
---
[Referensdokumentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Biblioteks](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [källkodspaket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Använda den här snabbstarten

Det finns flera steg för att använda den här snabbstarten:

* Skapa en Personalizer-resurs i Azure-portalen
* I Azure-portalen, för Personalizer-resursen, på **sidan Konfiguration,** ändrar du modellens uppdateringsfrekvens till ett mycket kort intervall
* Skapa en kodfil i en kodredigerare och redigera kodfilen
* På kommandoraden eller terminalen installerar du SDK från kommandoraden
* På kommandoraden eller terminalen kör du kodfilen

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskad redigerare eller IDE.

I ett konsolfönster (till exempel cmd, PowerShell eller `new` Bash) använder du kommandot `personalizer-quickstart`dotnet för att skapa en ny konsolapp med namnet . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
```

Ändra katalogen till den nyskapade appmappen. Du kan skapa programmet med:

```console
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

## <a name="install-the-sdk"></a>Installera SDK:n

Installera Personalizer-klientbiblioteket för .NET i programkatalogen med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Om du använder Visual Studio IDE är klientbiblioteket tillgängligt som ett nedladdningsbart NuGet-paket.

## <a name="object-model"></a>Objektmodell

Personalizer-klienten är ett [PersonalizerClient-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) som autentiserar till Azure med Microsoft.Rest.ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om det enskilt bästa objektet i innehållet skapar du en [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)och skickar det sedan till [klienten. Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metod. Rank-metoden returnerar en RankResponse.

Om du vill skicka en belöningspoäng till Personalizer skapar du en [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)och skickar den sedan till [klienten. Belöningsmetod.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)

Att bestämma belöningspoängen, i denna snabbstart är trivialt. I ett produktionssystem, fastställandet av vad som påverkar [belöningspoängen](../concept-rewards.md) och hur mycket som kan vara en komplex process, som du kan välja att ändra över tiden. Det här designbeslutet bör vara ett av de primära besluten i din Personalizer-arkitektur.

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med Personalizer-klientbiblioteket för .NET:

* [Skapa en Personalizer-klient](#create-a-personalizer-client)
* [Rank API](#request-the-best-action)
* [Belönings-API](#send-a-reward)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna den **Program.cs** filen i önskad redigerare eller IDE från projektkatalogen. Ersätt den `using` befintliga koden `using` med följande direktiv:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till personalizer-resursinformation

I klassen **Program** skapar du variabler för resursens Azure-nyckel och slutpunkt som hämtats från miljövariablerna med namnet `PERSONALIZER_RESOURCE_KEY` och `PERSONALIZER_RESOURCE_ENDPOINT`. Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE: t eller shell som kör det stängas och laddas om för att komma åt variabeln. Metoderna skapas senare i den här snabbstarten.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en Personalizer-klient

Skapa sedan en metod för att returnera en Personalizer-klient. Parametern till metoden `PERSONALIZER_RESOURCE_ENDPOINT` är och ApiKey `PERSONALIZER_RESOURCE_KEY`är .

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Få matvaror som rangliga åtgärder

Åtgärder representerar de innehållsalternativ som du vill att Personalizer ska välja det bästa innehållsobjektet. Lägg till följande metoder i klassen Program för att representera uppsättningen åtgärder och deras funktioner.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Hämta användarinställningar för kontext

Lägg till följande metoder i klassen Program för att hämta en användares indata från kommandoraden för den tid på dagen och aktuell matinställning. Dessa kommer att användas som kontextfunktioner.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Båda metoderna `GetKey` använder metoden för att läsa användarens val från kommandoraden.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Skapa utbildningsloopen

Personalizers inlärningsslinga är en cykel av [rank-](#request-the-best-action) och [belöningssamtal.](#send-a-reward) I den här snabbstarten följs varje rank-samtal för att anpassa innehållet av ett belöningssamtal för att tala om för Personalizer hur bra tjänsten fungerade.

Följande kod loopar genom en cykel för att be användaren sina preferenser på kommandoraden, skicka den informationen till Personalizer för att välja den bästa åtgärden, presentera valet till kunden att välja bland listan, sedan skicka en belöning poäng till Personalizer signalerar hur bra tjänsten gjorde i sitt val.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Lägg till följande metoder, som [hämtar innehållsalternativen,](#get-food-items-as-rankable-actions)innan du kör kodfilen:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Begär den bästa åtgärden

För att slutföra Rank-begäran ber programmet användarens inställningar `currentContent` att skapa ett av innehållsalternativen. Processen kan skapa innehåll som kan uteslutas från åtgärderna, som visas som `excludeActions`. Rank-begäran behöver åtgärderna och deras funktioner, aktuellaKontextfunktioner, excludeActions och ett unikt händelse-ID för att få svaret.

Denna snabbstart har enkla sammanhang funktioner i tid på dagen och användarens mat preferens. I produktionssystem kan det vara en icke-trivial fråga att fastställa och [utvärdera](../concept-feature-evaluation.md) [åtgärder och funktioner.](../concepts-features.md)

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning

För att få belöningspoängen att skicka in belöningsbegäran får programmet användarens val från kommandoraden, tilldelar ett numeriskt värde till markeringen och skickar sedan det unika händelse-ID:et och belöningspoängen som det numeriska värdet till belönings-API:et.

Den här snabbstarten tilldelar ett enkelt nummer som belöningspoäng, antingen en nolla eller en 1. I produktionssystem kan det vara en icke-trivial fråga att bestämma när och vad du ska skicka till [belöningssamtalet,](../concept-rewards.md) beroende på dina specifika behov.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med kommandot `run` dotnet från programkatalogen.

```console
dotnet run
```

![Snabbstartsprogrammet ställer ett par frågor för att samla in användarinställningar, så kallade funktioner, och ger sedan den översta åtgärden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Källkoden för den här snabbstarten](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) är tillgänglig i Personalizer-exemplen GitHub-databasen.
