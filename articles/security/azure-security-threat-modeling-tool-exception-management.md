---
title: Undantag Management – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
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
ms.openlocfilehash: 5c768497cc21b3fae82a9db290531af33841a2cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877310"
---
# <a name="security-frame-exception-management--mitigations"></a>Security ram: Hantering av undantag | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - Ange inte serviceDebug noden i konfigurationsfilen](#servicedebug)</li><li>[WCF - Ange inte serviceMetadata noden i konfigurationsfilen](#servicemetadata)</li></ul> |
| **Webb-API** | <ul><li>[Se till att rätt undantagshantering görs i ASP.NET Web API](#exception)</li></ul> |
| **Webbprogram** | <ul><li>[Visa inte information om säkerhet i felmeddelanden](#messages)</li><li>[Implementera standard sidan för felhantering](#default)</li><li>[Ange metod för distribution till fullversion i IIS](#deployment)</li><li>[Undantag bör inte fungera på ett säkert sätt](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - Ange inte serviceDebug noden i konfigurationsfilen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generic, NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Steg** | Tjänster för Windows Communication Framework (WCF) kan konfigureras för att exponera felsökningsinformation. Felsöka information ska inte användas i produktionsmiljöer. Den `<serviceDebug>` tagg definierar om funktionen debug information är aktiverat för en WCF-tjänst. Om attributet includeExceptionDetailInFaults har angetts till SANT, undantagsinformation från programmet kommer att returneras till klienter. Angripare kan utnyttja ytterligare information som de får från felsökning utdata om du vill montera riktade attacker på ramverket, databas eller andra resurser som används av programmet. |

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
Inaktivera felsökningsinformation i tjänsten. Detta kan åstadkommas genom att ta bort den `<serviceDebug>` tagg från programmets konfigurationsfil. 

## <a id="servicemetadata"></a>WCF - Ange inte serviceMetadata noden i konfigurationsfilen

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Generic, NET Framework 3 |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [spikning Nordirland](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Steg** | Offentligt exponera information om en tjänst kan angriparna få värdefulla insikter i hur de kan utnyttja tjänsten. Den `<serviceMetadata>` -taggen låter publiceringsfunktionen metadata. Tjänstens metadata kan innehålla känslig information som inte ska vara tillgänglig för allmänheten. Tillåt endast betrodda användare att komma åt metadata och se till att onödig information inte exponeras minst. Dessutom finns helt inaktivera möjligheten att publicera metadata. En säker WCF-konfigurationen innehåller inte den `<serviceMetadata>` tagg. |

## <a id="exception"></a>Se till att rätt undantagshantering görs i ASP.NET Web API

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC 5, MVC 6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Undantagshantering i ASP.NET Web API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [modellera validering i ASP.NET webb-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | Som standard översätts mest undantag utan felhantering i ASP.NET Web API till en HTTP-svar med statuskod `500, Internal Server Error`|

### <a name="example"></a>Exempel
Kontrollera statuskoden som returneras av API: et, `HttpResponseException` kan användas som visas nedan: 
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
För ytterligare kontroll av undantag i svaret på `HttpResponseMessage` klassen kan användas som visas nedan: 
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
Att fånga upp ohanterade undantag som inte är av typen `HttpResponseException`, undantag filter kan användas. Undantag filter implementera de `System.Web.Http.Filters.IExceptionFilter` gränssnitt. Det enklaste sättet att skriva ett undantagsfilter är kan härledas från den `System.Web.Http.Filters.ExceptionFilterAttribute` klassen och åsidosätta metoden OnException. 

### <a name="example"></a>Exempel
Här är ett filter som konverterar `NotImplementedException` undantag till HTTP-statuskod `501, Not Implemented`: 
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

Det finns flera sätt att registrera ett webb-API-undantagsfilter:
- Efter åtgärd
- Av controller
- Globally

### <a name="example"></a>Exempel
Om du vill använda filtret på en specifik åtgärd, att lägga till filtret som ett attribut för åtgärden: 
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
Att tillämpa filtret till alla åtgärder på en `controller`, lägga till filtret som ett attribut till den `controller` klass: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exempel
Om du vill använda filtret globalt på alla domänkontrollanter i webb-API, lägger du till en instans av filtret till den `GlobalConfiguration.Configuration.Filters` samling. Undantag filter i den här samlingen gäller för alla åtgärder för webb-API-domänkontrollant. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exempel
För Modellvalidering på kan modell tillstånd skickas till CreateErrorResponse metoden som nedan: 
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

Kontrollera länkarna i referensavsnittet för ytterligare information om enastående hantering och verifiering av modellen i ASP.NET Web API 

## <a id="messages"></a>Visa inte information om säkerhet i felmeddelanden

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Allmänna felmeddelanden tillhandahålls direkt till användaren utan att inkludera känsliga programdata. Exempel på känsliga data:</p><ul><li>Servernamn</li><li>Anslutningssträngar</li><li>Användarnamn</li><li>Lösenord</li><li>SQL-procedurer</li><li>Information om dynamisk SQL-fel</li><li>Stackspårning och rader med kod</li><li>Variabler som lagrats i minnet</li><li>Enhets- och mappsökväg platser</li><li>Programmet installera punkter</li><li>Konfigurationsinställningar för värd</li><li>Information om andra interna program</li></ul><p>Svällning alla fel i ett program och ger allmänna felmeddelanden, samt aktivera anpassade fel i IIS kan förhindra att information röjs. SQL Server-databas och .NET undantagshantering, bland andra felhantering arkitekturer, är särskilt utförlig och mycket användbar för att en obehörig användare profilering ditt program. Gör inte direkt visa innehållet i en klass som härleds från klassen .NET-undantag och kontrollera att du har rätt undantagshantering så att ett oväntat undantag inte oavsiktligt höjs direkt till användaren.</p><ul><li>Ange allmänna felmeddelanden direkt till användaren om att abstrahera away specifik information direkt i undantag/felmeddelande</li><li>Visa inte innehållet i en .NET-undantagsklass direkt till användaren</li><li>Fånga alla felmeddelanden och meddela användaren via ett allmänt felmeddelande som skickas till klienten om det är lämpligt</li><li>Visar inte innehållet i klassen undantag direkt till användaren, särskilt returvärdet från `.ToString()`, eller värdena för egenskaperna meddelandet eller stackspårning. På ett säkert sätt Logga den här informationen och visa en mer harmlöst meddelande för användaren</li></ul>|

## <a id="default"></a>Implementera standard sidan för felhantering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [ASP.NET-fel sidor dialogrutan Redigera inställningar](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Steg** | <p>När ett ASP.NET-program misslyckas och gör ett HTTP/1.x 500 Internt serverfel, eller en funktionskonfiguration (till exempel begäransfiltrering) som förhindrar att en sida visas, genereras ett felmeddelande. Administratörer kan välja huruvida programmet ska visa ett meddelande till klienten, detaljerat felmeddelande till klienten eller detaljerat felmeddelande till localhost endast. Den <customErrors> tagg i web.config har tre lägen:</p><ul><li>**På:** Anger att anpassade fel är aktiverade. Om inget defaultRedirect attribut anges ser användarna ett allmänt fel. Anpassade fel visas för fjärranslutna klienter och den lokala värden</li><li>**Av:** Anger att anpassade fel är inaktiverat. Detaljerade ASP.NET-fel visas för fjärranslutna klienter och den lokala värden</li><li>**RemoteOnly:** Anger att anpassade fel visas endast för fjärranslutna klienter och att ASP.NET-fel visas för den lokala värden. Detta är standardvärdet</li></ul><p>Öppna den `web.config` för programmet/webbplatsen och kontrollera att taggen har antingen `<customErrors mode="RemoteOnly" />` eller `<customErrors mode="On" />` definierats.</p>|

## <a id="deployment"></a>Ange metod för distribution till fullversion i IIS

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Distribution |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [distribution Element (ASP.NET inställningsschema)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Steg** | <p>Den `<deployment retail>` växel är avsedd att användas av IIS-servrar för produktion. Den här växeln används för att program körs med de bästa möjliga prestanda och minsta möjliga säkerhet information läckage genom att inaktivera programmets möjlighet att generera spårningsutdata på en sida, inaktiverar möjligheten att visa detaljerade felmeddelanden till slutanvändare och inaktiverar debug-växeln.</p><p>Ofta, växlar och alternativ som är riktade till utvecklare, t.ex. Det gick inte begära spårning och felsökning kan aktiveras under aktivt med utveckling. Du rekommenderas att distributionsmetoden på alla servrar i produktionsmiljö anges till fullversion. Öppna filen machine.config och se till att `<deployment retail="true" />` ställd till true.</p>|

## <a id="fail"></a>Undantag bör inte fungera på ett säkert sätt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Misslyckas på ett säkert sätt](https://www.owasp.org/index.php/Fail_securely) |
| **Steg** | Programmet bör inte fungera på ett säkert sätt. Valfri metod som returnerar ett booleskt värde, baserat på vilka vissa beslut fattas, bör ha undantagsblock noggrant skapas. Det finns många logiska fel på grund av som utfärdar security krypning i när undantagsblock skrivs vårdslöst.|

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
Metoden ovan returneras alltid SANT om vissa undantag inträffar. Om användaren får en felaktig URL som webbläsaren respekterar, men `Uri()` konstruktor inte detta genereras ett undantagsfel och offer kommer att tas till giltiga men felaktigt-URL. 
