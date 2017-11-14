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
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Hantera förfallotiden för webbinnehåll i Azure innehåll Delivery Network
 i Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/molntjänster, ASP.NET och IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Filer från en offentligt tillgänglig ursprung webbserver kan cachelagras i Azure Content Delivery Network (CDN) tills deras time to live (TTL) går ut. TTL-värdet bestäms av den [ `Cache-Control` huvud](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) i HTTP-svaret från den ursprungliga servern. Den här artikeln beskriver hur du ställer in `Cache-Control` huvuden för funktionen Web Apps i Microsoft Azure App Service, Azure Cloud Services, ASP.NET-program och Internet Information Services-platser, som är konfigurerade på samma sätt.

> [!TIP]
> Du kan välja att ange inga TTL-värde för en fil. I det här fallet gäller Azure CDN automatiskt en standard-TTL sju dagar.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till filer och andra resurser finns [översikt över Azure innehållsleveransnätverk](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Inställningen Cache-Control huvuden i konfigurationsfiler
För statiskt innehåll, till exempel bilder och formatmallar, kan du styra uppdateringsfrekvensen genom att ändra den **applicationHost.config** eller **web.config** filer för ditt webbprogram. Den **system.webServer\staticContent\clientCache** element i filen konfigurationsuppsättningar den `Cache-Control` huvud för ditt innehåll. För **web.config** filer, konfigurationsinställningarna påverkar allt i mappen och dess undermappar, såvida de åsidosätts på nivån undermappen. Du kan till exempel ange en standardinställning för TTL-värde i rotmappen för att cachelagra innehåll för alla statiska tre dagar och ange en undermapp med mer variabeln innehåll till att cachelagra innehåll för endast sex timmar. Även om **applicationHost.config** inställningar påverkar alla program på platsen, de åsidosätts av inställningarna för befintliga **web.config** filer i program.

I följande XML-exempel visas hur du ställer in **clientCache** att ange en maximal ålder för tre dagar:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Ange **UseMaxAge** orsakar en `Cache-Control: max-age=<nnn>` rubrik som ska läggas till i svar baserat på värdet som anges i den **CacheControlMaxAge** attribut. Formatet för timespan för den **cacheControlMaxAge** attributet `<days>.<hours>:<min>:<sec>`. Mer information om den **clientCache** nod, se [klientcachen <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Inställningen Cache-Control huvuden i koden
För ASP.NET-program kan du styra CDN cachelagring av frågesträngar via programmering genom att ange den **HttpResponse.Cache** egenskapen. Mer information om den **HttpResponse.Cache** egenskap, se [HttpResponse.Cache egenskapen](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) och [HttpCachePolicy klassen](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Följ dessa steg för att programmatiskt cache programinnehåll i ASP.NET:
   1. Kontrollera att innehållet är cachelagringsbara genom att ange `HttpCacheability` till *offentliga*. 
   2. Ange en cache-verifieraren genom att anropa en av följande metoder:
      - Anropa `SetLastModified` att ställa in en LastModified tidsstämpel.
      - Anropa `SetETag` att ställa in en `ETag` värde.
   3. Du kan också ange en cache-förfallotid genom att anropa `SetExpires`. I annat fall gäller standard cache heuristik som beskrivs ovan i det här dokumentet.

Till exempel till cachen innehållet för en timme, lägger du till följande C#-kod:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Nästa steg
* [Läs mer information om den **clientCache** element](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Läs i dokumentationen för den **HttpResponse.Cache** egenskapen](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Läs i dokumentationen för den **HttpCachePolicy klassen**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

