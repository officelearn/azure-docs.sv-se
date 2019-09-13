---
title: 'Snabbstart: För-klient bibliotek för Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Kom igång med personanpassa klient biblioteket för Node. js med hjälp av en inlärnings slinga.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: a7c02c92ed61e4c8c8bd6a634cc9c6ad3538396e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883693"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>Snabbstart: Anpassa klient bibliotek för Node. js

Visa personligt innehåll i den här Node. js-snabb starten med tjänsten personanpassa.

Kom igång med personanpassa klient biblioteket för Node. js. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

 * Rangordna en lista med åtgärder för anpassning.
 * Rapportera belönings Poäng som indikerar att det främsta antalet rankade åtgärder lyckades.

[Exempel på biblioteks käll kods](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) [paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node. js](https://nodejs.org).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-personalizer-azure-resource"></a>Skapa en personanpassa Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Personanpassare med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har skaffat en nyckel från din utvärderings prenumeration eller resurs skapar du två [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`för resurs nyckeln.
* `PERSONALIZER_ENDPOINT`för resurs slut punkten.

I Azure Portal är både nyckel-och slut punkts värden tillgängliga på sidan **snabb start** .


### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot för att skapa en `package.json` fil. `npm init -y` 

```console
npm init -y
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Installera Node. js-biblioteket för Personanpassare

Installera det personliga klient biblioteket för Node. js med följande kommando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Installera de återstående NPM-paketen för den här snabb starten:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

### <a name="change-the-model-update-frequency"></a>Ändra modell uppdaterings frekvensen

Ändra **uppdaterings frekvensen för modellen** till 10 sekunder i den personliga resursen i Azure Portal. Detta kommer att träna tjänsten snabbt, så att du kan se hur de viktigaste åtgärderna ändras för varje iteration.

![Ändra modell uppdaterings frekvens](./media/settings/configure-model-update-frequency-settings.png)

När en säkerhetsslinga först instansieras finns det ingen modell eftersom det inte har skett några belönings-API-anrop att träna från. Ranknings anrop returnerar lika många sannolikheter för varje objekt. Ditt program borde fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett PersonalizerClient-objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om innehållets rangordning skapar du en RankRequest och skickar den sedan till klienten. Rangordnings metod. Rangordnings metoden returnerar en RankResponse som innehåller det rankade innehållet. 

Om du vill skicka en belöning till Personanpassan skapar du en RewardRequest och skickar den till klienten. Belönings metod. 

Att fastställa belöningen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Detta bör vara ett av de primära design besluten i din anpassnings arkitektur. 

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande med det anpassade klient biblioteket för Node. js:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Begär en rang](#request-a-rank)
* [Skicka en belöning](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa ett nytt Node. js-program i önskat redigerings program eller IDE `sample.js`-namn. 

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna **Sample. js** -filen i önskat redigerings program eller IDE. Lägg till följande `requires` för att lägga till NPM-paketen:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

Skapa variabler för resursens Azure-nyckel och slut punkt från miljövariablerna, med `PERSONALIZER_KEY` namnet `PERSONALIZER_ENDPOINT`och. Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE eller gränssnittet som kör det stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare i den här snabb starten.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till-metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey `PERSONALIZER_RESOURCE_KEY`är.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Hämta innehålls val som visas som åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska rangordna. Lägg till följande metoder i program-klassen för att få en användares indata från kommando raden för tid på dag och aktuell kost preferens.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av [rang](#request-a-rank) -och [belönings](#send-a-reward) samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten rangordnade innehållet. 

Följande upprepnings kod loopar genom en cykel som ber användaren att ange sina inställningar på kommando raden, vilket skickar informationen till en person som ska rangordnas, och som presenterar det rankade valet för kunden att välja bland listan och sedan skicka en belöning till Personligare signalerar hur väl tjänsten gjorde en rangordning av valet.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Ta en närmare titt på rang-och belönings anropen i följande avsnitt.

## <a name="request-a-rank"></a>Begär en rang

För att slutföra ranknings förfrågan ber programmet användarens inställningar att skapa innehålls val. Processen kan skapa innehåll som ska undantas från rangordningen, som `excludeActions`visas som. Ranknings förfrågan behöver [åtgärderna](concepts-features.md#actions-represent-a-list-of-options), CurrentContext, excludeActions och ett unikt ID för ranknings händelse (som GUID) för att ta emot det rankade svaret. 

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](concept-feature-evaluation.md) [åtgärder och funktioner](concepts-features.md) .  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning

För att slutföra belönings förfrågan hämtar programmet användarens val från kommando raden, tilldelar ett numeriskt värde till varje val och skickar sedan det unika ID: t för rangordning och det numeriska värdet till belönings metoden.

Den här snabb starten tilldelar ett enkelt tal som en belöning, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas till [belönings](concept-rewards.md) anropet som en icke-trivial fråga, beroende på dina behov. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med Node. js från program katalogen.

```console
node sample.js
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

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
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js).
