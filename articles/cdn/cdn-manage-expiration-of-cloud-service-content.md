---
title: Hanterar du förfallodatum för webbinnehåll i Azure CDN | Microsoft Docs
description: Lär dig hur du hanterar du förfallodatum för Azure Web Apps/Cloud Services, ASP.NET eller IIS-innehåll i Azure CDN.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: 6e17b110cbfc293e19714399d5b2cdb753aa1ac4
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917965"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Hanterar du förfallodatum för webbinnehåll i Azure CDN
> [!div class="op_single_selector"]
> * [Azure-webbinnehåll](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Filer från offentligt tillgänglig ursprung webbservrar kan cachelagras i Azure Content Delivery Network (CDN) tills det ska gå att deras time to live (TTL). TTL-Perioden bestäms av den `Cache-Control` rubriken i HTTP-svaret från den ursprungliga servern. Den här artikeln beskriver hur du ställer in `Cache-Control` rubriker för funktionen Web Apps i Microsoft Azure App Service, Azure Cloud Services, ASP.NET-program och webbplatser i Internet Information Services (IIS), som är konfigurerade på samma sätt. Du kan ange den `Cache-Control` sidhuvud antingen med hjälp av konfigurationsfiler eller programmässigt. 

Du kan också styra inställningar för cachelagring i Azure Portal genom att ange [CDN-cachelagringsregler](cdn-caching-rules.md). Om du skapar en eller flera cachelagring regler och ange deras cachelagringsbeteendet till **åsidosätta** eller **kringgå cache**, ignoreras de angivna ursprunget cachelagringsinställningarna som beskrivs i den här artikeln. Information om allmänna begrepp för cachelagring finns i [så här fungerar cachelagring](cdn-how-caching-works.md).

> [!TIP]
> Du kan välja att ange inga TTL för en fil. I det här fallet gäller Azure CDN automatiskt en standard-TTL på sju dagar, såvida inte du har konfigurerat cachelagringsregler i Azure-portalen. Det här standardvärdet TTL gäller enbart för allmän leverans optimeringar. Standard-TTL är en dag för optimering av stora filer, och för direktuppspelning optimeringar, standard-TTL är ett år.
> 
> Mer information om hur Azure CDN fungerar för att påskynda åtkomst till filer och andra resurser finns i [översikt över Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ange Cache-Control-huvuden genom att använda CDN-cachelagringsregler
Den bästa metoden för att ställa in en webbserver `Cache-Control` rubrik är att använda cachelagringsregler i Azure-portalen. Läs mer om CDN-cachelagringsregler [Kontrollera Cachelagringsbeteendet med cachelagringsregler](cdn-caching-rules.md).

> [!NOTE] 
> Cachelagringsregler är endast tillgängliga för **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler. För **Azure CDN Premium från Verizon** profiler, måste du använda den [Azure CDN regelmotor](cdn-rules-engine.md) i den **hantera** portal för liknande funktionalitet.

**Navigera till sidan CDN cachelagring regler**:

1. Välj en CDN-profil i Azure-portalen och väljer du slutpunkten för webbservern.

1. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![CDN-cachelagring regler knappen](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![CDN-cachelagring sidan](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Ange ett webb-serverns Cache-Control-huvuden med globala cachelagringsregler:**

1. Under **globala cachelagringsregler**anger **cachelagring av frågesträngar** till **Ignorera frågesträngar** och ange **Cachelagringsbeteende** till  **Åsidosätt**.
      
1. För **giltighetstid för Cache**, ange 3600 i den **sekunder** box eller 1 i den **timmar** box. 

   ![CDN globala cachelagring regler-exempel](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Den här globala cachelagringsregeln anger en cachevaraktighet på en timme och påverkar alla förfrågningar till slutpunkten. Åsidosätter eventuella `Cache-Control` eller `Expires` HTTP-huvuden som skickas av den ursprungliga servern som anges av slutpunkten.   

1. Välj **Spara**.

**Ange en webbserver filens Cache-Control-huvuden med hjälp av anpassade cachelagringsreglerna:**

1. Under **anpassade cachelagringsregler**, skapa två matchningsvillkor:

     a. Ange för den första matchningsvillkor **matchningsvillkor** till **sökväg** och ange `/webfolder1/*` för **matchar värdet**. Ange **Cachelagringsbeteende** till **åsidosätta** och ange 4 i den **timmar** box.

     b. För andra matchningsvillkor ställer du in **matchningsvillkor** till **sökväg** och ange `/webfolder1/file1.txt` för **matchar värdet**. Ange **Cachelagringsbeteende** till **åsidosätta** och anger 2 i den **timmar** box.

    ![CDN anpassad cachelagring regler-exempel](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Den första anpassa cachelagringsregeln anger en cachelagringens varaktighet på fyra timmar för alla filer i den `/webfolder1` mapp på den ursprungliga servern som anges av slutpunkten. Den andra regeln åsidosätter den första regeln för den `file1.txt` bara fil och anger en cachevaraktighet på två timmar för den.

1. Välj **Spara**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Att Cache-Control-huvuden med konfigurationsfiler
För statiskt innehåll som bilder och formatmallar, kan du styra uppdateringsfrekvensen genom att ändra den **applicationHost.config** eller **Web.config** konfigurationsfilerna för ditt webbprogram. Ange den `Cache-Control` rubrik för ditt innehåll, Använd den `<system.webServer>/<staticContent>/<clientCache>` element i filerna.

### <a name="using-applicationhostconfig-files"></a>Med hjälp av ApplicationHost.config filer
Den **ApplicationHost.config** filen är rotfilen av konfigurationssystemet för IIS. Konfigurationsinställningarna i en **ApplicationHost.config** filen påverkar alla program på webbplatsen, men åsidosätts av inställningarna för någon **Web.config** filer som finns för ett webbprogram.

### <a name="using-webconfig-files"></a>Med hjälp av Web.config-filer
Med en **Web.config** -fil som du kan anpassa hur programmets hela webben eller en enskild katalog på ditt webbprogram fungerar. Normalt kan du ha minst en **Web.config** filen i rotmappen för ditt webbprogram. För varje **Web.config** fil i en viss mapp konfigurationsinställningarna påverkar allt i den mappen och dess undermappar, om de inte åsidosätts på undermapp nivå av en annan **Web.config** filen. 

Du kan till exempel ange en `<clientCache>` element i en **Web.config** filen i rotmappen för ditt webbprogram för att cachelagra alla statiskt innehåll på ditt webbprogram för tre dagar. Du kan också lägga till en **Web.config** fil i en undermapp med mer variabeln innehåll (till exempel `\frequent`) och Ställ in dess `<clientCache>` elementet cachelagra undermappens innehåll under sex timmar. Resultatet är att innehåll på hela webbplatsen cachelagras i tre dagar, utom allt innehåll i den `\frequent` directory som cachelagras i bara sex timmar.  

I följande XML-konfiguration-filen exempel visar hur du ställer in den `<clientCache>` elementet så att du anger en maximal ålder på tre dagar:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Du använder den **cacheControlMaxAge** attribut, måste du ange värdet för den **cacheControlMode** attributet `UseMaxAge`. Den här inställningen orsakade HTTP-huvud och direktiv, `Cache-Control: max-age=<nnn>`, som ska läggas till svaret. Formatet för timespan-värde för den **cacheControlMaxAge** attributet är `<days>.<hours>:<min>:<sec>`. Värdet som konverteras till sekunder och används som värde för den `Cache-Control` `max-age` direktiv. Mer information om den `<clientCache>` element, se [klientcachen <clientCache> ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Ange Cache-Control-huvuden programmässigt
För ASP.NET-program kan du styra CDN-nätverkets funktionssätt programmässigt genom att ange den **HttpResponse.Cache** egenskapen för .NET-API. Information om den **HttpResponse.Cache** egenskap, finns i [HttpResponse.Cache egenskapen](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) och [HttpCachePolicy klass](/dotnet/api/system.web.httpcachepolicy).  

Följ dessa steg för att programmässigt cache programinnehåll i ASP.NET:
   1. Kontrollera att innehållet är cachelagringsbara genom att ange `HttpCacheability` till `Public`. 
   1. Ange en cache-verifieraren genom att anropa något av följande `HttpCachePolicy` metoder:
      - Anropa `SetLastModified` att ställa in ett tidsstämpelvärde för den `Last-Modified` rubrik.
      - Anropa `SetETag` att ange ett värde för den `ETag` rubrik.
   1. Du kan också ange en förfallotid för cache genom att anropa `SetExpires` att ange ett värde för den `Expires` rubrik. Annars gäller standard cache heuristik som beskrivs tidigare i detta dokument.

Till exempel att cacheminnet content under en timme, lägger du till följande C#-kod:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testa Cache-Control-huvudet
Du kan enkelt kontrollera TTL-inställningarna för ditt webbinnehåll. Med din webbläsare [utvecklarverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test som webbinnehållet innehåller den `Cache-Control` svarshuvudet. Du kan också använda ett verktyg som **wget**, [Postman](https://www.getpostman.com/), eller [Fiddler](https://www.telerik.com/fiddler) att undersöka svarshuvuden.

## <a name="next-steps"></a>Nästa steg
* [Läs information om den **clientCache** element](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Läs i dokumentationen för den **HttpResponse.Cache** egenskap](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Läs i dokumentationen för den **HttpCachePolicy-klass**](/dotnet/api/system.web.httpcachepolicy)  
* [Lär dig mer om cachelagring begrepp](cdn-how-caching-works.md)
