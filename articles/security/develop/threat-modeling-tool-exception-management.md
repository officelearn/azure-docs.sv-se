---
title: Undantagshantering – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
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
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728081"
---
# <a name="security-frame-exception-management--mitigations"></a>Säkerhetsram: Undantagshantering | Mitigations 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- Inkludera inte serviceDebug-nod i konfigurationsfilen](#servicedebug)</li><li>[WCF- Inkludera inte serviceMetadata-nod i konfigurationsfilen](#servicemetadata)</li></ul> |
| **Webb-API** | <ul><li>[Kontrollera att korrekt undantagshantering utförs i ASP.NET webb-API](#exception)</li></ul> |
| **Webbprogram** | <ul><li>[Visa inte säkerhetsinformation i felmeddelanden](#messages)</li><li>[Implementera sidan Standardfelhantering](#default)</li><li>[Ange distributionsmetod till butik i IIS](#deployment)</li><li>[Undantag bör misslyckas på ett säkert sätt](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- Inkludera inte serviceDebug-nod i konfigurationsfilen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Steg** | WCF-tjänster (Windows Communication Framework) kan konfigureras för att exponera felsökningsinformation. Felsökningsinformation bör inte användas i produktionsmiljöer. Taggen `<serviceDebug>` definierar om felsökningsinformationsfunktionen är aktiverad för en WCF-tjänst. Om attributet includeExceptionDetailInFaults är inställt på true returneras undantagsinformation från programmet till klienter. Angripare kan utnyttja den ytterligare information som de får från felsökning av utdata för att montera attacker som är inriktade på ramverket, databasen eller andra resurser som används av programmet. |

### <a name="example"></a>Exempel
Följande konfigurationsfil `<serviceDebug>` innehåller taggen: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Inaktivera felsökningsinformation i tjänsten. Detta kan åstadkommas genom `<serviceDebug>` att ta bort taggen från programmets konfigurationsfil. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- Inkludera inte serviceMetadata-nod i konfigurationsfilen

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Allmänt, NET Framework 3 |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kungariket](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Steg** | Att offentligt exponera information om en tjänst kan ge angripare värdefull insikt i hur de kan utnyttja tjänsten. Taggen `<serviceMetadata>` aktiverar funktionen för metadatapublicering. Tjänstmetadata kan innehålla känslig information som inte bör vara allmänt tillgänglig. Åtminstone tillåter endast betrodda användare att komma åt metadata och se till att onödig information inte exponeras. Ännu bättre, helt inaktivera möjligheten att publicera metadata. En säker WCF-konfiguration `<serviceMetadata>` innehåller inte taggen. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Kontrollera att korrekt undantagshantering utförs i ASP.NET webb-API

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC 5, MVC 6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Undantagshantering i ASP.NET webb-API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [modellvalidering i ASP.NET webb-API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | Som standard översätts de flesta oåtgärdsundantag undantag i ASP.NET webb-API till ett HTTP-svar med statuskod`500, Internal Server Error`|

### <a name="example"></a>Exempel
För att styra statuskoden som `HttpResponseException` returneras av API: et, kan användas som visas nedan: 
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
För ytterligare kontroll av undantagssvaret `HttpResponseMessage` kan klassen användas enligt nedan: 
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
Om du vill fånga upp ohanterade undantag som inte är av typen `HttpResponseException`kan undantagsfilter användas. Undantagsfilter `System.Web.Http.Filters.IExceptionFilter` implementerar gränssnittet. Det enklaste sättet att skriva ett undantagsfilter är att härleda från `System.Web.Http.Filters.ExceptionFilterAttribute` klassen och åsidosätta OnException-metoden. 

### <a name="example"></a>Exempel
Här är ett filter `NotImplementedException` som konverterar `501, Not Implemented`undantag till HTTP-statuskod: 
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

Det finns flera sätt att registrera ett undantagsfilter för webb-API:
- Genom åtgärd
- Efter handkontroll
- Globalt

### <a name="example"></a>Exempel
Om du vill använda filtret på en viss åtgärd lägger du till filtret som ett attribut i åtgärden: 
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
Om du vill använda filtret `controller`på alla åtgärder i en `controller` lägger du till filtret som ett attribut i klassen: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exempel
Om du vill använda filtret globalt på alla webb-API-styrenheter lägger du till en instans av filtret i `GlobalConfiguration.Configuration.Filters` samlingen. Undantagsfilter i den här samlingen gäller för alla åtgärder för webb-API-styrenhet. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exempel
För modellvalidering kan modelltillståndet skickas till Metoden CreateErrorResponse enligt nedan: 
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

Mer information om exceptionell hantering och modellvalidering finns ASP.NET i länkarna i referensavsnittet. 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Visa inte säkerhetsinformation i felmeddelanden

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Allmänna felmeddelanden tillhandahålls direkt till användaren utan att inkludera känsliga programdata. Exempel på känsliga data är:</p><ul><li>Servernamn</li><li>Anslutningssträngar</li><li>Användarnamn</li><li>Lösenord</li><li>SQL-procedurer</li><li>Information om dynamiska SQL-fel</li><li>Stackspårning och kodrader</li><li>Variabler som lagras i minnet</li><li>Enhets- och mappplatser</li><li>Installationspunkter för program</li><li>Konfigurationsinställningar för värd</li><li>Andra interna ansökningsuppgifter</li></ul><p>Genom att svälla alla fel i ett program och tillhandahålla allmänna felmeddelanden, samt aktivera anpassade fel i IIS, förhindrar du att informationen lämnas ut. SQL Server-databas och .NET Undantagshantering, bland andra felhanteringsarkitekturer, är särskilt utförliga och mycket användbara för en skadlig användarprofilering av ditt program. Visa inte direkt innehållet i en klass som härleds från klassen .NET Exception och se till att du har rätt undantagshantering så att ett oväntat undantag inte oavsiktligt utlöses direkt till användaren.</p><ul><li>Ge allmänna felmeddelanden direkt till användaren som abstrahera bort specifik information som finns direkt i undantaget/felmeddelandet</li><li>Visa inte innehållet i en .NET-undantagsklass direkt för användaren</li><li>Svälla alla felmeddelanden och informera användaren om det är lämpligt via ett allmänt felmeddelande som skickas till programklienten</li><li>Visa inte innehållet i klassen Undantag direkt för användaren, `.ToString()`särskilt inte returvärdet från eller värdena för egenskaperna Meddelande eller StackTrace. Logga den här informationen på ett säkert sätt och visa ett mer harmlöst meddelande till användaren</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementera sidan Standardfelhantering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Redigera dialogrutan för inställningar av ASP.NET-felsidor](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Steg** | <p>När ett ASP.NET program misslyckas och ett http/1.x 500 internt serverfel eller en funktionskonfiguration (till exempel begärandefiltrering) förhindrar att en sida visas genereras ett felmeddelande. Administratörer kan välja om programmet ska visa ett eget meddelande till klienten, ett detaljerat felmeddelande till klienten eller ett detaljerat felmeddelande endast till localhost. Taggen `<customErrors>` i web.config har tre lägen:</p><ul><li>**På:** Anger att anpassade fel är aktiverade. Om inget defaultRedirect-attribut har angetts ser användarna ett allmänt fel. De anpassade felen visas för fjärrklienterna och för den lokala värden</li><li>**Av:** Anger att anpassade fel är inaktiverade. De detaljerade ASP.NET felen visas för fjärrklienterna och för den lokala värden</li><li>**Fjärrdred:** Anger att anpassade fel endast visas för fjärrklienterna och att ASP.NET fel visas för den lokala värden. Det här är standardvärdet</li></ul><p>Öppna `web.config` filen för programmet/platsen och se till `<customErrors mode="RemoteOnly" />` `<customErrors mode="On" />` att taggen har antingen eller definierats.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Ange distributionsmetod till butik i IIS

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Distribution |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [distributionselement (ASP.NET inställningsschema)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Steg** | <p>Växeln `<deployment retail>` är avsedd att användas av produktions-IIS-servrar. Den här växeln används för att hjälpa program att köras med bästa möjliga prestanda och minsta möjliga säkerhetsinformationsläckage genom att inaktivera programmets förmåga att generera spårningsutdata på en sida, vilket inaktiverar möjligheten att visa detaljerade felmeddelanden till slutanvändare och inaktivera felsökningsväxeln.</p><p>Ofta aktiveras växlar och alternativ som är utvecklarfokuserade, till exempel spårning av misslyckade begäranden och felsökning, under aktiv utveckling. Distributionsmetoden på alla produktionsserverr bör anges till återförsäljarberäkning. öppna filen machine.config och `<deployment retail="true" />` se till att den förblir inställd på true.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Undantag bör misslyckas på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Misslyckas på ett säkert sätt](https://www.owasp.org/index.php/Fail_securely) |
| **Steg** | Programmet bör misslyckas på ett säkert sätt. Alla metoder som returnerar ett booleskt värde, baserat på vilket visst beslut fattas, bör ha undantagsblock noggrant skapat. Det finns många logiska fel på grund av vilka säkerhetsproblem kryper in, när undantagsblocket skrivs slarvigt.|

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
Ovanstående metod returnerar alltid Sant, om något undantag inträffar. Om slutanvändaren tillhandahåller en felaktig webbadress, som webbläsaren respekterar, men `Uri()` konstruktorn inte gör det, kommer detta att utlösa ett undantag och offret kommer att tas till den giltiga men missbildade webbadressen. 
