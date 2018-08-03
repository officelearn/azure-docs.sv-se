---
title: 'Azure AD-Node.js-webb-API: et komma igång | Microsoft Docs'
description: Hur du skapar ett Node.js REST webb-API som kan integreras med Azure AD för autentisering.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 1137e7164ac83a2ee0bf05804296aeeb5c3496fb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437455"
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD-Node.js-webb-API: et komma igång

Den här artikeln visar hur du skyddar en [Restify](http://restify.com/) API-slutpunkt med [Passport](http://passportjs.org/) med hjälp av den [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modul för att hantera kommunikationen med Azure Active Directory (AAD). 

Tillämpningsområdet för den här självstudien omfattar problem angående att säkra API-slutpunkter. Problem med inloggning och behålla autentiseringstoken implementeras inte här och är ansvar för ett klientprogram. Mer information kring implementering av klienten finns [Node.js-webbapp inloggning och utloggning med Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md).

Fullständig kodexemplet som är associerade med den här artikeln är tillgänglig på [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Skapa exempelprojektet
Den här serverprogram kräver några paketberoenden stöder Restify och Passport, samt information som skickas till AAD-konto.

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

En gång `package.json` skapas, kör `npm install` i Kommandotolken att installera paketberoenden. 

### <a name="configure-the-project-to-use-active-directory"></a>Konfigurera projektet för att använda Active Directory

Om du vill komma igång med att konfigurera programmet, finns det några kontospecifikt värden som du kan hämta från Azure CLI. Det enklaste sättet att komma igång med CLI är att använda Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ange följande kommando i cloudshell: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Den [argument](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) för den `create` kommandot är:

| Argumentet  | Beskrivning |
|---------|---------|
|`display-name` | Eget namn för registreringen |
|`homepage` | URL: en där användare kan logga in och använda ditt program |
|`identifier-uris` | Utrymme avgränsade unikt URI: er som Azure AD kan använda för det här programmet |

Innan du kan ansluta till Azure Active Directory, behöver du följande information:

| Namn  | Beskrivning | Variabelnamn i konfigurationsfilen |
| ------------- | ------------- | ------------- |
| Klientnamn  | [Klientnamn](active-directory-howto-tenant.md) du vill använda för autentisering | `tenantName`  |
| Klient-ID  | Klient-ID är OAuth-term som används för AAD _program-ID_. |  `clientID`  |

Kopiera från registreringssvar i Azure Cloud Shell den `appId` värde och skapa en ny fil med namnet `config.js`. Lägg i följande kod och Ersätt värdena med inom hakparenteser token:

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
Mer information om de enskilda inställningarna i [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modulen dokumentation.

## <a name="implement-the-server"></a>Implementera servern
Den [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) modulen har två autentiseringsstrategier: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) och [ägar](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategier. Servern som implementerats i den här artikeln använder ägarstrategi för att skydda API-slutpunkt.

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

- Den `restify` och `restify-plugins` moduler refererar till för att ställa in en Restify-server.

- Den `passport` och `passport-azure-ad` moduler ansvarar för att kommunicera med AAD.

- Den `config` variabeln har initierats med värden från den `config.js` filen som skapades i föregående steg.

- En matris skapas för `authenticatedUserTokens` kan lagra användartoken de skickas till skyddade slutpunkter.

- Den `serverPort` definieras antingen från miljön processen port eller från konfigurationsfilen.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Steg 2: Skapa en instans av en autentiseringsstrategi
När du skyddar en slutpunkt måste du ange en strategi som är ansvarig för att bedöma huruvida den aktuella begäran kommer från en autentiserad användare. Här den `authenticatonStrategy` variabeln är en instans av den `passport-azure-ad` `BearerStrategy` klass. Lägg till följande kod efter den `require` instruktioner.

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
Den här implementeringen använder automatisk registrering genom att lägga till autentiseringstoken till den `authenticatedUserTokens` matrisen om de inte redan finns.

När en ny instans av strategin har skapats måste du ange i Passport via den `use` metoden. Lägg till följande kod till `app.js` att använda strategin i Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Steg 3: Serverkonfiguration
Med autentiseringsstrategi definierats, kan nu konfigurera Restify-server med några grundläggande inställningar och inställd på att använda Passport för säkerhet.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Den här servern har initierats och konfigurerats för att analysera auktorisering rubriker och ange sedan för användning av Passport.


### <a name="step-4-define-routes"></a>Steg 4: Definiera vägar
Nu kan du definiera vägar och avgör vilken ska skyddas med AAD. Det här projektet innehåller två vägar där rotnivå är öppen och `/api` vägen är konfigurerad att kräva autentisering.

I `app.js` Lägg till följande kod för rot på vägen:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Rot-väg innebär att alla begäranden via flödet och returnerar ett meddelande som innehåller ett kommando för att testa den `/api` väg. Däremot kommer den `/api` vägen är låst nedåt med hjälp av [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Lägg till följande kod efter rot-vägen.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Den här konfigurationen tillåter endast autentiserade begäranden som innehåller en ägar-token åtkomst till `/api`. Alternativet att `session: false` används för att inaktivera sessioner för att kräva att en token överförs med varje begäran till API: et.

Slutligen servern är inställt på att lyssna på den konfigurerade porten genom att anropa den `listen` metoden.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Kör exemplet

Nu när servern har implementerats kan du starta servern genom att öppna en kommandotolk och ange:

```Shell
npm start
```

Du kan skicka en begäran till servern för att testa resultaten med servern som kör. Öppna ett bash-gränssnitt för att demonstrera svaret från roten väg, och ange följande kod:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Om du har konfigurerat din server korrekt, bör svaret likna:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Du kan sedan testa den väg som kräver autentisering genom att ange följande kommando i bash-gränssnittet:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Om du har konfigurerat servern korrekt så servern ska svara med statusen `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Nu när du har skapat en säker API kan implementera du en klient som kan skicka autentiseringstoken till API: et.

## <a name="next-steps"></a>Nästa steg
Enligt informationen i inledningen, måste du implementera en motsvarighet för klienten att ansluta till den server som hanterar loggar in, logga ut och hantera token. Kodbaserad exempel kan du referera till klientprogram i [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) och [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). En stegvis självstudie finns i följande artikel:

> [!div class="nextstepaction"]
> [Node.js-webbapp inloggning och utloggning med Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md)