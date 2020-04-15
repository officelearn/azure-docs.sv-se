---
title: Backen slutar med flera innehavare
titleSuffix: Azure Application Gateway
description: Den här sidan beskriver hur du kan använda serverdelar för flera klientorganisationer med Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: a171dc795e685655b5a3c73d088d3963c2aaa4ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312308"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Stöd för programgateway för bakåtänddar med flera innehavare, till exempel apptjänst

I arkitekturdesigner för flera innehavare på webbservrar körs flera webbplatser på samma webbserverinstans. Värdnamn används för att skilja mellan de olika program som finns. Som standard ändrar inte Application Gateway den inkommande HTTP-rubriken från klienten. Rubriken skickas oförändrad till servern. Detta fungerar bra för serverdelspoolmedlemmar som nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser och FQDN eftersom dessa inte är beroende av ett specifikt värdhuvud eller SNI-tillägg för att lösa till rätt slutpunkt. Det finns dock många tjänster som Azure App-tjänstwebbappar och Azure API-hantering som är av flera innehavare till sin natur och förlitar sig på ett specifikt värdhuvud eller SNI-tillägg för att lösa till rätt slutpunkt. Vanligtvis skiljer sig DNS-namnet på programmet, som i sin tur är DNS-namnet som är associerat med programgatewayen, från domännamnet för serverdelstjänsten. Därför är värdhuvudet i den ursprungliga begäran som tas emot av programgatewayen inte samma sak som värdnamnet för backend-tjänsten. På grund av detta, om inte värdhuvudet i begäran från programgatewayen till backend ändras till värdnamnet för backend-tjänsten, kan backend-post med flera innehavare inte matcha begäran till rätt slutpunkt. 

Application Gateway erbjuder en funktion där användarna kan åsidosätta HTTP-värdrubriken i begäran baserat på namnet på serverdelen. Detta gör det möjligt att använda serverdelar med flera klientorganisationer som Azure App Service-webbappar och API-hantering. Den här funktionen är tillgänglig för både v1-, v2-, standard- och WAF-SKU:er. 

![åsidosättning av värd](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Detta gäller inte för Azure App-tjänstmiljö (ASE) eftersom ASE är en dedikerad resurs till skillnad från Azure App-tjänst som är en resurs för flera innehavare.

## <a name="override-host-header-in-the-request"></a>Åsidosätt värdhuvudet i begäran

Möjligheten att ange en värd åsidosättning definieras i [HTTP-inställningarna](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) och kan tillämpas på alla backend-pool under skapandet av regeln. Följande två sätt att åsidosätta värdhuvudet och SNI-tillägget för bakåtändar med flera innehavare stöds:

- Möjligheten att ange värdnamnet till ett fast värde som uttryckligen anges i HTTP-inställningarna. Den här funktionen säkerställer att värdhuvudet åsidosätts för det här värdet för all trafik till backend-poolen där de särskilda HTTP-inställningarna tillämpas. När du använder TLS från till används det här åsidosatta värdnamnet i SNI-tillägget. Med den här funktionen kan scenarier där en backend-poolgrupp förväntar sig ett värdhuvud som skiljer sig från det inkommande kundvärdhuvudet.

- Möjligheten att härleda värdnamnet från IP- eller FQDN för backend-poolmedlemmarna. HTTP-inställningar ger också ett alternativ för att dynamiskt välja värdnamnet från en backend-poolmedlems FQDN om det är konfigurerat med alternativet att härleda värdnamn från en enskild backend-poolmedlem. När du använder slut-till-slut-TLS härleds det här värdnamnet från FQDN och används i SNI-tillägget. Med den här funktionen kan scenarier där en backend-pool kan ha två eller flera PaaS-tjänster med flera innehavare, till exempel Azure-webbappar och begärans värdhuvud till varje medlem, innehåller värdnamnet som härleds från dess FQDN. För att implementera det här scenariot använder vi en växel i HTTP-inställningarna som kallas [Välj värdnamn från backend-adress](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) som dynamiskt åsidosätter värdhuvudet i den ursprungliga begäran till den som nämns i backend-poolen.  Om din servergrupp FQDN till exempel innehåller "contoso11.azurewebsites.net" och "contoso22.azurewebsites.net", åsidosätts den ursprungliga begärans värdhuvud som är contoso.com för att contoso11.azurewebsites.net eller contoso22.azurewebsites.net när begäran skickas till lämplig server för server för server för server för server för server för server för server. 

  ![Scenario för webbappar](./media/application-gateway-web-app-overview/scenario.png)

Med den här funktionen skapar kunderna lämplig konfiguration genom att konfigurera alternativen i HTTP-inställningarna och de anpassade avsökningarna. Den här inställningen är sedan knuten till en lyssnare och en backend-pool med hjälp av en regel.

## <a name="special-considerations"></a>Särskilda överväganden

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS-avslutning och till TLS med tjänster med flera innehavare

Både TLS-avslutning och slutpunkt till TLS-kryptering stöds med tjänster med flera innehavare. För TLS-avslutning vid programgatewayen måste TLS-certifikatet läggas till i programgatewaylyssnaren. Vid slutet till TLS kräver dock betrodda Azure-tjänster som Azure App-tjänstwebbappar inte att du vitlistar backends i programgatewayen. Därför finns det ingen anledning att lägga till några autentiseringscertifikat. 

![på TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Observera att det i bilden ovan inte finns något krav på att lägga till autentiseringscertifikat när App-tjänsten väljs som serverning.

### <a name="health-probe"></a>Hälsoavsökning

Åsidosättning av värdhuvudet i **HTTP-inställningarna** påverkar bara begäran och dess routning. Det påverkar inte hälsosondens beteende. För att funktionen för slutpunkt till slutpunkt ska fungera måste både avsöknings- och HTTP-inställningarna ändras så att de återspeglar korrekt konfiguration. Förutom att ge möjlighet att ange ett värdhuvud i avsökningskonfigurationen stöder anpassade avsökningar också möjligheten att härleda värdhuvudet från de för närvarande konfigurerade HTTP-inställningarna. Den här konfigurationen kan anges med hjälp av `PickHostNameFromBackendHttpSettings`-parametern i avsökningskonfigurationen.

### <a name="redirection-to-app-services-url-scenario"></a>Omdirigering till App Service URL-scenario

Det kan finnas scenarier där värdnamnet i svaret från App-tjänsten kan dirigera slutanvändarens webbläsare till *.azurewebsites.net-värdnamnet i stället för den domän som är associerad med Programgateway. Det här problemet kan uppstå när:

- Du har konfigurerat omdirigering på apptjänsten. Omdirigering kan vara så enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure AD-autentisering som orsakar omdirigering.

LÃ¶s sådana ärenden genom [att lÃ¤ser felsÃ¶k omdirigering till apptjänstens URL-problem](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en programgateway med en app med flera innehavare, till exempel Azure App-tjänstwebbapp som en backend-poolmedlem genom att besöka [Konfigurera App Service-webbappar med Application Gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)
