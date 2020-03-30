---
title: Använda Twilio för röst-, VoIP- och SMS-meddelanden i Azure
description: Läs om hur du ringer ett telefonsamtal och skickar ett SMS-meddelande med Twilio API-tjänsten på Azure. Kodexempel skrivna i Node.js.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637276"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Använda Twilio för röst-, VoIP- och SMS-meddelanden i Azure
Den här guiden visar hur du skapar appar som kommunicerar med Twilio och node.js på Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Vad är Twilio?
Twilio är en API-plattform som gör det enkelt för utvecklare att ringa och ta emot telefonsamtal, skicka och ta emot textmeddelanden och bädda in VoIP-samtal i webbläsarbaserade och inbyggda mobila applikationer. Låt oss kort gå igenom hur detta fungerar innan dykning i.

### <a name="receiving-calls-and-text-messages"></a>Ta emot samtal och textmeddelanden
Twilio tillåter utvecklare att [köpa programmerbara telefonnummer][purchase_phone] som kan användas för att både skicka och ta emot samtal och textmeddelanden. När ett Twilio-nummer tar emot ett inkommande samtal eller sms skickar Twilio ett HTTP-POST eller GET-förfrågan till webbprogrammet och ber dig om instruktioner om hur du hanterar samtalet eller texten. Servern svarar på Twilios HTTP-begäran med [TwiML][twiml], en enkel uppsättning XML-taggar som innehåller instruktioner om hur du hanterar ett samtal eller en text. Vi kommer att se exempel på TwiML på bara ett ögonblick.

### <a name="making-calls-and-sending-text-messages"></a>Ringa samtal och skicka textmeddelanden
Genom att göra HTTP-begäranden till Twilio-webbtjänst-API:et kan utvecklare skicka textmeddelanden eller initiera utgående telefonsamtal. För utgående samtal måste utvecklaren också ange en URL som returnerar TwiML-instruktioner för hur det utgående samtalet ska hanteras när det är anslutet.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Bädda in VoIP-funktioner i användargränssnittskod (JavaScript, iOS eller Android)
Twilio tillhandahåller en SDK på klientsidan som kan förvandla valfri webbläsare, iOS-app eller Android-app till en VoIP-telefon. I den här artikeln kommer vi att fokusera på hur du använder VoIP-samtal i webbläsaren. Förutom *Twilio JavaScript SDK* som körs i webbläsaren måste ett serverprogram (vårt node.js-program) användas för att utfärda en "capability token" till JavaScript-klienten. Du kan läsa mer om hur du använder VoIP med node.js [på Twilio dev-bloggen][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Registrera dig för Twilio (Microsoft Rabatt)
Innan du använder Twilio-tjänster måste du först [registrera dig för ett konto][signup]. Microsoft Azure-kunder får en särskild rabatt - [se till att registrera dig här!][signup]

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Skapa och distribuera en nod.js Azure-webbplats
Därefter måste du skapa en node.js-webbplats som körs på Azure. [Den officiella dokumentationen för att göra detta finns här][azure_new_site]. På en hög nivå kommer du att göra följande:

* Registrera dig för ett Azure-konto, om du inte redan har ett konto
* Använda Azure-administratörskonsolen för att skapa en ny webbplats
* Lägga till stöd för källkontroll (vi antar att du använde git)
* Skapa en `server.js` fil med ett enkelt nod.js-webbprogram
* Distribuera det här enkla programmet till Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Konfigurera Twilio-modulen
Därefter kommer vi att börja skriva ett enkelt node.js-program som använder sig av Twilio API. Innan vi börjar måste vi konfigurera våra Twilio-kontouppgifter.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfigurera Twilio-autentiseringsuppgifter i systemmiljövariabler
För att kunna göra autentiserade förfrågningar mot Twilio-backend behöver vi vårt konto SID och auth-token, som fungerar som användarnamn och lösenord för vårt Twilio-konto. Det säkraste sättet att konfigurera dessa för användning med nodmodulen i Azure är via systemmiljövariabler, som du kan ange direkt i Azure-administratörskonsolen.

Välj din node.js webbplats och klicka på länken "KONFIGURERA".  Om du rullar nedåt lite visas ett område där du kan ange konfigurationsegenskaper för ditt program.  Ange dina Twilio-kontouppgifter[(som finns på Twilio-konsolen)][twilio_console]som visas – se till att namnge dem `TWILIO_ACCOUNT_SID` respektive: `TWILIO_AUTH_TOKEN`

![Azure-administratörskonsol][azure-admin-console]

När du har konfigurerat dessa variabler startar du om programmet i Azure-konsolen.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Deklarera Twilio-modulen i package.json
Därefter måste vi skapa ett package.json för att hantera våra nodmodulberoenden via [npm]. På samma nivå `server.js` som filen som du skapade i *azure/node.js-självstudien* skapar du en fil med namnet `package.json`.  Placera följande i den här filen:

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

Detta förklarar twilio-modulen som ett beroende, liksom det populära [Express-webbramverket][express] och EJS-mallmotorn.  Okej, nu är vi redo - låt oss skriva lite kod!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Ringa ett utgående samtal
Låt oss skapa ett enkelt formulär som kommer att ringa ett samtal till ett nummer vi väljer. Öppna `server.js`och ange följande kod. Notera där det står "CHANGE_ME" - sätt namnet på din azurblå webbplats där:

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

Skapa sedan en `views` katalog som heter - i `index.ejs` den här katalogen, skapa en fil med namnet med följande innehåll:

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

Distribuera nu din webbplats till Azure och öppna ditt hem. Du bör kunna ange ditt telefonnummer i textfältet och få ett samtal från ditt Twilio-nummer!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Skicka ett SMS-meddelande
Nu ska vi ställa in ett användargränssnitt och formulärhanteringslogik för att skicka ett textmeddelande. `server.js`Öppna och lägg till följande kod efter `app.post`det senaste anropet i:

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

I `views/index.ejs`lägger du till ett annat formulär under det första som ett nummer och ett textmeddelande:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Distribuera om ditt program till Azure, och du bör nu kunna skicka formuläret och skicka ett sms till dig själv (eller någon av dina närmaste vänner).

<a id="nextsteps"/>

## <a name="next-steps"></a>Efterföljande moment
Du har nu lärt dig grunderna i att använda node.js och Twilio för att skapa appar som kommunicerar. Men dessa exempel skrapa knappt ytan av vad som är möjligt med Twilio och node.js. Mer information om hur du använder Twilio med node.js finns i följande resurser:

* [Officiella moduldokument][docs]
* [Självstudiekurs om VoIP med node.js-program][voipnode]
* [Votr - en sms-röstningsansökan i realtid med node.js och CouchDB (tre delar)][votr]
* [Para ihop programmering i webbläsaren med nod.js][pair]

Vi hoppas att du älskar hacking node.js och Twilio på Azure!

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
