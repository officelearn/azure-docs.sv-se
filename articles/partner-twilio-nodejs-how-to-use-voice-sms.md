---
title: Använda Twilio för röst-, VoIP- och SMS-meddelanden i Azure
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kodexempel som skrivits i Node.js.
services: ''
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: ''
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: d9f419c48f64ba697e031dfc680bc9cb12bba5c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422927"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Använda Twilio för röst-, VoIP- och SMS-meddelanden i Azure
Den här guiden visar hur du skapar appar som kommunicerar med Twilio och node.js i Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Vad är Twilio?
Twilio är en API-plattform som gör det enkelt för utvecklare att göra och ta emot samtal, skicka och ta emot textmeddelanden och bädda in VoIP anropa den webbläsarbaserade och inbyggda mobilappar. Nu ska vi du kort gå igenom hur det fungerar innan du börjar med.

### <a name="receiving-calls-and-text-messages"></a>Ta emot samtal och textmeddelanden
Twilio gör att utvecklare kan [köpa programmerbart telefonnummer] [ purchase_phone] som kan användas både skicka och ta emot samtal och textmeddelanden. När ett Twilio-nummer får ett inkommande samtal eller SMS, skickar Twilio webbappen en HTTP POST eller GET-begäran ber dig anvisningar om hur du hanterar samtal eller textmeddelande. Servern kommer att besvara Twilios HTTP-begäran med [TwiML][twiml], en enkel uppsättning XML-taggar som innehåller instruktioner om hur du hanterar ett samtal eller SMS. Vi ska se exempel på TwiML i ett ögonblick.

### <a name="making-calls-and-sending-text-messages"></a>Anrop och skicka textmeddelanden
Utvecklare kan skicka textmeddelanden eller initiera utgående telefonsamtal genom att göra HTTP-begäranden till den webbtjänst Twilio-API. För utgående samtal måste utvecklaren även ange en URL som returnerar TwiML anvisningar om hur du hanterar utgående samtal när anslutningen är upprättad.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Bädda in VoIP-funktioner i UI-kod (JavaScript, iOS eller Android)
Twilio ger en klient-SDK som kan använda alla skrivbord webbläsare, en iOS-app eller en Android-app i en VoIP-telefon. I den här artikeln fokuserar vi på hur du använder VoIP anrop i webbläsaren. Förutom den *Twilio JavaScript SDK* som körs i webbläsaren, ett program för serversidan (vår node.js-program) måste användas för att utfärda ”funktionen token” till JavaScript-klient. Du kan läsa mer om hur du använder VoIP med node.js [på dev-bloggen Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Registrera dig för Twilio (Microsoft rabatt)
Innan du använder Twilio-tjänster, måste du först [registrera dig för ett konto][signup]. Microsoft Azure-kunder får en särskild rabatt - [måste du registrera dig här][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Skapa och distribuera en node.js-webbplats i Azure
Därefter behöver du skapa en node.js-webbplats som körs på Azure. [Den officiella dokumentationen för att göra detta finns här][azure_new_site]. På hög nivå, kommer du att göra följande:

* Registrera dig för ett Azure-konto om du inte redan har en
* Använda Azure-administrationskonsolen för att skapa en ny webbplats
* Att lägga till stöd för källa (förutsätter vi att du har använt git)
* Skapa en fil `server.js` med en enkel node.js-webbprogram
* Distribuera den här enkla tillämpningen till Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurera Twilio-modulen
Nu ska börjar vi att skriva ett enkelt node.js-program som använder Twilio-API. Innan vi börjar måste vi konfigurera våra Twilio-autentiseringsuppgifter.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurera Twilio-autentiseringsuppgifter i systemets miljövariabler
För att göra autentiserade förfrågningar mot serverdelen Twilio, måste vi våra kontots SID och autentiseringstoken, vilken funktion som användarnamnet och lösenordet som angetts för vår Twilio-konto. Det säkraste sättet att konfigurera dem för användning med modulen noden i Azure är via systemets miljövariabler som du kan ställa in direkt i Azure-administrationskonsolen.

Välj din node.js-webbplats och klicka på länken ”konfigurera”.  Om du rullar ner lite visas ett område där du kan ange konfigurationsegenskaper för programmet.  Ange dina Twilio-autentiseringsuppgifter ([finns på din Twilio-konsol][twilio_console]) enligt – se till att ge dem `TWILIO_ACCOUNT_SID` och `TWILIO_AUTH_TOKEN`respektive:

![Azure-administrationskonsolen][azure-admin-console]

När du har konfigurerat dessa variabler, startar du om ditt program i Azure-konsolen.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarera Twilio-modulen i package.json
Nu ska vi måste skapa en package.json för att hantera vår noden modulberoenden via [npm]. På samma nivå som den `server.js` filen du skapade i den *Azure/node.js* självstudien skapa en fil med namnet `package.json`.  Placera följande i den här filen:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Detta försäkrar twilio-modulen som ett beroende, samt populära [Express webbramverk] [ express] och EJS mall-motorn.  Okej, nu vi då var allt klart – skriva vi kod!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Göra ett utgående anrop
Nu ska vi skapa ett enkelt formulär som placerar ett anrop till ett tal som vi välja. Öppna `server.js`, och ange följande kod. Observera står det ”CHANGE_ME” - lagra namnet på din azure-webbplats:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Därefter skapar en katalog med namnet `views` – i den här katalogen, skapa en fil med namnet `index.ejs` med följande innehåll:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Distribuera din webbplats till Azure och öppna hemmet. Du ska kunna ange ditt telefonnummer i textfältet och får ett samtal från ditt Twilio-nummer!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Skicka ett SMS-meddelande
Nu ska vi ställer in ett användargränssnitt och formuläret hantering av logik för att skicka ett SMS. Öppna `server.js`, och Lägg till följande kod efter det senaste anropet till `app.post`:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

I `views/index.ejs`, lägga till en annan form under den första mallen för att skicka en siffra och ett textmeddelande:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Distribuera programmet till Azure och du ska nu kunna skicka som formar och skicka dig själv (eller någon av dina närmaste vänner) ett textmeddelande!

<a id="nextsteps"/>

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig grunderna i att skapa appar som kommunicerar med hjälp av node.js och Twilio. Men de här exemplen början knappt på vad du kan göra med Twilio och node.js. Mer information med Twilio med node.js finns i följande resurser:

* [Officiella modulen docs][docs]
* [Självstudiekurs om VoIP med node.js-program][voipnode]
* [Votr – en i realtid SMS röstningsprogrammet med node.js och CouchDB (tre delar)][votr]
* [Par programmering i webbläsaren med node.js][pair]

Vi hoppas att du gillar kodar node.js och Twilio Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
