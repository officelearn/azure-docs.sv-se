---
title: "Azure AD-Node.js-webb-API komma igång | Microsoft Docs"
description: "Hur du skapar en Node.js-REST-webb-API som kan integreras med Azure AD för autentisering."
services: active-directory
documentationcenter: nodejs
author: craigshoemaker
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: cshoe
ms.custom: aaddev
ms.openlocfilehash: 2ed0874b79601976e0d5a73fe82c7c03331d9bea
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD-Node.js-webb-API komma igång

Den här artikeln visar hur du skyddar en [Restify](http://restify.com/) API-slutpunkten med [Passport](http://passportjs.org/) med hjälp av den [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modul som ska hantera kommunikationen med Azure Active Directory (AAD). 

Tillämpningsområdet för den här kursen omfattar problem angående att säkra API-slutpunkter. Gäller för inloggning och behålla autentiseringstoken implementeras inte här och är ett klientprogram ansvar. Mer information om runt en implementering av klienten finns [Node.js-webbapp-inloggning och utloggning med Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md).

Fullständig kodexemplet som är associerade med den här artikeln är tillgänglig på [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Skapa exempelprojektet
Den här serverprogram kräver några paketberoenden att stödja Restify och Passport samt information som skickas till AAD-konto.

Börja genom att lägga till följande kod i en fil med namnet `package.json`:

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

En gång `package.json` skapas, kör `npm install` i din Kommandotolken för att installera paketberoenden. 

### <a name="configure-the-project-to-use-active-directory"></a>Konfigurera projektet för att använda Active Directory

För att komma igång med att konfigurera programmet, finns det några konto-specifika värden som du kan hämta från Azure CLI. Det enklaste sättet att komma igång med CLI är att använda Azure Cloud-gränssnittet.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ange följande kommando i molnet shell: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Den [argument](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) för den `create` är:

| Argumentet  | Beskrivning |
|---------|---------|
|`display-name` | Eget namn för registrering |
|`homepage` | URL: en där användare kan logga in och använda ditt program |
|`identifier-uris` | Utrymme avgränsade unikt URI: er som Azure AD kan använda för det här programmet |

Innan du kan ansluta till Azure Active Directory, behöver du följande information:

| Namn  | Beskrivning | Variabelnamn i konfigurationsfilen |
| ------------- | ------------- | ------------- |
| Innehavarens namn  | [Innehavarens namn](active-directory-howto-tenant.md) du vill använda för autentisering | `tenantName`  |
| Klient-ID  | Klient-ID är OAuth-term för AAD _program-ID_. |  `clientID`  |

Kopiera från registreringssvar i Azure Cloud-gränssnittet i `appId` värde och skapa en ny fil med namnet `config.js`. Sedan lägger du till följande kod och ersätter värdena med inom hakparenteser token:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
Mer information om de enskilda inställningarna i [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dokumentation.

## <a name="implement-the-server"></a>Implementera servern
Den [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modulen har två autentiseringsstrategier: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) och [ägar](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategier. Den server som implementerats i den här artikeln använder ägarstrategi för att skydda API-slutpunkt.

### <a name="step-1-import-dependencies"></a>Steg 1: Importera beroenden
Skapa en ny fil med namnet `app.js` och klistra in följande text:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

I det här avsnittet av kod:

- Den `restify` och `restify-plugins` refererar till moduler för att ställa in en Restify-server.

- Den `passport` och `passport-azure-ad` moduler ansvarar för att kommunicera med AAD.

- Den `config` variabel har initierats med värden från den `config.js` filen som skapades i föregående steg.

- En matris som har skapats för `authenticatedUserTokens` att lagra användartoken när de skickas till skyddade slutpunkter.

- Den `serverPort` definieras antingen från miljön processen port eller från konfigurationsfilen.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Steg 2: Skapa en instans av en autentiseringsstrategi
När du skyddar en slutpunkt måste du ange en strategi som ansvarar för att fastställa huruvida den aktuella begäranden kommer från en autentiserad användare. Här den `authenticatonStrategy` variabeln är en instans av den `passport-azure-ad` `BearerStrategy` klass. Lägg till följande kod efter den `require` instruktioner.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
Den här implementeringen använder automatisk registrering genom att lägga till autentiseringstoken i den `authenticatedUserTokens` matrisen om de inte redan finns.

När du har skapat en ny instans av strategin måste du skicka den till Passport via den `use` metoden. Lägg till följande kod i `app.js` att använda strategin i Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Steg 3: Serverkonfiguration
Med autentiseringsstrategi definierats, kan nu installera Restify-server med några grundläggande inställningar och inställd på att använda Passport för säkerhet.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Den här servern har initierats och konfigurerats för att analysera auktorisering huvuden och ange sedan för användning av Passport.


### <a name="step-4-define-routes"></a>Steg 4: Definiera vägar
Du kan nu definierar vägar och bestäm som du vill skydda med AAD. Det här projektet innehåller två vägar där rotnivån är öppen och `/api` väg är inställd att kräva autentisering.

I `app.js` Lägg till följande kod för nivå vägen roten:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Rot-vägen innebär att alla begäranden via flödet och returnerar ett meddelande som innehåller ett kommando för att testa den `/api` vägen. Däremot kommer den `/api` väg låst med [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Lägg till följande kod efter rot-vägen.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Den här konfigurationen tillåter endast autentiserade begäranden som innehåller en ägar-token åtkomst till `/api`. Alternativet att `session: false` används för att inaktivera sessioner för att kräva att en token skickas med varje begäran-API: et.

Slutligen servern har angetts till att lyssna på den konfigurerade porten genom att anropa den `listen` metoden.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Kör exemplet

Nu när servern har implementerats kan du starta om servern genom att öppna en kommandotolk och ange:

```Shell
npm start
```

Du kan skicka en begäran till servern att testa resultaten med servern som kör. För att demonstrera svaret från roten flödet, öppna ett bash-gränssnitt och ange följande kod:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Om servern har konfigurerats korrekt, bör svaret likna:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Därefter kan du testa det flöde som kräver autentisering genom att ange följande kommando i bash-gränssnitt:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Om du har konfigurerat servern korrekt, så att servern ska svara med statusen `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Nu när du har skapat en säker API kan implementera du en klient som kan skicka autentiseringstoken-API: et.

## <a name="next-steps"></a>Nästa steg
Du måste implementera en motsvarighet för klienten att ansluta till servern som hanterar logga in, logga ut och hantera token som anges i inledningen. Kodbaserad exempel kan du referera till klientprogram i [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) och [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). En stegvis självstudiekurs finns i följande artikel:

> [!div class="nextstepaction"]
> [Node.js-webbapp-inloggning och utloggning med Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md)