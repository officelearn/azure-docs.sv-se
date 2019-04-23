---
title: Gateway-programkomponenter
description: Den här artikeln innehåller information om de olika komponenterna i en application gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008077"
---
# <a name="application-gateway-components"></a>Gateway-programkomponenter

 En application gateway fungerar som den enda kontaktpunkten för klienter. Den distribuerar inkommande trafik över flera serverdelspooler, bland annat virtuella Azure-datorer, skalningsuppsättningar för virtuella datorer, Azure App Service och på plats/externa servrar. För att distribuera trafik använder en Programgateway flera komponenter som beskrivs i den här artikeln.

![De komponenter som används i en application gateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Frontend-IP-adresser

En frontend-IP-adress är IP-adressen som är associerade med en Programgateway. Du kan konfigurera en Programgateway med en offentlig IP-adress, en privat IP-adress eller båda. En application gateway har stöd för en offentlig eller en privat IP-adress. Ditt virtuella nätverk och en offentlig IP-adress måste vara på samma plats som din application gateway. När den har skapats är en frontend-IP-adress associerad med en lyssnare.

### <a name="static-versus-dynamic-public-ip-address"></a>Statiska och dynamiska offentliga IP-adress

V2-SKU: N för Azure Application Gateway stöder både statiska interna och statiska offentliga IP-adresser, även om v1-SKU: N stöder endast statiska interna IP-adresser. Den virtuella IP (VIP)-adressen kan ändras om en Programgateway stoppas och startas.

DNS-namnet som associeras med en Programgateway ändras inte under hela livscykeln för gatewayen. Därför bör du använda en CNAME-alias och gå till DNS-adressen för application gateway.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk enhet som söker efter inkommande anslutningsbegäranden. En lyssnare accepterar en begäran om protokoll, port, värddator och IP-adress som är associerade med förfrågan matchar samma element som är associerade med Lyssnarkonfigurationen.

Innan du använder en application gateway, måste du lägga till minst en lyssnare. Det kan finnas flera lyssnare som är kopplade till en Programgateway och de kan användas för samma protokoll.

När en lyssnare identifierar inkommande begäranden från klienter, dirigerar application gateway dessa begäranden till medlemmar i serverdelspoolen. Application gateway använder routningsregler för begäran som definierats för den lyssnare som tog emot den inkommande begäranden.

Lyssnare har stöd för följande portar och protokoll.

### <a name="ports"></a>Portar

En port är där en lyssnare lyssnar efter klientbegäran. Du kan konfigurera portar mellan 1 och 65502 för v1-SKU och 1 65199 för v2-SKU.

### <a name="protocols"></a>Protokoll

Application Gateway stöder fyra protokoll: HTTP, HTTPS, HTTP/2 och WebSocket:

- Ange protokoll i Lyssnarkonfigurationen mellan HTTP och HTTPS.
- Stöd för [WebSockets och HTTP/2-protokollen](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) tillhandahålls internt, och [WebSocket-stöd](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. Använda WebSockets med både HTTP och HTTPS-lyssnare.
- Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverad som standard. Du kan välja att aktivera den.

Använd en HTTPS-lyssnare för SSL-avslutning. En HTTPS-lyssnare avlastar kryptering och dekryptering arbetet som application gateway, så dina webbservrar inte burdened av arbetet. Dina appar kan sedan fokusera på affärslogik.

### <a name="custom-error-pages"></a>Anpassade felsidor

Application Gateway kan du skapa anpassade felsidor i stället för den standard-felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida. Application Gateway visas en sida med anpassade fel när en begäran inte kan nå serverdelen.

Mer information finns i [anpassade felsidor för din Programgateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typer av lyssnare

Det finns två typer av lyssnare:

- **Basic**. Den här typen av lyssnare lyssnar på en enda domän-webbplats med en enda DNS-mappning till IP-adressen för application gateway. Den här Lyssnarkonfigurationen krävs om du agerar värd för en enda plats bakom en Programgateway.

- **Multisite**. Den här Lyssnarkonfigurationen krävs när du konfigurerar mer än ett webbprogram på samma application gateway-instans. Det kan du konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en Programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Till exempel peka tre underdomäner, abc.contoso.com, xyz.contoso.com och pqr.contoso.com, på IP-adressen för application gateway. Du skulle skapa tre lyssnare för flera platser och konfigurerar varje lyssnare för respektive port och protokollinställningen.

    Mer information finns i [som är värd för flera platser](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

När du skapar en lyssnare kan associera du den med en regel för vidarebefordran av begäran. Den här regeln beräknar hur begäran tas emot på lyssnaren som ska dirigeras till serverdelen.

Application Gateway bearbetar lyssnare i den ordning som visas. Om grundläggande lyssnare matchar en inkommande begäran bearbetas först. Konfigurera en lyssnare för flera platser innan en grundläggande lyssnare för att dirigera trafik till rätt serverdel.

## <a name="request-routing-rules"></a>Routningsregler för begäran

En regel för vidarebefordran av begäran är en viktig del av en Programgateway eftersom den avgör hur du dirigerar trafiken på lyssnaren. Regeln Binder lyssnaren och backend-serverpoolen serverdelens HTTP-inställningar.

När en lyssnare tar emot en begäran, begäran-regel för vidarebefordran vidarebefordrar begäran till serverdelen eller omdirigerar den någon annanstans. Om begäran vidarebefordras till serverdelen, definierar vilken backend-serverpoolen så att den vidarebefordras till en routningsregel för begäran. Dessutom anger en routningsregel för begäran även om rubriker i begäran ska skrivas. En lyssnare kan kopplas till en regel.

Det finns två typer av regler för routning av begäran:

- **Basic**. Alla begäranden på den associerade lyssnaren (till exempel blog.contoso.com/*) vidarebefordras till den associera serverdelspoolen med hjälp av den associerade HTTP-inställningen.

- **Sökvägsbaserad**. Denna regel för vidarebefordran kan du dirigera begäranden på den associerade lyssnaren till en specifik serverdelspool baserat på URL i förfrågan. Om sökvägen till URL: en i en begäran matchar mönstret sökväg i en sökvägsbaserad regel, dirigerar begäran i regeln. Det gäller mönstret sökväg till en URL-sökväg, inte till dess Frågeparametrar. Om URL-sökvägen för en lyssnare begäran inte matchar någon av de sökvägsbaserad reglerna, omdirigerar begäran till Standardpool för serverdelen och HTTP-inställningar.

Mer information finns i [URL-baserad routning](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Stöd för omdirigering

En routningsregel för begäran kan du omdirigera trafik på application gateway. Det här är en mekanism för allmän omdirigering, så att du kan omdirigera till och från alla portar som du definierar med regler.

Du kan välja på omdirigering av att målet ska vara en annan lyssnare (som kan bidra till att aktivera automatisk HTTP till HTTPS-omdirigering) eller en extern webbplats. Du kan också välja att ha omdirigeringen vara tillfälligt eller permanent eller att lägga till URI-sökväg och fråga strängen till den omdirigerade platsen.

Mer information finns i [omdirigera trafik på application gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

Med hjälp av reglerna för routning av begäran, du kan lägga till, ta bort eller uppdatera HTTP (S)-begäran och svarshuvuden som begäran och svar paket flytta mellan klienten och serverdelen pooler via application gateway.

Rubrikerna som kan anges med statiska värden eller andra huvuden och servervariabler. Detta hjälper med viktiga användarfall, t.ex extrahering av klientens IP-adresser, ta bort känslig information om serverdelen, att lägga till mer säkerhet och så vidare.

Mer information finns i [skriva om HTTP-huvuden på application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-inställningar

En application gateway dirigerar trafik till backend-servrar (anges i en routningsregel för begäran som innehåller inställningar för HTTP) med hjälp av portnumret, protokoll och andra inställningar som beskrivs i den här komponenten.

Port och protokoll som används i HTTP-inställningarna avgör om trafiken mellan gatewayen och serverdelen programservrar krypteras (som tillhandahåller slutpunkt till slutpunkt SSL) eller okrypterade.

Den här komponenten används också för att:

- Avgöra om det är en användarsession vara kvar på samma server med hjälp av den [Cookiebaserad sessionstillhörighet](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Ta bort medlemmar i serverdelspoolen genom att använda ett smidigt sätt [Anslutningstömning](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Associera en anpassad avsökning för att övervaka hälsotillstånd för serverdel, ange timeout för begäran, Åsidosätt värdnamnet och sökvägen i begäran och tillhandahåller en enkel om du vill ange inställningar för App Service-serverdelen.

## <a name="backend-pools"></a>Serverdelspooler

En serverdelspool dirigerar begäran till backend-servrar som betjänar begäran. Serverdelspooler kan innehålla:

- Nätverkskort
- Skalningsuppsättningar för virtuella datorer
- Offentliga IP-adresser
- Interna IP-adresser
- FQDN
- Serverdelar för flera innehavare (till exempel App Service)

Medlemmar i Application Gateway serverdelspoolen inte är kopplad till en tillgänglighetsuppsättning. En application gateway kan kommunicera med instanser utanför det virtuella nätverket som den tillhör. Därför kan medlemmarna i serverdelspooler vara mellan kluster i datacenter eller utanför Azure, så länge det finns en IP-anslutning.

Om du använder interna IP-adresser som medlemmar i serverdelspool, måste du använda [virtuell nätverkspeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) eller en [VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Virtuell nätverkspeering är stöds och fördelaktigt för belastningsutjämning trafik i andra virtuella nätverk.

En application gateway kan även kommunicera med till den lokala servrar när de är anslutna via Azure ExpressRoute eller VPN-tunnlar om trafik tillåts.

Du kan skapa olika serverdelspooler för olika typer av begäranden. Till exempel skapa en backend-pool för allmän begäranden och sedan en annan serverdelspool för begäranden till mikrotjänster för ditt program.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Som standard en application gateway övervakar hälsotillståndet för alla resurser i serverdelspoolen och tar automatiskt bort felaktiga ansikten. Den och sedan övervakar felaktiga instanser och lägger till dem till Felfri backend-poolen när de blir tillgängliga och svara på hälsokontroller av slutpunkter.

Förutom att använda standard hälsoövervakning för avsökning kan anpassa du också hälsoavsökningen så att det passar ditt program. Anpassade avsökningar kan mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar kan konfigurera du avsökningsintervallet, URL: en och sökväg för att testa och hur många misslyckade svar godkänna innan serverdels-serverpoolinstans markeras som felaktig. Vi rekommenderar att du konfigurerar anpassade avsökningar för att övervaka hälsotillståndet för varje serverdelspool.

Mer information finns i [övervaka hälsotillståndet för din Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Nästa steg

Skapa en Programgateway:

* [I Azure portal](quick-create-portal.md)
* [Med hjälp av Azure PowerShell](quick-create-powershell.md)
* [Med hjälp av Azure CLI](quick-create-cli.md)
