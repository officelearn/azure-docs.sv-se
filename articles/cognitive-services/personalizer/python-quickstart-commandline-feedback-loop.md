---
title: 'Snabb start: ett personligt klient bibliotek för python'
titleSuffix: Azure Cognitive Services
description: Den här snabb starten visar hur du kommer igång med personanpassa klient biblioteket för python med hjälp av en inlärnings slinga.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 01/09/2020
ms.author: diberry
ms.openlocfilehash: 0073b03cd06bcf5a6e0733ef1b72061e72c3afe2
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860307"
---
# <a name="quickstart-personalizer-client-library-for-python"></a>Snabb start: ett personligt klient bibliotek för python

Visa personligt innehåll i den här python-snabb starten med tjänsten personanpassa.

Kom igång med personanpassa klient biblioteket för python. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

 * Rangordna en lista med åtgärder för anpassning.
 * Rapportera belönings Poäng som indikerar att det främsta antalet rankade åtgärder lyckades.

[Referens dokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python) | [Library Source Code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer)| [Package (pypi) | -](https://pypi.org/project/azure-cognitiveservices-personalizer/) [exempel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Använd den här snabb starten


Det finns flera steg för att använda den här snabb starten:

* I Azure Portal skapar du en personanpassar-resurs
* I Azure Portal för personanpassa resursen, på sidan **konfiguration** , ändrar du modell uppdaterings frekvensen
* Skapa en kod fil i en kod redigerare och redigera kod filen
* På kommando raden eller terminalen installerar du SDK från kommando raden
* Kör kod filen i kommando raden eller terminalen


## <a name="create-a-personalizer-azure-resource"></a>Skapa en personanpassa Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Personanpassare med hjälp av [Azure Portal](https://portal.azure.com/) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Mer information finns i så här [skapar du en Cognitive Services resurs med hjälp av Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) . Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har skaffat en nyckel från din utvärderings prenumeration eller resurs skapar du två [miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` för resurs nyckeln.
* `PERSONALIZER_ENDPOINT` för resurs slut punkten.

I Azure Portal är både nyckel-och slut punkts värden tillgängliga på sidan **snabb start** .


## <a name="install-the-python-library-for-personalizer"></a>Installera python-biblioteket för Personanpassare

Installera det personliga klient biblioteket för python med följande kommando:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="change-the-model-update-frequency"></a>Ändra modell uppdaterings frekvensen

I Azure Portal i gruppen personanpassa på sidan **konfiguration** ändrar du **modell uppdaterings frekvensen** till 10 sekunder. Detta kommer att träna tjänsten snabbt, så att du kan se hur de viktigaste åtgärderna ändras för varje iteration.

![Ändra modell uppdaterings frekvens](./media/settings/configure-model-update-frequency-settings.png)

När en säkerhetsslinga först instansieras finns det ingen modell eftersom det inte har skett några belönings-API-anrop att träna från. Ranknings anrop returnerar lika många sannolikheter för varje objekt. Ditt program borde fortfarande alltid rangordna innehåll med hjälp av utdata från RewardActionId.

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett PersonalizerClient-objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om innehållets rangordning skapar du en RankRequest och skickar den sedan till klienten. Rangordnings metod. Rangordnings metoden returnerar en RankResponse som innehåller det rankade innehållet.

Om du vill skicka en belöning till Personanpassan skapar du en RewardRequest och skickar den till klienten. Belönings metod.

Att fastställa belöningen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Detta bör vara ett av de primära design besluten i din anpassnings arkitektur.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med personanpassa klient biblioteket för python:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Begär en rang](#request-a-rank)
* [Skicka en belöning](#send-a-reward)

## <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE med namnet `sample.py`.

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **Sample.py** -filen i önskat redigerings program eller IDE. Lägg till följande:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

Skapa variabler för resursens Azure-nyckel och slut punkt från miljövariablerna, med namnet `PERSONALIZER_RESOURCE_KEY` och `PERSONALIZER_RESOURCE_ENDPOINT`. Om du har skapat miljövariablerna när programmet har startats måste redigeraren, IDE eller gränssnittet som kör det stängas och läsas in igen för att få åtkomst till variabeln. Metoderna kommer att skapas senare i den här snabb starten.

Resurs namnet är en del av slut punktens URL: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey är `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Hämta innehålls val som visas som åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska rangordna. Lägg till följande metoder för att få en användares indata från kommando raden för tid på dag och aktuell kost preferens.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av [rang](#request-a-rank) -och [belönings](#send-a-reward) samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten rangordnade innehållet.

Följande kod går igenom en cykel som ber användaren om sina inställningar på kommando raden, vilket skickar informationen till en person som ska rangordnas, och som presenterar det rankade valet för kunden att välja bland listan, och sedan skicka en belöning till Personanpassan signalerar hur väl tjänsten gjorde en rangordning av valet.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Ta en närmare titt på rang-och belönings anropen i följande avsnitt.

Lägg till följande metoder, som [hämtar innehålls valen](#get-content-choices-represented-as-actions), innan du kör kod filen:

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Begär en rang

För att slutföra ranknings förfrågan ställer programmet till användarens inställningar för att skapa en `currentContent` av innehålls valen. Processen kan skapa innehåll som ska undantas från rankningen, som visas som `excludeActions`. Ranknings förfrågan behöver åtgärderna, currentContext, excludeActions och ett unikt ID för ranknings händelse (som GUID) för att ta emot det rankade svaret.

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](concept-feature-evaluation.md) [åtgärder och funktioner](concepts-features.md) .

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning

För att slutföra belönings förfrågan hämtar programmet användarens val från kommando raden, tilldelar ett numeriskt värde till varje val och skickar sedan det unika ID: t för rangordning och det numeriska värdet till belönings metoden.

Den här snabb starten tilldelar ett enkelt tal som en belöning, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas till [belönings](concept-rewards.md) anropet som en icke-trivial fråga, beroende på dina behov.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med python från program katalogen.

```console
python sample.py
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Så här fungerar Personanpassaren](how-personalizer-works.md)

* [Vad är Personanpassare?](what-is-personalizer.md)
* [Var kan du använda Personanpassare?](where-can-you-use-personalizer.md)
* [Troubleshooting](troubleshooting.md) (Felsökning)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
