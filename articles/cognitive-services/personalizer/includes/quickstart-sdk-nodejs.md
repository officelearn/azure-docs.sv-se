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
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122882"
---
[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Ursprungskod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org) och NPM.

## <a name="using-this-quickstart"></a>Använda den här snabbstarten


Det finns flera steg för att använda den här snabbstarten:

* Skapa en Personalizer-resurs i Azure-portalen
* I Azure-portalen, för Personalizer-resursen, på **sidan Konfiguration,** ändrar du modellens uppdateringsfrekvens till ett mycket kort intervall
* Skapa en kodfil i en kodredigerare och redigera kodfilen
* På kommandoraden eller terminalen installerar du SDK från kommandoraden
* På kommandoraden eller terminalen kör du kodfilen

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den.

```console
mkdir myapp && cd myapp
```

Kör `npm init -y` kommandot för `package.json` att skapa en fil.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Installera nod.js-biblioteket för Personalizer

Installera Personalizer-klientbiblioteket för Node.js med följande kommando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installera de återstående NPM-paketen för den här snabbstarten:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objektmodell

Personalizer-klienten är ett [PersonalizerClient-objekt](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) som autentiserar till Azure med Microsoft.Rest.ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om det enskilt bästa objektet i innehållet skapar du en [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)och skickar det sedan till [klienten. Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) metod. Rank-metoden returnerar en RankResponse.

Om du vill skicka en belöning till Personalizer skapar du en [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)och skickar den sedan till [belöningsmetoden](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) i klassen Events.

Att bestämma belöningen, i denna snabbstart är trivialt. I ett produktionssystem, fastställandet av vad som påverkar [belöningspoängen](../concept-rewards.md) och hur mycket som kan vara en komplex process, som du kan välja att ändra över tiden. Detta bör vara ett av de primära designbesluten i din Personalizer-arkitektur.

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med Personalizer-klientbiblioteket för Node.js:

* [Skapa en Personalizer-klient](#create-a-personalizer-client)
* [Rank API](#request-the-best-action)
* [Belönings-API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa ett nytt Node.js-program i önskad `sample.js`redigerare eller IDE med namnet .

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna **sample.js-filen** i önskad redigerare eller IDE. Lägg till `requires` följande för att lägga till NPM-paket:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till personalizer-resursinformation

Skapa variabler för resursens Azure-nyckel och slutpunkt som `PERSONALIZER_KEY` `PERSONALIZER_ENDPOINT`hämtats från miljövariablerna med namnet och . Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE: t eller shell som kör det stängas och laddas om för att komma åt variabeln. Metoderna skapas senare i den här snabbstarten.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en Personalizer-klient

Skapa sedan en metod för att returnera en Personalizer-klient. Parametern till metoden `PERSONALIZER_RESOURCE_ENDPOINT` är och ApiKey `PERSONALIZER_RESOURCE_KEY`är .

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Få innehållsval representerade som åtgärder

Åtgärder representerar de innehållsalternativ som du vill att Personalizer ska välja det bästa innehållsobjektet. Lägg till följande metoder i klassen Program för att representera uppsättningen åtgärder och deras funktioner.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Skapa utbildningsloopen

Personalizers inlärningsslinga är en cykel av [rank-](#request-the-best-action) och [belöningssamtal.](#send-a-reward) I den här snabbstarten följs varje rank-samtal för att anpassa innehållet av ett belöningssamtal för att tala om för Personalizer hur bra tjänsten fungerade.

Följande kod loopar genom en cykel för att be användaren sina preferenser på kommandoraden, skicka den informationen till Personalizer för att välja den bästa åtgärden, presentera valet till kunden att välja bland listan, sedan skicka en belöning till Personalizer Personalizer signalerar hur bra tjänsten gjorde i sitt val.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Ta en närmare titt på rang och belöning samtal i följande avsnitt.

Lägg till följande metoder, som [hämtar innehållsalternativen,](#get-content-choices-represented-as-actions)innan du kör kodfilen:

* getActionsList
* fåContextFeaturesList

## <a name="request-the-best-action"></a>Begär den bästa åtgärden

För att slutföra Rank-begäran ber programmet användarens inställningar att skapa innehållsval. Processen kan skapa innehåll som kan uteslutas från åtgärderna, som visas som `excludeActions`. Rank-begäran behöver [åtgärderna](../concepts-features.md#actions-represent-a-list-of-options) och deras funktioner, aktuellaKontextfunktioner, excludeActions och ett unikt rankhändelse-ID för att få det rankade svaret.

Denna snabbstart har enkla sammanhang funktioner i tid på dagen och användarens mat preferens. I produktionssystem kan det vara en icke-trivial fråga att fastställa och [utvärdera](../concept-feature-evaluation.md) [åtgärder och funktioner.](../concepts-features.md)

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning


För att få belöningspoängen att skicka in belöningsbegäran får programmet användarens val från kommandoraden, tilldelar ett numeriskt värde till markeringen och skickar sedan det unika händelse-ID:et och belöningspoängen som det numeriska värdet till belönings-API:et.

Den här snabbstarten tilldelar ett enkelt nummer som belöningspoäng, antingen en nolla eller en 1. I produktionssystem kan det vara en icke-trivial fråga att bestämma när och vad du ska skicka till [belöningssamtalet,](../concept-rewards.md) beroende på dina specifika behov.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med nod.js från programkatalogen.

```console
node sample.js
```

![Snabbstartsprogrammet ställer ett par frågor för att samla in användarinställningar, så kallade funktioner, och ger sedan den översta åtgärden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
