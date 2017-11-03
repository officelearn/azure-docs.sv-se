---
title: Sessionen Management - verktyget Microsoft Threat modellering - Azure | Microsoft Docs
description: "ändringar för hot som exponeras i verktyget Modeling hot"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 56471d8ef68eacacb3ecebad5056d7e7a9f3ca40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-session-management--articles"></a>Säkerhet ram: Sessionshantering | Artiklar 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementera rätt logga ut med hjälp av ADAL metoder när du använder Azure AD](#logout-adal)</li></ul> |
| IoT-enhet | <ul><li>[Använd begränsad livslängd för genererade SaS-token](#finite-tokens)</li></ul> |
| **Azure dokumentet DB** | <ul><li>[Använda minsta livstid för token för den genererade resurs token](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementera rätt logga ut med WsFederation metoder när du använder AD FS](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[Implementera rätt logga ut när du använder Identity Server](#proper-logout)</li></ul> |
| **Webbprogram** | <ul><li>[Program som är tillgängliga via HTTPS måste använda säkra cookies](#https-secure-cookies)</li><li>[Alla HTTP-baserade program bör ange http endast för cookie-definition](#cookie-definition)</li><li>[Skyddar mot attacker webbplatser begära förfalskning (CSRF) för ASP.NET-webbsidor](#csrf-asp)</li><li>[Ställ in sessionen för inaktivitet livslängd](#inactivity-lifetime)</li><li>[Implementera rätt logga ut från programmet](#proper-app-logout)</li></ul> |
| **Webb-API** | <ul><li>[Skyddar mot attacker webbplatser begära förfalskning (CSRF) på ASP.NET Web API: er](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementera rätt logga ut med hjälp av ADAL metoder när du använder Azure AD

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Om programmet är beroende av åtkomst-token som utfärdas av Azure AD, anropa händelsehanteraren logga ut |

### <a name="example"></a>Exempel
```C#
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exempel
Det bör också förstöra användarsession genom att anropa metoden Session.Abandon(). Följande metod visar säker implementering av användaren logga ut:
```C#
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Använd begränsad livslängd för genererade SaS-token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | SaS-token genereras för att autentisera till Azure IoT-hubben ska ha en begränsad upphör att gälla. Använd SaS-token livslängd som möjligt att begränsa den tid som de kan återupprepas om token komprometteras.|

## <a id="resource-tokens"></a>Använda minsta livstid för token för den genererade resurs token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure dokumentet DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Minska tidsintervallet för resurs-token till ett minsta värde som krävs. Resurs-token har ett giltigt timespan standardvärdet 1 timme.|

## <a id="wsfederation-logout"></a>Implementera rätt logga ut med WsFederation metoder när du använder AD FS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | ADFS | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Om programmet är beroende av STS-token som utfärdas av AD FS, anropa logga ut händelsehanteraren WSFederationAuthenticationModule.FederatedSignOut() metod för att logga ut användaren. Även den aktuella sessionen förstöras och token-värde för session ska återställa och undanröjda.|

### <a name="example"></a>Exempel
```C#
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Implementera rätt logga ut när du använder Identity Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identity Server | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Federerad IdentityServer3 logga ut](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Steg** | IdentityServer stöder möjligheten att federera med externa identitetsleverantörer. När en användare loggar ut från en överordnad identitetsleverantör, kan beroende på det protokoll som används, det vara möjligt att få ett meddelande när användaren loggar ut. Det gör IdentityServer att meddela sina klienter så att de kan också logga ut användaren. I referensavsnittet för implementeringen finns i dokumentationen.|

## <a id="https-secure-cookies"></a>Program som är tillgängliga via HTTPS måste använda säkra cookies

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [httpCookies Element (inställningsschema för ASP.NET)](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure egenskapen](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Steg** | Cookies är endast tillgänglig för domänen som de har begränsats normalt. Definitionen för ”domain” inkluderar tyvärr inte protokollet så att cookies som skapas via HTTPS är tillgänglig via HTTP. Attributet ”secure” indikerar att webbläsaren som cookien ska endast göras tillgängliga via HTTPS. Kontrollera att alla cookies över HTTPS används den **säker** attribut. Kravet kan tillämpas i web.config-filen genom att attributet requireSSL till true. Det är att föredra eftersom den kommer att verkställa den **säker** attribut för alla aktuella och framtida cookies utan att behöva göra några ytterligare kodändringar.|

### <a name="example"></a>Exempel
```C#
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Inställningen tillämpas även om HTTP används för att få åtkomst till programmet. Om HTTP används för att få åtkomst till programmet, bryts inställningen för programmet, eftersom cookies anges med attributet secure och webbläsaren kommer inte att skicka dem till programmet.

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär, MVC5 |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | Saknas  |
| **Steg** | När webbprogrammet är den förlitande parten och IdP är AD FS-servern, secure token FedAuth-attributet kan konfigureras genom att ange requireSSL till True i `system.identityModel.services` avsnitt i web.config:|

### <a name="example"></a>Exempel
```C#
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Alla HTTP-baserade program bör ange http endast för cookie-definition

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Säker Cookie-attribut](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Steg** | Om du vill minska risken för avslöjande av information med en attack med globala webbplatsskript (XSS), ett nytt attribut - httpOnly - introducerades så att cookies som stöds av alla större webbläsare. Attributet anger en cookie inte är tillgänglig via skript. Ett webbprogram minskar risken att känslig information i cookien stulen via skript och skickas till en angripares webbplats med hjälp av HttpOnly cookies. |

### <a name="example"></a>Exempel
Alla HTTP-baserade program som använder cookies ska ange HttpOnly i cookie-definition genom att implementera följande konfiguration i web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Web Forms |
| **Attribut**              | Saknas  |
| **Referenser**              | [Egenskapen FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Steg** | Egenskapsvärdet RequireSSL anges i konfigurationsfilen för ett ASP.NET-program genom att använda attributet requireSSL för konfigurationselementet. Du kan ange i Web.config-filen för ASP.NET-program om SSL (Secure Sockets Layer) måste returnera cookien för formulär för autentisering till servern genom att ange requireSSL-attributet.|

### <a name="example"></a>Exempel 
Följande exempel anger attributet requireSSL i Web.config-filen.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [Windows Identity Foundation (WIF) konfiguration – del II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Steg** | Om du vill ange httpOnly attribut för FedAuth cookies ska hideFromCsript attributvärdet anges till True. |

### <a name="example"></a>Exempel
Följande konfiguration visar korrekt konfiguration:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>Skyddar mot attacker webbplatser begära förfalskning (CSRF) för ASP.NET-webbsidor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Begäran förfalskning (CSRF eller XSRF) är en typ av angrepp där angriparen kan utföra åtgärder i säkerhetskontexten för en annan användare upprättad session på en webbplats. Målet är att ändra eller ta bort innehåll, om den aktuella webbplatsen använder uteslutande sessionscookies att autentisera tog emot begäran. En angripare kan utnyttja detta problem genom att få en annan användare webbläsaren att läsa in en URL med ett kommando från en sårbar plats där användaren är redan inloggad. Det finns många sätt för en angripare att göra det, som värd för en annan webbplats som läser in en resurs från sårbara servern eller att användaren klickar på en länk. Angrepp kan förhindras om servern skickar en ytterligare token till klienten kräver att klienten inkluderar den token i alla framtida förfrågningar och verifierar att alla kommande begäranden inkluderar en token som rör den aktuella sessionen, som med hjälp av ASP.NET AntiForgeryToken eller ViewState. |

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [XSRF/CSRF förebyggande i ASP.NET MVC och webbsidor](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Steg** | Anti-CSRF och formulär för ASP.NET MVC - använder den `AntiForgeryToken` hjälpmetod på vyer; placera en `Html.AntiForgeryToken()` i formuläret, t.ex.|

### <a name="example"></a>Exempel
```C#
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Exempel
```C#
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
Samtidigt ger Html.AntiForgeryToken() användaren en cookie som kallas __RequestVerificationToken med samma värde som slumpmässiga dolda värdet som visas ovan. Lägg sedan för att verifiera en inkommande formuläret post till filtret [ValidateAntiForgeryToken] till målmetoden för åtgärden. Exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Auktoriseringsfilter som kontrollerar att:
* Inkommande begäran har en cookie som kallas __RequestVerificationToken
* Inkommande begäran har en `Request.Form` post med namnet __RequestVerificationToken
* Dessa cookies och `Request.Form` värden matchar förutsatt att alla är korrekt, begäran går igenom som vanligt. Men om det inte finns sedan ett auktoriseringsfel med meddelandet ”en obligatorisk antiförfalskningstoken har inte angetts eller var ogiltig”. 

### <a name="example"></a>Exempel
Skydd mot CSRF och AJAX: formulär-token kan vara ett problem för AJAX-begäranden eftersom en AJAX-begäran kan skicka JSON-data, inte data i HTML-formulär. En lösning är att skicka tokens i ett anpassat HTTP-huvud. Följande kod använder Razor-syntaxen för att generera token och lägger sedan till token i en AJAX-begäran. 
```C#
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exempel
När du bearbetar begäran extrahera token från begärandehuvudet. Sedan anropa metoden AntiForgery.Validate för att validera token. Validate-metoden genereras ett undantag om token inte är giltiga.
```C#
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Web Forms |
| **Attribut**              | Saknas  |
| **Referenser**              | [Dra nytta av ASP.NET inbyggda funktioner för att slippa webbattacker](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Steg** | CSRF attacker i WebForm baserat program kan begränsas genom att ange ViewStateUserKey till en slumpmässig sträng som varierar för varje användare - användar-ID eller ännu bättre sessions-ID. För ett antal tekniska och är sessions-ID en mycket bättre anpassning eftersom en session-ID är oförutsägbart, timeout och varierar per användare.|

### <a name="example"></a>Exempel
Här är koden som du behöver i alla sidor:
```C#
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Ställ in sessionen för inaktivitet livslängd

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Egenskapen HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Steg** | Tidsgräns för session representerar händelsen inträffar när en användare inte utför någon åtgärd på en webbplats under ett intervall (definieras av webbserver). En händelse på serversidan, ändra status för användarsessionen till 'ogiltigt' (till exempel ”används inte längre”) och att webbservern att ta bort den (ta bort alla data som finns i den). Följande exempel anger attributet timeout-session till 15 minuter i Web.config-filen.|

### <a name="example"></a>Exempel
'''XML-koden <configuration> < system.web > <sessionState mode="InProc" cookieless="true" timeout="15" /> < /system.web ></configuration>
```

## <a id="threat-detection"></a>Enable Threat detection on Azure SQL
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Web Forms |
| **Attribut**              | Saknas  |
| **Referenser**              | [Element för formulär för autentisering (inställningsschema för ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Steg** | Ange tidsgränsen för cookie biljetten för formulär för autentisering till 15 minuter|

### <a name="example"></a>Exempel
''' XML-kod<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms, MVC5 |
| **Attributes**              | EnvironmentType - OnPrem |
| **References**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steps** | When the web application is Relying Party and ADFS is the STS, the lifetime of the authentication cookies - FedAuth tokens - can be set by the following configuration in web.config:|

### Example
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Exempel
Också begära ADFS utfärdat SAML-token livstid ska anges till 15 minuter, genom att köra följande powershell-kommando på AD FS-servern:
```C#
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementera rätt logga ut från programmet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Utföra rätt logga ut från programmet, när användaren trycker på Logga ut knappen. På Logga ut, bör program förstör användarens session, och även återställa och upphäver session cookie-värde, tillsammans med att återställa och också ångras autentisering cookie-värde. Dessutom när flera sessioner är knutna till en enskild användaridentitet, måste de gemensamt avslutas på serversidan på timeout eller logga ut. Kontrollera slutligen att logga ut funktionalitet är tillgänglig på varje sida. |

## <a id="csrf-api"></a>Skyddar mot attacker webbplatser begära förfalskning (CSRF) på ASP.NET Web API: er

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Begäran förfalskning (CSRF eller XSRF) är en typ av angrepp där angriparen kan utföra åtgärder i säkerhetskontexten för en annan användare upprättad session på en webbplats. Målet är att ändra eller ta bort innehåll, om den aktuella webbplatsen använder uteslutande sessionscookies att autentisera tog emot begäran. En angripare kan utnyttja detta problem genom att få en annan användare webbläsaren att läsa in en URL med ett kommando från en sårbar plats där användaren är redan inloggad. Det finns många sätt för en angripare att göra det, som värd för en annan webbplats som läser in en resurs från sårbara servern eller att användaren klickar på en länk. Angrepp kan förhindras om servern skickar en ytterligare token till klienten kräver att klienten inkluderar den token i alla framtida förfrågningar och verifierar att alla kommande begäranden inkluderar en token som rör den aktuella sessionen, som med hjälp av ASP.NET AntiForgeryToken eller ViewState. |

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Förhindra attacker med förfalskning (CSRF) av begäran i ASP.NET webb-API](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Steg** | Skydd mot CSRF och AJAX: formulär-token kan vara ett problem för AJAX-begäranden eftersom en AJAX-begäran kan skicka JSON-data, inte data i HTML-formulär. En lösning är att skicka tokens i ett anpassat HTTP-huvud. Följande kod använder Razor-syntaxen för att generera token och lägger sedan till token i en AJAX-begäran. |

### <a name="example"></a>Exempel
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Exempel
När du bearbetar begäran extrahera token från begärandehuvudet. Sedan anropa metoden AntiForgery.Validate för att validera token. Validate-metoden genereras ett undantag om token inte är giltiga.
```C#
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Exempel
Skydd mot CSRF och formulär för ASP.NET MVC - Hjälpmetoden AntiForgeryToken på vyer. Placera en Html.AntiForgeryToken() i formuläret, t.ex.
```C#
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exempel
Exemplet ovan kommer att skrivas ut ungefär så här:
```C#
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
Samtidigt ger Html.AntiForgeryToken() användaren en cookie som kallas __RequestVerificationToken med samma värde som slumpmässiga dolda värdet som visas ovan. Lägg sedan för att verifiera en inkommande formuläret post till filtret [ValidateAntiForgeryToken] till målmetoden för åtgärden. Exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Auktoriseringsfilter som kontrollerar att:
* Inkommande begäran har en cookie som kallas __RequestVerificationToken
* Inkommande begäran har en `Request.Form` post med namnet __RequestVerificationToken
* Dessa cookies och `Request.Form` värden matchar förutsatt att alla är korrekt, begäran går igenom som vanligt. Men om det inte finns sedan ett auktoriseringsfel med meddelandet ”en obligatorisk antiförfalskningstoken har inte angetts eller var ogiltig”.

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Identitet Provider - ADFS, identitetsleverantör - Azure AD |
| **Referenser**              | [Skydda ett webb-API med enskilda konton och lokala inloggning i ASP.NET Web API 2.2](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Steg** | Om webb-API kan skyddas med OAuth 2.0 sedan förväntar sig ett ägartoken i Authorization-huvud för begäran och ger åtkomst till begäran om token är giltig. Till skillnad från cookie-baserad autentisering Anslut webbläsare inte ägar-token på begäran. Den begärande klienten måste koppla explicit ägartoken i huvudet i begäran. ASP.NET Web API: er skyddas med hjälp av OAuth 2.0, betraktas därför ägar-token som skydd mot attacker CSRF. Observera att om den MVC-delen av programmet använder formulärautentisering (d.v.s. använder cookies), skydd mot förfalskning token måste användas av MVC-webbapp. |

### <a name="example"></a>Exempel
Webb-API har informeras lita enbart på ägar-token och inte på cookies. Det kan göras med följande konfiguration i `WebApiConfig.Register` metod: '''C skarpa kod config. SuppressDefaultHostAuthentication(); Config. Filters.Add (nya HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```
The SuppressDefaultHostAuthentication method tells Web API to ignore any authentication that happens before the request reaches the Web API pipeline, either by IIS or by OWIN middleware. That way, we can restrict Web API to authenticate only using bearer tokens.