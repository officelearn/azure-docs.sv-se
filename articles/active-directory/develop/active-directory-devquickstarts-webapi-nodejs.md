---
title: "Azure AD Node.js komma igång | Microsoft Docs"
description: "Hur du skapar en Node.js-REST-webb-API som kan integreras med Azure AD för autentisering."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 4f58177f540c14172d7ece8b4bc8c8a2b9787f8f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-web-apis-for-nodejs"></a>Komma igång med web API för Node.js
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* är ett mellanprogram för autentisering för Node.js. Flexibel och modulära, Passport diskret kan släppas till alla Express-baserade eller Restify-webbappar. En omfattande uppsättning strategier stöder autentisering med ett användarnamn och lösenord, Facebook, Twitter och mycket mer. Vi har utvecklat en strategi för Microsoft Azure Active Directory (AD Azure). Vi installerar den här modulen och lägger sedan till Microsoft Azure Active Directory `passport-azure-ad` plugin-programmet.

Om du vill göra det måste du:

1. Registrera ett program med Azure AD.
2. Konfigurera din app att använda Passport `passport-azure-ad` plugin-programmet.
3. Konfigurera ett klientprogram att anropa att göra-lista webb-API.

Koden för den här självstudiekursen [finns på GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> Den här artikeln beskriver inte hur du implementerar inloggning, registrering och profilhantering med Azure AD B2C. Den fokuserar på anropande webb-API: er när användaren redan autentiserats.  Vi rekommenderar att du börjar med [att integrera med Azure Active Directory-dokumentet](active-directory-how-to-integrate.md) att lära dig grunderna i Azure Active Directory.
>
>

Vi har publicerat källkoden exempelvis den här körs i GitHub under MIT-licens, så passa på att klona (eller ännu bättre förgrening) och ge feedback och pull-förfrågningar.

## <a name="about-nodejs-modules"></a>Om Node.js-moduler
Vi använder Node.js-moduler i den här genomgången. Moduler är inläsningsbar JavaScript-paket som tillhandahåller funktioner för ditt program. Du installerar vanligtvis moduler med Node.js en NPM-kommandoradsverktyget i NPM-installationskatalogen. Dock ingår vissa moduler, till exempel HTTP-modul i core Node.js-paketet.

Installerade moduler sparas i den **node_modules** katalogen i roten på din Node.js-installationskatalogen. Varje modul i den **node_modules** directory hanterar en egen **node_modules** katalog som innehåller alla moduler som den är beroende av. Varje modul som krävs har dessutom en **node_modules** directory. Den här rekursiv katalogstruktur representerar beroendekedjan.

Den här strukturen för beroende kedja resulterar i en större storleken för programmet. Men det garanterar även att alla beroenden är uppfyllda och att den version av moduler som används för att utveckla används också i produktion. Detta gör det mer förutsägbar produktion Apps beteende och förhindrar versionshantering problem som kan påverka användare.

## <a name="step-1-register-an-azure-ad-tenant"></a>Steg 1: Registrera en Azure AD-klient
Om du vill använda det här exemplet behöver du en Azure Active Directory-klient. Om du inte är säker på vilken en klient eller hur du hämtar ett, finns [hur du hämtar en Azure AD-klient](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>Steg 2: Skapa ett program
Nu kan du skapa en app i katalogen som ger Azure AD den information som krävs för att kommunicera säkert med din app.  Både klientappen och webb-API som representeras av ett enda **program-ID** i det här fallet eftersom de omfattar en logisk app.  Du skapar en app genom att följa [dessa anvisningar](active-directory-how-applications-are-added.md). Om du skapar en line-of-business-app [dessa ytterligare instruktioner kan vara användbar](../active-directory-applications-guiding-developers-for-lob-applications.md).

Skapa ett program:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj ditt konto på den översta menyn. Sedan, under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.

3. Välj på menyn till vänster **fler tjänster**, och välj sedan **Azure Active Directory**.

4. Välj **App registreringar**, och välj sedan **Lägg till**.

5. Följ anvisningarna för att skapa en **webbprogram och/eller WebAPI**.

      * Den **namn** beskriver ditt program för slutanvändare av programmet.

      * Den **inloggnings-URL** är den grundläggande Webbadressen för din app.  Standard-URL i exempelkoden är `https://localhost:8080`.

6. När du registrerar tilldelar Azure AD appen ett unikt program-ID. Du behöver det här värdet i nästa avsnitt så kopiera den från appen på sidan.

7. Från den **inställningar** -> **egenskaper** för programmet, uppdatera App-ID-URI. Den **App-ID URI** är en unik identifierare för programmet. Konventionen är att använda `https://<tenant-domain>/<app-name>`, till exempel: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Skapa en **nyckeln** för programmet från den **inställningar** sida och kopiera den någonstans. Du behöver den snart.

## <a name="step-3-download-nodejs-for-your-platform"></a>Steg 3: Ladda ned Node.js för din plattform
Du måste ha en fungerande installation av Node.js. för att kunna använda det här exemplet.

Installera Node.js från [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>Steg 4: Installera MongoDB för din plattform
Du måste ha en fungerande installation av MongoDB för att kunna använda det här exemplet. Du kan använda MongoDB för att göra det REST API beständiga över serverinstanser.

Installera MongoDB från [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Den här genomgången förutsätter att du använder standardslutpunkterna för installation och server för MongoDB, som vid tidpunkten för detta skrivs är mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>Steg 5: Installera Restify-modulerna i ditt webb-API
Vi använder Restify för att skapa vårt REST-API. Restify är ett minimalt och flexibelt Node.js programramverk som härleds från Express. Det har kraftfulla och stabila funktioner för att skapa REST API:er utöver Connect.

### <a name="install-restify"></a>Installera Restify
1. Från kommandoraden ändrar du katalog till den **azuread** directory. Om den **azuread** katalogen inte finns, skapar du den.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Ange följande kommando:

    `npm install restify`

    Restify installeras med det här kommandot.

#### <a name="did-you-get-an-error"></a>Fick du ett felmeddelande?
När du använder NPM i vissa operativsystem, kan ett felmeddelande som säger **fel: EPERM chmod ”/ usr/lokal/bin /...”** och förslag du försöka köra kontot som administratör. Om detta inträffar kan du använda sudo-kommando för att köra NPM på en högre Privilegienivå.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Fick du ett felmeddelande om DTRACE?
Du kan se ett fel så här när du installerar Restify:

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
Restify erbjuder en kraftfull mekanism för att spåra REST-anrop med DTrace. Många operativsystem har dock inte DTrace. Du kan ignorera dessa fel utan att oroa dig.

Kommandots utdata bör likna följande utdata:

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


## <a name="step-6-install-passportjs-in-your-web-api"></a>Steg 6: Installera Passport.js i ditt webb-API
[Passport](http://passportjs.org/) är ett mellanprogram för autentisering för Node.js. Flexibel och modulära, Passport diskret kan släppas till alla Express-baserade eller Restify-webbappar. En omfattande uppsättning strategier stöder autentisering med ett användarnamn och lösenord, Facebook, Twitter och mycket mer.

Vi har utvecklat en strategi för Azure Active Directory. Vi installerar den här modulen och lägger sedan till plugin-programmet Azure Active Directory strategin.

1. Från kommandoraden ändrar du katalog till den **azuread** directory.

2. Ange följande kommando för att installera passport.js:

    `npm install passport`

    Resultatet av kommandot ska se ut ungefär så här:

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>Steg 7: Lägg till Passport-Azure-AD i ditt webb-API
Nästa vi lägga till OAuth-strategin genom att använda `passport-azure-ad`, en uppsättning strategier som ansluter Azure Active Directory till Passport. Vi använder den här strategin för ägar-token i REST API-exemplet.

> [!NOTE]
> Även om OAuth2 erbjuder ett ramverk där alla kända token-typer kan utfärdas, används vanligtvis bara vissa token-typer. Ägar-token är de vanligaste token som skyddar slutpunkter. De är de mest typ token i OAuth2. Många olika implementeringar utgår ifrån att ägar-token är den enda typen av token som utfärdas.
>
>

Från kommandoraden ändrar du katalog till den **azuread** directory.

Skriv följande kommando för att installera Passport.js `passport-azure-ad module`:

`npm install passport-azure-ad`

Utdata från kommandot bör likna följande utdata:


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



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>Steg 8: Lägga till MongoDB-moduler i ditt webb-API
Vi använder MongoDB som våra datalagret. Därför som vi behöver installera vanligt plugin-programmet kallas Mongoose för att hantera modeller och scheman. Vi behöver också installerar databasdrivrutinen för MongoDB (som också kallas MongoDB).

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>Steg 9: Installera ytterligare moduler
Vi installera sedan resterande moduler som krävs.

1. Från kommandoraden ändrar du katalog till den **azuread** mappen om du inte visas.

    `cd azuread`

2. Ange följande kommandon för att installera modulerna i din **node_modules** directory:

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>Steg 10: Skapa en server.js med dina beroenden
Server.js-fil innehåller de flesta funktionerna för våra API-webbserver. Vi lägga till största delen av koden i den här filen. För produktion rekommenderar vi att du refactor funktionerna i mindre filer, till exempel separata vägar och styrenheter. I den här demon använder vi du server.js för den här funktionen.

1. Från kommandoraden ändrar du katalog till den **azuread** mappen om du inte visas.

    `cd azuread`

2. Skapa en `server.js` filen i din favorit-redigeraren och Lägg sedan till följande information:

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. Spara filen. Vi återkommer till det inom kort.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>Steg 11: Skapa en konfigurationsfil för att lagra dina Azure AD-inställningar
Den här kodfilen skickar konfigurationsparametrarna från din Azure Active Directory-portalen till Passport.js. Du har skapat konfigurationsvärdena när du lade till webb-API i portalen under den första delen av genomgången. När du har kopierat koden förklarar vi vad du ska ange i värdena för dessa parametrar.

1. Från kommandoraden ändrar du katalog till den **azuread** mappen om du inte visas.

    `cd azuread`

2. Skapa en `config.js` filen i din favorit-redigeraren och Lägg sedan till följande information:

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. Spara filen.

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>Steg 12: Lägg till konfigurationsvärden i din server.js-fil
Vi behöver läsa värdena från .config-filen som du skapade i vårt program. Detta gör vi lägga till .config-filen som en nödvändig resurs i vårt program. Vi anger globala variabler för att matcha variabler i config.js-dokumentet.

1. Från kommandoraden ändrar du katalog till den **azuread** mappen om du inte visas.

    `cd azuread`

2. Öppna din `server.js` filen i din favorit-redigeraren och Lägg sedan till följande information:

    ```Javascript
    var config = require('./config');
    ```
3. Lägg till ett nytt avsnitt som `server.js` med följande kod:

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Spara filen.

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Steg 13: Lägg till information i MongoDB-modellen och schemat genom att använda Mongoose
Nu kommer dessa förberedelser att starta betala av som vi kombinera dessa tre filer i en REST API-tjänsten.

Vi använder MongoDB för att lagra våra uppgifter som beskrivs i steg 4 i den här genomgången.

I den `config.js` -filen som vi skapade i steg 11, vi har ringt vår databas `tasklist`eftersom som har vi gjort i slutet av våra **mogoose_auth_local** anslutnings-URL. Du behöver inte skapa den här databasen i förväg i MongoDB. I stället skapar MongoDB det för oss på den första körningen av vårt serverprogram (förutsatt att databasen inte redan finns).

Nu när vi har redan nämnt servern vilken MongoDB-databas som vi vill använda, behöver vi skriva ytterligare kod för att skapa modellen och schemat för vår server uppgifter.

### <a name="discussion-of-the-model"></a>Beskrivning av modellen
Vår schemamodellen är enkelt. Du kan expandera den efter behov.

NAMN: Namnet på den person som har tilldelats aktiviteten. En **sträng**.

UPPGIFT: Själva aktiviteten. En **sträng**.

DATUM: Det datum då uppgiften förfaller. EN **DATETIME**.

SLUTFÖRD: Om aktiviteten har slutförts eller inte. EN **BOOLESKT**.

### <a name="creating-the-schema-in-the-code"></a>Skapa schemat i koden
1. Från kommandoraden ändrar du katalog till den **azuread** mappen om du inte visas.

    `cd azuread`

2. Öppna din `server.js` filen i din favorit-redigeraren och Lägg sedan till följande information under konfigurationsposten:

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
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
Som du ser från koden skapar vi vårt schemat först. Sedan skapar vi ett modellobjekt som vi använder för att lagra våra data i hela koden när vi definiera vår **vägar**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>Steg 14: Lägga till våra vägar för våra REST API-aktivitetsserver
Nu när vi har en databasmodell att arbeta med Lägg till de vägar som vi ska använda för vårt REST API-servern.

### <a name="about-routes-in-restify"></a>Om vägar i Restify
I Restify fungerar vägar på samma sätt som i Express-stacken. Du definierar vägar genom att använda den URI som du förväntar dig att klientprogram anropar. Vanligtvis kan du definiera vägarna i en separat fil. För våra ändamål låter vi vårt vägar i server.js-fil. Vi rekommenderar att du strukturerar dessa vägar till sina egna fil för produktion.

Ett typiskt mönster för en Restify-väg är följande:

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Det här är mönstret på den mest grundläggande nivån. Restify (och Express) ger mycket mer ingående funktioner, till exempel definiera programtyper och tillhandahåller komplex Routning över olika slutpunkter. För våra ändamål hindrar VI vägarna enkla.

### <a name="add-default-routes-to-our-server"></a>Lägga till standardvägar i vår server
Vi nu lägga till grundläggande CRUD-vägarna för skapa, hämta, uppdatera och ta bort.

1. Från kommandoraden ändrar du katalog till den **azuread** mapp om du inte redan det:

    `cd azuread`

2. Öppna den `server.js` filen i din favorit-redigeraren och Lägg sedan till följande information under de föregående databasposter du gjorde:

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
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

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
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

### <a name="add-error-handling-in-our-apis"></a>Lägg till felhantering i våra API: er
```

///--- Errors for communicating something interesting back to the client.

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


## <a name="step-15-create-your-server"></a>Steg 15: Skapa servern
Vi har definierat vår databas och våra vägar på plats. Det sista du behöver göra är att lägga till serverinstansen som hanterar våra anrop.

I Restify (och snabb) kan du göra mycket anpassning av en REST API-server, men igen vi ska använda den mest grundläggande installationen för våra ändamål.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>Steg 16: Lägg till vägar på servern (utan autentisering för tillfället)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
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
// Register a default '/' handler.
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

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>Steg 17: Kör servern (innan du lägger till stöd för OAuth)
Testa servern innan vi lägger till autentisering.

Det enklaste sättet att testa din server är med hjälp av curl på en kommandorad. Innan vi gör det, måste ett verktyg som gör att vi kan analysera utdata som JSON.

1. Installera följande JSON-verktyget (i följande exempel används det här verktyget):

    `$npm install -g jsontool`

    Då installeras JSON-verktyget globalt. Nu när vi har åstadkommas som vi spela upp på servern:

2. Kontrollera först att mongoDB-instansen körs:

    `$sudo mongod`

3. Sedan kan ändra till katalogen och starta curling:

    `$ cd azuread` `$ node server.js`

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

4. Sedan vi kan lägga till en aktivitet för det här sättet:

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
    Och vi kan ange aktiviteter för Jens det här sättet:

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Om allt detta fungerar redo vi att lägga till OAuth i REST API-servern.

Du har en REST API-server med MongoDB!

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>Steg 18: Lägg till autentisering i vårt REST API-server
Nu när vi har en aktiv REST-API kan vi börjar att det är användbart med Azure AD.

Från kommandoraden ändrar du katalog till den **azuread** mappen om du inte visas.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Använd den OIDC-ägarstrategi som ingår i passport-azure-ad
Hittills har vi skapat en typisk REST TODO-server utan någon typ av auktorisering. Detta är där vi börja sätta ihop som.

1. Vi måste först ange att vi vill använda Passport. Placera den här rättigheten efter din andra serverkonfiguration:

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > När du skriver API: er, rekommenderar vi att du alltid länka dina data till något unikt från token som användaren inte kan förfalska. När den här servern lagrar TODO-objekt, lagrar dem baserat på objekt-ID för användaren i token (anropas via token.oid), som vi har gjort i fältet ”ägare”. Detta säkerställer att bara den användaren kan komma åt sina TODOs. Det finns inga exponering API ”ägare” en extern användare kan begära TODOs av andra, även om de autentiseras.                    

2. Nästa ska vi använda den ägarstrategi som medföljer `passport-azure-ad`. Titta på koden för tillfället och förklarar vi resten inom kort. Placera det efter vad du klistrade in ovan:

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
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


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
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

    passport.use(bearerStrategy);
```

Passport använder ett liknande mönster för alla dess strategier (Twitter, Facebook och så vidare) som alla strategiskrivare följer. Tittar på strategin ser du vi skickar en funktion som har en token och klart som parametrar. Strategin kommer tillbaka till oss när den har sitt arbete. När det har vi lagra användaren och token så att vi inte behöver fråga efter den igen.

> [!IMPORTANT]
> Föregående kod tar alla användare som inträffar att autentisera till vår server. Detta kallas för automatisk registrering. Produktionsservrar bör gå du rekommenderar vi att du inte släpper vem som helst utan att behöva dem igenom en registreringsprocess som du väljer. Detta är oftast det mönster du ser i konsumentappar, så att du kan registrera med Facebook men sedan uppmanad att fylla i ytterligare information. Om det inte var ett kommandoradsprogram kunnat vi extrahera den e-postadressen från tokenobjektet som returneras och sedan bett användaren att fylla i ytterligare information. Eftersom det här är en testserver lägger vi helt enkelt till dem i den minnesinterna databasen.
>
>

### <a name="protect-some-endpoints"></a>Skydda vissa slutpunkter
Du skyddar slutpunkter genom att ange den `passport.authenticate()` anropa med det protokoll som du vill använda.

Se vår serverkoden gör något mer intressant, vi redigera vägen.

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

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>Steg 19: Kör serverprogrammet igen och se till att det avvisar dig
Nu ska vi använda `curl` igen för att se om vi nu har OAuth2-skydd mot vår slutpunkter. Vi gör det här testet innan du kan köra klient-SDK: er mot den här slutpunkten. Rubriker som returneras borde räcka för att berätta för oss om vi ned sökvägen.

1. Kontrollera först att mongoDB-instansen körs:

    `$sudo mongod`

2. Sedan kan ändra till katalogen och starta curling.

      `$ cd azuread` `$ node server.js`

3. Försök med ett grundläggande INLÄGG.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Ett 401 är det svar du letar efter här. Svaret anger att Passport-lagret försöker omdirigera till auktoriserade slutpunkten, vilket är exakt vad du vill.

## <a name="next-steps"></a>Nästa steg
Du har gjort så mycket du kan med den här servern utan att använda en OAuth2-kompatibel klient. Du behöver gå igenom ett ytterligare genomgången.

Nu har du lärt dig hur du implementerar en REST-API med Restify och OAuth2. Du kan också ha mer än tillräckligt med kod för att hålla utveckla tjänsten och lär dig hur du bygger på det här exemplet.

Om du är intresserad av i nästa steg i din ADAL resa, kan vissa klienter som stöds ADAL rekommenderar vi att du fortsätta att arbeta med.

Klona ned till datorn utvecklare och konfigurera enligt beskrivningen i den här genomgången.

[ADAL för iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL för Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
