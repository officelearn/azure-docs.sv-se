---
title: Lägga till ett CDN i en Azure App Service | Microsoft Docs
description: Lägg till ett Content Delivery Network (CDN) i en Azure App Service för att cachelagra och leverera statiska filer från servrar nära dina kunder runtom i världen.
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 131424b4a09beeca7c2e318a04e454bea7dab6a7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>Självstudier: Lägga till ett Content Delivery Network (CDN) i en Azure App Service

[Azure CDN (Content Delivery Network)](../cdn/cdn-overview.md) cachelagrar statiskt webbinnehåll på strategiskt placerade platser för att leverera innehåll till användarna med maximalt dataflöde. CDN minskar även serverbelastningen på din webbapp. I den här självstudiekursen får du veta hur du lägger till Azure CDN till en [webbapp i Azure App Service](app-service-web-overview.md). 

Här är startsidan för den statiska exempel-HTML-platsen som du kommer att arbeta med:

![Exempelstartsida för app](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt.
> * Uppdatera cachelagrade tillgångar.
> * Använda frågesträngar för att styra cachelagrade versioner.
> * Använda en anpassad domän för CDN-slutpunkten.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- [Installera Git](https://git-scm.com/)
- [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Skapa webbappen

För att skapa webbappen som du ska arbeta med följer du [snabbstarten för statisk HTML](app-service-web-get-started-html.md) via steget **Bläddra till appen**.

### <a name="have-a-custom-domain-ready"></a>Ha ett anpassat domän redo

För att slutföra det anpassade domänsteget i den här självstudien måste du äga en anpassad domän och ha tillgång till DNS-registret för domänleverantören. För att till exempel lägga till DNS-poster för `contoso.com` och `www.contoso.com` måste du ha tillgång till att konfigurera DNS-inställningarna för rotdomänen `contoso.com`.

Om du inte redan har ett domännamn kan du följa [självstudien för App Service-domän](custom-dns-web-site-buydomains-web-app.md) för att köpa en domän med Azure Portal. 

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Skapa en CDN-profil och en slutpunkt

I det vänstra navigeringsfönstret väljer du **App Services** och sedan väljer du den app som du skapade i [static HTML quickstart](app-service-web-get-started-html.md) (snabbstart för statisk HTML).

![Välj App Service-app i portalen](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

På sidan **App Service** i avsnittet **Inställningar** väljer du **Networking > Configure Azure CDN for your app** (Nätverk > Konfigurera Azure CDN för din app).

![Välj CDN i portalen](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

På sidan **Azure Content Delivery Network** anger du inställningarna för**Ny slutpunkt** som anges i tabellen.

![Skapa en profil och en slutpunkt i portalen](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Inställning | Föreslaget värde | Beskrivning |
| ------- | --------------- | ----------- |
| **CDN-profil** | myCDNProfile | Välj **Skapa ny** för att skapa en CDN-profil. En CDN-profil är en samling CDN-slutpunkter på samma prisnivå. |
| **prisnivå** | Standard Akamai | [Prisnivån](../cdn/cdn-overview.md#azure-cdn-features) anger providern och funktioner som är tillgängliga. I den här självstudien använder vi Standard Akamai. |
| **CDN-slutpunktsnamn** | Vilket namn som helst som är unikt för domänen azureedge.net | Du kommer åt dina cachelagrade resurser i domänen *\<endpointname>.azureedge.net*.

Välj **Skapa**.

Azure skapar en profil och en slutpunkt. Den nya slutpunkten visas i listan **Slutpunkter** på samma sida och när den har etablerats är statusen **Körs**.

![Ny slutpunkt i listan](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testa CDN-slutpunkten

Om du har valt Verizon-prisnivån tar det vanligtvis cirka 90 minuter för slutpunktsspridning. Det tar några minuter för spridning för Akamai

Exempelappen har en `index.html`-fil och *css*, *img*, och *js*-mappar som innehåller andra statiska tillgångar. Sökvägarna till innehållet för alla de här filerna är samma i CDN-slutpunkten. Båda följande URL:er har till exempel åtkomst till filen *bootstrap.css* i mappen *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Öppna en webbläsare och navigera till följande adress:

```
http://<endpointname>.azureedge.net/index.html
```

![Exempelstartsida för app som hämtats från CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Du ser samma sida som du körde tidigare i en Azure-webbapp. Azure CDN har hämtat den ursprungliga webbappens tillgångar och fungerar som värd för dem från CDN-slutpunkten

Uppdatera sidan för att se till att den cachelagras i CDN. Ibland krävs två begäranden för samma tillgång för att CDN ska cachelagra det begärda innehållet.

Mer information om hur du skapar Azure CDN-profiler och slutpunkter finns i [Komma igång med Azure CDN](../cdn/cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Rensa CDN

CDN uppdaterar regelbundet dess resurser från den ursprungliga webbappen baserat på konfigurationen för TTL (time to live). Standard-TTL är sju dagar.

Ibland kan du behöva uppdatera CDN innan TTL upphör, exempelvis när du distribuerar uppdaterat innehåll till webbappen. För att utlösa en uppdatering kan du rensa CDN-resurser manuellt. 

I det här avsnittet av självstudien distribuerar du en ändring till webbappen och rensar CDN för att utlösa en uppdatering av cache i CDN.

### <a name="deploy-a-change-to-the-web-app"></a>Distribuera en ändring till webbappen

Öppna filen `index.html` och lägg till "- V2" till rubriken H1, enligt vad som visas i följande exempel: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Spara ändringen och distribuera den till webbappen.

```bash
git commit -am "version 2"
git push azure master
```

När distributionen är färdig bläddrar du till webbappens URL där du kan se ändringen.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 i rubriken i webbappen](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Om du bläddrar till URL:en för CDN-slutpunkten för startsidan så ser du inte ändringen eftersom den cachelagrade versionen i CDN inte har upphört ännu. 

```
http://<endpointname>.azureedge.net/index.html
```

![Ingen V2 i rubriken i CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Rensa CDN i portalen

Rensa CDN för att utlösa uppdateringen av den cachelagrade versionen av CDN.

I det vänstra navigeringsfönstret i portalen väljer du **Resursgrupper**, och sedan väljer du den resursgrupp som du skapade för din webbapp (myResourceGroup).

![Välj resursgrupp](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Välj din CDN-slutpunkt i listan över resurser.

![Välj slutpunkt](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

Längst upp på sidan **Slutpunkt** klickar du på **Rensa**.

![Välj Rensa](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Ange sökvägarna till innehållet som du vill ta bort. Du kan skicka en fullständig filsökväg för att rensa en enskild fil eller ett vägsegment för att rensa och uppdatera allt innehåll i en viss mapp. Eftersom du ändrade `index.html`, se till att det är en av sökvägarna.

Välj **Rensa** längst ned på sidan.

![Rensa sida](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Bekräfta att CDN har uppdaterats

Vänta till bearbetningen av rensningsbegäran slutförs, det tar vanligtvis några minuter. Välj klockikonen längst upp på sidan för att se aktuell status. 

![Rensningsavisering](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Bläddra till URL:en för CDN-slutpunkten för `index.html` och nu ser du den V2 som du lade till i titeln på startsidan. Det här visar att CDN-cachen har uppdaterats.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 i rubriken i CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

Mer information finns i [Purge an Azure CDN endpoint](../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt). 

## <a name="use-query-strings-to-version-content"></a>Använda frågesträngar för versioninnehåll

Azure CDN erbjuder följande beteendealternativ för cachelagring:

* Ignorera frågesträngar
* Kringgå cachelagring för frågesträngar
* Cachelagra varje unik URL 

Det första av dessa alternativ är standard, vilket innebär att det endast finns en cachelagrad version av en tillgång, oberoende av frågesträngen i webbadressen. 

I det här avsnittet i självstudien kan du ändra beteendet för cachelagring för att cachelagra alla unika URL:er.

### <a name="change-the-cache-behavior"></a>Ändra beteendet för cachelagring

På sidan**CDN-slutpunkt** i Azure Portal väljer du **Cache**.

Välj **Cachelagra varje unik URL** i listrutan för **Beteende för cachelagring av frågesträngar**.

Välj **Spara**.

![Välj beteende för cachelagring av frågesträngar](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Kontrollera att unika URL:er cachelagras separat

Gå till startsidan i CDN-slutpunkten i en webbläsare, men inkludera en frågesträng: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

CDN returnerar det aktuella webbappsinnehållet som innehåller "V2" i rubriken. 

Uppdatera sidan för att se till att den cachelagras i CDN. 

Öppna `index.html` och ändra "V2" till "V3" och distribuera ändringen. 

```bash
git commit -am "version 3"
git push azure master
```

Gå till CDN-slutpunktens URL i en webbläsare med en ny frågesträng som `q=2`. CDN hämtar den aktuella `index.html`-filen och visar "V3".  Men om du går till CDN-slutpunkten med frågesträngen `q=1` visas "V2".

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 i rubriken i CDN, frågesträng 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 i rubriken i CDN, frågesträng 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Det här resultatet visar att varje frågesträng behandlas olika:

* q=1 användes innan, så cachelagrat innehåll returneras (V2).
* q=2 är nytt, så det senaste webbappinnehållet hämtas och returneras (V3).

Mer information finns i [Kontrollera cachelagringsbeteendet med frågesträngar](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Mappa en anpassad domän till en CDN-slutpunkt

Du mappar den anpassade domänen till CDN-slutpunkten genom att skapa en CNAME-post. En CNAME-post är en DNS-funktion som mappar en källdomän till en måldomän. Du kan till exempel mappa `cdn.contoso.com` eller `static.contoso.com` till `contoso.azureedge.net`.

Om du inte redan har en anpassad domän kan du följa [självstudien för App Service-domän](custom-dns-web-site-buydomains-web-app.md) för att köpa en domän med Azure Portal. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Hitta värdnamnet du ska använda med CNAME

På sidan **Slutpunkt** i Azure Portal kontrollerar du att **Översikt** är markerat i det vänstra navigeringsfönstret och sedan väljer du knappen **+ Anpassad domän** längst upp på sidan.

![Välj Lägg till en anpassad domän](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

På sidan **Lägg till en anpassad domän** kan du se värdnamnet för slutpunkten som används för att skapa en CNAME-post. Värdnamnet är härlett från din URL för CDN-slutpunkten: **&lt;EndpointName>.azureedge.net**. 

![Lägg till domänsida](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Konfigurera CNAME med din domänregistrator

Gå till din domänregistrators webbplats och leta upp avsnittet för att skapa DNS-poster. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.

Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden CNAME, Alias eller Underdomäner.

Skapa en CNAME-post som matchar den valda underdomänen (till exempel **statisk** eller **cdn**) till det **Värdnamn för slutpunkt** som visades tidigare i portalen. 

### <a name="enter-the-custom-domain-in-azure"></a>Ange det anpassade domänet i Azure

Gå tillbaka till sidan **Lägg till en anpassad domän** och ange din anpassade domän, inklusive underdomänen, i dialogrutan. Ange till exempel `cdn.contoso.com`.   
   
Azure verifierar att domännamnet som du har angett har en CNAME-post. Om CNAME är korrekt verifieras din anpassade domän.

Det kan ta tid för CNAME-posten att spridas till namnservrarna på Internet. Om domänen inte verifieras omedelbart väntar du en stund och försöker igen.

### <a name="test-the-custom-domain"></a>Testa den anpassade domänen

Gå till `index.html`-filen i en webbläsare med din anpassade domän (till exempel `cdn.contoso.com/index.html`) för att kontrollera att resultatet är samma som när du går direkt till `<endpointname>azureedge.net/index.html`.

![Exempelstartsidan för app med URL för en anpassad domän](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Mer information finns i [Map Azure CDN content to a custom domain](../cdn/cdn-map-content-to-custom-domain.md) (Mappa Azure CDN-innehåll till en anpassad domän).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt.
> * Uppdatera cachelagrade tillgångar.
> * Använda frågesträngar för att styra cachelagrade versioner.
> * Använda en anpassad domän för CDN-slutpunkten.

Lär dig hur du optimerar CDN-prestanda i följande artiklar:

> [!div class="nextstepaction"]
> [Förbättra prestandan genom att komprimera filer i Azure CDN](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Läsa in tillgångar för en Azure CDN-slutpunkt i förväg](../cdn/cdn-preload-endpoint.md)
