---
title: "Hantera förfallotiden för webbinnehåll i Azure CDN | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c207d780857a61d4b1fc0f39e6185cae67abc955
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Hantera förfallodatum för Azure Web Apps/Cloud Services, ASP.NET och IIS innehåll i Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/molntjänster, ASP.NET och IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Storage blob-tjänst](cdn-manage-expiration-of-blob-content.md)
> 
> 

Filer från en offentligt tillgänglig ursprung webbserver kan cachelagras i Azure CDN tills dess time to live (TTL) går ut.  TTL-värdet bestäms av den [ *Cache-Control* huvud](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) i HTTP-svaret från den ursprungliga servern.  Den här artikeln beskriver hur du ställer in `Cache-Control` huvuden för Azure-Webbappar, Azure Cloud Services, ASP.NET-program och Internet Information Services-platser, som är konfigurerade på samma sätt.

> [!TIP]
> Du kan välja att ange inga TTL-värde för en fil.  I det här fallet gäller Azure CDN automatiskt en standard-TTL sju dagar.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till filer och andra resurser finns i [översikt över Azure CDN](cdn-overview.md).
> 
> 

## <a name="setting-cache-control-headers-in-configuration"></a>Inställningen Cache-Control huvuden i konfigurationen
För statiskt innehåll, till exempel bilder och formatmallar, kan du styra uppdateringsfrekvensen genom att ändra den **applicationHost.config** eller **web.config** filer för ditt webbprogram.  Den **system.webServer\staticContent\clientCache** element i konfigurationsfilen anger den `Cache-Control` -huvud för ditt innehåll. För **web.config**, konfigurationsinställningarna påverkar allt i mappen och alla undermappar såvida åsidosätts på nivån undermappen.  Du kan exempelvis ange en standard time to live rot ha alla statiskt innehåll som cachelagrats för 3 dagar, men har en undermapp som har fler variabeln innehåll med en cache-inställningen 6 timmar.  För **applicationHost.config**, alla program på platsen kommer att påverkas, men kan åsidosättas i **web.config** filer i program.

Följande XML visar ett exempel på hur **clientCache** att ange en maximal ålder för 3 dagar:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Ange **UseMaxAge** lägger till en `Cache-Control: max-age=<nnn>` huvudet i svaret baserat på värdet som anges i den **CacheControlMaxAge** attribut. Formatet för timespan är för den **cacheControlMaxAge** attributet <days>.<hours>:<min>:<sec>. Mer information om den **clientCache** nod, se [klientcachen <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Inställningen Cache-Control huvuden i koden
För ASP.NET-program kan du ange CDN cachelagring av frågesträngar via programmering genom att ange den **HttpResponse.Cache** egenskapen. Mer information om den **HttpResponse.Cache** egenskap, se [HttpResponse.Cache egenskapen](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) och [HttpCachePolicy klassen](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Om du vill programmässigt cache-innehåll i ASP.NET, kontrollera att innehållet är cachelagringsbara genom att ange HttpCacheability *offentliga*. Kontrollera också att en cache-verifieraren har angetts. Cache-verifieraren kan vara en senast ändrad tidsstämpel som anropar SetLastModified eller ett etag-värde som angetts genom att anropa SetETag. Alternativt kan du kan även ange en cache-förfallotid genom att anropa SetExpires eller du förlita dig på standard cache heuristik som beskrivits tidigare i det här dokumentet.  

Till exempel cache innehållet för en timme, lägga till följande:  

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

