---
title: Azure Active Directory v2.0 Node.js web app inloggning | Microsoft Docs
description: Lär dig hur du skapar en Node.js-webbapp som loggar in användare med hjälp av både ett personligt microsoftkonto och ett arbets- eller skolkonto konto.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb16502ca255bf99c3780ff3975b6ca7f5a79993
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Lägga till inloggning till en Node.js-webbapp

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner fungerar med v2.0-slutpunkten. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten eller v1.0 slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
>

I den här självstudiekursen kommer vi använda [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) att göra följande:

* Logga in användaren med hjälp av Azure Active Directory (Azure AD) och v2.0-slutpunkten i en webbapp.
* Visa information om användaren.
* Logga ut från appen användaren.

[Passport](http://passportjs.org/) är ett mellanprogram för autentisering för Node.js. Flexibel och modulära, Passport kan släppas diskret in i alla Express-baserade eller restify-webbappar. I Passport, en omfattande uppsättning strategier stöder autentisering med ett användarnamn och lösenord, Facebook, Twitter eller andra alternativ. Vi har utvecklat en strategi för Azure AD. I den här artikeln vi hur du kan installera modulen och Lägg sedan till Azure AD **passport-azure-ad** plugin-programmet.

## <a name="download"></a>Ladda ned
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Om du vill följa kursen, kan du [ladda ned appens stomme som en .zip-fil](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) eller klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Du kan också få det färdiga programmet i slutet av den här kursen.

## <a name="1-register-an-app"></a>1: registrera en app
Skapa en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa [dessa detaljerade steg](active-directory-v2-app-registration.md) att registrera en app. Se till att du:

* Kopiera den **program-Id** tilldelats din app. Du behöver den för den här självstudiekursen.
* Lägg till den **Web** plattform för din app.
* Lägg till följande URL-adress som konfigurerats i exemplet `http://localhost:3000/auth/openid/return` som den **omdirigerings-URI**.

## <a name="2-prerequisities-to-run-the-sample"></a>2: Prerequisities att köra exemplet

Behöver du ha installerat för att skapa och köra det här exemplet Node.js. Du kan installera Node.js från http://nodejs.org/.

Ändra kataloger för att gå till din rotmapp om du inte redan har det i en kommandotolk.
Kör följande kommando för att installera de nödvändiga nod-moduler som anges i den `package.json` filen:

`npm install`

Detta installerar biblioteken även som `passport-azure-ad` använder.

## <a name="3-set-up-your-app-to-use-passport-azure-ad"></a>3: konfigurera din app att använda passport-azure-ad
Konfigurera Express-mellanprogrammet att använda autentiseringsprotokollet OpenID Connect. Du kan använda Passport för att utfärda inloggnings- och utloggningsförfrågningar, hantera användarens session och få information om användare, bland annat.

1.  Öppna filen config.js i roten av projektet. I den `exports.creds` ange din Apps konfigurationsvärden.

  * `clientID`: **Program-Id** som har tilldelats din app i portalen.
  * `returnURL`: **Omdirigerings-URI** som du angav på portalen.
  * `clientSecret`: Den hemlighet som du genererade på portalen.

2.  Öppna filen app.js i roten av projektet. Att anropa OIDCStrategy som medföljer `passport-azure-ad`, Lägg till följande anrop:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Använd strategin som du precis refererade till för att hantera dina inloggning begäranden:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
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

Passport använder ett liknande mönster för alla sina strategier (Twitter, Facebook och så vidare). Alla strategigenererare följer mönstret. Skicka en strategi för en `function()` som använder en token och `done` som parametrar. Strategin som returneras när den gör allt arbete. Lagra användaren och token så att du inte behöver fråga efter den igen.

  > [!IMPORTANT]
  > Föregående kod tar alla användare som kan autentisera till servern. Detta kallas för automatisk registrering. På en produktionsserver skulle du vill låta alla i utan att behöva dem gå igenom den registreringsprocess som du väljer. Detta är oftast det mönster som du ser i konsumentappar. Appen kan tillåta dig att registrera med Facebook, men sedan du ombedd att ange ytterligare information. Om du inte använde ett kommandoradsprogram för den här självstudiekursen, kan du extrahera den e-postadressen från tokenobjektet som returneras. Sedan kan du be användaren att ange ytterligare information. Eftersom detta är en testserver kan du lägga till användaren direkt till den minnesintern databasen.
  >
  >

4.  Lägg till metoder som används för att hålla reda på användare som är inloggad, vilket krävs av Passport. Detta inkluderar serialisering och avserialisering av användarens information:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  Lägg till kod som läser in Express-motorn. Du använder standard-/views och /routes mönster som Express tillhandahåller:

  ```JavaScript

  // Set up Express (section 2)

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

6.  Lägg till POST dirigerar att leverera de faktiska inloggning förfrågningar till den `passport-azure-ad` motorn:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-add-sign-in-and-sign-out-requests-to-azure-ad"></a>4: lägga till inloggning och utloggningsförfrågningar till Azure AD
Appen har konfigurerats för att kommunicera med v2.0-slutpunkten med hjälp av autentiseringsprotokollet OpenID Connect. Den `passport-azure-ad` strategi tar hand om alla detaljer om utforma autentiseringsmeddelanden, verifiera token från Azure AD och upprätthålla användarsessionen. Återstår gör är att ge användarna ett sätt att logga in och logga ut och för att samla in mer information om den användare som har loggat in.

1.  Lägg till den **standard**, **inloggning**, **konto**, och **logga ut** metoder i filen app.js:

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
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Här följer information:

    * Den `/` kommandot omdirigerar till vyn index.ejs. Passerar användaren i begäran (om den finns).
    * Den `/account` vidarebefordra först *innebär att autentiseras* (du implementera som i följande kod). Sedan, skickas användaren i begäran. Detta är så att du kan få mer information om användaren.
    * Den `/login` kommandot anropar din `azuread-openidconnect` autentiseraren från `passport-azuread`. Om inte fungerar som den dirigerar användaren till `/login`.
    * Den `/logout` vägen anropar logout.ejs visa (och väg). Detta tar bort cookies och returnerar sedan användaren till index.ejs.

2.  Lägg till den **EnsureAuthenticated** metod som du använde tidigare i `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  I app.js, skapar du servern:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express"></a>5: skapa vyerna och vägarna i Express
Lägga till vägarna och vyerna som visar information för användaren. Vägarna och vyerna hanterar även de `/logout` och `/login` vägar som du skapade.

1. Skapa i rotkatalogen på `/routes/index.js` väg.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  Skapa i rotkatalogen på `/routes/user.js` väg.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` och `/routes/user.js` är enkla vägar skickar vidare begäran i vyer, inklusive användare, om sådan finns.

3.  Skapa i rotkatalogen på `/views/index.ejs` vyn. Den här sidan anropar din **inloggning** och **logga ut** metoder. Du också använda den `/views/index.ejs` vyn för att avbilda kontoinformation. Du kan använda villkorliga `if (!user)` som användaren som skickas via i begäran. Det finns bevis att du har en inloggad användare.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  Skapa i rotkatalogen på `/views/account.ejs` vyn. Den `/views/account.ejs` vy kan du visa ytterligare information som `passport-azuread` placerar i användarens begäran.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Lägg till en layout. Skapa i rotkatalogen på `/views/layout.ejs` vyn.

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
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```


  ## <a name="6-build-and-run-your-app"></a>6: skapa och kör din app
  1. Om du vill skapa och kör din app, kör du följande kommando från rotkatalogen appar.

  `node app.js`

   Gå sedan till `http://localhost:3000` i webbläsaren.

  2. Logga in med ett personligt microsoftkonto eller ett arbets- eller skolkonto konto. Observera att användarens identitet avspeglas i den `/account` vägen.

Nu har du en webbapp som skyddas med hjälp av standardprotokollen. Du kan autentisera användare i din app genom att använda sina personliga och arbetet eller skolan konton.

## <a name="next-steps"></a>Nästa steg
Referens tillhandahålls det slutförda exemplet (utan dina konfigurationsvärden) som [en .zip-fil](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Du kan också klona det från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Därefter kan du gå vidare till mer avancerade avsnitt. Du kanske vill prova:

[Skydda ett Node.js-webb-API med hjälp av v2.0-slutpunkten](active-directory-v2-devquickstarts-node-api.md)

Här följer några ytterligare resurser:

* [Utvecklarhandbok för Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Stacken spill ”azure-active-directory” tagg](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du loggar som ska meddelas när säkerhetsincidenter. På den [Microsoft tekniska säkerhetsmeddelanden](https://technet.microsoft.com/security/dd252948) kan prenumerera på rekommendationerna säkerhetsaviseringar.
