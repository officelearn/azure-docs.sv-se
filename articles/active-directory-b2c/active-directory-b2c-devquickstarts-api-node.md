<properties
    pageTitle="B2C-förhandsgranskning: Säkra ett webb-API med Node.js | Microsoft Azure"
    description="Hur du skapar ett webb-API för Node.js som accepterar token från en B2C-klient"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="brandwe"/>

# B2C-förhandsgranskning: Säkra ett webb-API med Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] Den här artikeln innehåller inte information om hur du implementerar inloggning, registrering och profilhantering med hjälp av Azure AD B2C. Den fokuserar på anropande webb-API: er när användaren har verifierats. Börja med [självstudierna för att komma igång med webbappen .NET](active-directory-b2c-devquickstarts-web-dotnet.md) om du vill lära dig grunderna i Azure Active Directory B2C.


> [AZURE.NOTE]  Det är tänkt att du ska ansluta till det här exemplet genom att använda vårt [iOS B2C-exempelprogram](active-directory-b2c-devquickstarts-ios.md). Gör den här genomgången först och följ sedan det exemplet.

**Passport** är ett mellanprogram för autentisering för Node.js. Modulbaserade Passport är mycket flexibelt och kan diskret installeras i alla Express-baserade eller Restify-webbprogram. En omfattande uppsättning strategier stöder autentisering med användarnamn och lösenord, Facebook, Twitter och mycket mer. Vi har utvecklat en strategi för Azure Active Directory (Azure AD). Du installerar den här modulen och lägger sedan till plugin-programmet Azure AD `passport-azure-ad`.

För detta behöver du göra följande:

1. Registrera ett program med Azure AD.
2. Ställa in så att appen använder plugin-programmet för Passport `azure-ad-passport`.
3. Konfigurera ett klientprogram att anropa ett webb-API för ”uppgiftslistan”.

Koden för dessa självstudier [underhålls på GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Om du vill följa med kan du [ladda ned appens stomme som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) eller klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

Det slutförda programmet finns i slutet av dessa självstudier.

> [AZURE.WARNING]   Om du vill använda vår B2C-förhandsgranskning måste du använda samma **klient-ID**,/**Program-ID** och principer för både webb-API:ets uppgiftsserver och den klient som den ansluter till. Det här gäller även självstudierna för iOS och Android. Om du tidigare har skapat ett program i någon av dessa snabbstarter använder du dessa värden. Skapa inte nya.


## Hämta en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C, måste du skapa en katalog eller klient.  En katalog är en behållare för alla användare, appar, grupper och mer.  Om du inte redan har en [skapar du en B2C-katalog](active-directory-b2c-get-started.md) innan du fortsätter.

## Skapa ett program

Därefter måste du skapa en app i B2C-katalogen som ger Azure AD information som krävs för säker kommunikation med din app. I det här fallet representeras både klientappen och webb-API av ett enda **Program-ID** eftersom de omfattar en logisk app. Skapa en app genom att följa [anvisningarna](active-directory-b2c-app-registration.md). Se till att:

- Inkludera en **webbapp/ett webb-api** i programmet
- Ange `http://localhost/TodoListService` som en **Reply URL**. Den är standard-URL för den här kodexemplet.
- Skapa en **programhemlighet** för programmet och kopiera den. Du behöver den senare. Du behöver den snart. Tänk på att det här värdet måste vara [Esc-XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) innan du använder det.
- Kopiera det **Program-ID** som har tilldelats din app. Du behöver även detta senare.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Skapa principer

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Den här appen innehåller tre identitetsupplevelser: registrera, logga in och logga ut med Facebook. Du måste skapa en princip av varje typ, enligt beskrivningen i [artikeln om principreferens](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Tänk på följande när du skapar din tre principer:

- Välj **Visningsnamn** och andra registreringsattribut i principen för registrering.
- Välj det **visningsnamn** och **objekt-ID** som programmet gör anspråk på i varje princip.  Du kan också välja andra anspråk.
- Kopiera ned **namnet** för varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`.  Du behöver dem senare.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina tre principer, är du redo att bygga din app.

Tänk på att den här artikeln inte beskriver hur du använder principer som du nyss har skapat. Börja med [självstudierna för att komma igång med webbappen för .NET](active-directory-b2c-devquickstarts-web-dotnet.md) om du vill veta mer om hur principer fungerar i Azure AD B2C.

## Ladda ned Node.js för plattformen

Du måste ha en fungerande installation av Node.js. för att kunna använda det här exemplet.

Installera Node.js från [nodejs.org](http://nodejs.org).

## Installera MongoDB för din plattform

Du måste också ha en fungerande installation av MongoDB för att kunna använda det här exemplet. Du använder MongoDB för att göra dina REST-API:er beständiga över serverinstanser.

Installera MongoDB från [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Den här genomgången förutsätter att du använder standardslutpunkterna för installation och server, vilket (när detta skrivs) är `mongodb://localhost`.

## Installera Restify-modulerna i ditt webb-API

Du använder Restify för att skapa REST-API. Restify är ett minimalt och flexibelt Node.js-program härlett från Express. Det har kraftfulla och stabila funktioner för att skapa REST API:er utöver Connect.

### Installera Restify

Ändra katalogen till `azuread` från kommandoraden. Skapa `azuread`-katalogen om den inte finns.

`cd azuread` eller `mkdir azuread;`

Ange följande kommando:

`npm install restify`

Restify installeras med det här kommandot.

#### Fick du ett felmeddelande?

När du använder `npm` kan du, i vissa operativsystem, få felmeddelandet `Error: EPERM, chmod '/usr/local/bin/..'` och en förfrågan om att köra kontot som administratör. Om det händer använder du `sudo`-kommandot för att köra `npm` på en högre privilegienivå.

#### Fick du ett felmeddelande om DTrace?

När du installerar Restify kan du stöta på något som ser ut så här:

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

Resultatet av kommandot ska se ut ungefär så här:

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

## Installera Passport i ditt webb-API

[Passport](http://passportjs.org/) är ett mellanprogram för autentisering för Node.js. Modulbaserade Passport är mycket flexibelt och kan diskret installeras i alla Express-baserade eller Restify-webbprogram. En omfattande uppsättning strategier stöder autentisering med användarnamn och lösenord, Facebook, Twitter och mycket mer. Vi har utvecklat en strategi för Azure AD. Du installerar den här modulen och lägger sedan till plugin-programmet för Azure AD-strategin.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där.

Installera Passport genom att ange följande kommando:

`npm install passport`

Resultatet av kommandot ska se ut ungefär så här:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## Lägg till passport-azuread i ditt webb-API

Lägg sedan till OAuth-strategin genom att använda `passport-azuread`, en uppsättning strategier som ansluter Azure AD med Passport. Använd den här strategin för ägar-token i REST API-exemplet.

> [AZURE.NOTE] Även om OAuth2 erbjuder ett ramverk där alla kända token-typer kan utfärdas, är det bara vissa token-typer som används ofta. De token som skyddar slutpunkter är ägar-token. Det är den token-typ som används oftast i OAuth2. Många olika implementeringar utgår ifrån att ägar-token är den enda typ av token som utfärdas.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där.

Installera Passport `passport-azure-ad`-modulen genom att ange följande kommando:

`npm install passport-azure-ad`

Resultatet av kommandot ska se ut ungefär så här:

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

## Lägga till MongoDB-moduler i ditt webb-API

Du använder MongoDB som datalager. Därför måste du installera både Mongoose (ett vanligt plugin-program för att hantera modeller och scheman) och databasdrivrutinen för MongoDB, som också kallas MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## Installera ytterligare moduler

Installera sedan resterande moduler som krävs.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Ange följande kommandon för att installera modulerna i din `node_modules`-katalog:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Skapa en server.js-fil med dina beroenden

`server.js`-filen innehåller de flesta funktioner för Webb-API-servern. Du kommer att lägga till största delen av koden i den här filen. När det gäller produktion ska du omstrukturera funktionerna i mindre filer, till exempel separata vägar och styrenheter. För dessa självstudier använder du server.js för den här funktionen.

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

## Skapa en config.js-fil för att lagra dina Azure AD-inställningar

Den här kodfilen skickar konfigurationsparametrarna från din Azure AD-portal till `Passport.js`-filen. Du har skapat konfigurationsvärdena när du la till ditt webb-API i portalen under den första delen av genomgången. När du har kopierat koden förklarar vi vad du ska ange i värdena för dessa parametrar.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Skapa en `config.js`-fil i en textredigerare. Lägg till följande information:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Värden som krävs

`IdentityMetadata`: Det är där `passport-azure-ad` letar efter konfigurationsdata för din identitetsprovider. Det är också här den letar efter nycklarna för att validera JSON-webb-token. Du vill förmodligen inte ändra den här om du använder Azure AD.

`audience`: Den URI (Uniform Resource Identifier) från portalen som identifierar din tjänst. I exemplet används  `http://localhost/TodoListService`.

`tenantName`: Ditt klientnamn (till exempel **contoso.onmicrosoft.com**).

`policyName`: Den princip som du vill använda för att validera token som kommer till servern. Det bör vara samma princip som du använder i klientprogrammet för inloggning.

> [AZURE.NOTE] Använd samma principer för både klient- och serverinstallationen när det gäller B2C-förhandsgranskning. Om du har redan gjort en genomgång och skapat dessa principer behöver du inte göra det igen. Eftersom du har gjort genomgången ska du inte behöva ställa in nya principer för klientgenomgångarna på webbplatsen.

## Lägg till konfigurationen i din server.js-fil

Du måste läsa värdena från den `config.js`-fil som du just har skapat för ditt program. Gör det genom att lägga till `.config`-filen som en nödvändig resurs i ditt program och sedan ange de globala variablerna till dem i `config.js`- dokumentet.

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
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Lägg till informationen om MongoDB-modellen och -schemat genom att använda Mongoose

Det du har förberett tidigare kommer att vara till hjälp när du för ihop dessa tre filer i en REST API-tjänst.

Lagra dina uppgifter genom att använda MongoDB för den här genomgången, som beskrivits tidigare.

I den `config.js`-fil som du skapade kallade du databasen för **Aktivitetslista**. Det var även det du angav i slutet av anslutnings-URL:en för `mongoose_auth_local`. Du behöver inte skapa den här databasen i förväg i MongoDB. Databasen skapas åt dig första gången du kör serverprogrammet, om den inte redan finns.

När du har talat om för servern vilken MongoDB-databas som ska användas, måste du skriva ytterligare kod för att skapa modellen och schemat för serveraktiviteterna.

### Expandera modellen

Den här schemamodellen är väldigt enkelt. Du kan expandera den efter behov.

`name`: Den som har tilldelats aktiviteten. Det här är en **-sträng**.

`task`: Själva aktiviteten. Det här är en **-sträng**.

`date`: Det datum då aktiviteten ska vara klar. Det här är en **datetime**.

`completed`: Om aktiviteten har slutförts eller inte. Det här är en **boolesk**.

### Skapa schemat i koden

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Öppna `server.js`-filen i en textredigerare. Lägg till följande information under konfigurationsposten:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Det ansluter till MongoDB-servern och lämnar tillbaka ett schemaobjekt.

### Använd schemat för att skapa modellen i koden

Lägg till följande kod under den du skrivit ovan:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Du skapar först schemat och sedan ett modellobjekt som du använder för att lagra data i hela koden när du definierar dina **vägar**.

## Lägga till vägar för din REST API-aktivitetsserver

Lägg till de vägar du ska använda för REST API-servern nu när du har en databasmodell att arbeta med.

### Om vägar i Restify

I Restify fungerar vägar på exakt samma sätt som när de använder Express-stacken. Du definierar vägar genom att använda den URI som du förväntar dig att klientprogram anropar. I de flesta fall bör du definiera vägarna i en separat fil. För dessa självstudier placerar du vägarna i `server.js`-filen. Vi rekommenderar att du strukturerar dem i en egen fil för när de ska användas i produktion.

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

Det här är mönstret på den mest grundläggande nivån. Restify och Express kan ge mycket mer ingående funktioner, till exempel att definiera programtyper och göra komplex routning över olika slutpunkter. För dessa självstudier håller vi vägarna enkla.

#### Lägga till standardvägar i servern

Du ska nu lägga till de grundläggande CRUD-vägarna för **skapa**, **hämta**, **uppdatera**, och **ta bort**.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

Öppna `server.js`-filen i en textredigerare. Lägg till följande information under de databasposter du gjorde ovan:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
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
// Delete a task by name
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
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
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
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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

#### Lägg till felhantering för vägarna

Du bör lägga till viss felhantering, så att du kan informera klienten om eventuella problem på ett sätt som den kan förstå.

Lägg till följande kod under den du skrev ovan:

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


## Skapa servern

Du har nu definierat databasen och placerat ut vägarna. Det som är kvar är att lägga till serverinstansen som ska hantera dina anrop.

Med Restify och Express kan du anpassa för en REST API-server på en mycket ingående nivå, men här använder du den mest grundläggande installationen.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
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
}));
```
## Lägg till vägar på servern (utan autentisering)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
## Kör servern innan du lägger till stöd för OAuth

Du bör testa servern innan du lägger till autentisering.

Det enklaste sättet att göra det är genom att använda `curl` i kommandoraden. Men innan du gör det behöver du ett enkelt verktyg som gör att du kan analysera utdata som JSON. Installera först JSON-verktyget.

`$npm install -g jsontool`

Då installeras JSON-verktyget globalt. När du har installerat JSON-verktyget testar du servern:

Kontrollera att MongoDB-instansen körs.

`$sudo mongodb`

Ändra till `azuread`-katalogen och använd `curl`.

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
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

Lägg till en aktivitet:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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
Du kan ange aktiviteter för användaren ”Jens” på det här sättet:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Om det fungerar är du redo att lägga till OAuth i REST API-servern.

Du har en REST API-server med MongoDB.

## Lägg till autentisering i REST API-servern

Nu när du har en aktiv REST API-server kan du använda den med Azure AD.

Ändra katalogen till `azuread` från kommandoraden, om den inte redan finns där:

`cd azuread`

### Använd den OIDC-ägarstrategi som ingår i passport-azure-ad

Hittills har du skapat en typisk REST ToDo-server utan någon typ av auktorisering. Nu kan du börja sätta ihop auktoriseringen.

Först måste du ange att du vill använda Passport. Lägg till det direkt under din andra serverkonfiguration:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
När du skriver API: er bör du alltid länka dina data till något unikt från token, något som användaren inte kan förfalska. När servern lagrar ToDo-objekt, sker det baserat på **objekt-ID** för användaren i token (anropas via token.oid). Det anges i fältet ”ägare”. Det gör att det bara är just den användaren som har åtkomst till sina ToDo-objekt, och att ingen annan har åtkomst till de objekt som har angetts. Ägarens API exponeras aldrig, så en extern användare kan begära andras ToDo-objekt, även om de autentiseras.

Använd sedan den ägarstrategi som kommer med `passport-azure-ad`. (Vi ska bara titta på koden för tillfället.) Placera det efter det du klistrade in ovan:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
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

Passport använder ett mönster för alla sina strategier (inklusive Twitter och Facebook). Det liknar dem som alla strategiskrivare följer. Du skickar en `function()` som har `token` och `done` som parametrar. Strategin kommer tillbaka till dig när det har gjort klart jobbet. Du bör då lagra användaren och spara token så att du inte behöver fråga efter den igen.

> [AZURE.IMPORTANT]
Koden ovan tar alla användare som autentiserar till servern. Detta kallas autoregistrering. När det gäller produktionsservrar bör alla användare som släpps in först gå igenom den registreringsprocess som du har valt. Det är oftast det mönster du ser i konsumentappar. Det gör att du kan registrera dig med hjälp av Facebook men du blir sedan uppmanad att fylla i ytterligare information. Om det inte var ett kommandoradsprogram hade vi kunnat extrahera mejladressen från det token-objekt som returneras och sedan bett användarna att fylla i ytterligare information. Eftersom det här är en testserver lägger vi helt enkelt till dem i den minnesinterna databasen.

### Skydda slutpunkter

Du skyddar slutpunkter när du anger `passport.authenticate()`-anropet med hjälp av det protokoll som du vill använda.

Du kan redigera vägen i serverkoden så att den gör något mer intressant:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## Kör serverprogrammet igen för att verifiera att det avvisar dig

Du kan använda `curl` igen om du vill kontrollera att du nu har OAuth2-skydd mot dina slutpunkter. Gör detta innan du kör någon av dina klient-SDK:er mot den här slutpunkten. De rubriker som returneras borde räcka för att se om att du är på rätt väg.

Kontrollera att MongoDB-instansen körs:

    $sudo mongodb

Ändra till katalogen och använd `curl`:

    $ cd azuread
    $ node server.js

Prova med ett grundläggande INLÄGG:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Ett 401-fel är det svar du vill ha. Det visar att Passport-lagret försöker omdirigera till slutpunkten för auktorisering .


## Du har nu en REST API-tjänst som använder OAuth2

Du har gjort så mycket du kan med den här servern utan att använda en OAuth2-kompatibel klient. Om du vill göra det behöver du ytterligare en genomgång.

Om du bara vill ha information om hur du implementerar en REST-API med Restify och OAuth2 har du nu tillräckligt med kod så att du kan fortsätta att utveckla tjänsten och bygga vidare på det här exemplet.

Som referens tillhandahålls det slutförda exemplet (utan dina konfigurationsvärden) [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Du kan också klona den från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Nästa steg

Du kan nu gå vidare  till mer avancerade ämnen, till exempel:

[Ansluta till ett webb-API genom att använda iOS med B2C](active-directory-b2c-devquickstarts-ios.md)



<!--HONumber=Jun16_HO2-->


