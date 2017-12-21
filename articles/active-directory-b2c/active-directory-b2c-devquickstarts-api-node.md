---
title: "Azure AD B2C: Säkra ett webb-API med Node.js | Microsoft Docs"
description: "Hur du skapar ett webb-API för Node.js som accepterar token från en B2C-klient"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: xerners
ms.openlocfilehash: 3a0249f2f7dfd76d89cbf497376f53fe06c250c3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: Säkra ett webb-API med Node.js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Med Azure Active Directory (Active AD) B2C kan du skydda ett webb-API med hjälp av OAuth 2.0-åtkomsttoken. Med hjälp av dessa token kan dina klientappar som använder Azure AD B2C autentisera mot API:et. Den här artikeln visar hur du skapar en "uppgiftslista" för API som tillåter användare att lägga till och göra en lista med aktiviteter. Webb-API:t skyddas med hjälp av Azure AD B2C och tillåter endast autentiserade användare att hantera sina att göra-listor.

> [!NOTE]
> Det är tänkt att du ska ansluta till det här exemplet genom att använda vårt [iOS B2C-exempelprogram](active-directory-b2c-devquickstarts-ios.md). Gör den här genomgången först och följ sedan det exemplet.
>
>

**Passport** är ett mellanprogram för autentisering för Node.js. Modulbaserade Passport är flexibelt och kan diskret installeras i alla Express-baserade webbappar eller Restify-webbappar. En omfattande uppsättning strategier stöder autentisering med användarnamn och lösenord, Facebook, Twitter och mycket mer. Vi har utvecklat en strategi för Azure Active Directory (Azure AD). Du installerar den här modulen och lägger sedan till Azure AD-plugin-programmet `passport-azure-ad`.

Om du vill göra det här exemplet måste du:

1. Registrera ett program med Azure AD.
2. Ställa in så att programmet använder plugin-programmet för Passport `passport-azure-ad`.
3. Konfigurera ett klientprogram att anropa ett webb-API för ”uppgiftslistan”.

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient.  En katalog är en behållare för alla användare, appar, grupper och mer.  Om du inte redan har en [skapar du en B2C-katalog](active-directory-b2c-get-started.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program
Därefter måste du skapa en app i B2C-katalogen som ger Azure AD information som krävs för säker kommunikation med din app. I det här fallet representeras både klientappen och webb-API av ett enda **Program-ID** eftersom de omfattar en logisk app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

* Inkludera en **webbapp/ett webb-api** i programmet
* Ange `http://localhost/TodoListService` som en **Reply URL**. Den är standard-URL:en för det här kodexemplet.
* Skapa en **programhemlighet** för programmet och kopiera den. Du behöver dessa data senare. Tänk på att det här värdet måste ha [ett XM-escape-tecken](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) innan du använder det.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver dessa data senare.

## <a name="create-your-policies"></a>Skapa principer
I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Det här programmet innehåller två identitetsupplevelser: registrera sig och logga in. Du måste skapa en princip av varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  Tänk på följande när du skapar dina tre principer:

* Välj **visningsnamnet** och andra registreringsattribut i registreringsprincipen.
* Välj det **visningsnamn** och **objekt-ID** som programmet gör anspråk på i varje princip.  Du kan också välja andra anspråk.
* Kopiera ned **namnet** för varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`.  Du behöver dem senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina tre principer, är du redo att bygga din app.

Börja med [självstudierna för att komma igång med .NET-webbappar](active-directory-b2c-devquickstarts-web-dotnet.md) om du vill veta mer om hur principer fungerar i Azure AD B2C.

## <a name="download-the-code"></a>Ladda ned koden
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Om du vill bygga exemplet allteftersom kan du [ladda ned stommen av ett projekt som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Du kan också klona stommen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

Den färdiga appen finns också [som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) eller i `complete`-grenen för samma centrallager.

## <a name="download-nodejs-for-your-platform"></a>Ladda ned Node.js för plattformen
Du behöver en fungerande installation av Node.js. för att kunna använda det här exemplet.

Installera Node.js från [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Installera MongoDB för din plattform
Du behöver en fungerande installation av MongoDB för att kunna använda det här exemplet. Vi använder MongoDB för att göra dina REST-API:er beständiga över serverinstanser.

Installera MongoDB från [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Den här genomgången förutsätter att du använder standardslutpunkterna för MongoDB, vilket (när detta skrivs) är `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Installera Restify-modulerna i ditt webb-API
Vi använder Restify för att skapa REST-API. Restify är ett minimalt och flexibelt Node.js-program härlett från Express. Det har kraftfulla och stabila funktioner för att skapa REST API:er utöver Connect.

### <a name="install-restify"></a>Installera Restify
Ändra katalogen till `azuread` från kommandoraden. Skapa `azuread`-katalogen om den inte finns.

`cd azuread` eller `mkdir azuread;`

Ange följande kommando:

`npm install restify`

Restify installeras med det här kommandot.

#### <a name="did-you-get-an-error"></a>Fick du ett felmeddelande?
När du använder `npm` kan du, i vissa operativsystem, få felmeddelandet `Error: EPERM, chmod '/usr/local/bin/..'` och en förfrågan om att köra kontot som administratör. Om detta problem inträffar använder du `sudo`-kommandot för att köra `npm` på en högre privilegienivå.

#### <a name="did-you-get-a-dtrace-error"></a>Fick du ett felmeddelande om DTrace?
När du installerar Restify kan du stöta på något som ser ut som den här texten:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify erbjuder en kraftfull mekanism för att spåra REST-anrop med DTrace. Men många operativsystem har inte DTrace tillgängligt. Du kan ignorera dessa fel utan att oroa dig.

Resultatet av kommandot ska se ut ungefär som den här texten:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Installera Passport i ditt webb-API
Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där.

Installera Passport med följande kommando:

`npm install passport`

Resultatet av kommandot ska se ut ungefär som den här texten:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Lägg till passport-azuread i ditt webb-API
Lägg sedan till OAuth-strategin genom att använda `passport-azuread`, en uppsättning strategier som ansluter Azure AD med Passport. Använd den här strategin för ägar-token i REST API-exemplet.

> [!NOTE]
> Även om OAuth2 erbjuder ett ramverk där alla kända token-typer kan utfärdas, är det bara vissa token-typer som används ofta. De token som skyddar slutpunkter är ägar-token. Dessa typer av token är de mest utfärdade i OAuth2. Många olika implementeringar utgår ifrån att ägar-token är den enda typ av token som utfärdas.
>
>

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där.

Installera Passport `passport-azure-ad`-modulen genom att ange följande kommando:

`npm install passport-azure-ad`

Resultatet av kommandot ska se ut ungefär som den här texten:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Lägga till MongoDB-moduler i ditt webb-API
Det här exemplet använder MongoDB som datalager. Installera därför Mongoose, ett vanligt plugin-program för att hantera modeller och scheman.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Installera ytterligare moduler
Installera sedan resterande moduler som krävs.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Installera moduler i din `node_modules`-katalog:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Skapa en server.js-fil med dina beroenden
`server.js`-filen innehåller de flesta funktioner för Webb-API-servern.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Skapa en `server.js`-fil i en textredigerare. Lägg till följande information:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Spara filen. Du kommer tillbaka till den senare.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Skapa en config.js-fil för att lagra dina Azure AD-inställningar
Den här kodfilen skickar konfigurationsparametrarna från din Azure AD-portal till `Passport.js`-filen. Du har skapat konfigurationsvärdena när du la till ditt webb-API i portalen under den första delen av genomgången. När du har kopierat koden förklarar vi vad du ska ange i värdena för dessa parametrar.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Skapa en `config.js`-fil i en textredigerare. Lägg till följande information:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Värden som krävs
`clientID`: Klient-ID för programmet Web API.

`IdentityMetadata`: Det är där `passport-azure-ad` letar efter konfigurationsdata för din identitetsprovider. Den letar också efter nycklarna för att validera JSON-webb-token.

`audience`: Den URI (Uniform Resource Identifier) från portalen som identifierar ditt anropande program.

`tenantName`: Ditt klientnamn (till exempel **contoso.onmicrosoft.com**).

`policyName`: Den princip som du vill använda för att validera token som kommer till servern. Det bör vara samma princip som du använder i klientprogrammet för inloggning.

> [!NOTE]
> Använd samma principer för både klient- och serverinstallationen. Om du har redan gjort en genomgång och skapat dessa principer behöver du inte göra det igen. Eftersom du har gjort genomgången ska du inte behöva ställa in nya principer för klientgenomgångarna på webbplatsen.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Lägg till konfigurationen i din server.js-fil
För att läsa värdena från `config.js`-filen du skapade, lägg till `.config`-filen som en nödvändig resurs i ditt program och sedan ange de globala variablerna till dem i `config.js`-dokumentet.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Öppna `server.js`-filen i en textredigerare. Lägg till följande information:

```Javascript
var config = require('./config');
```
Lägg till ett nytt avsnitt i `server.js` som innehåller följande kod:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Sedan lägger vi till platshållare för de användare som vi får från våra anropande program.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Vi fortsätter med att skapa våra transaktionsloggfiler också.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Lägg till informationen om MongoDB-modellen och -schemat genom att använda Mongoose
Det du har förberett tidigare kommer att vara till hjälp när du för ihop dessa tre filer i en REST API-tjänst.

Lagra dina uppgifter genom att använda MongoDB för den här genomgången, som beskrivits tidigare.

I `config.js`-filen, kallade du databasen för **Aktivitetslista**. Detta namn var även det du angav i slutet av anslutnings-URL:en för `mongoose_auth_local`. Du behöver inte skapa den här databasen i förväg i MongoDB. Den skapar databasen på den första körningen av serverprogrammet.

När du har talat om för servern vilken MongoDB-databas som ska användas, måste du skriva ytterligare kod för att skapa modellen och schemat för serveraktiviteterna.

### <a name="expand-the-model"></a>Expandera modellen
Den här schemamodellen är enkel. Du kan expandera den efter behov.

`owner`: Den som har tilldelats aktiviteten. Det här objektet är en **sträng**.  

`Text`: Själva aktiviteten. Det här objektet är en **sträng**.

`date`: Det datum då aktiviteten ska vara klar. Det här objektet är en **datetime**.

`completed`: Om aktiviteten har slutförts. Det här objektet är en **boolesk**.

### <a name="create-the-schema-in-the-code"></a>Skapa schemat i koden
Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Öppna `server.js`-filen i en textredigerare. Lägg till följande information under konfigurationsposten:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Du skapar först schemat och sedan ett modellobjekt som du använder för att lagra data i hela koden när du definierar dina **vägar**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Lägga till vägar för din REST API-aktivitetsserver
Lägg till de vägar du ska använda för REST API-servern nu när du har en databasmodell att arbeta med.

### <a name="about-routes-in-restify"></a>Om vägar i Restify
I Restify fungerar vägar på samma sätt som när de använder Express-stacken. Du definierar vägar genom att använda den URI som du förväntar dig att klientprogram anropar.

Ett typiskt mönster för en Restify-väg är:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify och Express kan ge mycket mer ingående funktioner, till exempel att definiera programtyper och göra komplex routning över olika slutpunkter. För dessa självstudier håller vi vägarna enkla.

#### <a name="add-default-routes-to-your-server"></a>Lägga till standardvägar i servern
Du lägger nu till grundläggande CRUD-vägar för **skapa** och **lista** för vår REST API. Andra vägar finns i `complete`-grenen av exemplet.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Öppna `server.js`-filen i en textredigerare. Lägg till följande information under de databasposter du gjorde ovan:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Lägg till felhantering för vägarna
Lägg till viss felhantering, så att du kan informera klienten om eventuella problem på ett sätt som den kan förstå.

Lägg till följande kod:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Skapa servern
Du har nu definierat databasen och placerat ut vägarna. Det som är kvar är att lägga till serverinstansen som hanterar dina anrop.

Med Restify och Express kan anpassa för en REST API-server på en mycket ingående nivå, men här använder vi den mest grundläggande installationen.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Lägg till vägar på servern (utan autentisering)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Lägg till autentisering i REST API-servern
Nu när du har en aktiv REST API-server kan du använda den med Azure AD.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Använd den OIDC-ägarstrategi som ingår i passport-azure-ad
> [!TIP]
> När du skriver API: er bör du alltid länka dina data till något unikt från token, något som användaren inte kan förfalska. När servern lagrar ToDo-objekt, sker det baserat på **oid** för användaren i token (anropas via token.oid). Det anges i fältet ”ägare”. Det här värdet garanterar att bara denna användaren kan komma åt sina egna ToDo-objekt. Ägarens API exponeras aldrig, så en extern användare kan begära andras ToDo-objekt, även om de autentiseras.
>
>

Använd sedan den ägarstrategi som kommer med `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport använder samma mönster för alla strategier. Du skickar en `function()` som har `token` och `done` som parametrar. Strategin kommer tillbaka när den har utfört allt sitt arbete. Du bör då lagra användaren och spara token så att du inte behöver fråga efter den igen.

> [!IMPORTANT]
> Koden ovan tar alla användare som autentiserar till servern. Denna process kallas autoregistrering. I produktionsservrar ska du inte ge alla användare åtkomst till API:en utan att de först går igenom en registreringsprocess. Denna process är oftast det mönster du ser i konsumentappar. Det gör att du kan registrera dig med hjälp av Facebook men du blir sedan uppmanad att fylla i ytterligare information. Om detta program inte var ett kommandoradsprogram hade vi kunnat extrahera mejladressen från det token-objekt som returneras och sedan bett användarna att fylla i ytterligare information. Eftersom det här är ett exempel, lägger vi till dem till en databas i minnet.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Kör serverprogrammet för att verifiera att det avvisar dig
Du kan använda `curl` om du vill kontrollera att du nu har OAuth2-skydd mot dina slutpunkter. De rubriker som returneras borde räcka för att se om att du är på rätt väg.

Kontrollera att MongoDB-instansen körs:

    $sudo mongodb

Gå till katalogen och kör servern:

    $ cd azuread
    $ node server.js

I ett nytt terminalfönster kör `curl`

Prova med ett grundläggande INLÄGG:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Ett 401-fel är det svar du vill ha. Det visar att Passport-lagret försöker omdirigera till slutpunkten för auktorisering .

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Du har nu en REST API-tjänst som använder OAuth2
Du har implementerat en REST API med Restify och OAuth! Du har nu tillräcklig kod så att du kan fortsätta att utveckla din tjänst och bygga på det här exemplet. Du har gjort så mycket du kan med den här servern utan att använda en OAuth2-kompatibel klient. För nästkommande steg använder du ytterligare en genomgång, såsom genomgången [Anslut till en webb-API med iOS har B2C](active-directory-b2c-devquickstarts-ios.md).

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare  till mer avancerade ämnen, till exempel:

[Ansluta till ett webb-API genom att använda iOS med B2C](active-directory-b2c-devquickstarts-ios.md)
