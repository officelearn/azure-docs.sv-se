---
title: Översikt över serverdelar för flera innehavare, till exempel Azure App service med Azure Application Gateway
description: Den här sidan beskriver hur du kan använda serverdelar för flera klientorganisationer med Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224506"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway stöd för flera klientorganisationer slutar som App service

I flera innehavare arkitekturritningar för servrar körs flera webbplatser på samma web server-instansen. Värdnamn används för att skilja mellan olika program som finns. Som standard ändrar inte Application Gateway den inkommande HTTP-rubriken från klienten. Rubriken skickas oförändrad till servern. Det här fungerar bra för medlemmar i serverdelspoolen, t.ex nätverkskort VM skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser och FQDN eftersom dessa inte förlita dig på en specifik värdrubrik eller ett SNI-tillägg för att matcha mot rätt slutpunkt. Men finns det många tjänster, till exempel Azure App service web apps och Azure API management som flera klientorganisationer och förlitar sig på en specifik värdrubrik eller ett SNI-tillägg för att matcha mot rätt slutpunkt. Vanligtvis är skiljer DNS-namnet på programmet, vilket i sin tur är DNS-namnet som är associerade med application gateway, sig från domännamnet för backend-tjänsten. Därför är värdhuvudet i den ursprungliga begäran tas emot av application gateway inte samma som namnet på backend-tjänsten. På grund av detta, såvida inte värdhuvudet i begäran från programgatewayen till serverdelen ändras till värdnamnet för backend-tjänsten är serverdelar för flera innehavare inte kan matcha begäran till rätt slutpunkt. 

Application gateway erbjuder en funktion där användarna kan åsidosätta HTTP-Värdrubriken i begäran baserat på namnet på serverdelen. Den här funktionen möjliggör stöd för serverdelar för flera innehavare, till exempel Azure App service-webbappar och API-hantering. Den här funktionen är tillgänglig för både v1 och v2 standard och WAF SKU: er. 

![Vara värd för åsidosättning](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Detta gäller inte för Azure App service-miljö (ASE) eftersom ASE är en resurs som är dedikerad till skillnad från Azure App service som är en resurs för flera innehavare.

## <a name="override-host-header-in-the-request"></a>Åsidosätt värdhuvudet i begäran

Möjligheten att värdåsidosättningar definieras i den [HTTP-inställningar](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) och kan tillämpas på valfri serverdelspool samband med regelgenereringen. Följande två sätt för att åsidosätta värdrubrik eller ett SNI-tillägg för serverdelar för flera innehavare stöds:

- Möjligheten att ange värdnamnet till ett fast värde som anges i HTTP-inställningarna. Den här funktionen ser till att värdhuvudet åsidosätts till det här värdet för all trafik till backend-poolen där de specifika HTTP-inställningarna används. När du använder SSL för slutpunkt till slutpunkt används det åsidosatta värdnamnet i SNI-tillägget. Den här funktionen möjliggör scenarier där en backend-poolen servergrupp förväntar sig en värdrubrik som skiljer sig från den inkommande klientvärdrubriken.

- Möjligheten att härleda värdnamnet från IP-Adressen eller FQDN för backend-poolmedlemmar. HTTP-inställningarna innehåller också ett alternativ för att dynamiskt hämta värdnamnet från medlem backend-poolen FQDN om har konfigurerats med alternativet för att härleda värdnamnet från en medlem i den enskilda backend-poolen. När SSL för slutpunkt till slutpunkt används härleds det här värdnamnet från det fullständigt kvalificerade domännamnet och används i SNI-tillägget. Den här funktionen möjliggör scenarier där en serverdelspool kan ha två eller flera flera innehavare PaaS-tjänster som Azure-webbappar och varje medlem i begäran värdhuvudet innehåller värdnamn som härletts från dess FQDN. För att genomföra det här scenariot använder vi en växel i HTTP-inställningarna som kallas [Välj värdnamnet från serverdelsadressen](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address) som åsidosätter dynamiskt värdhuvudet i den ursprungliga begäran till den som nämns i serverdelspoolen.  Till exempel om backend-pool FQDN innehåller ”contoso11.azurewebsites.net” och ”contoso22.azurewebsites.net”, kommer värdhuvud för den ursprungliga begäran som är contoso.com att åsidosättas contoso11.azurewebsites.net eller contoso22.azurewebsites.net När begäran skickas till lämplig backend-servern. 

  ![Scenario för webbappar](./media/application-gateway-web-app-overview/scenario.png)

Med den här funktionen skapar kunderna lämplig konfiguration genom att konfigurera alternativen i HTTP-inställningarna och de anpassade avsökningarna. Den här inställningen kopplas sedan till en lyssnare och en backend-poolen med hjälp av en regel.

## <a name="special-considerations"></a>Att tänka på

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>SSL-avslutning och SSL för slutpunkt till slutpunkt med tjänster med flera innehavare

Både SSL-avslutning och slutpunkt till slutpunkt SSL-kryptering stöds med tjänster med flera innehavare. För SSL-avslutning på application gateway fortsätter SSL-certifikatet att krävas som ska läggas till i application gateway-lyssnaren. Men när det gäller från slutpunkt till slutpunkt SSL, betrodda Azure-tjänster som Azure App service-webbappar inte kräver listan över tillåtna program serverdelar i application gateway. Det är därför behöver inte lägga till några autentiseringscertifikat. 

![slutpunkt till slutpunkt SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Observera att i bilden ovan finns det några krav på att lägga till certifikat för autentisering när App service är markerad som serverdel.

### <a name="health-probe"></a>Hälsoavsökning

Åsidosätta värdhuvudet i den **HTTP-inställningar** påverkar endast begäran och dess routning. det påverkar inte beteendet vid hälsoavsökningar. För att funktionen för slutpunkt till slutpunkt ska fungera måste både avsöknings- och HTTP-inställningarna ändras så att de återspeglar korrekt konfiguration. Anpassade avsökningar stöder också möjligheten att härleda värdhuvudet från de konfigurerade HTTP-inställningarna förutom att tillhandahålla möjligheten att ange en värdrubrik i avsökningskonfigurationen. Den här konfigurationen kan anges med hjälp av `PickHostNameFromBackendHttpSettings`-parametern i avsökningskonfigurationen.

### <a name="redirection-to-app-services-url-scenario"></a>Omdirigering till scenariot för App Service-URL

Det kan finnas scenarier där värdnamnet i svaret från App service kan dirigera slutanvändare webbläsaren till den *. azurewebsites.net värdnamnet i stället för den domän som är associerad med Application Gateway. Det här problemet kan inträffa när:

- Du har omdirigering som konfigurerats i App Service. Omdirigering kan vara lika enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure AD-autentisering vilket gör att omdirigeringen.

För att lösa sådana fall kan se [felsöka omdirigering till App service-URL problemet](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en Programgateway med en app för flera klienter, till exempel Azure App service-webbapp som en medlem i serverdelspoolen genom att besöka [konfigurera App Service web apps med Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-web-app)
