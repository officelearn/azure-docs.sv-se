---
title: inkludera fil
description: inkludera fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.date: 07/30/2020
ms.openlocfilehash: ba7885859adc1d9899c66917204306c8a0d0092f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246254"
---
[Referens dokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)  | [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org) och NPM.

## <a name="using-this-quickstart"></a>Använd den här snabb starten


Det finns flera steg för att använda den här snabb starten:

* I Azure Portal skapar du en personanpassar-resurs
* I Azure Portal för personanpassa resursen, på sidan **konfiguration** , ändrar du modell uppdaterings frekvensen till ett mycket kort intervall
* Skapa en kod fil i en kod redigerare och redigera kod filen
* På kommando raden eller terminalen installerar du SDK från kommando raden
* Kör kod filen i kommando raden eller terminalen

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir myapp && cd myapp
```

Kör `npm init -y` kommandot för att skapa en `package.json` fil.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Installera Node.js bibliotek för Personanpassare

Installera installations program för en personanpassa klient för Node.js med följande kommando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installera de återstående NPM-paketen för den här snabb starten:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om det enda bästa objektet i innehållet skapar du en [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)och skickar det sedan till [klienten. Rangordnings](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) metod. Rangordnings metoden returnerar en RankResponse.

Om du vill skicka en belöning till Personanpassan skapar du en [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)och skickar den sedan till [belönings](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) metoden i händelse klassen.

Att fastställa belöningen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](../concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Detta bör vara ett av de primära design besluten i din anpassnings arkitektur.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med det anpassade klient biblioteket för Node.js:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Rang-API](#request-the-best-action)
* [Belönings-API](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa ett nytt Node.js-program i önskat redigerings program eller IDE-namn `sample.js` .

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna **sample.js** -filen i önskat redigerings program eller IDE. Lägg till följande `requires` för att lägga till NPM-paketen:

[!code-javascript[Add module dependencies](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

Redigera nyckel-och slut punkts variablerna överst i kod filen för resursens Azure-nyckel och slut punkt. 

[!code-javascript[Add Personalizer resource information](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till-metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey är `PERSONALIZER_RESOURCE_KEY` .

[!code-javascript[Create a Personalizer client](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Hämta innehålls val som visas som åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska välja det bästa innehålls objektet från. Lägg till följande metoder i program-klassen för att representera uppsättningen med åtgärder och deras funktioner.

[!code-javascript[Create user features](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av [rang](#request-the-best-action) -och [belönings](#send-a-reward) samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten utförs.

Följande kod går igenom en cykel som ber användaren att ange sina inställningar på kommando raden, vilket innebär att informationen skickas till Personanpassare för att välja den bästa åtgärden, vilket innebär att valet av kund kan välja bland listan och sedan skicka en belöning till en person som ska signalera hur väl tjänsten gjorde sitt val.

[!code-javascript[Create the learning loop](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=mainLoop)]

Ta en närmare titt på rang-och belönings anropen i följande avsnitt.

Lägg till följande metoder, som [hämtar innehålls valen](#get-content-choices-represented-as-actions), innan du kör kod filen:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Begär den bästa åtgärden

För att slutföra ranknings förfrågan ber programmet användarens inställningar att skapa innehålls val. Processen kan skapa innehåll som ska undantas från åtgärder, som visas som `excludeActions` . Ranknings förfrågan behöver [åtgärder](../concepts-features.md#actions-represent-a-list-of-options) och deras funktioner, currentContext-funktioner, excludeActions och ett unikt ID för ranknings händelse för att ta emot det rankade svaret.

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](../concept-feature-evaluation.md) [åtgärder och funktioner](../concepts-features.md) .

[!code-javascript[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning


För att få belönings poängen att skicka i belönings förfrågan får programmet användarens val från kommando raden, tilldelar ett numeriskt värde till markeringen och skickar sedan det unika händelse-ID: t och belönings poängen som det numeriska värdet till belönings-API: et.

Den här snabb starten tilldelar ett enkelt tal som en belönings poäng, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas till [belönings](../concept-rewards.md) anropet som en icke-trivial fråga, beroende på dina behov.

[!code-javascript[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med Node.js från program katalogen.

```console
node sample.js
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
