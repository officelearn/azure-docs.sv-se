---
title: Skydda ett Azure Active Directory v2.0 webb-API med Node.js | Microsoft Docs
description: "Lär dig mer om att skapa en Node.js-webb-API som accepterar token från en personligt Microsoft-konto och från arbetet eller skolan konton."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f07e421feedf3c82da7be16434891cdbe6069038
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Säkra ett webb-API med Node.js
> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner fungerar med v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten eller v1.0 slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

När du använder Azure Active Directory (AD Azure) v2.0-slutpunkten kan du använda [OAuth 2.0](active-directory-v2-protocols.md) åtkomst-tokens för att skydda ditt webb-API. Åtkomst-token, användare som har både en personliga Microsoft-konto och arbete eller skolkonton kan på ett säkert sätt komma åt ditt webb-API med OAuth 2.0.

*Passport* är ett mellanprogram för autentisering för Node.js. Flexibel och modulära, Passport kan släppas diskret in i alla Express-baserade eller restify-webbappar. I Passport, en omfattande uppsättning strategier stöder autentisering med ett användarnamn och lösenord, Facebook, Twitter eller andra alternativ. Vi har utvecklat en strategi för Azure AD. I den här artikeln vi hur du kan installera modulen och Lägg sedan till Azure AD `passport-azure-ad` plugin-programmet.

## <a name="download"></a>Ladda ned
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Om du vill följa kursen, kan du [ladda ned appens stomme som en .zip-fil](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip), eller klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Du kan också få det färdiga programmet i slutet av den här kursen.

## <a name="1-register-an-app"></a>1: registrera en app
Skapa en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa [dessa detaljerade steg](active-directory-v2-app-registration.md) att registrera en app. Se till att du:

* Kopiera den **program-Id** tilldelats din app. Du behöver det för den här kursen.
* Lägg till den **Mobile** plattform för din app.
* Kopiera den **omdirigerings-URI** från portalen. Du måste använda URI standardvärdet `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: Installera Node.js
Använd den här självstudiekursen, måste du [installera Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: Installera MongoDB
Om du vill kunna använda det här exemplet måste du [installera MongoDB](http://www.mongodb.org). I det här exemplet använder du MongoDB för att göra dina REST-API beständiga över serverinstanser.

> [!NOTE]
> I den här artikeln förutsätter vi att du använder standardslutpunkterna för installation och server för MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: Installera restify-modulerna i ditt webb-API
Vi använder Resitfy för att skapa vårt REST-API. Restify är ett minimalt och flexibelt Node.js programramverk som härleds från Express. Restify har en kraftfull uppsättning funktioner som du kan använda för att skapa REST API: er utöver Connect.

### <a name="install-restify"></a>Installera restify
1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

    Om den **azuread** katalogen inte finns, skapar du den:

    `mkdir azuread`

2.  Installera restify:

    `npm install restify`

    Kommandots utdata ska se ut så här:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Fick du ett felmeddelande?
I vissa operativsystem när du använder den `npm` kommandot kan du se meddelandet: `Error: EPERM, chmod '/usr/local/bin/..'`. Felet följs av en begäran om att du försöker köra kontot som administratör. Om detta inträffar kan du använda kommandot `sudo` att köra `npm` på en högre Privilegienivå.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Fick du ett fel som rör DTrace?
När du installerar restify kan det hända att det här meddelandet:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Restify har en kraftfull mekanism för att spåra REST-anrop med DTrace. DTrace är inte tillgänglig på många operativsystem. Du kan ignorera det här felmeddelandet.


## <a name="5-install-passportjs-in-your-web-api"></a>5: Installera Passport.js i ditt webb-API
1.  I Kommandotolken, ändra katalogen till **azuread**.

2.  Installera Passport.js:

    `npm install passport`

    Resultatet av kommandot ska se ut så här:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Lägg till passport-azure-ad i ditt webb-API
Lägg sedan till OAuth-strategin genom att använda passport-azuread. `passport-azuread`är en uppsättning strategier som ansluter Azure AD med Passport. Vi använder den här strategin för ägar-token i REST API-exemplet.

> [!NOTE]
> Även om OAuth 2.0 erbjuder ett ramverk där alla kända token-typer kan utfärdas, används ofta för vissa typer av token. Ägar-token används ofta för att skydda slutpunkter. Ägar-token är den mest typ token i OAuth 2.0. Många OAuth 2.0 olika implementeringar utgår ifrån att ägar-token är den enda typ av token som utfärdas.
> 
> 

1.  I Kommandotolken, ändra katalogen till **azuread**.

    `cd azuread`

2.  Installera Passport.js `passport-azure-ad` modulen:

    `npm install passport-azure-ad`

    Resultatet av kommandot ska se ut så här:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: lägga till MongoDB-moduler i ditt webb-API
I det här exemplet använder vi MongoDB som våra datalagret. 

1.  Installera Mongoose ett vanligt plugin-program att hantera modeller och scheman: 

    `npm install mongoose`

2.  Installera databasdrivrutinen för MongoDB, som också kallas MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: installera ytterligare moduler
Installera de resterande modulerna som krävs.

1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

2.  Ange följande kommandon. Kommandona installera följande moduler i katalogen node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: skapa en Server.js-fil för dina beroenden
En Server.js-fil innehåller flesta av funktionerna för web API-servern. Lägg till de flesta av din kod till den här filen. Produktion ska refactor du funktionerna i mindre filer som för separata vägar och styrenheter. I den här artikeln använder vi Server.js för detta ändamål.

1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

2.  Med hjälp av ett redigeringsprogram, skapa en Server.js-fil. Lägg till följande information i filen:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Spara filen. Du kommer tillbaka till den inom kort.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: skapa en konfigurationsfil för att lagra dina Azure AD-inställningar
Den här kodfilen skickar konfigurationsparametrarna från din Azure AD-portalen till Passport.js. Du har skapat konfigurationsvärdena när du har lagt till webb-API i portalen i början av artikeln. När du har kopierat koden förklarar vi vad du ska ange i värdena för dessa parametrar.

1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

2.  Skapa en Config.js-fil i en textredigerare. Lägg till följande information:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Värden som krävs

*   **IdentityMetadata**: det är där `passport-azure-ad` letar efter konfigurationsdata för identitetsprovider (IDP) och nycklarna att validera JSON Web token (JWTs). Om du använder Azure AD kan vill du förmodligen inte ändra den här.

*   **målgruppen**: din omdirigerings-URI från portalen.

> [!NOTE]
> Distribuera dina nycklar med återkommande intervall. Var noga med att du alltid hämta från URL: en ”openid_keys” och att appen har åtkomst till Internet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: lägga till konfigurationen i filen Server.js
Programmet måste läsa värdena från den config-fil som du nyss skapade. Lägg till .config-filen som en nödvändig resurs i ditt program. Ange de globala variablerna till dem som finns i Config.js.

1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

2.  Öppna Server.js i en textredigerare. Lägg till följande information:

    ```Javascript
    var config = require('./config');
    ```

3.  Lägg till ett nytt avsnitt i Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: lägga till informationen om MongoDB-modellen och schemat genom att använda Mongoose
Anslut sedan dessa tre filer i en REST API-tjänsten.

Vi använder MongoDB för att lagra våra uppgifter i den här artikeln. Diskuterar vi detta i *steg 4*.

I den Config.js-fil som du skapade i steg 11 databasen kallas *tasklist*. Som var det du angav i slutet av din mongoose_auth_local anslutnings-URL. Du behöver inte skapa den här databasen i förväg i MongoDB. Databasen skapas första gången du kör serverprogrammet (förutsatt att databasen inte redan finns).

Du har redan nämnt servern vilken MongoDB-databas som ska användas. Därefter måste du skriva ytterligare kod för att skapa modellen och schemat för din server uppgifter.

### <a name="the-model"></a>Modellen
Schemamodellen är väldigt enkla. Du kan expandera den om du behöver. 

Schemamodellen har dessa värden:

*   **NAMNET**. Den person som tilldelats aktiviteten. Det här är en **sträng** värde.
*   **UPPGIFTEN**. Namnet på aktiviteten. Det här är en **sträng** värde.
*   **DATUM**. Det datum då uppgiften förfaller. Det här är en **datetime** värde.
*   **SLUTFÖRA**. Om aktiviteten är slutförd. Det här är en **booleskt** värde.

### <a name="create-the-schema-in-the-code"></a>Skapa schemat i koden
1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

2.  Öppna Server.js i redigeringsprogram. Lägg till följande information under konfigurationsposten:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Den här koden ansluter till MongoDB-servern. Den returnerar även ett schemaobjekt.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Använd schemat för att skapa modellen i koden
Lägg till följande kod under den föregående kod:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Som du ser från koden skapar du först ditt schema. Därefter skapar du ett modellobjekt. Du använder model-objektet för att lagra data i hela koden när du definierar din **vägar**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: lägga till din vägar för aktiviteten REST API-servern
Lägg till de vägar du ska använda för REST API-servern nu när du har en databasmodell att arbeta med.

### <a name="about-routes-in-restify"></a>Om vägar i restify
Vägar i restify fungerar på samma sätt de göra när du använder Express-stacken. Du definierar vägar genom att använda den URI som du förväntar dig att klientprogram anropar. Vanligtvis kan du definiera vägarna i en separat fil. I den här självstudiekursen lägger vi vårt vägar i Server.js. För produktion rekommenderar vi att du strukturerar vägar till sina egna fil.

Ett typiskt mönster för en restify-väg är:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Det här är mönstret på den mest grundläggande nivån. Restify (och Express) ger mycket mer ingående funktioner, t.ex. möjligheten att definiera programtyper och komplex Routning över olika slutpunkter.

#### <a name="add-default-routes-to-your-server"></a>Lägga till standardvägar i servern
Lägg till grundläggande CRUD-vägarna: **skapa**, **hämta**, **uppdatera**, och **ta bort**.

1.  I Kommandotolken, ändra katalogen till **azuread**:

    `cd azuread`

2.  Öppna Server.js i en textredigerare. Nedan de databasposter du gjorde tidigare, lägger du till följande information:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
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
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
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
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Lägg till felhantering för vägarna
Lägga till viss felhantering, så du kan kommunicera tillbaka till klienten om det fel som inträffade.

Lägg till följande kod under den kod som du redan har skrivit:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: skapa servern
Det sista du behöver göra är att lägga till din server-instans. Serverinstansen som hanterar dina anrop.

Restify (och Express) har djupgående anpassning som du kan använda med en REST API-server. I den här kursen använder vi den mest grundläggande installationen.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: lägga till vägar (utan autentisering för tillfället)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16: köra server
Det är en bra idé att testa servern innan du lägger till autentisering.

Det enklaste sättet att testa din server är med hjälp av curl vid en kommandotolk. Om du vill göra detta behöver du ett enkelt verktyg som du kan använda för att analysera utdata som JSON. 

1.  Installera JSON-verktyget som vi använder i följande exempel:

    `$npm install -g jsontool`

    Då installeras JSON-verktyget globalt.

2.  Kontrollera att MongoDB-instansen körs:

    `$sudo mongod`

3.  Ändra katalogen till **azuread**, och kör sedan curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Lägg till en aktivitet:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    Svaret bör vara:

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Lista över aktiviteter för Jens:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Om dessa kommandon körs utan problem, är du redo att lägga till OAuth i REST API-servern.

*Du har nu en REST API-server med MongoDB!*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: Lägg till autentisering i REST API-servern
Nu när du har en aktiv REST-API, ställa in den för att använda den med Azure AD.

I Kommandotolken, ändra katalogen till **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Använd den oidc-ägarstrategi som ingår i passport-azure-ad
Hittills har du skapat en typisk REST TODO-server utan någon typ av auktorisering. Lägg till autentisering.

Ange först att du vill använda Passport. Placera den här rättigheten efter tidigare serverkonfiguration:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> När du skriver API: er är en bra idé att alltid länka dina data till något unikt från token som användaren inte kan förfalska. När den här servern lagrar TODO-objekt, lagrar dem baserat på prenumerations-ID för användaren i token (anropas via token.sub). Du kan placera token.sub i fältet ”ägare”. Detta säkerställer att endast denna användare har åtkomst till användarens TODOs. Ingen annan kan komma åt TODOs som har angetts. Det finns inga exponering i API: et för ”ägare”. En extern användare kan begära TODOs för andra användare, även om de autentiseras.
> 
> 

Använd sedan den öppna ID Connect ägarstrategi som medföljer `passport-azure-ad`. Placera det efter vad du klistrade in ovan:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport använder ett liknande mönster för alla sina strategier (Twitter, Facebook och så vidare). Alla strategigenererare följer mönstret. Skicka en strategi för en `function()` som använder en token och `done` som parametrar. Strategin som returneras när den gör allt arbete. Lagra användaren och token så att du inte behöver fråga efter den igen.

> [!IMPORTANT]
> Föregående kod tar alla användare som kan autentisera till servern. Detta kallas för automatisk registrering. På en produktionsserver skulle du vill låta alla i utan att behöva dem gå igenom den registreringsprocess som du väljer. Detta är oftast det mönster du ser i konsumentappar. Appen kan tillåta dig att registrera med Facebook, men sedan du ombedd att ange ytterligare information. Om du inte använde ett kommandoradsprogram för den här självstudiekursen, kan du extrahera den e-postadressen från tokenobjektet som returneras. Sedan kan du be användaren att ange ytterligare information. Eftersom detta är en testserver kan du lägga till användaren direkt till den minnesintern databasen.
> 
> 

### <a name="protect-endpoints"></a>Skydda slutpunkter
Skydda slutpunkter genom att ange den **passport.authenticate()** anropa med det protokoll som du vill använda.

Du kan redigera din vägen i serverkoden för mer avancerade alternativ:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: kör serverprogrammet igen
Använd curl igen för att se om du har OAuth 2.0 skydd mot dina slutpunkter. Gör detta innan du kör någon av dina klient-SDK: er mot den här slutpunkten. Rubriker som returneras bör du informera om autentiseringen fungerar korrekt.

1.  Kontrollera att MongoDB-isntance körs:

    `$sudo mongod`

2.  Ändra till den **azuread** katalogen och Använd curl:

    `$ cd azuread`

    `$ node server.js`

3.  Prova med ett grundläggande INLÄGG:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Ett 401-svar som anger att Passport-lagret försöker omdirigera till slutpunkten för auktorisering, vilket är exakt vad du vill.

*Du har nu en REST API-tjänst som använder OAuth 2.0!*

Du har gjort så mycket du kan med den här servern utan att använda en OAuth 2.0-kompatibel klient. För att behöver du granska en ytterligare vägledning.

## <a name="next-steps"></a>Nästa steg
Referens tillhandahålls det slutförda exemplet (utan dina konfigurationsvärden) som [en .zip-fil](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Du kan också klona det från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Du kan nu gå vidare till mer avancerade avsnitt. Du kanske vill prova [skydda en Node.js-webbapp med v2.0-slutpunkten](active-directory-v2-devquickstarts-node-web.md).

Här följer några ytterligare resurser:

* [Utvecklarhandbok för Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Stacken spill ”azure-active-directory” tagg](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du loggar som ska meddelas när säkerhetsincidenter. På den [Microsoft tekniska säkerhetsmeddelanden](https://technet.microsoft.com/security/dd252948) kan prenumerera på rekommendationerna säkerhetsaviseringar.

