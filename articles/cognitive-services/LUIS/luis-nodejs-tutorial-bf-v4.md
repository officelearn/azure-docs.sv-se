---
title: 'Självstudie: Language Understanding bot Node. js v4'
titleSuffix: Azure Cognitive Services
description: Använd Node.js och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattroboten använder appen Personalfrågor för att snabbt implementera en robotlösning. Roboten skapas med Bot Framework version 4 och webbappsroboten i Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 9a38f43b24e5db6a60ff38cd0f1d9b59b9875bba
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492694"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Självstudie: använda en Web App-robot som är aktive rad med Language Understanding i Node. js 

Använd Node. js för att bygga en chatt-robot integrerad med språk förståelse (LUIS). Roboten är byggd med Azure [Web App bot-](https://docs.microsoft.com/azure/bot-service/) resursen och [bot Framework version](https://github.com/Microsoft/botbuilder-dotnet) v4.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Hämta bot-projektet som skapats av Web bot-tjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Förutsättningar

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Skapa en bot-resurs för webb program

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-nodejs-bot-resource-group`.|
    |Plats|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App Service-plan/plats|Ändra inte från angivet standardvärde.|
    |Application Insights|Ändra inte från angivet standardvärde.|
    |Microsoft app-ID och lösen ord|Ändra inte från angivet standardvärde.|

1. I **robot-mallen**väljer du följande och väljer sedan knappen **Välj** under följande inställningar:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**Node.js**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|
    
1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-nodejs-bot-XXXX`. Namnet baseras på/Azure bot service-appens namn.

    [![Skapa en webbappsrobot](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänsten har skapats innan du fortsätter.

## <a name="the-bot-has-a-language-understanding-model"></a>Roboten har en Language Understanding modell

Processen för att skapa bot-tjänsten skapar också en ny LUIS-app med avsikter och exempel yttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter: 

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Book Flight|`Travel to Paris`|
|Avbryt|`bye`|
|GetWeather|`what's the weather like?`|
|Ingen|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa roboten i Web Chat

1. Medan du fortfarande beAzure Portal för den nya bot väljer du **test i Web Chat**. 
1. I text rutan **Skriv ditt meddelande** anger du text `Book a flight from Seattle to Berlin tomorrow`. Roboten svarar med verifiering som du vill boka en flygning på. 

    ![Skärm bild av Azure Portal anger du texten Hej.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda test funktionerna för att snabbt testa din robot. Om du vill ha mer fullständig testning, inklusive fel sökning, laddar du ned bot koden och använder Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Ladda ned käll koden för webb programs bot
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn. 

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen. 

1. Välj **Download Bot source code** (Ladda ned robotens källkod). 

    [![Ladda ned källkoden för webbappsroboten för grundläggande robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup-dialogrutan frågar **innehåller appinställningar i den hämtade ZIP-filen?** väljer du **Ja**. Detta ger LUIS-inställningarna. 

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken. 

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet med Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska koden för att skicka uttryck till LUIS och hämta svar

1. Om du vill skicka användar uttryck till LUIS förutsägelse slut punkten öppnar du **dialog rutorna-> flightBookingRecognizer. js-** fil. Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från **executeLuisQuery** -metoden.  

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

1. **Dialog rutorna – > mainDialog** fångar uttryck och skickar dem till ExecuteLuisQuery i actStep-metoden.


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Använd bot-emulatorn för att testa roboten

Ställ en fråga på roboten för att ställa en fråga om bok flyg avsikten.

1. Starta bot-emulatorn och välj **Öppna bot**.
1. Ange din robot-URL i dialog rutan **öppna en robot** -pop, till exempel `http://localhost:3978/api/messages`. `/api/messages` vägen är webb adressen för bot.
1. Ange **Microsoft app-ID** och **Microsoft app-lösenordet**, som finns i **. kuvert** -filen i roten av den robot kod som du laddade ned.

1. I bot-emulatorn anger `Book a flight from Seattle to Berlin tomorrow` och får samma svar för den grundläggande roboten som du fick i **testet i Web Chat**.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Välj **Ja**. Roboten svarar med en sammanfattning av dess åtgärder. 
1. Från loggen för bot-emulatorn väljer du den rad som innehåller `Luis Trace`. Detta visar JSON-svaret från LUIS för avsikten och entiteten i uttryck.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar. 

> [!div class="nextstepaction"]
> [Bygg en Language Understanding-app med en anpassad ämnes domän](luis-quickstart-intents-only.md)