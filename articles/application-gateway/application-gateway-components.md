---
title: Azure Application Gateway-komponenter
description: Den här artikeln innehåller information om de olika komponenterna i Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 0e1232391ddf4b57b5dad5ade9776e9c7cfea9b4
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727339"
---
# <a name="application-gateway-components"></a>Gateway-programkomponenter

 En application gateway fungerar som den enda kontaktpunkten för klienter. Den distribuerar inkommande trafik över flera serverdelspooler, till exempel virtuella Azure-datorer, skalningsuppsättningar för virtuella datorer, App Services eller på plats/externa servrar. Detta gör använder det flera komponenter som beskrivs i den här artikeln.

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip-address"></a>Klientdelens ip-adress

En frontend-IP-adress är IP-adressen som är associerad med programgatewayen. Du kan konfigurera programgatewayen så att den antingen har en offentlig IP-adress eller en privat IP-adress eller båda. Endast en offentlig IP-adress eller en privat IP-adress stöds. Ditt virtuella nätverk och en offentlig IP-adress måste vara på samma plats som din application gateway.

En Frontend-IP-adress är kopplad till en *lyssnare* när den har skapats. 

### <a name="static-vs-dynamic-public-ip-address"></a>Statiska och dynamiska offentliga IP-adress

V1-SKU: N stöder statiska interna IP-adresser men har inte stöd för statiska offentliga IP-adresser. VIP-Adressen kan ändras om application gateway stoppas och startas. DNS-namnet som associeras med application gateway ändras inte under hela livscykeln för gatewayen. Därför rekommenderar vi att du använder en CNAME-alias och gå till DNS-adressen för application gateway.

SKU: N för Application Gateway v2 har stöd för statiska offentliga IP-adresser, samt statiska interna IP-adresser. 

## <a name="listeners"></a>Lyssnare

Innan du börjar använda din application gateway, måste du lägga till en eller flera lyssnare. En lyssnare är en logisk enhet som söker efter inkommande anslutningsbegäranden och accepterar begäranden om protokoll, port, värddator och IP-adress som är associerat med begäran-matchning med protokollet, porten, värd och IP-adress som är associerade med Lyssnarkonfigurationen. Det kan finnas flera lyssnare som är kopplade till en Programgateway och de kan användas för samma protokoll. När lyssnaren identifierar inkommande begäranden från klienterna, dirigerar Application Gateway dessa begäranden till medlemmarna i serverdelspoolen, med hjälp av begäran routningsregler som du definierar för den lyssnare som tog emot den inkommande begäranden.

Lyssnare har stöd för följande portar och protokoll:

### <a name="ports"></a>Portar

Det här är porten där lyssnar lyssnaren för klientbegäran. Du kan konfigurera portar mellan 1 och 65502 för V1-SKU och 1 65199 för V2-SKU.

### <a name="protocols"></a>Protokoll

Application gateway stöder följande fyra protokoll: HTTP, HTTPS, HTTP/2 och WebSocket

Du anger explicit valet mellan HTTP och HTTPS-protokoll i Lyssnarkonfigurationen. Den [stöd för WebSockets och HTTP/2-protokollen](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) tillhandahålls internt. [Websocket-stöd](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. Du kan använda WebSockets med både HTTP och HTTPS-lyssnare. Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverad som standard. Du kan välja att aktivera den.

Du kan använda en HTTPS-lyssnare för SSL-avslutning. En HTTPS-lyssnare avlastar kryptering och dekryptering arbetar för att din application gateway så att dina webbservrar inte burdened genom den overhead. Dina program kan sedan att fokusera på sina affärslogik.

### <a name="custom-error-pages"></a>Anpassade felsidor

Application gateway kan du skapa anpassade felsidor i stället för den standard-felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida. Application gateway kan visa en anpassad felsida när en begäran inte kan nå serverdelen. Mer information finns i [anpassade felsidor för din Programgateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typer av lyssnare

Det finns två typer av lyssnare:

- **Basic**: Den här typen av lyssnare lyssnar på en enda domän-plats med en enda DNS-mappning till IP-adressen för application gateway. Den här Lyssnarkonfigurationen krävs när du är värd för en enda plats bakom en Programgateway.
- **Multisite**: Den här Lyssnarkonfigurationen krävs när du konfigurerar mer än ett webbprogram på samma application gateway-instans. På så sätt kan du konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en Programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Exempel:  För tre underdomäner – abc.contoso.com, xyz.contoso.com och pqr.contoso.com som pekar på IP-adressen för application gateway. Skapa tre avlyssning av typen *multisite* och konfigurera varje lyssnare för respektive port och protokoll-inställningen. Mer information finns i [flera webbplatser](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

När du skapar en lyssnare kan associera du den med en routningsregel för begäran som anger hur begäran tas emot på lyssnaren ska dirigeras till serverdelen.

Lyssnare bearbetas i den ordning som de visas. Därför, om en grundläggande lyssnare matchar en inkommande begäran bearbetas först. Lyssnare för flera platser bör konfigureras innan en grundläggande lyssnare så att trafik dirigeras till rätt serverdel.

## <a name="request-routing-rule"></a>Begär routningsregel

Detta är den viktigaste komponenten i application gateway och avgör hur trafiken på lyssnaren som är associerade med den här regeln dirigeras. Regeln Binder lyssnaren och backend-serverpoolen serverdelens HTTP-inställningar. När en begäran har godkänts av en lyssnare, anger en routningsregel för begäran om begäran är att vidarebefordras till serverdelen eller omdirigerad någon annanstans. Om begäran bestäms ska vidarebefordras till serverdelen, definierar en routningsregel för begäran vilken backend-serverpool ska den vidarebefordras till. Dessutom kan anger en routningsregel för begäran även om rubriker i begäran ska skrivas. En lyssnare kan kopplas till endast en regel.

Det kan finnas två typer av regler för routning av begäran:

- **Grundläggande:** Alla begäranden på den associerade lyssnaren (till exempel: blog.contoso.com/*) vidarebefordras till den associerade serverdelspoolen genom att använda den associerade HTTP-inställningen.
- **Sökvägsbaserad:** Den här regeltypen kan du dirigera begäranden på den associerade lyssnaren till en specifik serverdelspool baserat på URL i förfrågan. Om sökvägen till URL: en i en begäran matchar mönstret sökväg i en sökvägsbaserad regel, dirigeras begäran med hjälp av regeln. Sökvägsmönster tillämpas endast på sökvägen till URL-Adressen, inte till dess Frågeparametrar. Om sökvägen till URL: en för en begäran på en lyssnare matchar inte någon av de sökvägsbaserad reglerna kommer begäran dirigeras till den *standard* serverdelspool och *standard* HTTP-inställningar. Mer information finns i [URL-baserad routning](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Stöd för omdirigering

En routningsregel för begäran kan du omdirigera trafik på application gateway. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Du kan välja på omdirigering av att målet ska vara en annan lyssnare (som kan bidra till att aktivera automatisk HTTP till HTTPS-omdirigering) eller en extern webbplats, Välj omdirigeringen vara tillfälligt eller permanent eller välja att lägga till URI-sökväg och fråga strängen till den omdirigerade platsen. Mer information finns i [omdirigera trafik på Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

Med hjälp av routningsregler för begäran kan du lägga till, ta bort eller uppdatera HTTP (S) för begäran och svarshuvuden medan begäran och svarspaket flytta mellan klienten och serverdelen poolerna via application gateway. Sidhuvuden kan inte bara ställas in att statiska värden utan också för andra sidhuvuden och viktiga servervariabler. Detta hjälper dig att utföra flera viktiga användningar, t.ex extrahering av IP-adressen för klienter, och som tar bort känslig information om serverdelen, som att lägga till ytterligare säkerhetsåtgärder, osv. Mer information finns i [skriva om HTTP-huvuden på application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-inställningar

Application gateway-dirigerar trafik till serverdelen servrar (anges i en routningsregel för begäran som HTTP-inställningarna är ansluten) använder portnummer, protokoll och andra inställningar som anges i den här komponenten. Port och protokoll som används i HTTP-inställningarna avgör om trafiken mellan gatewayen och serverdelen programservrar krypteras, därför utföra slutpunkt till slutpunkt SSL eller okrypterade. Den här komponenten används också för att: avgöra om det är en användarsession vara kvar på samma server med hjälp av den [Cookiebaserad sessionstillhörighet](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), utföra korrekt borttagning av medlemmar i serverdelspool med hjälp av [anslutning tömmer](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), koppla anpassad avsökning åsidosätta värdnamnet och sökvägen i begäran om du vill övervaka hälsotillstånd för serverdel, ange timeout för begäran och tillhandahålla en enkel serverdel inställning för App service-serverdelen. 

## <a name="backend-pool"></a>Serverdelspool

Serverdelspoolen används för att dirigera begäranden till backend-servrar som betjänar begäran. Serverdelspooler kan bestå av nätverkskort, virtuella datorer skala uppsättningar, offentliga IP-adresser, interna IP-adresser, FQDN och flera innehavare serverprogram som Azure App Service. Medlemmar i Application gateway serverdelspoolen är inte kopplade till en tillgänglighetsuppsättning. Application Gateway kan kommunicera med instanser utanför det virtuella nätverket att den är i, därför medlemmarna i serverdelspooler kan vara över kluster, Datacenter, eller utanför Azure, så länge det finns en IP-anslutning. Om du planerar att använda interna IP-adresser som medlemmar i serverdelspool så det krävs [VNET-Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) eller [VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet-peering som stöds och är bra för belastningsutjämning trafik i andra virtuella nätverk. Application gateway kan även kommunicera med till den lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar, förutsatt att trafik tillåts.

Du kan skapa olika serverdelspooler för olika typer av begäranden. Till exempel skapa en backend-pool för allmän begäranden och andra backend-pool för begäranden till mikrotjänster för ditt program.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Application gateway som standard övervakar hälsotillståndet för alla resurser i serverdelspoolen och att ta bort automatiskt alla resurser som anses vara felaktigt från poolen. Det fortsätter att övervaka de felaktiga instanserna och lägger till dem till Felfri backend-poolen när de blir tillgängliga och svara på hälsokontroller av slutpunkter. Förutom att använda standard hälsoövervakning för avsökning kan anpassa du också hälsoavsökningen så att det passar ditt program. Anpassade avsökningar kan du ha en mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar kan konfigurera du avsökningsintervallet, URL: en och sökväg för att testa och hur många misslyckade svar godkänna innan serverdels-serverpoolinstans markeras som felaktig. Vi rekommenderar starkt att du konfigurerar anpassade avsökningar för att övervaka hälsotillståndet för varje serverdelspool. Mer information finns i [övervaka hälsotillståndet för din Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Nästa steg

När du läst om Application Gateway-komponenter kan du:
* [Skapa en Programgateway i Azure portal](quick-create-portal.md)
* [Skapa en Programgateway med hjälp av Azure PowerShell](quick-create-powershell.md)
* [Skapa en Programgateway med hjälp av Azure CLI](quick-create-cli.md)
