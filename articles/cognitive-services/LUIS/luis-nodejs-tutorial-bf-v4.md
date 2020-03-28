---
title: 'Handledning: Språk understanding Bot Node.js v4'
description: Använd Node.js och skapa en chattrobot som är integrerad med språköverensning (LUIS) i den här självstudien. Den här chattroboten använder appen Personalfrågor för att snabbt implementera en robotlösning. Roboten skapas med Bot Framework version 4 och webbappsroboten i Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987856"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Självstudiekurs: Använd en webbapprobot aktiverad med språk understanding in Node.js

Använd Node.js för att skapa en chattrobot som är integrerad med språksyssning (LUIS). Roboten är byggd med Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) resurs och Bot Framework [version](https://github.com/Microsoft/botbuilder-dotnet) V4.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Ladda ner bot-projektet som skapats av webbrobottjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Krav

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio-kod](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Skapa en botresurs för webbappar

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-nodejs-bot-resource-group`.|
    |Location|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App serviceplan/plats|Ändra inte från det angivna standardvärdet.|
    |Application Insights|Ändra inte från det angivna standardvärdet.|
    |Microsoft-app-ID och lösenord|Ändra inte från det angivna standardvärdet.|

1. I **Bot-mallen**väljer du följande och väljer sedan knappen **Välj** under följande inställningar:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**Node.js**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|

1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-nodejs-bot-XXXX`. Det här namnet baseras på namnet på /Azure Bot Service-appen.

    > [!div class="mx-imgBorder"]
    > [![Skapa webbapprobot](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänsten skapas innan du fortsätter.

## <a name="the-bot-has-a-language-understanding-model"></a>Boten har en språk understanding-modell

Bot-tjänsten skapar också en ny LUIS-app med avsikter och exempelyttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter:

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Boka flyg|`Travel to Paris`|
|Avbryt|`bye`|
|GetWeather|`what's the weather like?`|
|Inget|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa boten i webbchatt

1. Medan du fortfarande är i Azure-portalen för den nya roboten väljer du **Testa i webbchatt**.
1. Skriv texten i **textrutan Skriv** `Book a flight from Seattle to Berlin tomorrow`meddelandet . Roboten svarar med en verifiering att du vill boka ett flyg.

    ![Skärmbild av Azure-portalen anger du texten "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda testfunktionen för att snabbt testa din robot. För mer komplett testning, inklusive felsökning, ladda ner botkoden och använd Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Ladda ner källkoden för webbapproken
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn.

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen.

1. Välj **Download Bot source code** (Ladda ned robotens källkod).

    [![Ladda ner webbapp bot källkod för grundläggande bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup-dialogrutan frågar **Inkludera appinställningar i den nedladdade zip-filen?** väljer du **Ja**. Detta ger LUIS-inställningarna.

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken.

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet med Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska kod för att skicka yttrande till LUIS och få svar

1. Om du vill skicka användarens uttryck till slutpunkten LUIS-förutsägelse öppnar du **dialogrutorna -> flightBookingRecognizer.js-filen.** Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från metoden **executeLuisQuery.**

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **Dialogrutorna -> mainDialog** fångar uttrycket och skickar det till executeLuisQuery i metoden actStep.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Använd bot emulator för att testa bot

Ställ bot en fråga för Book Flight avsikt.

1. Börja Bot Emulator och välj **Öppna Bot**.
1. I popup-dialogrutan **Öppna en robot** anger du din `http://localhost:3978/api/messages`bot-URL, till exempel . Rutten `/api/messages` är webbadressen för roboten.
1. Ange **lösenordet för Microsoft App ID** och **Microsoft App**som finns i **.env-filen** i roten till den hämtade robotkoden.

1. I bot emulator, `Book a flight from Seattle to Berlin tomorrow` ange och få samma svar för den grundläggande bot som du fick i **test i webbchatt**.

    [![Grundläggande bot svar i emulatorn](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Välj **Ja**. Roboten svarar med en sammanfattning av sina åtgärder.
1. Välj den rad som innehåller `Luis Trace`. Detta visar JSON-svaret från LUIS för avsikten och entiteterna för uttryck.

    [![Grundläggande bot svar i emulatorn](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar.

> [!div class="nextstepaction"]
> [Skapa en språk understanding-app med en anpassad ämnesdomän](luis-quickstart-intents-only.md)