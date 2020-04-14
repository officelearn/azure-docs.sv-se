---
title: Hantera förfallodatum för webbinnehåll i Azure CDN | Microsoft-dokument
description: Lär dig hur du hanterar förfallodatum för Azure Web Apps/Cloud Services, ASP.NET eller IIS-innehåll i Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: 4598e6cee6ffbaaeb2a99727842fcd17fe0046c7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260572"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Hantera utgång av webbinnehåll i Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Filer från webbservrar med allmänt tillgängligt ursprung kan cachelagras i CDN (Azure Content Delivery Network) tills deras tid att vara (TTL) förflutet. TTL bestäms av `Cache-Control` huvudet i HTTP-svaret från ursprungsservern. I den hÃ¤r artikeln beskrivs hur du anger `Cache-Control` rubriker fÃ¤r webbappfunktionen fÃ¤r Microsoft Azure App Service, Azure Cloud Services, ASP.NET applications och Internet Information Services (IIS) sites, som alla är konfigurerade på samma sätt. Du kan `Cache-Control` ställa in huvudet antingen med hjälp av konfigurationsfiler eller programmässigt. 

Du kan också styra cacheinställningarna från Azure-portalen genom att ange [CDN-cachelagringsregler](cdn-caching-rules.md). Om du skapar en eller flera cachelagringsregler och anger att cachelagringsbeteendet för **cachelagring åsidosätts** eller **kringgås**ignoreras cacheinställningarna för cachelagring av ursprung som beskrivs i den här artikeln. Information om allmänna cachelagringsbegrepp finns i [Så här fungerar cachelagring](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att inte ange någon TTL på en fil. I det här fallet tillämpar Azure CDN automatiskt en standard-TTL på sju dagar, såvida du inte har ställt in cachelagringsregler i Azure-portalen. Den här standard-TTL gäller endast för allmänna webbleveransoptimeringar. För stora filoptimeringar är standardTL en dag, och för optimering av media är standardTL ett år.
> 
> Mer information om hur Azure CDN arbetar för att öka [åtkomsten](cdn-overview.md)till filer och andra resurser finns i Översikt över Azure Content Delivery Network .
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ange cachekontrollrubriker med hjälp av CDN-cachelagringsregler
Den metod som föredras för `Cache-Control` att ange en webbservers huvud är att använda cachelagringsregler i Azure-portalen. Mer information om CDN-cachelagringsregler finns i [Kontrollera Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md).

> [!NOTE] 
> Cachelagringsregler är endast tillgängliga för **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profiler.** För **Azure CDN Premium från Verizon-profiler** måste du använda [Azure CDN-regelmotorn](cdn-rules-engine.md) i **hantera-portalen** för liknande funktioner.

**Så här navigerar du till cdn-cachelagringsreglerna:**

1. I Azure-portalen väljer du en CDN-profil och väljer sedan slutpunkten för webbservern.

1. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN-cachelagringsregler](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Cdn-cachelagringssida](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Så här anger du en webbservers cachekontrollhuvuden med hjälp av globala cachelagringsregler:**

1. Under **Globala cachelagringsregler**anger du **frågesträngcachelagringsbeteende** till **Ignorera frågesträngar** och ange **cachelagringsbeteende** till **Åsidosättning**.
      
1. Ange 3600 i rutan **Sekunder** eller 1 i rutan **Timmar** för att gälla för **slutdatum**för cache. 

   ![Exempel på globala cachelagringsregler för CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Den här globala cachelagringsregeln anger en cachevaraktighet på en timme och påverkar alla begäranden till slutpunkten. Den åsidosätter `Cache-Control` `Expires` alla eller HTTP-huvuden som skickas av ursprungsservern som anges av slutpunkten.   

1. Välj **Spara**.

**Så här anger du cachekontrollhuvudena för en webbserverfil med hjälp av anpassade cachelagringsregler:**

1. Skapa två matchningsvillkor under **Anpassade cachelagringsregler:**

     a. För det första matchningsvillkoret anger `/webfolder1/*` du Matcha **villkor** till **Sökväg** och anger för Matcha **värde**. Ange **cachelagringsbeteendet** för **åsidosättning** och ange 4 i rutan **Timmar.**

     b. För det andra matchningsvillkoret anger `/webfolder1/file1.txt` du **Matchningsvillkor** till **Sökväg** och anger för Matcha **värde**. Ange **cachelagringsbeteendet** för **åsidosättning** och ange 2 i rutan **Timmar.**

    ![Exempel på anpassade cachelagringsregler för CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Den första anpassade cachelagringsregeln anger en cachevaraktighet på fyra timmar för alla filer i `/webfolder1` mappen på ursprungsservern som anges av slutpunkten. Den andra regeln åsidosätter den `file1.txt` första regeln för filen och anger en cachevaraktighet på två timmar för den.

1. Välj **Spara**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Ange cachekontrollhuvuden med hjälp av konfigurationsfiler
För statiskt innehåll, till exempel bilder och formatmallar, kan du styra uppdateringsfrekvensen genom att ändra **konfigurationsfilerna applicationHost.config** eller **Web.config** för webbprogrammet. Om du `Cache-Control` vill ange sidhuvudet för innehållet använder du elementet `<system.webServer>/<staticContent>/<clientCache>` i någon av filerna.

### <a name="using-applicationhostconfig-files"></a>Använda ApplicationHost.config-filer
**Filen ApplicationHost.config** är rotfilen i IIS-konfigurationssystemet. Konfigurationsinställningarna i en **ApplicationHost.config-fil** påverkar alla program på webbplatsen, men åsidosätts av inställningarna för alla **Web.config-filer** som finns för ett webbprogram.

### <a name="using-webconfig-files"></a>Använda Web.config-filer
Med en **Web.config-fil** kan du anpassa hela webbprogrammet eller en viss katalog i webbprogrammet. Vanligtvis har du minst en **Web.config-fil** i webbprogrammets rotmapp. För varje **Web.config-fil** i en viss mapp påverkar konfigurationsinställningarna allt i mappen och dess undermappar, såvida de inte åsidosätts på undermappsnivå av en annan **Web.config-fil.** 

Du kan till exempel `<clientCache>` ange ett element i en **Web.config-fil** i webbprogrammets rotmapp för att cachelagra allt statiskt innehåll i webbprogrammet i tre dagar. Du kan också lägga till en **Web.config-fil** i en `\frequent`undermapp med `<clientCache>` mer variabelt innehåll (till exempel) och ange att elementet ska cachelagra undermappens innehåll i sex timmar. Nettoresultatet är att innehållet på hela webbplatsen cachelagras i tre dagar, med undantag för innehåll i katalogen, `\frequent` som cachelagras i endast sex timmar.  

I följande exempel på XML-konfigurationsfil visas hur elementet `<clientCache>` ska ange en maximal ålder på tre dagar:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Om du vill använda attributet **cacheControlMaxAge** måste du ange `UseMaxAge`värdet för **attributet cacheControlMode** till . Den här inställningen gjorde att `Cache-Control: max-age=<nnn>`HTTP-huvudet och -direktivet lades till i svaret. Formatet för tidsspannet för attributet `<days>.<hours>:<min>:<sec>` **cacheControlMaxAge** är . Dess värde konverteras till sekunder och används `Cache-Control` `max-age` som direktivets värde. Mer information om `<clientCache>` elementet finns i [Client Cache \<clientCache>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Ställa in cache-kontrollrubriker programmässigt
För ASP.NET-program styr du CDN-cachelagringsbeteendet programmässigt genom att ange egenskapen **HttpResponse.Cache** för .NET API. Information om egenskapen **HttpResponse.Cache** finns i [egenskapen HttpResponse.Cache](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) och [klassen HttpCachePolicy](/dotnet/api/system.web.httpcachepolicy).  

Så här cachelagrar du programinnehåll i ASP.NET programmatiskt:
   1. Kontrollera att innehållet är markerat `HttpCacheability` som `Public`cachelagbart genom att ange . 
   1. Ange en cache validator genom `HttpCachePolicy` att anropa någon av följande metoder:
      - Anrop `SetLastModified` för att ange ett `Last-Modified` tidsstämpelvärde för huvudet.
      - Anrop `SetETag` för att `ETag` ange ett värde för huvudet.
   1. Du kan också ange en `SetExpires` förfallotid för `Expires` cache genom att anropa för att ange ett värde för huvudet. Annars gäller standardcacheweuriserna som beskrivits tidigare i det här dokumentet.

Om du till exempel vill cachelagra innehåll i en timme lägger du till följande C#-kod:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testa cachekontrollhuvudet
Du kan enkelt verifiera TTL-inställningarna för webbinnehållet. Med webbläsarens [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)testar du att webbinnehållet innehåller svarshuvudet. `Cache-Control` Du kan också använda ett verktyg som **wget**, [Postman](https://www.getpostman.com/)eller [Fiddler](https://www.telerik.com/fiddler) för att undersöka svarsrubrikerna.

## <a name="next-steps"></a>Efterföljande moment
* [Läs mer om **elementet clientCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Läs dokumentationen för egenskapen **HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Läs dokumentationen för **klassen HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Lär dig mer om cachelagringskoncept](cdn-how-caching-works.md)
