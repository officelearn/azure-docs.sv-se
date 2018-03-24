---
title: Azure AD-Node.js-webbapp komma igång | Microsoft Docs
description: Lär dig hur du skapar en Node.js Express MVC-webbapp som kan integreras med Azure AD för inloggning.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 477056581b847693ae115f94c2f6466036add765
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Azure AD-Node.js-webbapp komma igång
Här kan vi använda Passport:

* Logga in användaren i appen med Azure Active Directory (AD Azure).
* Visa information om användaren.
* Logga ut från appen användaren.

Passport är ett mellanprogram för autentisering för Node.js. Flexibel och modulära, Passport diskret kan släppas till alla Express-baserade eller restify-webbappar. En omfattande uppsättning strategier stöder autentisering som använder ett användarnamn och lösenord, Facebook, Twitter och mycket mer. Vi har utvecklat en strategi för Microsoft Azure Active Directory. Vi installerar den här modulen och lägger sedan till Microsoft Azure Active Directory `passport-azure-ad` plugin-programmet.

Om du vill göra det, gör du följande:

1. Registrera en app.
2. Konfigurera din app att använda den `passport-azure-ad` strategi.
3. Använda Passport för att utfärda inloggnings- och utloggningsförfrågningar till Azure AD.
4. Skriva ut data om användaren.

Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Om du vill följa med kan du [ladda ned appens stomme som en .zip-fil](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) eller klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Det färdiga programmet har angetts i slutet av den här kursen samt.

## <a name="step-1-register-an-app"></a>Steg 1: Registrera en app
1. Logga in på [Azure Portal](https://portal.azure.com).

2. I menyn överst på sidan, Välj ditt konto. Under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.

3. Välj **alla tjänster** på menyn till vänster på skärmen och väljer sedan **Azure Active Directory**.

4. Välj **App registreringar**, och välj sedan **Lägg till**.

5. Följ anvisningarna för att skapa en **webbprogram** och/eller **WebAPI**.
  * Den **namn** beskriver ditt program till användare av programmet.

  * Den **inloggnings-URL** är den grundläggande Webbadressen för din app.  Den stommen standardvärdet är `http://localhost:3000/auth/openid/return`.

6. När du registrerar tilldelar Azure AD appen ett unikt-ID. Du behöver det här värdet i de följande avsnitten så kopiera den från appen på sidan.
7. Från den **inställningar** -> **egenskaper** för programmet, uppdatera App-ID-URI. Den **App-ID URI** är en unik identifierare för programmet. Konventionen är att använda formatet `https://<tenant-domain>/<app-name>`, till exempel: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Steg 2: Lägg till förutsättningar för din katalog
1. Från kommandoraden ändrar du katalog till din rotmapp om du inte redan där, och kör sedan följande kommandon:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Dessutom behöver du `passport-azure-ad`:
    * `npm install passport-azure-ad`

Detta installerar biblioteken som `passport-azure-ad` beror på.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Steg 3: Konfigurera din app att använda passport-nod-js-strategi
Här kan konfigurera vi Express för att använda autentiseringsprotokollet OpenID Connect.  Passport används för att göra olika saker, bland annat problemet inloggning och utloggning förfrågningar, hantera användarens session och få information om användaren.

1. Öppna först den `config.js` filen i roten av projektet och sedan ange din Apps konfigurationsvärden i den `exports.creds` avsnitt.

  * Den `clientID` är den **program-Id** som har tilldelats din app i portalen för registrering.

  * Den `returnURL` är den **omdirigerings-Uri** som du angav på portalen.

  * Den `clientSecret` är den hemlighet som du genererade på portalen.

2. Därefter öppnar den `app.js` filen i roten av projektet. Lägg till följande anrop för att anropa den `OIDCStrategy` strategin som medföljer `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Efter det att använda strategin vi precis refererade till för att hantera vår inloggning begäranden.

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
Passport använder ett liknande mönster för alla dess strategier (Twitter, Facebook och så vidare) som alla strategiskrivare följer. Tittar på strategin ser du att vi skickar en funktion som har en token och klart som parametrar. Strategin kommer tillbaka till oss när den har sitt arbete. Sedan vill vi lagra användaren och token så att vi inte behöver fråga efter den igen.

> [!IMPORTANT]
Föregående kod tar alla användare som inträffar att autentisera till vår server. Detta kallas för automatisk registrering. Vi rekommenderar att du inte låta alla autentisera till en produktionsserver utan att behöva dem registreras via en process som du väljer. Detta är oftast det mönster du ser i konsumentappar, så att du kan registrera med Facebook men be dig att ange ytterligare information. Om det inte var ett exempelprogram kunnat vi extrahera användarens e-postadress från tokenobjektet som returneras och sedan bett användaren att fylla i ytterligare information. Eftersom detta är en testserver vi lägga till dem i InMemory-databasen.


4. Nu ska vi lägga till de metoder som gör det möjligt för oss att spåra inloggade användare som krävs av Passport. De här metoderna omfattar serialisering och avserialisering av användarens information.

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

5.  Nu ska vi lägga till kod för att läsa in Express-motorn. Vi använder här standard /views /routes mönster som Express tillhandahåller.

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

6. Slutligen ska vi lägga till dirigeringskommandona som lämnar de faktiska inloggning förfrågningar till den `passport-azure-ad` motorn:

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
Appen har nu konfigurerats korrekt för att kommunicera med slutpunkten med hjälp av autentiseringsprotokollet OpenID Connect.  `passport-azure-ad` har tagit hand om alla detaljer om utforma autentiseringsmeddelanden, verifiera token från Azure AD och upprätthålla användarsessioner. Allt som återstår är ge användarna ett sätt att logga in och logga ut och samla in ytterligare information om de inloggade användarna.

1. Först ska vi lägga till den standard, logga in, konto och utloggningsmetoderna i vår `app.js` fil:

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

2.  Nu ska vi se dessa i detalj:

  * Den `/`kommandot omdirigerar till vyn index.ejs skicka användaren i begäran (om den finns).
  * Den `/account` vidarebefordra först *garanterar vi autentiseras* (vi implementera som i följande exempel), och därefter skickas användaren i begäran så att vi kan få ytterligare information om användaren.
  * Den `/login` väg anropar våra azuread openidconnect autentiseraren från `passport-azuread`. Om som inte lyckas omdirigeras användaren till /login.
  * Den `/logout` kommandot bara anropar logout.ejs (och väg) rensar cookies och returnerar sedan användaren till index.ejs.

3. För den sista delen av `app.js`, ska vi lägga till den **EnsureAuthenticated** metod som används i `/account`, enligt tidigare.

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

4. Slutligen ska vi skapar du själva servern i `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Steg 5: Om du vill visa våra användare på webbplatsen, skapa vyerna och vägarna i Express
Nu `app.js` är klar. Vi behöver bara lägga till vägarna och vyerna som visar den information som vi får användaren som hanterar den `/logout` och `/login` vägar som vi skapade.

1. Skapa `/routes/index.js`-vägen under rotkatalogen.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Skapa `/routes/user.js`-vägen under rotkatalogen.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 Dessa skicka vidare förfrågan till vår vyer, inklusive användaren om den finns.

3. Skapa `/views/index.ejs`-vyn under rotkatalogen. Det här är en enkel sida som anropar våra logga in och logga ut metoder och ger oss möjlighet att hämta kontoinformation. Observera att du kan använda villkorliga `if (!user)` som användaren som skickas via i begäran är bevis som vi har en inloggad användare.

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

4. Skapa den `/views/account.ejs` vyn under rotkatalogen så att vi kan visa ytterligare information som `passport-azuread` har satt i användarens begäran.

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
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Vi behöver kontrollera sökningen bra genom att lägga till en layout. Skapa den ' / views/layout.ejs' vyn under rotkatalogen.

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

## <a name="next-steps"></a>Nästa steg
Slutligen skapar och kör din app. Kör `node app.js`, gå sedan till `http://localhost:3000`.

Logga in med ett personligt microsoftkonto eller ett konto för arbetet eller skolan och hur användarens identitet visas i listan över /account. Nu har du en webbapp som skyddas med standardprotokollen som kan autentisera användare med både sina personliga och arbete/skola konton.

Det färdiga exemplet (utan dina konfigurationsvärden) är tillgängligt som referens i form av en [ZIP-fil](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Du kan också klona det från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Du kan nu gå vidare till mer avancerade avsnitt. Du kanske vill prova:

[Skydda ett webb-API med Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
