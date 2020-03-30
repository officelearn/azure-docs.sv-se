---
title: Sessionshantering – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
description: mildrande åtgärder för hot som exponeras i hotmodelleringsverktyget
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161507"
---
# <a name="security-frame-session-management"></a>Säkerhetsram: Sessionshantering
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementera korrekt utloggning med ADAL-metoder när du använder Azure AD](#logout-adal)</li></ul> |
| IoT-enhet | <ul><li>[Använd finita livstider för genererade SaS-token](#finite-tokens)</li></ul> |
| **Azure-dokument DB** | <ul><li>[Använd minsta tokenlivstid för genererade resurstoken](#resource-tokens)</li></ul> |
| **Adfs** | <ul><li>[Implementera korrekt utloggning med WsFederation-metoder när du använder ADFS](#wsfederation-logout)</li></ul> |
| **Identitetsserver** | <ul><li>[Implementera korrekt utloggning när du använder Identity Server](#proper-logout)</li></ul> |
| **Webbprogram** | <ul><li>[Program som är tillgängliga via HTTPS måste använda säkra cookies](#https-secure-cookies)</li><li>[Alla http-baserade program bör ange http endast för cookie definition](#cookie-definition)</li><li>[Mildra mot CSRF-attacker (Cross-Site Request Forgery) på ASP.NET webbsidor](#csrf-asp)</li><li>[Konfigurera session för inaktivitetslivslängd](#inactivity-lifetime)</li><li>[Implementera korrekt utloggning från programmet](#proper-app-logout)</li></ul> |
| **Webb-API** | <ul><li>[Mildra mot CSRF-attacker (Cross-Site Request Forgery) mot ASP.NET webb-API:er](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementera korrekt utloggning med ADAL-metoder när du använder Azure AD

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Om programmet är beroende av åtkomsttoken som utfärdats av Azure AD, bör händelsehanteraren för utloggning anropa |

### <a name="example"></a>Exempel
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exempel
Det bör också förstöra användarens session genom att ringa Session.Abandon() metod. Följande metod visar säker implementering av användarinloggning:
```csharp
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

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Använd finita livstider för genererade SaS-token

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | SaS-token som genereras för autentiserar till Azure IoT Hub bör ha en begränsad utgångsperiod. Håll SaS-tokenlivstiderna till ett minimum för att begränsa den tid de kan spelas upp om tokens äventyras.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Använd minsta tokenlivstid för genererade resurstoken

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure-dokument DB | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Minska tidsspannet för resurstoken till ett lägsta värde som krävs. Resurstokens har en standard giltig tidsspann på 1 timme.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementera korrekt utloggning med WsFederation-metoder när du använder ADFS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | ADFS | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Om programmet är beroende av STS-token som utfärdats av ADFS, bör utloggningshändelsehanteraren anropa WSFederationAuthenticationModule.FederatedSignOut() för att logga ut användaren. Även den aktuella sessionen bör förstöras och sessionstokenvärdet ska återställas och upphävas.|

### <a name="example"></a>Exempel
```csharp
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

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementera korrekt utloggning när du använder Identity Server

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IdentityServer3-Federerad ut logga ut](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Steg** | IdentityServer stöder möjligheten att federera med externa identitetsleverantörer. När en användare loggar ut från en uppströms identitetsprovider, beroende på vilket protokoll som används, kan det vara möjligt att få ett meddelande när användaren loggar ut. Det gör att IdentityServer kan meddela sina klienter så att de också kan logga ut användaren. Mer information finns i dokumentationen i referensavsnittet.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Program som är tillgängliga via HTTPS måste använda säkra cookies

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [httpCookies Element (ASP.NET Inställningar Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure Egenskap](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Steg** | Cookies är normalt endast tillgängliga för den domän som de omfattades av. Tyvärr innehåller definitionen av "domän" inte protokollet så cookies som skapas via HTTPS är tillgängliga via HTTP. Attributet "secure" anger för webbläsaren att cookien endast ska göras tillgänglig via HTTPS. Se till att alla cookies som ställs in via HTTPS använder det **säkra** attributet. Kravet kan tillämpas i filen web.config genom att ange attributet requireSSL till true. Det är den föredragna metoden eftersom det kommer att genomdriva det **säkra** attributet för alla nuvarande och framtida cookies utan att behöva göra några ytterligare kodändringar.|

### <a name="example"></a>Exempel
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Inställningen tillämpas även om HTTP används för att komma åt programmet. Om HTTP används för att komma åt programmet bryter inställningen programmet eftersom cookies är inställda med det säkra attributet och webbläsaren kommer inte att skicka tillbaka dem till programmet.

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär, MVC5 |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | När webbprogrammet är den förlitande parten och IdP är ADFS-server kan FedAuth-tokens säkra attribut `system.identityModel.services` konfigureras genom att ange requireSSL to True i avsnittet web.config:|

### <a name="example"></a>Exempel
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Alla http-baserade program bör ange http endast för cookie definition

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Attributet Säker cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Steg** | För att minska risken för utlämnande av information med en XSS-attack (Cross-site scripting) introducerades ett nytt attribut - httpOnly - på cookies och stöds av alla större webbläsare. Attributet anger att en cookie inte är tillgänglig via skript. Genom att använda HttpOnly-cookies minskar en webbapplikation möjligheten att känslig information som finns i cookien kan stjälas via skript och skickas till en angripares webbplats. |

### <a name="example"></a>Exempel
Alla HTTP-baserade program som använder cookies bör ange HttpOnly i cookie-definitionen, genom att implementera följande konfiguration i web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [FormsAuthentication.requiressl-egenskap](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Steg** | Egenskapsvärdet RequireSSL anges i konfigurationsfilen för ett ASP.NET program med hjälp av attributet requireSSL för konfigurationselementet. Du kan ange i web.config-filen för ditt ASP.NET program om SSL (Secure Sockets Layer) krävs för att returnera formulärautentiseringscookien till servern genom att ange attributet requireSSL.|

### <a name="example"></a>Exempel 
I följande kodexempel anges attributet requireSSL i filen Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5 (PÅ ANDRA) |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [Wif-konfiguration (Windows Identity Foundation) – del II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Steg** | Om du vill ange httpOnly-attribut för FedAuth-cookies bör attributet HideFromCsript anges till True. |

### <a name="example"></a>Exempel
Följande konfiguration visar rätt konfiguration:
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

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Mildra mot CSRF-attacker (Cross-Site Request Forgery) på ASP.NET webbsidor

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Förfalskning av begäran på flera webbplatser (CSRF eller XSRF) är en typ av attack där en angripare kan utföra åtgärder i säkerhetskontexten för en annan användares etablerade session på en webbplats. Målet är att ändra eller ta bort innehåll, om den riktade webbplatsen enbart förlitar sig på sessionscookies för att autentisera mottagen begäran. En angripare kan utnyttja det här säkerhetsproblemet genom att hämta en annan användares webbläsare för att läsa in en URL med ett kommando från en sårbar plats där användaren redan är inloggad. Det finns många sätt för en angripare att göra det, till exempel genom att vara värd för en annan webbplats som läser in en resurs från den sårbara servern, eller för att få användaren att klicka på en länk. Attacken kan förhindras om servern skickar ytterligare en token till klienten, kräver att klienten inkluderar den token i alla framtida begäranden och verifierar att alla framtida begäranden innehåller en token som hör till den aktuella sessionen, till exempel genom att använda ASP.NET AntiForgeryToken eller ViewState. |

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [XSRF/CSRF Prevention i ASP.NET MVC och webbsidor](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Steg** | Anti-CSRF och ASP.NET MVC-formulär `AntiForgeryToken` - Använd hjälpmetoden för vyer; sätta `Html.AntiForgeryToken()` i form, till exempel,|

### <a name="example"></a>Exempel
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Exempel
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
Samtidigt ger Html.AntiForgeryToken() besökaren en cookie som kallas __RequestVerificationToken, med samma värde som det slumpmässiga dolda värdet som visas ovan. Om du vill validera ett inkommande formulärinlägg lägger du sedan till filtret [ValidateAntiForgeryToken] i målåtgärdsmetoden. Ett exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Auktoriseringsfilter som kontrollerar att:
* Den inkommande begäran har en cookie som kallas __RequestVerificationToken
* Den inkommande begäran `Request.Form` har en post som heter __RequestVerificationToken
* Dessa cookies `Request.Form` och värden matchar Förutsatt att allt är bra, går begäran igenom som vanligt. Men om inte, då ett auktoriseringsfel med meddelandet "En obligatorisk anti-förfalskning token inte angavs eller var ogiltig". 

### <a name="example"></a>Exempel
Anti-CSRF och AJAX: Formulärtoken kan vara ett problem för AJAX-förfrågningar, eftersom en AJAX-begäran kan skicka JSON-data, inte HTML-formulärdata. En lösning är att skicka token i ett anpassat HTTP-huvud. Följande kod använder Razor-syntax för att generera token och lägger sedan till token i en AJAX-begäran. 
```csharp
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
När du bearbetar begäran extraherar du token från begäranden. Anropa sedan metoden AntiForgery.Validate för att validera token. Metoden Validate genererar ett undantag om token inte är giltiga.
```csharp
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

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Dra nytta av ASP.NET inbyggda funktioner för att parera webbattacker](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Steg** | CSRF-attacker i WebForm-baserade program kan mildras genom att ställa in ViewStateUserKey till en slumpmässig sträng som varierar för varje användare - användar-ID eller, ännu bättre, sessions-ID. Av ett antal tekniska och sociala skäl passar sessions-ID mycket bättre eftersom ett sessions-ID är oförutsägbart, time out och varierar beroende på användarbasis.|

### <a name="example"></a>Exempel
Här är koden du behöver ha på alla dina sidor:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Konfigurera session för inaktivitetslivslängd

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Egenskapen httpSessionState.timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Steg** | Tidsgränsen för sessionen representerar den händelse som inträffar när en användare inte utför någon åtgärd på en webbplats under ett intervall (definierat av webbservern). Händelsen, på serversidan, ändra status för användarsessionen till "ogiltig" (till exempel "används inte längre") och instruera webbservern att förstöra den (ta bort alla data som finns i den). I följande kodexempel anges tidsgränsen för sessionsattributet till 15 minuter i filen Web.config.|

### <a name="example"></a>Exempel
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Aktivera hotidentifiering på Azure SQL

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Formulärelement för autentisering (ASP.NET inställningar Schema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Steg** | Ange tidsgränsen för cookien för formulärautentiseringsbiljett till 15 minuter|

### <a name="example"></a>Exempel
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär, MVC5 |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [asdeqa (som en följd av detta)](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steg** | När webbprogrammet är Relying Party och ADFS är STS, livslängden för autentiseringscookies - FedAuth tokens - kan ställas in av följande konfiguration i web.config:|

### <a name="example"></a>Exempel
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
Även den ADFS utfärdade SAML-anspråkstokens livstid bör ställas in på 15 minuter, genom att köra följande powershell-kommando på ADFS-servern:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementera korrekt utloggning från programmet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Utför korrekt logga ut från programmet, när användaren trycker på utloggningsknappen. Vid utloggning bör programmet förstöra användarens session och även återställa och upphäva värdet för sessionscookie, tillsammans med återställning och nullifiering av autentiseringscookiens värde. När flera sessioner är kopplade till en enda användaridentitet måste de också avslutas kollektivt på serversidan vid timeout eller utloggning. Slutligen, se till att Logout funktionalitet är tillgänglig på varje sida. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Mildra mot CSRF-attacker (Cross-Site Request Forgery) mot ASP.NET webb-API:er

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Förfalskning av begäran på flera webbplatser (CSRF eller XSRF) är en typ av attack där en angripare kan utföra åtgärder i säkerhetskontexten för en annan användares etablerade session på en webbplats. Målet är att ändra eller ta bort innehåll, om den riktade webbplatsen enbart förlitar sig på sessionscookies för att autentisera mottagen begäran. En angripare kan utnyttja det här säkerhetsproblemet genom att hämta en annan användares webbläsare för att läsa in en URL med ett kommando från en sårbar plats där användaren redan är inloggad. Det finns många sätt för en angripare att göra det, till exempel genom att vara värd för en annan webbplats som läser in en resurs från den sårbara servern, eller för att få användaren att klicka på en länk. Attacken kan förhindras om servern skickar ytterligare en token till klienten, kräver att klienten inkluderar den token i alla framtida begäranden och verifierar att alla framtida begäranden innehåller en token som hör till den aktuella sessionen, till exempel genom att använda ASP.NET AntiForgeryToken eller ViewState. |

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Förhindra CSRF-attacker (Cross-Site Request Forgery) i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Steg** | Anti-CSRF och AJAX: Formulärtoken kan vara ett problem för AJAX-förfrågningar, eftersom en AJAX-begäran kan skicka JSON-data, inte HTML-formulärdata. En lösning är att skicka token i ett anpassat HTTP-huvud. Följande kod använder Razor-syntax för att generera token och lägger sedan till token i en AJAX-begäran. |

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
När du bearbetar begäran extraherar du token från begäranden. Anropa sedan metoden AntiForgery.Validate för att validera token. Metoden Validate genererar ett undantag om token inte är giltiga.
```csharp
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
Anti-CSRF och ASP.NET MVC-formulär - Använd hjälpmetoden AntiForgeryToken på Vyer; sätta en Html.AntiForgeryToken() i formuläret, till exempel,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exempel
Exemplet ovan kommer att mata ut något i stil med följande:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
Samtidigt ger Html.AntiForgeryToken() besökaren en cookie som kallas __RequestVerificationToken, med samma värde som det slumpmässiga dolda värdet som visas ovan. Om du vill validera ett inkommande formulärinlägg lägger du sedan till filtret [ValidateAntiForgeryToken] i målåtgärdsmetoden. Ett exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Auktoriseringsfilter som kontrollerar att:
* Den inkommande begäran har en cookie som kallas __RequestVerificationToken
* Den inkommande begäran `Request.Form` har en post som heter __RequestVerificationToken
* Dessa cookies `Request.Form` och värden matchar Förutsatt att allt är bra, går begäran igenom som vanligt. Men om inte, då ett auktoriseringsfel med meddelandet "En obligatorisk anti-förfalskning token inte angavs eller var ogiltig".

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Identitetsprovider – ADFS, identitetsprovider – Azure AD |
| **Referenser**              | [Skydda ett webb-API med enskilda konton och lokal inloggning i ASP.NET webb-API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Steg** | Om webb-API:et är skyddat med OAuth 2.0 förväntar den sig en innehavartoken i huvudet för auktoriseringsbegäran och ger åtkomst till begäran endast om token är giltig. Till skillnad från cookie-baserad autentisering bifogar webbläsare inte innehavartoken till begäranden. Den begärande klienten måste uttryckligen bifoga innehavartoken i begäranhuvudet. För ASP.NET webb-API:er som skyddas med OAuth 2.0 betraktas därför innehavartoken som ett försvar mot CSRF-attacker. Observera att om MVC-delen av programmet använder formulärautentisering (dvs. använder cookies), måste anti-förfalskningstoken användas av MVC-webbappen. |

### <a name="example"></a>Exempel
Webb-API:et måste informeras om att endast förlita sig på innehavartokens och inte på cookies. Det kan göras med följande `WebApiConfig.Register` konfiguration i metod:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoden SuppressDefaultHostAuthentication talar om för webb-API:et att ignorera all autentisering som sker innan begäran når web-API-pipelinen, antingen av IIS eller av OWIN middleware. På så sätt kan vi begränsa webb-API till autentisera endast med bärare tokens.
