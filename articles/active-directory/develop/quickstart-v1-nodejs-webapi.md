---
title: Skydda ett webb-API med Azure AD | Microsoft Docs
description: Lär dig hur du skapar ett Node.js REST-webb-API som integreras med Azure AD för autentisering.
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
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 9683eb8cbfcabb946f8b364ac9cc8aeeb167d023
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120299"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Snabbstart: Skydda ett webb-API med Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

I den här snabbstarten lär du dig hur du skyddar en [Restify](http://restify.com/) API-slutpunkt med [Passport](http://passportjs.org/) med hjälp av modulen [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) för att hantera kommunikation med Azure Active Katalog (AD Azure).

Omfånget för den här snabbstarten beskriver frågor som rör skydd av API-slutpunkter. Frågorna om inloggning och att behålla autentiseringstoken implementeras inte här, och sköts av ett klientprogram. Mer information kring en klientimplementering finns i [In- och utloggning via en Node.js-webbapp med Azure AD](quickstart-v1-openid-connect-code.md).

Det fullständiga kodexempel som är associerat med den här artikeln finns på [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra följande krav.

### <a name="create-the-sample-project"></a>Skapa exempelprojektet

Serverprogrammet kräver några paketberoenden för att stödja Restify och Passport samt kontoinformation som skickas till Azure AD.

Börja med att lägga till följande kod i en fil med namnet `package.json`:

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

När `package.json` har skapats kör du `npm install` i kommandotolken för att installera paketberoendena. 

#### <a name="configure-the-project-to-use-active-directory"></a>Konfigurera projektet för att använda Active Directory

För komma igång med att konfigurera programmet finns det några kontospecifika värden som du kan hämta från Azure CLI. Det enklaste sättet att komma igång med CLI är att använda Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ange följande kommandon i Cloud Shell:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenten](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) för kommandot `create` är:

| Argument  | Beskrivning |
|---------|---------|
|`display-name` | Eget namn för registreringen |
|`homepage` | URL där användare kan logga in och använda ditt program |
|`identifier-uris` | Blankstegsavgränsade unik URI:er som Azure AD kan använda för det här programmet |

Innan du kan ansluta till Azure Active Directory behöver du följande information:

| Namn  | Beskrivning | Variabelnamn i konfigurationsfilen |
| ------------- | ------------- | ------------- |
| Namn på klientorganisation  | [Namn på klientorganisation](quickstart-create-new-tenant.md) som du vill använda för autentisering | `tenantName`  |
| Klientorganisations-ID  | Klientorganisations-ID är den OAuth-term som används för AAD-_program-ID_. |  `clientID`  |

Från registreringssvaret i Azure Cloud Shell kopierar du `appId`-värdet och skapar en ny fil med namnet `config.js`. Lägg sedan till följande kod och ersätt värdena med token som är omslutna med hakparenteser:

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

Mer information om de enskilda konfigurationsinställningarna finns i dokumentationen till modulen [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

### <a name="implement-the-server"></a>Implementera servern

Modulen [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) har två autentiseringsstrategier: strategierna [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) och [ägartoken](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). Den server som implementeras i den här artikeln använder strategin med ägartoken för att skydda API-slutpunkten.

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

I det här avsnittet i koden:

- Modulerna `restify` och `restify-plugins` refereras till för att konfigurera en Restify-server.
- Modulerna `passport` och `passport-azure-ad` ansvarar för att kommunicera med Azure AD.
- Variabeln `config` initieras med värden från filen `config.js` som skapades i föregående steg.
- En matris skapas för `authenticatedUserTokens` för att lagra användartoken när de skickas till skyddade slutpunkter.
- `serverPort` definieras antingen från processmiljöns port eller från konfigurationsfilen.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Steg 2: Instansiera en autentiseringsstrategi

När du skyddar en slutpunkt måste du ange en strategi som ansvarar för att bedöma huruvida den aktuella begäran kommer från en autentiserad användare. Här är variabeln `authenticatonStrategy` en instans av `passport-azure-ad` `BearerStrategy`-klassen. Lägg till följande kod efter `require`-instruktionerna.

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

Den här implementeringen använder automatisk registrering genom att lägga till autentiseringstoken till `authenticatedUserTokens`-matrisen om de inte redan finns.

När en ny instans av strategin har skapats måste du skicka den till Passport via metoden `use`. Lägg till följande kod i `app.js` för att använda strategin i Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Steg 3: Serverkonfiguration

Med autentiseringsstrategin definierad kan du nu konfigurera Restify-servern med några grundläggande inställningar och ställa in den att använda Passport för säkerhet.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Den här servern initieras och konfigureras för att parsa auktoriseringsrubriker och sedan ställas in att använda Passport.

### <a name="step-4-define-routes"></a>Steg 4: Definiera vägar

Du kan nu definiera vägar och bestämma vilka du vill skydda med Azure AD. Det här projektet innehåller två vägar där rotnivån är öppen och vägen `/api` är konfigurerad för att kräva autentisering.

I `app.js` lägger du till följande kod för rotnivåvägen:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Rotvägen tillåter alla begäranden via vägen och returnerar ett meddelande som innehåller ett kommando för att testa vägen `/api`. Däremot är vägen `/api` låst med hjälp av [`passport.authenticate`](http://passportjs.org/docs/authenticate). Lägg till följande kod efter rotvägen.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Den här konfigurationen tillåter endast autentiserade begäranden som innehåller en ägartokenåtkomst till `/api`. Alternativet `session: false` används för att inaktivera sessioner för att kräva att en token skickas med varje begäran till API:et.

Slutligen är servern inställd på att lyssna på den konfigurerade porten genom att anropa metoden `listen`.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Kör exemplet

Nu när servern har implementerats kan du starta servern genom att öppna en kommandotolk och ange:

```shell
npm start
```

Servern körs och du kan skicka en begäran till servern för att testa resultatet. För att demonstrera svaret från rotvägen öppnar du ett bash-gränssnitt och anger följande kod:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Om du har konfigurerat servern korrekt bör svaret likna följande:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Sedan kan du testa den väg som kräver autentisering genom att ange följande kommando i bash-gränssnittet:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Om du har konfigurerat servern korrekt bör den svara med statusen `Unauthorized`.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Nu när du har skapat ett säkert API kan du implementera en klient som kan skicka autentiseringstoken till API:et.

## <a name="next-steps"></a>Nästa steg

* Du måste implementera en klientmotsvarighet för att ansluta till den server som hanterar inloggning, utloggning och hantering av token. Kodbaserade exempel finns i klientprogrammen i [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) och [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android).
* En stegvis självstudiekurs finns i [In- och utloggning via en Node.js-webbapp med Azure AD](quickstart-v1-openid-connect-code.md).