---
title: Skapa en Node.js Express-webbapp för inloggning och utloggning med Azure Active Directory | Microsoft Docs
description: Lär dig hur du skapar en Node.js Express MVC-webbapp som integreras med Azure AD för inloggning.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19563f76c261fda1fca53babcb553f2dceeaa345
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990101"
---
# <a name="quickstart-build-a-nodejs-express-web-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Snabbstart: Skapa en Node.js Express-webbapp för inloggning och utloggning med Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Passport är ett mellanprogram för autentisering för Node.js. Passport är flexibelt och modulärt och du kan diskret släppa det i valfri Express-baserad eller restify-baserad webbapp. En omfattande uppsättning strategier stöder autentisering som använder användarnamn och lösenord, Facebook, Twitter och mycket mer. För Azure Active Directory (Azure AD) installerar vi den här modulen och sedan plugin-programmet Azure AD `passport-azure-ad`.

I den här snabbstarten lär du dig hur du använder Passport till att:

* Logga in användaren i appen med Azure AD.
* Visa information om användaren.
* Logga ut användaren ur appen.

Om du vill skapa en komplett, fungerande app måste du:

1. Registrera en app.
2. Konfigurera din app att använda strategin `passport-azure-ad`.
3. Använda Passport för att utfärda inloggnings- och utloggningsförfrågningar till Azure AD.
4. Skriv ut data om användaren.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra följande krav:

* [Ladda ned appens stomme som en .zip-fil](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)
  
    * Klona stommen:

        ```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

    Koden för den här snabbstarten finns [på GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Du hittar den färdiga appen i slutet av den här snabbstarten.

* Ha en Azure AD-klientorganisationen där du kan skapa användare och registrera en app. Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-app"></a>Steg1: Registrera en app

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj ditt konto på menyn högst upp på sidan. Under **kataloglistan** väljer du den Active Directory-klientorganisation där du vill registrera appen.
1. Välj **Alla tjänster** på menyn till vänster på skärmen och välj sedan **Azure Active Directory**.
1. Välj **Appregistreringar** och sedan **Lägg till**.
1. Följ anvisningarna för att skapa en **webbapp** och/eller ett **WebAPI**.

    * **Namnet** på appen beskriver den för användarna.
    * **Inloggnings-URL** är appens grundläggande webbadress. Stommens standardinställning är `http://localhost:3000/auth/openid/return`.

1. När du har registrerat tilldelar Azure AD din app ett unikt program-ID. Du behöver det här värdet i följande avsnitt, så kopiera det från appsidan.
1. På sidan **Inställningar > Egenskaper** för appen uppdaterar du app-ID-URI. 
    
    **App-ID-URI** är en unik identifierare för appen. Konventionen är att använda formatet `https://<tenant-domain>/<app-name>`, till exempel: `https://contoso.onmicrosoft.com/my-first-aad-app`.

1. På sidan **Inställningar > Svars-URL:er** för appen lägger du till URL:en som lades till i Inloggnings-URL i steg 5 och välj **Spara**.
1. Skapa en hemlig nyckel med anvisningarna i steg 4 i avsnittet om [att lägga till autentiseringsuppgifter eller behörigheter för åtkomst till webb-API:er](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis).

   > [!IMPORTANT]
   > Kopiera programnyckelns värde. Det här är värdet för `clientSecret`, som du behöver för **steg 3** nedan. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>Steg 2: Gör nödvändiga förberedelser för katalogen

1. På kommandoraden ändrar du katalog till rotmappen, om du inte redan är där, och kör sedan följande kommandon:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

1. Du behöver även `passport-azure-ad`, så kör följande kommando:

    * `npm install passport-azure-ad`

Det installerar biblioteken som `passport-azure-ad` är beroende av.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Steg3: Konfigurera din app att använda passport-node-js-strategin

Här konfigurerar du Express att använda autentiseringsprotokollet OpenID Connect. Passport används för att göra olika saker, bland annat utfärda inloggnings- och utloggningsförfrågningar, hantera användarens session och hämta information om användaren.

1. Öppna `config.js`-filen i projektroten och ange sedan din apps konfigurationsvärden i `exports.creds`-avsnittet.

    * `clientID` är det **program-ID** som din app tilldelats på registreringsportalen.
    * `returnURL` är **svars-URL:en** som du har angett i portalen.
    * `clientSecret` är den hemlighet som du har genererat i portalen.

1. Öppna sedan `app.js`-filen i projektroten. Lägg därefter till följande anrop för att anropa `OIDCStrategy`-strategin som medföljer `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

1. Efter det använder du strategin vi precis refererade till för att hantera inloggningsförfrågningar.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
   Passport använder ett liknade mönster för alla sina strategier (Twitter, Facebook osv.) som alla strategiskrivare följer. Om du tittar på strategin ser du att vi skickar den en funktion som har en token och en ”done” som parametrarna. Strategin kommer tillbaka till oss när den har gjort sitt jobb. Sedan vill vi lagra användaren och token så att vi inte behöver fråga efter den igen.

   > [!IMPORTANT]
   > Den föregående koden tar alla användare som autentiserar på vår server. Detta kallas autoregistrering. Vi rekommenderar att du inte låter någon autentisera på en produktionsserver utan att registrera sig via en process som du själv väljer. Det är oftast det mönster du ser i konsumentappar, där du kan registrera dig med Facebook men du blir sedan uppmanad att ange ytterligare information. Om det här inte vore en exempelapp kunde vi ha extraherat användarens e-postadress från tokenobjektet som returneras och sedan uppmanat användaren att fylla i ytterligare information. Eftersom det här är en testserver lägger vi till dem i den minnesinterna databasen.

1. Lägg till metoder som gör att vi kan spåra de inloggade användarna som krävs av Passport. Dessa metoder omfattar serialisering och avserialisering av användarens information.

    ```JavaScript
    // Passport session setup. (Section 2)

    //   To support persistent sign-in sessions, Passport needs to be able to
    //   serialize users into the session and deserialize them out of the session. Typically,
    //   this is done simply by storing the user ID when serializing and finding
    //   the user by ID when deserializing.
    passport.serializeUser(function(user, done) {
        done(null, user.email);
    });

    passport.deserializeUser(function(id, done) {
        findByEmail(id, function (err, user) {
            done(err, user);
        });
    });

    // array to hold signed-in users
    var users = [];

    var findByEmail = function(email, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            log.info('we are using user: ', user);
            if (user.email === email) {
                return fn(null, user);
            }
        }
        return fn(null, null);
    };
    ```

1. Lägg till koden för att läsa in Express-motorn. Här använder vi standardmönstren /views och /routes som Express tillhandahåller.

    ```JavaScript
    // configure Express (section 2)

      var app = express();
      app.configure(function() {
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.logger());
      app.use(express.methodOverride());
      app.use(cookieParser());
      app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
      app.use(bodyParser.urlencoded({ extended : true }));
      // Initialize Passport!  Also use passport.session() middleware, to support
      // persistent login sessions (recommended).
      app.use(passport.initialize());
      app.use(passport.session());
      app.use(app.router);
      app.use(express.static(__dirname + '/../../public'));
    });
    ```

1. Lägg slutligen till vägarna som lämnar över själva inloggningsförfrågningarna till `passport-azure-ad`-motorn:

    ```JavaScript
    // Our Auth routes (section 3)

    // GET /auth/openid
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. The first step in OpenID authentication involves redirecting
    //   the user to their OpenID provider. After authenticating, the OpenID
    //   provider redirects the user back to this application at
    //   /auth/openid/return.
    app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
        log.info('Authentication was called in the Sample');
        res.redirect('/');
    });

    // GET /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.get('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });

    // POST /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.post('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });
    ```

## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Steg 4: Använda Passport för att utfärda inloggnings- och utloggningsförfrågningar till Azure AD

Nu är din app korrekt konfigurerad för att kommunicera med slutpunkten med hjälp av autentiseringsprotokollet OpenID Connect. `passport-azure-ad` ansvarar helt för att utforma autentiseringsmeddelanden, verifiera token från Azure AD och upprätthålla användarsessioner. Allt som återstår är att ge dina användare ett sätt att logga in och logga ut, och samla in ytterligare information om de inloggade användarna.

1. Lägg till de förvalda inloggnings-, konto- och utloggningsmetoderna i `app.js`-filen:

    ```JavaScript
    //Routes (section 4)

    app.get('/', function(req, res){
      res.render('index', { user: req.user });
    });

    app.get('/account', ensureAuthenticated, function(req, res){
      res.render('account', { user: req.user });
    });

    app.get('/login',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('Login was called in the Sample');
        res.redirect('/');
    });

    app.get('/logout', function(req, res){
      req.logout();
      res.redirect('/');
    });
    ```

1. Granska dem i detalj:

    * `/`-vägen omdirigerar till vyn index.ejs och skickar användaren i begäran (om den finns).
    * `/account`-vägen *kontrollerar först att vi har autentiserats* (vi implementerar det i följande exempel) och skickar sedan användaren i begäran så att vi kan få ytterligare information om användaren.
    * `/login`-vägen anropar azuread-openidconnect-autentiseraren från `passport-azuread`. Om det inte lyckas omdirigeras användaren tillbaka till /login.
    * `/logout`-vägen anropar helt enkelt logout.ejs (och kommando), som rensar cookies och skickar tillbaka användaren till index.ejs.

1. För den sista delen av `app.js` lägger du till metoden **EnsureAuthenticated** som användas i `/account`, som visades tidigare.

    ```JavaScript
    // Simple route middleware to ensure user is authenticated. (section 4)

    //   Use this route middleware on any resource that needs to be protected. If
    //   the request is authenticated (typically via a persistent sign-in session),
    //   the request proceeds. Otherwise, the user is redirected to the
    //   sign-in page.
    function ensureAuthenticated(req, res, next) {
      if (req.isAuthenticated()) { return next(); }
      res.redirect('/login')
    }
    ```

1. Slutligen skapar du själva servern i `app.js`:

    ```JavaScript
    app.listen(3000);
    ```

## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Steg 5: Visa användaren på webbplatsen genom att skapa vyerna och vägarna i Express

Nu är `app.js` klar. Du behöver bara lägga till vägarna och vyerna som visar informationen vi får för användaren samt hantera `/logout`- och `/login`-vägarna som vi har skapats.

1. Skapa `/routes/index.js`-vägen under rotkatalogen.

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

1. Skapa `/routes/user.js`-vägen under rotkatalogen.

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

    Dessa skickar begäran till vyerna, inklusive användaren om den finns.

1. Skapa `/views/index.ejs`-vyn under rotkatalogen. Det här är en enkel sida som anropar våra inloggnings- och utloggningsmetoder och gör så att vi kan hämta kontoinformation. Observera att vi kan använda villkorligt `if (!user)` eftersom användaren som skickas genom i begäran är bevis på att vi har en inloggad användare.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

1. Skapa `/views/account.ejs`-vyn under rotkatalogen så att vi kan visa ytterligare information som `passport-azure-ad` har lagt till i användarbegäran.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
    <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

1. Lägg till en layout för att förbättra utseendet på sidan. Skapa `/views/layout.ejs`-vyn under rotkatalogen.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

## <a name="step-6-build-and-run-your-app"></a>Steg 6: Skapa och köra din app

1. Kör `node app.js` och gå sedan till `http://localhost:3000`.
1. Logga in med ett personligt Microsoft-konto eller ett arbets- eller skolkonto.

    Observera hur användarens identitet visas i /account-listan. Nu har du en webbapp som skyddas med branschstandardprotokoll som kan autentisera användare med både personliga och arbets-/skolkonton.

    Det färdiga exemplet (utan dina konfigurationsvärden) är tillgängligt som referens i form av en [ZIP-fil](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/master.zip). Du kan också klona det fån GitHub:

    ```git clone --branch master https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

## <a name="next-steps"></a>Nästa steg

Nu kan du gå vidare och prova andra scenarier:

> [!div class="nextstepaction"]
> [Skydda ett webb-API med Azure AD](quickstart-v1-nodejs-webapi.md)