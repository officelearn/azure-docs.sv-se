---
title: Undantag Management - verktyget Microsoft Threat modellering - Azure | Microsoft Docs
description: ändringar för hot som exponeras i verktyget Modeling hot
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
ms.openlocfilehash: 3fae9390b41d12361b820e2c37601283b37bc302
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031720"
---
# <a name="security-frame-exception-management--mitigations"></a>Säkerhet ram: Hantering av undantag | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - Använd innehåller inte serviceDebug nod i konfigurationsfilen](#servicedebug)</li><li>[WCF - Använd innehåller inte noden serviceMetadata i konfigurationsfilen](#servicemetadata)</li></ul> |
| **Webb-API** | <ul><li>[Se till att rätt undantagshantering är klar i ASP.NET Web API ](#exception)</li></ul> |
| **Webbprogram** | <ul><li>[Exponera inte säkerhetsinformation i felmeddelanden ](#messages)</li><li>[Implementera standard felhantering sida ](#default)</li><li>[Ange metod för distribution till fullversion i IIS](#deployment)</li><li>[Undantag ska misslyckas på ett säkert sätt](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - Använd innehåller inte serviceDebug nod i konfigurationsfilen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk NET Framework 3 |
| **attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Steg** | Windows Communication Framework (WCF) tjänster kan konfigureras för att visa felsökningsinformation. Felsöka information ska inte användas i produktionsmiljöer. Den `<serviceDebug>` tagg definierar om funktionen debug-information är aktiverat för en WCF-tjänst. Om attributet includeExceptionDetailInFaults är inställd på true, undantagsinformation från programmet returneras till klienter. Angripare kan utnyttja de ytterligare information som de får från felsökning utdata för att montera riktade attacker på ramen, databaser eller andra resurser som används av programmet. |

### <a name="example"></a>Exempel
Följande konfigurationsfil som innehåller den `<serviceDebug>` tagg: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Inaktivera felsökningsinformation i tjänsten. Detta kan åstadkommas genom att ta bort den `<serviceDebug>` taggen från programmets konfigurationsfil. 

## <a id="servicemetadata"></a>WCF - Använd innehåller inte noden serviceMetadata i konfigurationsfilen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Generisk NET Framework 3 |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Steg** | Offentligt visar information om en tjänst kan angriparna få värdefulla insikter i hur de kan utnyttja tjänsten. Den `<serviceMetadata>` taggen kan publiceringsfunktionen metadata. Klustertjänstens metadata kan innehålla känslig information som inte ska vara tillgängligt för allmänheten. Tillåt endast betrodda användare att komma åt metadata och se till att onödig information inte exponeras minst. Ännu bättre är helt inaktivera möjligheten att publicera metadata. En säker WCF-konfigurationen innehåller inte den `<serviceMetadata>` tagg. |

## <a id="exception"></a>Se till att rätt undantagshantering är klar i ASP.NET Web API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC 5, MVC 6 |
| **attribut**              | Gäller inte  |
| **Referenser**              | [Undantagshantering i ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling), [modell verifiering i ASP.NET Web API](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | Som standard är mest undantagsfel utan felhantering i ASP.NET Web API översättas till ett HTTP-svar med statuskoden `500, Internal Server Error`|

### <a name="example"></a>Exempel
Kontrollera statuskoden som returnerades av API: et, `HttpResponseException` kan användas som visas nedan: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Exempel
För ytterligare kontroll av undantag svaret på `HttpResponseMessage` klassen kan användas som visas nedan: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Fånga ohanterade undantag som inte är av typen `HttpResponseException`, undantagsfilter som kan användas. Undantagsfilter implementera den `System.Web.Http.Filters.IExceptionFilter` gränssnitt. Det enklaste sättet att skriva ett undantagsfilter är vara härledda från den `System.Web.Http.Filters.ExceptionFilterAttribute` klassen och åsidosätta metoden OnException. 

### <a name="example"></a>Exempel
Här är ett filter som konverterar `NotImplementedException` undantag i HTTP-statuskod `501, Not Implemented`: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Det finns flera sätt att registrera ett undantagsfilter webb-API:
- Åtgärden
- Av domänkontrollant
- Globalt

### <a name="example"></a>Exempel
Om du vill använda filtret på en specifik åtgärd, Lägg till filtret som ett attribut åtgärden: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Exempel
Tillämpa filtret till alla åtgärder på en `controller`, lägga till filtret som ett attribut till den `controller` klass: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exempel
Om du vill använda filtret globalt på alla domänkontrollanter i Web API, lägger du till en instans av filtret till den `GlobalConfiguration.Configuration.Filters` samling. Undantagsfilter i den här samlingen tillämpas på något Web API-styrenhet. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exempel
För modellverifiering av, kan tillståndet modell skickas till CreateErrorResponse metoden enligt nedan: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Kontrollera länkarna i referensavsnittet för ytterligare information om särskilda hantering och modellverifiering i ASP.Net Web API 

## <a id="messages"></a>Exponera inte säkerhetsinformation i felmeddelanden

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Allmänt felmeddelanden tillhandahålls direkt till användaren utan att inkludera känsliga programdata. Exempel på känsliga data:</p><ul><li>Servernamn</li><li>Anslutningssträngar</li><li>Användarnamn</li><li>Lösenord</li><li>SQL-procedurer</li><li>Information om dynamisk SQL-fel</li><li>Stackspårning och rader med kod</li><li>Variabler som lagrats i minnet</li><li>Enhet och mapp</li><li>Programmet installera punkter</li><li>Konfigurationsinställningar för värden</li><li>Andra interna programinformation</li></ul><p>Svällning alla fel i ett program och ger allmänna felmeddelanden samt aktivera anpassade fel i IIS kan förhindra att information röjs. SQL Server-databas och .NET undantagshantering, bland annat felhantering arkitekturer, är särskilt utförlig och mycket användbara obehöriga profilering av ditt program. Gör inte direkt visa innehållet i en klass som härleds från klassen .NET-undantag och se till att du har rätt undantagshantering så att ett oväntat undantag inte aktiveras oavsiktligt direkt till användaren.</p><ul><li>Ange allmänna felmeddelanden direkt till den användare som abstrakt in specifik information direkt i undantag/felmeddelande</li><li>Visa inte innehållet i en klass för .NET-undantag direkt till användaren</li><li>Fånga alla felmeddelanden och om det är lämpligt informera användaren via ett allmänt felmeddelande som skickas till klienten</li><li>Inte visar innehållet i klassen undantag direkt till användaren, särskilt returvärde från `.ToString()`, eller värden för egenskaperna meddelande eller stackspårning. Loggar den här informationen och visar ett mer harmlöst meddelande för användaren på ett säkert sätt</li></ul>|

## <a id="default"></a>Implementera standard felhantering sida

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | [Redigera ASP.NET inställningar dialogrutan felsidor](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Steg** | <p>När ett ASP.NET-program misslyckas och gör en HTTP/1.x 500 Internt serverfel eller en funktionskonfiguration (till exempel begäransfiltrering) förhindrar att en sida visas, genereras ett felmeddelande. Administratörer kan välja oavsett om programmet ska visa ett eget meddelande till klienten, detaljerat felmeddelande för klienten eller detaljerat felmeddelande endast localhost. Den <customErrors> i web.config har tre lägen:</p><ul><li>**På:** anger att anpassade fel är aktiverat. Om inget defaultRedirect attribut anges visas ett allmänt fel användare. Anpassade felmeddelanden visas till fjärranslutna klienter och för den lokala värden</li><li>**Av:** anger att anpassade fel är inaktiverat. Detaljerad ASP.NET-fel visas för fjärranslutna klienter och den lokala värden</li><li>**RemoteOnly:** anger att anpassade fel visas endast till fjärranslutna klienter och ASP.NET-fel visas för den lokala värden. Detta är standardvärdet</li></ul><p>Öppna den `web.config` för program/platsen och kontrollera att taggen har antingen `<customErrors mode="RemoteOnly" />` eller `<customErrors mode="On" />` definieras.</p>|

## <a id="deployment"></a>Ange metod för distribution till fullversion i IIS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | [distribution Element (inställningsschema för ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Steg** | <p>Den `<deployment retail>` växel är avsedd att användas av IIS produktionsservrar. Den här växeln används för att program körs med bästa möjliga prestanda och minsta möjliga säkerhet information läckage genom att inaktivera programmets förmåga att generera spårningsutdata på en sida, inaktivera möjligheten att visa detaljerade felmeddelanden som slutanvändare och inaktivera debug-växeln.</p><p>Ofta, växlar och alternativ som är fokuserad utvecklare, t.ex. Det gick inte begära spårning och felsökning aktiveras under aktiv utveckling. Du rekommenderas att distributionsmetoden på en produktionsserver anges till fullversion. Öppna filen machine.config och se till att `<deployment retail="true" />` är inställd på true.</p>|

## <a id="fail"></a>Undantag ska misslyckas på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **attribut**              | Gäller inte  |
| **Referenser**              | [Misslyckas på ett säkert sätt](https://www.owasp.org/index.php/Fail_securely) |
| **Steg** | Programmet ska misslyckas på ett säkert sätt. Valfri metod som returnerar ett booleskt värde baserat på vilka vissa beslut fattas bör ha undantagsblock noggrant skapas. Det finns många logiska fel på grund av som säkerhetsfrågor krypning i när undantagsblock skrivs vårdslöst.|

### <a name="example"></a>Exempel
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Metoden ovan returneras alltid True, om vissa undantag inträffar. Om användaren har en felaktig URL som webbläsaren respekterar men `Uri()` konstruktorn inte detta genereras ett undantagsfel och offrets kommer att tas till giltiga men felaktigt-URL. 