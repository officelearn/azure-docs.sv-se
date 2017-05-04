---
title: "Lägga till ett nätverk för innehållsleverans i en Azure App Service | Microsoft Docs"
description: "Lägg till ett nätverk för innehållsleverans i en Azure App Service för att leverera statiska filer från kantnoder."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Lägga till ett nätverk för innehållsleverans i en Azure App Service

I den här handledningen får du lägga till ett nätverk för innehållsleverans (CDN, Content Delivery Network) till din Azure App Service för att exponera det statiska innehållet på en edge-server. Du skapar en CDN-profil som är en samling av upp till 10 CDN-slutpunkter.

Ett nätverk för innehållsleverans (CDN) cachelagrar statiskt webbinnehåll på strategiskt placerade platser för att leverera innehåll till användarna med maximalt dataflöde. Här är några exempel på fördelarna med att använda CDN för att cachelagra webbplatstillgångar:

* Bättre prestanda och upplevelse för slutanvändarna, särskilt när de använder program där flera turer krävs för att läsa in innehållet.
* Skalning för effektiv hantering av plötsliga belastningstoppar, t.ex. i början av en produktlansering.
* Genom att distribuera användarförfrågningar och hämta innehåll från edge-servrar skickas mindre trafik till ursprunget.

> [!TIP]
> Se en uppdaterad lista över [Azure CDN POP-platser](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="deploy-the-sample"></a>Distribuera exemplet

För att slutföra den här självstudien behöver du ett program som distribueras i webbappen. Följ [snabbstart för statisk HTML](app-service-web-get-started-html.md) för att ge en grund för den här självstudien.

## <a name="step-1---login-to-azure-portal"></a>Steg 1 – Logga in på Azure Portal

Öppna favoritwebbläsaren och gå till Azure [Portal](https://portal.azure.com).

## <a name="step-2---create-a-cdn-profile"></a>Steg 2 – Skapa en CDN-profil

Klicka på `+ New` i det vänstra navigeringsfältet och klicka på **Webb + mobilt**. Välj **CDN** under kategorin Webb + mobilt.

I följande fält anger du:

| Fält | Exempelvärde | Beskrivning |
|---|---|---|
| Namn | myCDNProfile | Ett namn på CDN-profilen. |
| Plats | Västra Europa | Det här är den Azure-plats där din CDN-profilinformation kommer att lagras. Den påverkar inte CDN-slutpunktsplatser. |
| Resursgrupp | myResourceGroup | Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups) |
| Prisnivå | Standard Akamai | Se [CDN-översikt](../cdn/cdn-overview.md#azure-cdn-features) för en jämförelse mellan prisnivåer. |

Klicka på **Skapa**.

Öppna resursgruppshubben i det vänstra navigeringsfältet och välj **myResourceGroup**. Välj **myCDNProfile** i resurslistan.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Steg 3 – Skapa en CDN-slutpunkt

Klicka på **+ Slutpunkt** bland kommandona bredvid sökrutan så att bladet för att skapa slutpunkten öppnas.

I följande fält anger du:

| Fält | Exempelvärde | Beskrivning |
|---|---|
| Namn |  | Det här namnet används för att komma åt dina cachelagrade resurser i domänen `<endpointname>.azureedge.net` |
| Ursprungstyp | Webbapp | När du väljer en ursprungstyp får du sammanhangsbaserade menyer för återstående fält. När du väljer anpassat ursprung får du ett textfält för ursprungets värdnamn. |
| Ursprungets värdnamn | |  Listrutan visar en lista över alla tillgängliga ursprung av den ursprungstyp som du angav. Om du valde Anpassat ursprung som Typ av ursprung skriver du domänen för ditt anpassade ursprung  |

Klicka på **Lägg till**.

Slutpunkten skapas. När CDN-slutpunkten har skapats uppdateras statusen till **körs**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>Steg 4 – Leverera från Azure CDN

Nu när CDN-slutpunkten **körs** ska du kunna få åtkomst till innehåll från den.

Eftersom vi har använt [statisk HTML-snabbstart](app-service-web-get-started-html.md) som grund för den här självstudien bör följande mappar vara tillgängliga på vår CDN: `css`, `img`, `js`.

Sökvägarna till innehållet mellan webbappens URL `http://<app_name>.azurewebsites.net/img/` och CDN-slutpunktens URL `http://<endpointname>.azureedge.net/img/` är desamma. Det innebär att du bara kan ersätta CDN-slutpunktens domän för att få statistiskt innehåll att hanteras från CDN.

Vi hämtar vår första avbildning från CDN-slutpunkten genom att gå till följande url i din favoritwebbläsare:

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Nu när det statistiska innehållet är tillgängligt i din CDN kan du uppdatera programmet för att använda CDN-slutpunkten för att leverera innehållet till slutanvändaren.

Beroende på vilket språk webbplatsen är byggd med kan det finnas många ramverk som kan hjälpa dig med CDN-återställning. Till exempel innehåller ASP.NET stöd för [paketering och minimering](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn) vilket också ger funktioner för CDN-återställning.

Om ditt språk inte har inbyggd eller ett bibliotek för CDN-återställning kan du använda ett javascriptramverk som [FallbackJS](http://fallback.io/), som har stöd för inläsning av [skript](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [formatmallar](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) och [bilder](https://github.com/dolox/fallback/tree/master/examples/loading-images).

## <a name="step-5---purge-the-cdn"></a>Steg 5 – Rensa CDN

Ibland är det nödvändigt att tvinga fram en CDN-rensning om du vill få innehållet att förfalla före TTL (time-to-live) förfaller.

Det går att rensa Azure CDN manuellt, antingen från CDN-profilbladet eller CDN-slutpunktsbladet. Om du väljer att rensa från profilsidan måste du välja vilken slutpunkt du vill rensa.

Om du vill rensa innehåll skriver du sökvägarna till innehållet du vill ta bort. Du kan skicka en fullständig filsökväg för att rensa en enskild fil eller ett vägsegment för att rensa och uppdatera innehållet från en viss mapp.

När du har angett alla sökvägar till innehållet du vill ta bort klickar du på **Rensa**.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>Steg 6 – Mappa en anpassad domän

Om du mappar en anpassad domän till din CDN-slutpunkt får du en enhetlig domän för din webbapp.

Skapa en CNAME-post hos din domänregistrator för att mappa en anpassad domän till CDN-slutpunkten.

> [!NOTE]
> En CNAME-post är en DNS-funktion som mappar en källdomän, som `www.contosocdn.com` eller `static.contosocdn.com`, till en måldomän.

I det här fallet lägger vi till en `static.contosocdn.com`-källdomän som pekar på måldomänen som är CDN-slutpunkten.

| källdomän | måldomän |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

Från översiktsbladet för CDN-slutpunkten klickar du på knappen `+ Custom domain`.

På bladet Lägg till en anpassad domän anger du din anpassade domän, inklusive underdomänen, i dialogrutan. Ange till exempel domännamnet i formatet `static.contosocdn.com`.

Klicka på **Lägg till**.

## <a name="step-7---version-content"></a>Steg 7 – Versionsinnehåll

I CDN-slutpunktens vänstra navigeringsfält väljer du **Cache** under rubriken Inställningar.

I **cachebladet** kan du konfigurera hur CDN ska hantera frågesträngar i begäran.

> [!NOTE]
> Om du vill ha en beskrivning av beteendealternativen för cachelagring av frågesträngar kan du läsa avsnittet [Control Azure CDN caching behavior with query strings](../cdn/cdn-query-string.md) (Kontrollera beteendealternativen för Azure CDN-cachelagring med frågesträngar).

Välj **Cachelagra varje unik URL** i listrutan för beteendet för cachelagring av frågesträngar.

Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure CDN](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [Aktivera HTTPS på en anpassad Azure CDN-domän](../cdn/cdn-custom-ssl.md)
* [Förbättra prestandan genom att komprimera filer i Azure CDN](../cdn/cdn-improve-performance.md)
* [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](../cdn/cdn-preload-endpoint.md)

