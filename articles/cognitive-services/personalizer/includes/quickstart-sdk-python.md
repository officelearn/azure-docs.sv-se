---
title: inkludera fil
description: inkludera fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: aab4a59a35b098589adb462f2f0d6385802a9875
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246355"
---
[Referens dokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)  |  [Paket (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Använd den här snabb starten


Det finns flera steg för att använda den här snabb starten:

* I Azure Portal skapar du en personanpassar-resurs
* I Azure Portal för personanpassa resursen, på sidan **konfiguration** , ändrar du modell uppdaterings frekvensen till ett mycket kort intervall
* Skapa en kod fil i en kod redigerare och redigera kod filen
* På kommando raden eller terminalen installerar du SDK från kommando raden
* Kör kod filen i kommando raden eller terminalen

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Installera python-biblioteket för Personanpassare

Installera det personliga klient biblioteket för python med följande kommando:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om det enda bästa objektet i innehållet skapar du en [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)och skickar det sedan till klienten. Rangordnings metod. Rangordnings metoden returnerar en RankResponse.

Om du vill skicka en belönings poäng till Personanpassan ställer du in händelse-ID och belönings poängen (värdet) som ska skickas till [belönings](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) metoden i EventOperations-klassen.

Att fastställa belöningen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](../concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Detta bör vara ett av de primära design besluten i din anpassnings arkitektur.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med personanpassa klient biblioteket för python:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Rang-API](#request-the-best-action)
* [Belönings-API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE-namn `sample.py` .

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **Sample.py** -filen i önskat redigerings program eller IDE. Lägg till följande:

[!code-python[Add module dependencies](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

Redigera nyckel-och slut punkts variablerna överst i kod filen för resursens Azure-nyckel och slut punkt. 

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till-metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey är `PERSONALIZER_RESOURCE_KEY` .

[!code-python[Create the Personalizer client](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Hämta innehålls val som visas som åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska välja det bästa innehålls objektet från. Lägg till följande metoder i program-klassen för att representera uppsättningen med åtgärder och deras funktioner.

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av [rang](#request-the-best-action) -och [belönings](#send-a-reward) samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten utförs.

Följande kod går igenom en cykel som ber användaren att ange sina inställningar på kommando raden, vilket innebär att informationen skickas till Personanpassare för att välja den bästa åtgärden, vilket innebär att valet av kund kan välja bland listan och sedan skicka en belöning till en person som ska signalera hur väl tjänsten gjorde sitt val.

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=mainLoop&highlight=9,10,29)]

Lägg till följande metoder, som [hämtar innehålls valen](#get-content-choices-represented-as-actions), innan du kör kod filen:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Begär den bästa åtgärden


För att slutföra ranknings förfrågan ställer programmet till användarens inställningar för att skapa ett `currentContent` av innehålls valen. Processen kan skapa innehåll som ska undantas från åtgärder, som visas som `excludeActions` . Ranknings förfrågan behöver åtgärder och deras funktioner, currentContext-funktioner, excludeActions och ett unikt händelse-ID för att få svaret.

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](../concept-feature-evaluation.md) [åtgärder och funktioner](../concepts-features.md) .

[!code-python[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning


För att få belönings poängen att skicka i belönings förfrågan får programmet användarens val från kommando raden, tilldelar ett numeriskt värde till markeringen och skickar sedan det unika händelse-ID: t och belönings poängen som det numeriska värdet till belönings-API: et.

Den här snabb starten tilldelar ett enkelt tal som en belönings poäng, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas till [belönings](../concept-rewards.md) anropet som en icke-trivial fråga, beroende på dina behov.

[!code-python[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/python/Personalizer/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med python från program katalogen.

```console
python sample.py
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)