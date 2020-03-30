---
title: Komponenter för programgateway
description: Den här artikeln innehåller information om de olika komponenterna i en programgateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133100"
---
# <a name="application-gateway-components"></a>Komponenter för programgateway

 En programgateway fungerar som en enda kontaktpunkt för klienter. Den distribuerar inkommande programtrafik över flera serverdelar pooler, som inkluderar Virtuella Azure-datorer, virtuella datorskalningsuppsättningar, Azure App Service och lokala/externa servrar. Om du vill distribuera trafik använder en programgateway flera komponenter som beskrivs i den här artikeln.

![De komponenter som används i en programgateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>IP-adresser för klientdel

En klientdels-IP-adress är IP-adressen som är associerad med en programgateway. Du kan konfigurera en programgateway för att ha en offentlig IP-adress, en privat IP-adress eller båda. En programgateway stöder en offentlig eller en privat IP-adress. Ditt virtuella nätverk och den offentliga IP-adressen måste vara på samma plats som programgatewayen. När den har skapats associeras en IP-adress för klientdel med en lyssnare.

### <a name="static-versus-dynamic-public-ip-address"></a>Statisk kontra dynamisk offentlig IP-adress

Azure Application Gateway V2 SKU kan konfigureras för att stödja antingen både statisk intern IP-adress och statisk offentlig IP-adress, eller endast statisk offentlig IP-adress. Det går inte att konfigurera för att endast stödja statisk intern IP-adress.

V1 SKU kan konfigureras för att stödja statisk eller dynamisk intern IP-adress och dynamisk offentlig IP-adress. Den dynamiska IP-adressen för Application Gateway ändras inte på en gateway som körs. Den kan bara ändras när du stoppar eller startar gatewayen. Det ändras inte på systemfel, uppdateringar, Azure-värduppdateringar etc. 

DNS-namnet som är associerat med en programgateway ändras inte under gatewayens livscykel. Därför bör du använda ett CNAME-alias och peka det på DNS-adressen för programgatewayen.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk entitet som söker efter inkommande anslutningsbegäranden. En lyssnare accepterar en begäran om protokollet, porten, värdnamnet och IP-adressen som är associerad med begäran matchar samma element som är associerade med lyssnarkonfigurationen.

Innan du använder en programgateway måste du lägga till minst en lyssnare. Det kan finnas flera lyssnare kopplade till en programgateway och de kan användas för samma protokoll.

När en lyssnare har identifierat inkommande begäranden från klienter dirigerar programgatewayen dessa begäranden till medlemmar i serverdapoolen som konfigurerats i regeln.

Lyssnare stöder följande portar och protokoll.

### <a name="ports"></a>Portar

En port är där en lyssnare lyssnar efter klientbegäran. Du kan konfigurera portar från 1 till 65502 för v1 SKU och 1 till 65199 för v2 SKU.

### <a name="protocols"></a>Protokoll

Application Gateway stöder fyra protokoll: HTTP, HTTPS, HTTP/2 och WebSocket:
>[!NOTE]
>HTTP/2-protokollstöd är endast tillgängligt för klienter som ansluter till lyssnare för programgateway. Kommunikationen till serverpooler för server med server som är server som är server som är server som är server som inte är i kontakt med den är alltid över HTTP/1.1. Som standard är HTTP/2-stöd inaktiverat. Du kan välja att aktivera den.

- Ange mellan HTTP- och HTTPS-protokollen i lyssnarkonfigurationen.
- Stöd för [WebSockets- och HTTP/2-protokoll](features.md#websocket-and-http2-traffic) tillhandahålls internt och [WebSocket-stöd](application-gateway-websocket.md) är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. Använd WebSockets med både HTTP- och HTTPS-lyssnare.

Använd en HTTPS-lyssnare för TLS-avslutning. En HTTPS-lyssnare avlastar krypterings- och dekrypteringsarbetet till programgatewayen, så att webbservrarna inte belastas av omkostnaderna.

### <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardfelsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida. Application Gateway visar en anpassad felsida när en begäran inte kan nå backend.

Mer information finns i [Anpassade felsidor för programgatewayen](custom-error.md).

### <a name="types-of-listeners"></a>Typer av lyssnare

Det finns två typer av lyssnare:

- **Grundläggande**. Den här typen av lyssnare lyssnar på en enda domänplats, där den har en enda DNS-mappning till IP-adressen för programgatewayen. Den här lyssnarkonfigurationen krävs när du är värd för en enda plats bakom en programgateway.

- **Flera webbplatser**. Den här avlyssnarkonfigurationen krävs när du konfigurerar mer än ett webbprogram på samma programgatewayinstans. Det gör att du kan konfigurera en effektivare topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Till exempel pekar tre underdomäner, abc.contoso.com, xyz.contoso.com och pqr.contoso.com på IP-adressen för programgatewayen. Du skulle skapa tre lyssnare på flera webbplatser och konfigurera varje lyssnare för respektive port- och protokollinställning.

    Mer information finns i [Värd för flera webbplatser](application-gateway-web-app-overview.md).

När du har skapat en lyssnare associerar du den med en routningsregel för begäran. Den här regeln avgör hur begäran som tas emot på lyssnaren ska dirigeras till backend.

Application Gateway bearbetar lyssnare i den [ordning som visas](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Regler för begär routning

En routningsregel för begäran är en viktig komponent i en programgateway eftersom den avgör hur trafik dirigeras till lyssnaren. Regeln binder lyssnaren, backend-serverpoolen och http-inställningarna för serverdelen.

När en lyssnare accepterar en begäran vidarebefordrar routningsregeln begäran till backend eller omdirigerar den någon annanstans. Om begäran vidarebefordras till serverda definierar routningsregeln för begäran vilken serverpool som serverpoolen ska vidarebefordras till. Regeln för begärandirigering avgör också om rubrikerna i begäran ska skrivas om. En lyssnare kan kopplas till en regel.

Det finns två typer av routningsregler för begäran:

- **Grundläggande**. Alla begäranden på den associerade lyssnaren (till exempel blog.contoso.com/*) vidarebefordras till den associerade backend-poolen med hjälp av den associerade HTTP-inställningen.

- **Sökvägsbaserad**. Med den här routningsregeln kan du dirigera begäranden på den associerade lyssnaren till en viss backend-pool, baserat på URL:en i begäran. Om url:ens sökväg i en begäran matchar sökvägsmönstret i en sökvägsbaserad regel cirkulerar regeln som begär. Sökvägsmönstret tillämpas bara på URL-sökvägen, inte på frågeparametrarna. Om URL-sökvägen på en lyssnarbegäran inte matchar någon av de sökvägsbaserade reglerna dirigeras begäran till standardpoolen för backend och HTTP-inställningarna.

Mer information finns i [URL-baserad routning](url-route-overview.md).

### <a name="redirection-support"></a>Stöd för omdirigering

Med routningsregeln för begäran kan du också omdirigera trafik på programgatewayen. Det här är en allmän omdirigeringsmekanism, så du kan omdirigera till och från alla portar som du definierar med hjälp av regler.

Du kan välja omdirigeringsmålet för att vara en annan lyssnare (vilket kan hjälpa till att aktivera automatisk HTTP till HTTPS-omdirigering) eller en extern plats. Du kan också välja att omdirigeringen ska vara tillfällig eller permanent, eller lägga till URI-sökvägen och frågesträngen i den omdirigerade URL:en.

Mer information finns i [Omdirigera trafik på programgatewayen](redirect-overview.md).

### <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

Genom att använda reglerna för begäranderoutning kan du lägga till, ta bort eller uppdatera HTTP(S) begäran och svarshuvuden när begäran- och svarspaketen flyttas mellan klient- och backend-poolerna via programgatewayen.

Rubrikerna kan ställas in på statiska värden eller till andra rubriker och servervariabler. Detta hjälper till med viktiga användningsfall, till exempel extrahera klient-IP-adresser, ta bort känslig information om serverda, lägga till mer säkerhet och så vidare.

Mer information finns i [Skriva om HTTP-huvuden på programgatewayen](rewrite-http-headers.md).

## <a name="http-settings"></a>HTTP-inställningar

En programgateway dirigerar trafik till serverdelsservrarna (som anges i regeln för begärandirigering som innehåller HTTP-inställningar) med hjälp av portnummer, protokoll och andra inställningar som beskrivs i den här komponenten.

Porten och protokollet som används i HTTP-inställningarna avgör om trafiken mellan programgateway- och serverdelsservrarna är krypterad (som tillhandahåller TLS från slutpunkt till slutpunkt) eller okrypterad.

Den här komponenten används också för att:

- Ta reda på om en användarsession ska hållas på samma server med hjälp av den [cookie-baserade sessionstillhörigheten](features.md#session-affinity).

- Ta bort backend-poolmedlemmar på ett smidigt sätt med hjälp av [anslutningsdränning](features.md#connection-draining).

- Associera en anpassad avsökning för att övervaka serverda hälsotillstånd, ange tidsgränsen för begäran, åsidosätta värdnamn och sökväg i begäran och ge ett klick enkel att ange inställningar för apptjänstens serverda.

## <a name="backend-pools"></a>Backend-pooler

En servergruppsserveringsbegäran för att serverda servrar, som betjänar begäran. Backend pooler kan innehålla:

- Nätverkskort
- Skalningsuppsättningar för virtuella datorer
- Offentliga IP-adresser
- Interna IP-adresser
- FQDN
- Bakåtsträvande bakåtsträvande (t.ex. apptjänst)

Backend-poolmedlemmar för programgateway är inte kopplade till en tillgänglighetsuppsättning. En programgateway kan kommunicera med instanser utanför det virtuella nätverket som den finns i. Därför kan medlemmarna i serverdelspoolerna vara över kluster, över datacenter eller utanför Azure, så länge det finns IP-anslutning.

Om du använder interna IP-adresser som serverdpoolmedlemmar måste du använda [virtuell nätverks peering](../virtual-network/virtual-network-peering-overview.md) eller en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Virtuell nätverks peering stöds och är fördelaktigt för belastningsutjämningstrafik i andra virtuella nätverk.

En programgateway kan också kommunicera med lokala servrar när de är anslutna av Azure ExpressRoute- eller VPN-tunnlar om trafik tillåts.

Du kan skapa olika backend-pooler för olika typer av begäranden. Skapa till exempel en backend-pool för allmänna begäranden och sedan en annan backend-pool för begäranden till mikrotjänsterna för ditt program.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Som standard övervakar en programgateway hälsotillståndet för alla resurser i serverdapoolen och tar automatiskt bort felaktiga resurser. Det övervakar sedan felaktiga instanser och lägger till dem tillbaka till den friska serverda poolen när de blir tillgängliga och svarar på hälsoavsökningar.

Förutom att använda standardövervakning av hälsoavsökning kan du också anpassa hälsoavsökningen så att den passar programmets behov. Anpassade avsökningar tillåter mer detaljerad kontroll över hälsoövervakningen. När du använder anpassade avsökningar kan du konfigurera avsökningsintervallet, URL:en och sökvägen för att testa och hur många misslyckade svar som ska accepteras innan serverda poolinstansen markeras som felaktig. Vi rekommenderar att du konfigurerar anpassade avsökningar för att övervaka hälsotillståndet för varje serverda pool.

Mer information finns [i Övervaka hälsotillståndet för programgatewayen](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Nästa steg

Skapa en programgateway:

* [I Azure-portalen](quick-create-portal.md)
* [Genom att använda Azure PowerShell](quick-create-powershell.md)
* [Genom att använda Azure CLI](quick-create-cli.md)
