---
title: "Hantera förfallotiden för webbinnehåll i Azure innehåll Delivery Network | Microsoft Docs"
description: "Lär dig hur du hanterar du förfallodatum för Azure Web Apps/Cloud Services, ASP.NET och IIS innehållet i Azure CDN."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: dca6ca5f21f4a4f1701af57eb40d92094b6a4754
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Hantera förfallotiden för webbinnehåll i Azure innehåll Delivery Network
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Filer från en offentligt tillgänglig ursprung webbserver kan cachelagras i Azure Content Delivery Network (CDN) tills deras time to live (TTL) går ut. TTL-värdet bestäms av den `Cache-Control` rubriken i HTTP-svaret från den ursprungliga servern. Den här artikeln beskriver hur du ställer in `Cache-Control` huvuden för funktionen Web Apps i Microsoft Azure App Service, Azure Cloud Services, ASP.NET-program och platser för Internet Information Services (IIS), som är konfigurerade på samma sätt. Du kan ange den `Cache-Control` rubrik genom att använda konfigurationsfiler eller programmässigt. 

Du kan också styra inställningar för cachelagring i Azure Portal genom att ange [CDN cachelagring regler](cdn-caching-rules.md). Om du ställer in en eller flera cachelagring regler och ange sina cachelagringsbeteendet till **åsidosätta** eller **kringgå cache**, de angivna ursprung inställningar för cachelagring i den här artikeln ignoreras. Information om allmänna cachelagring begrepp finns [hur cachelagring fungerar](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att ange inga TTL-värde för en fil. I det här fallet gäller Azure CDN automatiskt standard TTL-värde på sju dagar, om du har ställt in cachelagring regler i Azure-portalen. Den här standardinställningen TTL-värde gäller enbart för Internet leverans optimeringar. Standard-TTL är en dag för stora filer optimeringar och för direktuppspelning av optimeringar, standard TTL-värde är ett år.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till filer och andra resurser finns [översikt över Azure innehållsleveransnätverk](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Att Cache-Control-huvuden med konfigurationsfiler
För statiskt innehåll, till exempel bilder och formatmallar, kan du styra uppdateringsfrekvensen genom att ändra den **applicationHost.config** eller **Web.config** konfigurationsfiler för webbprogram. Den `<system.webServer>/<staticContent>/<clientCache>` element i antingen filuppsättningar den `Cache-Control` huvud för ditt innehåll.

### <a name="using-applicationhostconfig-files"></a>Med hjälp av ApplicationHost.config filer
Den **ApplicationHost.config** filen är konfigurationssystemet för IIS-rotfilen. Konfigurationsinställningarna i en **ApplicationHost.config** filen påverkar alla program på platsen, men åsidosätts av inställningarna för någon **Web.config** filer som finns för ett webbprogram.

### <a name="using-webconfig-files"></a>Med hjälp av Web.config-filer
Med en **Web.config** fil, kan du anpassa hur fungerar webbprogrammet hela eller en specifik katalog på ditt webbprogram. Normalt har minst en **Web.config** filen i rotmappen för ditt webbprogram. För varje **Web.config** fil i en viss mapp konfigurationsinställningarna påverkar allt i den mappen och dess undermappar, såvida de åsidosätts på nivån undermapp av en annan **Web.config**fil. Du kan till exempel ange en `<clientCache>` element i en **Web.config** filen i rotmappen för ditt webbprogram till att cachelagra alla statiskt innehåll på ditt webbprogram för tre dagar. Du kan också lägga till en **Web.config** fil i en undermapp med mer variabeln innehåll (till exempel `\frequent`) och ange dess `<clientCache>` elementet så att cachelagra innehåll för undermappens under sex timmar. Resultatet är att innehållet på hela webbplatsen cachelagras i tre dagar, förutom allt innehåll i den `\frequent` directory som cachelagras i endast sex timmar.  

I följande XML-exempel visas hur du ställer in den `<clientCache>` element i en konfigurationsfil för att ange en maximal ålder för tre dagar:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Att använda den **cacheControlMaxAge** attribut, måste du ange värdet för den **cacheControlMode** attributet `UseMaxAge`. Den här inställningen orsakade HTTP-huvud och direktiv `Cache-Control: max-age=<nnn>`, som ska läggas till svaret. Formatet för timespan-värde för den **cacheControlMaxAge** attributet `<days>.<hours>:<min>:<sec>`. Värdet konverteras till sekunder och används som värde för den `Cache-Control` `max-age` direktiv. Mer information om den `<clientCache>` element, se [klientcachen <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Ange Cache-Control huvuden programmässigt
För ASP.NET-program som du styr CDN cachelagring av frågesträngar via programmering genom att ange den **HttpResponse.Cache** egenskap för .NET-API. Information om den **HttpResponse.Cache** egenskap, se [HttpResponse.Cache egenskapen](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) och [HttpCachePolicy klassen](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Följ dessa steg för att programmatiskt cache programinnehåll i ASP.NET:
   1. Kontrollera att innehållet är cachelagringsbara genom att ange `HttpCacheability` till `Public`. 
   2. Ange en cache-verifieraren genom att anropa en av följande `HttpCachePolicy` metoder:
      - Anropa `SetLastModified` att ange ett tidsstämpelvärde för den `Last-Modified` rubrik.
      - Anropa `SetETag` och ange ett värde för den `ETag` rubrik.
   3. Du kan också ange en cache-förfallotid genom att anropa `SetExpires` och ange ett värde för den `Expires` rubrik. I annat fall gäller standard cache heuristik som beskrivs ovan i det här dokumentet.

Till exempel till cachen innehållet för en timme, lägger du till följande C#-kod:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testa Cache-Control-huvudet
Du kan enkelt kontrollera TTL-inställningarna för ditt webbinnehåll. Med din webbläsare [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test som webbinnehållet innehåller den `Cache-Control` Svarsrubrik. Du kan också använda ett verktyg som **wget**, [Postman](https://www.getpostman.com/), eller [Fiddler](http://www.telerik.com/fiddler) att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Läs mer information om den **clientCache** element](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Läs i dokumentationen för den **HttpResponse.Cache** egenskapen](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Läs i dokumentationen för den **HttpCachePolicy-klass**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Lär dig mer om cachelagring begrepp](cdn-how-caching-works.md)
