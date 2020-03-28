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
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122924"
---
[Referensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Paket (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Använda den här snabbstarten


Det finns flera steg för att använda den här snabbstarten:

* Skapa en Personalizer-resurs i Azure-portalen
* I Azure-portalen, för Personalizer-resursen, på **sidan Konfiguration,** ändrar du modellens uppdateringsfrekvens till ett mycket kort intervall
* Skapa en kodfil i en kodredigerare och redigera kodfilen
* På kommandoraden eller terminalen installerar du SDK från kommandoraden
* På kommandoraden eller terminalen kör du kodfilen

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Installera Python-biblioteket för Personalizer

Installera Personalizer-klientbiblioteket för Python med följande kommando:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objektmodell

Personalizer-klienten är ett [PersonalizerClient-objekt](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) som autentiserar till Azure med Microsoft.Rest.ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om det enskilt bästa objektet i innehållet skapar du en [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)och skickar det sedan till klienten. Rank metod. Rank-metoden returnerar en RankResponse.

Om du vill skicka en belöningspoäng till Personalizer anger du händelse-ID och belöningspoängen (värdet) som ska skickas till [belöningsmetoden](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) i klassen EventOperations.

Att bestämma belöningen, i denna snabbstart är trivialt. I ett produktionssystem, fastställandet av vad som påverkar [belöningspoängen](../concept-rewards.md) och hur mycket som kan vara en komplex process, som du kan välja att ändra över tiden. Detta bör vara ett av de primära designbesluten i din Personalizer-arkitektur.

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du gör följande med Personalizer-klientbiblioteket för Python:

* [Skapa en Personalizer-klient](#create-a-personalizer-client)
* [Rank API](#request-the-best-action)
* [Belönings-API](#send-a-reward)

## <a name="create-a-new-python-application"></a>Skapa ett nytt pythonprogram

Skapa ett nytt Python-program i önskad `sample.py`redigerare eller IDE med namnet .

## <a name="add-the-dependencies"></a>Lägg till beroenden

Öppna den **sample.py** filen i önskad redigerare eller IDE från projektkatalogen. Lägg till följande:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till personalizer-resursinformation

Skapa variabler för resursens Azure-nyckel och slutpunkt som `PERSONALIZER_RESOURCE_KEY` `PERSONALIZER_RESOURCE_ENDPOINT`hämtats från miljövariablerna med namnet och . Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE: t eller shell som kör det stängas och laddas om för att komma åt variabeln. Metoderna skapas senare i den här snabbstarten.

Resursnamnet är en del av `https://<your-resource-name>.api.cognitive.microsoft.com/`slutpunkts-URL:en: .

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en Personalizer-klient

Skapa sedan en metod för att returnera en Personalizer-klient. Parametern till metoden `PERSONALIZER_RESOURCE_ENDPOINT` är och ApiKey `PERSONALIZER_RESOURCE_KEY`är .

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Få innehållsval representerade som åtgärder

Åtgärder representerar de innehållsalternativ som du vill att Personalizer ska välja det bästa innehållsobjektet. Lägg till följande metoder i klassen Program för att representera uppsättningen åtgärder och deras funktioner.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Skapa utbildningsloopen

Personalizers inlärningsslinga är en cykel av [rank-](#request-the-best-action) och [belöningssamtal.](#send-a-reward) I den här snabbstarten följs varje rankning för att anpassa innehållet av ett belöningssamtal för att tala om för Personalizer hur bra tjänsten fungerade.

Följande kod loopar genom en cykel för att be användaren sina preferenser på kommandoraden, skicka den informationen till Personalizer för att välja den bästa åtgärden, presentera valet till kunden att välja bland listan, sedan skicka en belöning till Personalizer Personalizer signalerar hur bra tjänsten gjorde i sitt val.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Lägg till följande metoder, som [hämtar innehållsalternativen,](#get-content-choices-represented-as-actions)innan du kör kodfilen:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Begär den bästa åtgärden


För att slutföra Rank-begäran ber programmet användarens inställningar `currentContent` att skapa ett av innehållsalternativen. Processen kan skapa innehåll som kan uteslutas från åtgärderna, som visas som `excludeActions`. Rank-begäran behöver åtgärderna och deras funktioner, aktuellaKontextfunktioner, excludeActions och ett unikt händelse-ID för att få svaret.

Denna snabbstart har enkla sammanhang funktioner i tid på dagen och användarens mat preferens. I produktionssystem kan det vara en icke-trivial fråga att fastställa och [utvärdera](../concept-feature-evaluation.md) [åtgärder och funktioner.](../concepts-features.md)

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning


För att få belöningspoängen att skicka in belöningsbegäran får programmet användarens val från kommandoraden, tilldelar ett numeriskt värde till markeringen och skickar sedan det unika händelse-ID:et och belöningspoängen som det numeriska värdet till belönings-API:et.

Den här snabbstarten tilldelar ett enkelt nummer som belöningspoäng, antingen en nolla eller en 1. I produktionssystem kan det vara en icke-trivial fråga att bestämma när och vad du ska skicka till [belöningssamtalet,](../concept-rewards.md) beroende på dina specifika behov.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med python från programkatalogen.

```console
python sample.py
```

![Snabbstartsprogrammet ställer ett par frågor för att samla in användarinställningar, så kallade funktioner, och ger sedan den översta åtgärden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)