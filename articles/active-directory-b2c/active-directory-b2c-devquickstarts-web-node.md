---
title: "Lägga till inloggning till en Node.js-webbapp för Azure B2C | Microsoft Docs"
description: "Hur du skapar en Node.js-webbapp som loggar in användare med hjälp av en B2C-klient."
services: active-directory-b2c
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: xerners
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: a4d9394983539da52105bda6cf06273205f8b0ad
ms.lasthandoff: 04/18/2017


---

# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Lägga till inloggning till en Node.js-webbapp

**Passport** är ett mellanprogram för autentisering för Node.js. Modulbaserade Passport är mycket flexibelt och kan diskret installeras i alla Express-baserade webbappar eller Restify-webbappar. En omfattande uppsättning strategier stöder autentisering med användarnamn och lösenord, Facebook, Twitter och mycket mer. 

Vi har utvecklat en strategi för Azure Active Directory (Azure AD). Du installerar den här modulen och lägger sedan till Azure AD-plugin-programmet `passport-azure-ad`.

Om du vill göra det måste du:

1. Registrera ett program med hjälp av Azure AD.
2. Konfigurera din app att använda plugin-programmet `passport-azure-ad`.
3. Använda Passport för att utfärda inloggnings- och utloggningsförfrågningar till Azure AD.
4. Skriva ut användardata.

Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Om du vill kan du [ladda ned appens stomme som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). Du kan också klona stommen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Du hittar det färdiga programmet i slutet av dessa självstudier.

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient.  En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en B2C-katalog [skapar du en](active-directory-b2c-get-started.md) innan du fortsätter den här guiden.

## <a name="create-an-application"></a>Skapa ett program

Därefter måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som tjänsten behöver för att kommunicera säkert med din app. Både klientappen och webb-API:et representeras av ett enda **program-ID** eftersom de bildar en logisk app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

- Lägga till ett **webb-API** /**för webbappen** i programmet.
- Ange `http://localhost:3000/auth/openid/return` som en **Reply URL**. Den är standard-URL:en för det här kodexemplet.
- Skapa en **programhemlighet** för programmet och kopiera den. Du behöver den senare. Tänk på att det här värdet måste ha [ett XM-escape-tecken](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) innan du använder det.
- Kopiera **program-ID:t** som har tilldelats din app. Du behöver även det senare.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Skapa dina principer

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Den här appen innehåller tre identitetsupplevelser: registrering, inloggning och inloggning med Facebook. Du måste skapa den här principen för varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Tänk på följande när du skapar dina tre principer:

- Välj **visningsnamnet** och andra registreringsattribut i registreringsprincipen.
- Välj det **visningsnamn** och **objekt-ID** som programmet gör anspråk på i varje princip. Du kan också välja andra anspråk.
- Kopiera **namnet** på varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`.  Du behöver principnamnen senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina tre principer kan du börja bygga appen.

Tänk på att den här artikeln inte beskriver hur du använder de principer som du nyss har skapat. Börja med [självstudierna för att komma igång med .NET-webbappar](active-directory-b2c-devquickstarts-web-dotnet.md) om du vill veta mer om hur principer fungerar i Azure AD B2C.

## <a name="add-prerequisites-to-your-directory"></a>Gör nödvändiga förberedelser för katalogen

Från kommandoraden byter du katalog till din rotmapp om du inte redan är där. Kör följande kommandon:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Dessutom använde vi `passport-azure-ad` för vår förhandsgranskning i stommen i Snabbstart.

- `npm install passport-azure-ad`

Detta installerar biblioteken som `passport-azure-ad` är beroende av.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Konfigurera din app att använda Passport-Node.js-strategin
Konfigurera Express-mellanprogrammet att använda autentiseringsprotokollet OpenID Connect. Passport används för att utfärda inloggnings- och utloggningsförfrågningar, hantera användarsessioner, få information om användare och mycket mer.

Öppna `config.js`-filen i projektroten och ange din apps konfigurationsvärden i `exports.creds`-avsnittet.
- `clientID`: Det **Program-ID** som din app tilldelats på registreringsportalen.
- `returnURL`: Det **Omdirigerings-URI** som du angav i portalen.
- `tenantName`: Klientnamnet för din app, till exempel **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Öppna `app.js`-filen i projektroten. Lägg till följande anrop för att anropa `OIDCStrategy`-strategin som medföljer `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Använd strategin som du precis refererade till för att hantera inloggningsförfrågningar.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
Passport använder ett liknande mönster för alla dess strategier (inklusive Twitter och Facebook). Alla strategigenererare följer detta mönster. När du tittar på strategin kan du se att du skickar ett `function()` som har en token och en `done` som parametrar. Strategin kommer tillbaka när den har utfört allt sitt arbete. Lagra användaren och token så att du inte behöver fråga efter den igen.

> [!IMPORTANT]
Föregående kod körs på alla användare som servern autentiserar. Det här är automatisk registrering. När du använder produktionsservrar vill du inte släppa in användare om de inte har genomgått en registreringsprocess som du har konfigurerat. Det här mönstret är vanligt i konsumentappar. Dessa appar låter användaren registrera sig med hjälp av Facebook, men uppmanar sedan användaren att fylla i ytterligare information. Om vårt program inte var ett exempelprogram skulle vi kunna extrahera en e-postadress från tokenobjektet som returneras och sedan be användaren att fylla i ytterligare information. Eftersom det här är en testserver lägger vi bara till användare i den minnesinterna databasen.

Lägg till metoder som gör att du kan hålla reda på användare som har loggat in, på det sätt som krävs av Passport. Serialisering och avserialisering av användarinformation är ett par exempel på den här typen av metoder:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Lägg till koden för att läsa in Express-motorn. Nedan kan du se att vi använder `/views`- och `/routes`-standardmönstret som Express tillhandahåller.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Lägg till `POST`-dirigeringskommandona som lämnar över själva inloggningsförfrågningarna till `passport-azure-ad`-motorn:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Använda Passport för att utfärda inloggnings- och utloggningsförfrågningar till Azure AD

Nu är din app korrekt konfigurerad för att kommunicera med v2.0-slutpunkten med hjälp av autentiseringsprotokollet OpenID Connect. `passport-azure-ad` ansvarar för att utforma autentiseringsmeddelanden, verifiera token från Azure AD och upprätthålla användarsessioner. Allt som återstår är att ge användarna en metod för att logga in och logga ut och att samla in ytterligare information om användare som har loggat in.

Börja med att lägga till de förvalda inloggnings-, konto- och utloggningsmetoderna i `app.js`-filen:

```JavaScript

//Routes (Section 4)

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

Granska dessa metoder i detalj:
- `/`-kommandot omdirigerar till `index.ejs`-vyn genom att skicka användaren i begäran (om den finns).
- `/account`-kommandot kontrollerar först att du har autentiserats (du ser implementeringen för detta nedan). Därefter skickas användaren med i begäran så att du kan få mer information om användaren.
- `/login`-kommandot anropar `azuread-openidconnect`-autentiseraren från `passport-azure-ad`. Om det inte lyckas omdirigerar kommandot användaren tillbaka till `/login`.
- `/logout` anropar bara `logout.ejs` (och dess dirigeringskommando). Detta kommando rensar cookies och skickar tillbaka användaren till `index.ejs`.


För den sista delen av `app.js` lägger du till `EnsureAuthenticated`-metoden som används i `/account`-vägen.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Slutligen skapar du själva servern i `app.js`.

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Skapa vyerna och vägarna i Express för att anropa dina principer

Nu är din `app.js` klar. Du behöver bara lägga till vägarna och vyerna som gör att du kan anropa inloggnings- och registreringsprinciperna. Dessa hanterar även de `/logout`- och `/login`-vägar som du skapade.

Skapa `/routes/index.js`-vägen under rotkatalogen.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Skapa `/routes/user.js`-vägen under rotkatalogen.

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Dessa enkla vägar skickar vidare förfrågningar till dina vyer. De innehåller användaren, om en sådan finns.

Skapa `/views/index.ejs`-vyn under rotkatalogen. Det här är en enkel sida som anropar principer för in- och utloggning. Du kan också använda den för att hämta kontoinformation. Notera att du kan använda det villkorliga `if (!user)` som användaren som skickas med i begäran för att bevisa att användaren är inloggad.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Skapa `/views/account.ejs`-vyn under rotkatalogen så att du kan visa ytterligare information som `passport-azure-ad` har lagt till i användarbegäran.

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
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Nu kan du skapa och köra din app.

Kör `node app.js` och gå till `http://localhost:3000`


Registrera dig eller logga in i appen med din e-postadress eller via Facebook. Logga ut och logga in igen som en annan användare.

##<a name="next-steps"></a>Nästa steg

Det färdiga exemplet (utan dina konfigurationsvärden) är tillgängligt som referens i form av en [ZIP-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Du kan också klona det från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Nu kan du gå vidare till mer avancerade avsnitt. Du kan prova följande:

[Skydda ett webb-API med hjälp av B2C-modellen i Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->

