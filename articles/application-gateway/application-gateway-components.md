---
title: Azure Application Gateway-komponenter
description: Den här artikeln innehåller information om de olika komponenterna i Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a7b4dd14cd6270838fde33b0b62ec5adeceb3434
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247026"
---
# <a name="application-gateway-components"></a>Gateway-programkomponenter

 En application gateway fungerar som den enda kontaktpunkten för klienter. Den distribuerar inkommande trafik över flera serverdelspooler, till exempel virtuella Azure-datorer, skalningsuppsättningar för virtuella datorer, App Services eller på plats/externa servrar. Detta gör använder det flera komponenter som beskrivs i den här artikeln.

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip-address"></a>Klientdelens ip-adress

En frontend-IP-adress är IP-adressen som är associerad med programgatewayen. Du kan konfigurera programgatewayen så att den antingen har en offentlig IP-adress eller en privat IP-adress eller båda. Endast en offentlig IP-adress har stöd för en Programgateway. Ditt virtuella nätverk och en offentlig IP-adress måste vara på samma plats som din application gateway.

### <a name="static-vs-dynamic-public-ip-address"></a>Statiska och dynamiska offentliga IP-adress

V1-SKU: N stöder statiska interna IP-adresser men har inte stöd för statiska offentliga IP-adresser. VIP-Adressen kan ändras om application gateway stoppas och startas. DNS-namnet som associeras med application gateway ändras inte under hela livscykeln för gatewayen. Därför rekommenderar vi att du använder en CNAME-alias och gå till DNS-adressen för application gateway.

SKU: N för Application Gateway v2 har stöd för statiska offentliga IP-adresser, samt statiska interna IP-adresser. Endast en offentlig IP-adress eller en privat IP-adress stöds.

En Frontend-IP-adress är kopplad till en *lyssnare* när den har skapats. En lyssnare söker efter inkommande begäranden på Frontend-IP-adress.

## <a name="listeners"></a>Lyssnare

Innan du börjar använda din application gateway, måste du lägga till en eller flera lyssnare. En lyssnare är en logisk enhet som söker efter inkommande anslutningsbegäranden och accepterar begäranden om protokoll, port, värddator och IP-adressen matchar Lyssnarkonfigurationen. Det kan finnas flera lyssnare som är kopplade till en Programgateway och de kan användas för samma protokoll. När lyssnaren identifierar inkommande begäranden från klienterna, Application Gateway routar dessa begäranden till backend-servrar i backend-poolen med hjälp av begäran routningsregler som du definierar för den lyssnare som tog emot den inkommande begäranden.

Lyssnare har stöd för följande portar och protokoll:

### <a name="ports"></a>Portar

Det här är porten där lyssnar lyssnaren för klientbegäran. Du kan konfigurera portar mellan 1 och 65502 för V1-SKU och 1 65199 för V2-SKU.

### <a name="protocols"></a>Protokoll

Application gateway stöder följande fyra protokoll: HTTP, HTTPS, HTTP/2 och WebSocket

När du konfigurerar lyssnaren, måste du välja mellan HTTP och HTTPS-protokoll. Application gateway erbjuder inbyggt stöd för WebSockets och HTTP/2-protokollen. Du kan använda WebSockets med både HTTP och HTTPS-lyssnare.

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverad som standard. Följande Azure PowerShell-kodfragmentet kodexempel visar hur du kan aktivera den:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

Websocket-stöd är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

Du kan använda en HTTPS-lyssnare för SSL-avslutning. En HTTPS-lyssnare avlastar kryptering och dekryptering arbetar för att din application gateway så att dina webbservrar inte burdened genom den overhead. Dina program kan sedan att fokusera på sina affärslogik.

Du måste distribuera minst en SSL-certifikat för en HTTPS-lyssnare. Mer information finns i [hur du konfigurerar SSL-avslutning](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Anpassade felsidor

Application gateway kan du skapa anpassade felsidor i stället för den standard-felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida. Application gateway kan visa en anpassad felsida när en begäran inte kan nå serverdelen. Mer information finns i [anpassade felsidor för din Programgateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typer av lyssnare

Det finns två typer av lyssnare:

- **Basic**: Den här typen av lyssnare lyssnar på en enda domän-plats med en enda DNS-mappning till IP-adressen för application gateway. Den här Lyssnarkonfigurationen krävs när du är värd för en enda plats bakom en Programgateway.
- **Multisite**: Den här Lyssnarkonfigurationen krävs när du konfigurerar mer än ett webbprogram på samma application gateway-instans. På så sätt kan du konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en Programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Exempel:  För tre underdomäner – abc.contoso.com, xyz.contoso.com och pqr.contoso.com som pekar på IP-adressen för application gateway. Skapa tre avlyssning av typen *multisite* och konfigurera varje lyssnare för respektive port och protokoll-inställningen.

När du skapar en lyssnare kan associera du den med en routningsregel för begäran som anger hur begäran tas emot på lyssnaren ska dirigeras till serverdelen.

Lyssnare bearbetas i den ordning som de visas. Därför, om en grundläggande lyssnare matchar en inkommande begäran bearbetas först. Lyssnare för flera platser bör konfigureras innan en grundläggande lyssnare så att trafik dirigeras till rätt serverdel.

## <a name="request-routing-rule"></a>Begär routningsregel

Detta är den viktigaste komponenten i application gateway och avgör hur trafiken på lyssnaren som är associerade med den här regeln dirigeras. Regeln Binder lyssnaren och backend-serverpoolen serverdelens HTTP-inställningar. Den definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare. En lyssnare kan kopplas till endast en regel.

Det kan finnas två typer av regler för routning av begäran:

- **Grundläggande:** Alla begäranden på den associerade lyssnaren (till exempel: blog.contoso.com/*) vidarebefordras till den associerade serverdelspoolen genom att använda den associerade HTTP-inställningen.
- **Sökvägsbaserad:** Den här regeltypen kan du dirigera begäranden på den associerade lyssnaren till en specifik serverdelspool baserat på URL i förfrågan. Om sökvägen till URL: en i en begäran matchar mönstret sökväg i en sökvägsbaserad regel, dirigeras begäran med hjälp av regeln. Sökvägsmönster tillämpas endast på sökvägen till URL-Adressen, inte till dess Frågeparametrar.

Om sökvägen till URL: en för en begäran på en lyssnare matchar inte någon av de sökvägsbaserad reglerna kommer begäran dirigeras till den *standard* serverdelspool och *standard* HTTP-inställningar.

Regler bearbetas i den ordning som de är konfigurerade. Du rekommenderas att multisite regler konfigureras innan grundläggande regler för att minska risken att trafik dirigeras till olämplig serverdelen som grundläggande regel matchar trafik baserat på port innan multisite-regeln som utvärderas.

### <a name="redirection-support"></a>Stöd för omdirigering

En routningsregel för begäran kan du omdirigera trafik på application gateway. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Detta kan hjälpa dig att aktivera automatisk HTTP till HTTPS-omdirigering att se till att all kommunikation mellan ett program och dina användare sker via en krypterad sökväg. Du kan ange den mållyssnare eller den externa platsen till den omdirigering som du vill. Konfigurationselementet har även stöd för alternativ för att lägga till URI-sökväg och fråga strängen till den omdirigerade platsen. Du kan också välja om omdirigering är en tillfällig (HTTP-statuskod 302) eller en permanent omdirigering (HTTP-statuskod 301). När du använder en grundläggande regel som är associerad med en käll-lyssnare omdirigerings-konfigurationen och är en global omdirigering. När en sökvägsbaserad regel används har omdirigerings-konfiguration definierats för Webbadress för sökvägskarta. Så gäller detta bara till området angiven sökväg för en plats. Mer information finns i [omdirigera trafik på Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>HTTP-huvuden

Programgateway infogar x vidarebefordras för x-vidarebefordrade-protokoll och x-vidarebefordrade-port rubriker i begäran vidarebefordras till serverdelen. Formatet för x-vidarebefordrade-för-huvudet är en kommaavgränsad lista över IP:port. Giltiga värden för x-vidarebefordrade-protokoll är HTTP eller HTTPS. X-vidarebefordrade-port Anger den port som nått begäran vid application gateway. Programgateway infogar också X-Original-värd-huvud som innehåller den ursprungliga värdhuvud som begäran kom. Den här rubriken är användbart i scenarier som Azure-webbplats integration, där inkommande värdhuvudet ändras innan trafiken dirigeras till serverdelen.

#### <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

Med hjälp av routningsregler för begäran kan du lägga till, ta bort eller uppdatera HTTP (S) för begäran och svarshuvuden medan begäran och svarspaket flytta mellan klienten och serverdelen poolerna via application gateway. Sidhuvuden kan inte bara ställas in att statiska värden utan också för andra sidhuvuden och viktiga servervariabler. Detta hjälper dig att utföra flera viktiga användningar, t.ex extrahering av IP-adressen för klienter, och som tar bort känslig information om serverdelen, som att lägga till ytterligare säkerhetsåtgärder, osv. Mer information finns i [skriva om HTTP-huvuden på application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>HTTP-inställningar

Application gateway dirigerar trafik till backend-servrarna med portnumret, protokoll och andra inställningar som anges i den här resursen.

Backend-adresspooler stöder följande portar och protokoll:

### <a name="ports"></a>Portar

Det här är den port som backend-servrarna lyssnar på trafik som kommer från Application Gateway. Du kan konfigurera portar mellan 1 och 65535.

### <a name="protocols"></a>Protokoll

Application gateway stöder både HTTP och HTTPS-protokoll för dirigering av begäranden till backend-servrarna.

Om du väljer HTTP-protokollet trafik du flöden okrypterat fram till backend-servrarna.

I sådana fall där okrypterad kommunikation till backend-servrarna inte är en acceptabel bör du välja HTTPS-protokollet. Den här inställningen kombinerade med välja HTTPS-protokollet i lyssnaren kan du aktivera [slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Det låter dig skicka känsliga data säkert till serverdelen i krypterad. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

Du kan använda flera funktioner för HTTP-inställning.

### <a name="cookie-based-session-affinity"></a>Cookie-baserad sessionstillhörighet

Den här funktionen är användbart när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan programgatewayen dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

Om programmet inte kan hantera cookie-baserad tillhörighet, kommer sedan du inte att kunna använda den här funktionen

### <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen kan tillämpas på alla medlemmar i en serverdelspool samband med regelgenereringen. När du har aktiverat, Programgateway ser du till att ta bort registrera instanser av en serverdelspool inte tar emot nya begäranden samtidigt som befintliga begäranden ska slutföras inom en konfigurerad tid. Detta gäller såväl serverdelsinstanser som uttryckligen tas bort från serverdelspoolen med hjälp av ett API-anrop som serverdelsinstanser som rapporteras som skadade enligt hälsoavsökningarna.

### <a name="override-backend-path"></a>Åsidosätt serverdelssökväg

Den här funktionen kan du åsidosätta sökvägen i URL: en så att begäranden för en angiven sökväg kan dirigeras till en annan sökväg. Exempel: Om du avser att dirigera begäranden för contoso.com/images standard och sedan ”/” i den här textrutan och bifoga därefter den här HTTP-inställning för regeln som är associerade med contoso.com/images.

### <a name="pick-host-name-from-a-backend-address"></a>Välj värdnamnet från en backend-adress

Den här funktionen anger den *värden* huvudet i begäran till värdnamnet för serverdelspoolen genom att använda en IP-adress eller fullständigt kvalificerade domännamnet - FQDN. Det här är användbart i scenarier där domännamnet för serverdelen skiljer sig från DNS-namnet på programmet, till exempel i ett scenario där Azure App Service används som serverdelen. Eftersom Azure App Service är en miljö med flera klientorganisationer med hjälp av en delad utrymme med en IP-adress, kan en App Service nås endast med värdnamn som konfigurerats i inställningarna för anpassad domän. Som standard är det *example.azurewebsites.net*. Om du vill komma åt din App Service med application gateway med ett värdnamn som inte är registrerad i App Service eller med Application Gateway FQDN som du behöver åsidosätta värdnamnet i den ursprungliga begäran till värdnamn för App Service.

### <a name="host-override"></a>**Vara värd för åsidosättning**

Den här funktionen ersätter den *värden* rubrik i den inkommande begäran på application gateway till värdnamn som du anger här.

När du har skapat en HTTP-inställning måste du koppla den med en eller flera be regler för routning.

## <a name="backend-pool"></a>Serverdelspool

Serverdelspoolen används för att dirigera begäranden till backend-servrar som betjänar begäran. Serverdelspooler kan bestå av nätverkskort, virtuella datorer skala uppsättningar, offentliga IP-adresser, interna IP-adresser, FQDN och flera innehavare serverprogram som Azure App Service. Medlemmar i Application gateway serverdelspoolen är inte kopplade till en tillgänglighetsuppsättning. Medlemmar i serverdelspooler kan vara över kluster, Datacenter, eller utanför Azure så länge som de har IP-anslutning. Du kan skapa olika serverdelspooler för olika typer av begäranden. Till exempel skapa en backend-pool för allmän begäranden och andra backend-pool för begäranden till mikrotjänster för ditt program.

När du skapar en serverdelspool kan behöva du associera det med en eller flera begäran routningsregler. Du måste också konfigurera hälsotillståndsavsökningar för varje serverdelspool på application gateway. När en begäran om routning villkor uppfylls, vidarebefordrar trafik till felfria servrarna (som bestäms av hälsoavsökningarna) i motsvarande serverdelspoolen application gateway.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Application gateway som standard övervakar hälsotillståndet för alla resurser i serverdelspoolen och att ta bort automatiskt alla resurser som anses vara felaktigt från poolen. Application gateway fortsätter att övervaka de felaktiga instanserna och lägger till dem till Felfri backend-poolen när de blir tillgängliga och svara på hälsokontroller av slutpunkter. Programgateway skickar hälsoavsökningar med samma port som definierats i serverdelens HTTP-inställningar.

Förutom att använda standard hälsoövervakning för avsökning kan anpassa du också hälsoavsökningen så att det passar ditt program. Anpassade avsökningar kan du ha en mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar kan konfigurera du avsökningsintervallet, URL: en och sökväg för att testa och hur många misslyckade svar godkänna innan serverdels-serverpoolinstans markeras som felaktig. Vi rekommenderar starkt att du konfigurerar anpassade avsökningar för att övervaka hälsotillståndet för varje serverdelspool. Mer information finns i [övervaka hälsotillståndet för din Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Nästa steg

När du läst om Application Gateway-komponenter kan du:
* [Skapa en Programgateway i Azure portal](quick-create-portal.md)
* [Skapa en Programgateway med hjälp av PowerShell](quick-create-powershell.md)
* [Skapa en Programgateway med hjälp av Azure CLI](quick-create-cli.md).