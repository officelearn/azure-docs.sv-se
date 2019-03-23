---
title: Sessionen Management – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
description: åtgärder för hot som exponeras i Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: e8f3cf3889b3f79e930630ff0e768a0c4875eec6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361735"
---
# <a name="security-frame-session-management"></a>Security ram: Sessionshantering
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementera rätt logga ut med hjälp av ADAL metoder när du använder Azure AD](#logout-adal)</li></ul> |
| IoT-enheter | <ul><li>[Använd begränsad livslängd för genererade SaS-token](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Använda minsta tokenlivslängder för genererade resurstokens](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementera rätt logga ut med hjälp av WsFederation metoder när du använder AD FS](#wsfederation-logout)</li></ul> |
| **Identitetsserver** | <ul><li>[Implementera rätt logga ut när du använder Identity Server](#proper-logout)</li></ul> |
| **Webbprogram** | <ul><li>[Program som är tillgängliga via HTTPS måste Använd säkra cookies](#https-secure-cookies)</li><li>[Alla HTTP-baserade program ange http endast för cookie-definition](#cookie-definition)</li><li>[Skyddar mot skriptkörning över flera begär förfalskning (CSRF) attacker på ASP.NET-webbsidor](#csrf-asp)</li><li>[Konfigurera livslängd för inaktivitet-session](#inactivity-lifetime)</li><li>[Implementera rätt logga ut från programmet](#proper-app-logout)</li></ul> |
| **Webb-API** | <ul><li>[Skyddar mot skriptkörning över flera begär förfalskning (CSRF) attacker på ASP.NET Web API: er](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementera rätt logga ut med hjälp av ADAL metoder när du använder Azure AD

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure AD | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Om programmet är beroende av åtkomsttoken som utfärdas av Azure AD, anropa händelsehanteraren utloggning |

### <a name="example"></a>Exempel
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Exempel
Det bör också ta bort användarens session genom att anropa metoden Session.Abandon(). Följande metod visar säker implementering av användaren logga ut:
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

## <a id="finite-tokens"></a>Använd begränsad livslängd för genererade SaS-token

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | SaS-token genereras för att autentisera till Azure IoT Hub ska ha en begränsad giltighetsperioden. Använd livslängd för SaS-token som möjligt att begränsa mängden tid som de kan återupprepas om token har komprometterats.|

## <a id="resource-tokens"></a>Använda minsta tokenlivslängder för genererade resurstokens

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Minska tidsintervallet för Resurstoken till en lägsta värde som krävs. Resurstokens har en giltig timespan standardvärdet 1 timme.|

## <a id="wsfederation-logout"></a>Implementera rätt logga ut med hjälp av WsFederation metoder när du använder AD FS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | ADFS | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Om programmet är beroende av STS-token som utfärdas av AD FS, anropa logga ut händelsehanteraren WSFederationAuthenticationModule.FederatedSignOut() metod för att logga ut användaren. Även den aktuella sessionen förstöras och sessionen token värde ska återställa och undanröjda.|

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

## <a id="proper-logout"></a>Implementera rätt logga ut när du använder Identity Server

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Identitetsserver | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [IdentityServer3-federerade logga ut](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Steg** | IdentityServer stöder möjligheten att federera med externa indentitetsprovidrar. När en användare loggar ut från en överordnad identitetsprovider, kan beroende på det protokoll som används, det vara möjligt att få ett meddelande när användaren loggar ut. Det gör IdentityServer att meddela dess klienter så att de kan också logga användaren. I referensavsnittet för mer information om implementeringen finns i dokumentationen.|

## <a id="https-secure-cookies"></a>Program som är tillgängliga via HTTPS måste Använd säkra cookies

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [httpCookies Element (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure Property](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Steg** | Cookies är normalt endast är tillgängliga för den domän som de har begränsad. Tyvärr omfattas definitionen av ”domän” inte av protokollet så att cookies som skapas via HTTPS är tillgängliga via HTTP. Attributet ”säkra” anger att webbläsaren att cookien ska endast göras tillgängliga via HTTPS. Kontrollera att alla cookies över HTTPS-användning i **säker** attribut. Kravet på tillämpas i web.config-filen genom att ange attributet requireSSL till true. Det är att föredra eftersom den kommer att tillämpa den **säker** attribut för alla nuvarande och framtida-cookies utan att behöva göra några ytterligare kodändringar.|

### <a name="example"></a>Exempel
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Inställningen tillämpas även om HTTP används för att få åtkomst till programmet. Om HTTP används för att få åtkomst till programmet, delar den här inställningen programmet eftersom cookies som anges med attributet säker och webbläsaren kommer inte att skicka dem till programmet.

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär, MVC5 |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | Gäller inte  |
| **Steg** | När webbprogrammet är den förlitande parten och IDP: N är AD FS-servern, secure token FedAuth-attributet kan konfigureras genom att ange requireSSL till True i `system.identityModel.services` avsnitt av web.config:|

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

## <a id="cookie-definition"></a>Alla HTTP-baserade program ange http endast för cookie-definition

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Säker Cookie-attribut](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Steg** | För att minska risken för avslöjande av information med en cross site scripting (XSS) attack, ett nytt attribut - httpOnly - introducerades så att cookies och stöds av alla större webbläsare. Attributet anger att en cookie inte är tillgänglig via skript. Genom att använda HttpOnly cookies, minskar risken att känslig information i cookien kan stals via skript och skickas till en angripares webbplats i ett webbprogram. |

### <a name="example"></a>Exempel
Alla HTTP-baserade program som använder cookies ska ange HttpOnly i cookie-definitionen genom att implementera följande konfiguration i web.config:
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
| **Tillämpliga tekniker** | Webbformulär |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [FormsAuthentication.RequireSSL Property](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Steg** | RequireSSL egenskapens värde har angetts i konfigurationsfilen för ett ASP.NET-program med hjälp av attributet requireSSL för konfigurationselementet. Du kan ange i Web.config-filen för ASP.NET-program om SSL (Secure Sockets Layer) krävs för att returnera cookien för formulär för autentisering till servern genom att ange attributet requireSSL.|

### <a name="example"></a>Exempel 
I följande kodexempel anger attributet requireSSL i Web.config-filen.
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

## <a id="csrf-asp"></a>Skyddar mot skriptkörning över flera begär förfalskning (CSRF) attacker på ASP.NET-webbsidor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Begäran-förfalskning (CSRF eller XSRF) är en typ av angrepp där angriparen kan utföra åtgärder i säkerhetskontexten för en annan användare upprättad session på en webbplats. Målet är att ändra eller ta bort innehåll, om den aktuella webbplatsen är beroende av exklusivt sessionscookies att autentisera fick begäran. En angripare kan utnyttja denna säkerhetsrisk genom att få en annan användares webbläsare att läsa in en URL med ett kommando från en sårbara plats där användaren är redan inloggad. Det finns många sätt för en angripare att göra det, till exempel av som är värd för en annan webbplats som läser in en resurs från sårbara servern eller att användaren klickar på en länk. Angreppet kan förhindras om servern skickar en extra token till klienten, kräver att klienten inkluderar den token i alla framtida begäranden och verifierar att alla framtida begäranden inkluderar en token som rör den aktuella sessionen, till exempel med hjälp av ASP.NET AntiForgeryToken eller ViewState. |

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [XSRF/CSRF Dataförlustskydd i ASP.NET MVC och på webbsidor](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Steg** | Skydd mot CSRF- och ASP.NET MVC-formulär – Använd den `AntiForgeryToken` hjälpmetoden i vyer, put en `Html.AntiForgeryToken()` i formuläret, till exempel|

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
På samma gång ger Html.AntiForgeryToken() besökscentret en cookie som kallas __RequestVerificationToken med samma värde som slumpmässiga dolda värdet som anges ovan. Därefter, för att verifiera en inkommande formuläret post, lägger du till [ValidateAntiForgeryToken] filtret åtgärdsmetod mål. Exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Auktoriseringsfilter som kontrollerar att:
* Inkommande begäran har en cookie som kallas __RequestVerificationToken
* Inkommande begäran har en `Request.Form` post som kallas __RequestVerificationToken
* Dessa cookie och `Request.Form` värden matchar förutsatt att alla är bra, begäran går igenom som vanligt. Men om inte, sedan en Auktoriseringen misslyckades med meddelandet ”en obligatorisk antiförfalskningstoken angavs inte eller var ogiltigt”. 

### <a name="example"></a>Exempel
Anti-CSRF and AJAX: Formulär-token kan vara ett problem för AJAX-begäranden, eftersom en AJAX-begäran kan skicka JSON-data och inte HTML-formulärdata. En lösning är att skicka token i ett anpassat HTTP-huvud. Följande kod använder Razor-syntaxen för att generera token och lägger sedan till token till en AJAX-begäran. 
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
När du bearbetar begäran extrahera token från huvudet för begäran. Sedan anropa metoden AntiForgery.Validate för att validera token. Verifiera metoden genereras ett undantag om token inte är giltiga.
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

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Dra nytta av ASP.NET inbyggda funktioner för att slippa webbattacker](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Steg** | CSRF attacker i WebForm baserat program kan undvikas genom att ange ViewStateUserKey till en slumpmässig sträng som varierar för varje användare - användar-ID eller bättre än, sessions-ID. För ett antal tekniska och sociala skäl passar sessions-ID mycket bättre eftersom en session-ID är oförutsägbart, tidsgränsen och varierar på basis av per användare.|

### <a name="example"></a>Exempel
Här är koden som du behöver ha i alla sidor:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Konfigurera livslängd för inaktivitet-session

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [HttpSessionState.Timeout Property](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Steg** | Tidsgräns för session representerar händelsen inträffar när en användare inte utför någon åtgärd på en webbplats under ett intervall (definieras av webbservern). Händelsen på serversidan, ändra status för användarens session till ”ogiltigt” (till exempel ”används inte längre”) och att webbservern att ta bort den (tar bort alla data som finns i den). I följande kodexempel anger attributet timeout-session till 15 minuter i Web.config-filen.|

### <a name="example"></a>Exempel
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Aktivera hotidentifiering i Azure SQL

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Element för formulär för autentisering (ASP.NET inställningsschema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Steg** | Ange tidsgränsen för formulär autentiseringsbiljetten cookie till 15 minuter|

### <a name="example"></a>Exempel
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär, MVC5 |
| **Attribut**              | EnvironmentType - OnPrem |
| **Referenser**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steg** | När webbprogrammet är förlitande part och AD FS STS, kan autentiseringscookies - FedAuth-token - livslängd anges med följande konfiguration i web.config:|

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
Också begära ADFS utfärdade SAML-token-livslängd ska vara inställd på 15 minuter, genom att köra följande powershell-kommando på AD FS-servern:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementera rätt logga ut från programmet

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Utföra rätt logga ut från programmet när användaren trycker på Logga ut knappen. Vid utloggning bör program förstör användarens session, och även återställa och upphäver session cookie-värde, tillsammans med återställning och också ångras cookievärdet för autentisering. Dessutom när flera sessioner är knutna till en enda användar-ID, måste de sammantaget avslutas på serversidan på timeout eller logga ut. Kontrollera slutligen att logga ut funktionen är tillgänglig på varje sida. |

## <a id="csrf-api"></a>Skyddar mot skriptkörning över flera begär förfalskning (CSRF) attacker på ASP.NET Web API: er

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Begäran-förfalskning (CSRF eller XSRF) är en typ av angrepp där angriparen kan utföra åtgärder i säkerhetskontexten för en annan användare upprättad session på en webbplats. Målet är att ändra eller ta bort innehåll, om den aktuella webbplatsen är beroende av exklusivt sessionscookies att autentisera fick begäran. En angripare kan utnyttja denna säkerhetsrisk genom att få en annan användares webbläsare att läsa in en URL med ett kommando från en sårbara plats där användaren är redan inloggad. Det finns många sätt för en angripare att göra det, till exempel av som är värd för en annan webbplats som läser in en resurs från sårbara servern eller att användaren klickar på en länk. Angreppet kan förhindras om servern skickar en extra token till klienten, kräver att klienten inkluderar den token i alla framtida begäranden och verifierar att alla framtida begäranden inkluderar en token som rör den aktuella sessionen, till exempel med hjälp av ASP.NET AntiForgeryToken eller ViewState. |

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Det gick inte att attacker med förfalskning (CSRF) av begäran i ASP.NET webb-API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Steg** | Anti-CSRF and AJAX: Formulär-token kan vara ett problem för AJAX-begäranden, eftersom en AJAX-begäran kan skicka JSON-data och inte HTML-formulärdata. En lösning är att skicka token i ett anpassat HTTP-huvud. Följande kod använder Razor-syntaxen för att generera token och lägger sedan till token till en AJAX-begäran. |

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
När du bearbetar begäran extrahera token från huvudet för begäran. Sedan anropa metoden AntiForgery.Validate för att validera token. Verifiera metoden genereras ett undantag om token inte är giltiga.
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
Skydd mot CSRF- och ASP.NET MVC-formulär - använda hjälpmetoden AntiForgeryToken på vyer. Placera en Html.AntiForgeryToken() i formuläret, till exempel
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Exempel
I exemplet ovan kommer att skrivas ut ungefär så här:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Exempel
På samma gång ger Html.AntiForgeryToken() besökscentret en cookie som kallas __RequestVerificationToken med samma värde som slumpmässiga dolda värdet som anges ovan. Därefter, för att verifiera en inkommande formuläret post, lägger du till [ValidateAntiForgeryToken] filtret åtgärdsmetod mål. Exempel:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Auktoriseringsfilter som kontrollerar att:
* Inkommande begäran har en cookie som kallas __RequestVerificationToken
* Inkommande begäran har en `Request.Form` post som kallas __RequestVerificationToken
* Dessa cookie och `Request.Form` värden matchar förutsatt att alla är bra, begäran går igenom som vanligt. Men om inte, sedan en Auktoriseringen misslyckades med meddelandet ”en obligatorisk antiförfalskningstoken angavs inte eller var ogiltigt”.

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5, MVC6 |
| **Attribut**              | Identitetsprovider för Identity - Provider – ADFS, Azure AD |
| **Referenser**              | [Skydda ett webb-API med enskilda konton och lokala inloggning i ASP.NET Web API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Steg** | Om webb-API är skyddade med OAuth 2.0, sedan förväntar sig en ägartoken i auktoriseringsrubriken för begäran och ger åtkomst till begäran endast om token är giltig. Till skillnad från cookie-baserad autentisering bifoga webbläsare inte ägar-token till begäranden. Den begärande klienten måste uttryckligen bifoga ägartoken i rubriken. ASP.NET Web API: er skyddas med hjälp av OAuth 2.0, betraktas därför ägar-token som skydd mot CSRF-attacker. Observera att om den MVC-delen av programmet använder formulär för autentisering (t.ex, använder cookies), har antiförfalskningstoken som ska användas av MVC-webbappen. |

### <a name="example"></a>Exempel
Webb-API har så att du förlita dig endast på ägar-token och inte på cookies. Det kan göras med följande konfiguration i `WebApiConfig.Register` metod:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoden SuppressDefaultHostAuthentication talar om för webb-API för att ignorera all verifiering som sker innan begäran når webb-API-pipeline, antingen av IIS eller genom att OWIN-mellanprogrammet. På så sätt kan vi begränsa webb-API för att autentisera med enbart ägar-token.
