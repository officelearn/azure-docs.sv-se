---
title: Azure Application Gateway-komponenter
description: Den här artikeln innehåller information om de olika komponenterna i Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 14f400a1b85e213496ac98996d6c2378cf559026
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675816"
---
# <a name="application-gateway-components"></a>Gateway-programkomponenter

 En Application Gateway fungerar som den enda kontaktpunkten för klienter. Den distribuerar inkommande trafik över flera serverdelspooler, till exempel virtuella Azure-datorer, VMSS, App Services eller på plats/externa servrar. Detta gör det använder sig av flera komponenter som beskrivs nedan:

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip"></a>Frontend-IP

Fronted IP är IP (Internet Protocol)-adressen som är associerad med Programgatewayen. Du kan konfigurera Programgatewayen så att den antingen har en offentlig IP-adress eller en privat IP, eller bådadera. Endast en offentlig IP-adress har stöd för en Programgateway. Ditt virtuella nätverk och en offentlig IP-adress måste vara på samma plats som din Application Gateway.

### <a name="static-vs-dynamic-public-ip"></a>Statiska och dynamiska offentliga IP-adress

V1-SKU: N stöder statiska interna IP-adresser men har inte stöd för statiska offentliga IP-adresser. VIP-Adressen kan ändras om application gateway stoppas och startas. DNS-namnet som associeras med application gateway ändras inte under hela livscykeln för gatewayen. Därför rekommenderar vi att du använder en CNAME-alias och gå till DNS-adressen för application gateway.

V2-SKU: N för Application Gateway stöder statiska offentliga IP-adresser och bra som interna IP-adresser. Endast en offentlig IP-adress har stöd för en Programgateway.

När du har skapat en Frontend-IP, den är kopplad till *lyssnare* som söka efter inkommande begäranden på Frontend-IP.

## <a name="listeners"></a>Lyssnare

Innan du börjar använda din Application Gateway, måste du lägga till en eller flera lyssnare. En lyssnare är en process med hjälp av som Application Gateway som söker efter inkommande anslutningsbegäranden, med hjälp av protokoll och port som du konfigurerar. När lyssnaren identifierar inkommande begäranden från klienterna, Application Gateway routar dessa begäranden till backend-servrar i backend-poolen med hjälp av begäran routningsregler som du definierar för den lyssnare som tog emot den inkommande begäranden.

Lyssnare har stöd för följande portar och protokoll:

### <a name="ports"></a>Portar

Det här är den port som lyssnaren lyssnar på klientbegäran. Följande intervall tillåts för portkonfigurationen: 1-65535

### <a name="protocols"></a>Protokoll

Application Gateway stöder följande 4 protokoll: HTTP, HTTPS, HTTP/2, Websocket

När du konfigurerar lyssnaren, måste du välja mellan HTTP och HTTPS-protokollet. Application Gateway erbjuder inbyggt stöd för WebSockets och HTTP/2-protokollen. Du kan använda WebSockets med både HTTP och HTTPS-lyssnare. 

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverad som standard. Följande Azure PowerShell-kodfragmentet kodexempel visar hur du kan aktivera den:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Websocket-stöd är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

Du kan använda en HTTPS-lyssnare för SSL-avslutning som kommer avlasta arbete för kryptering och dekryptering till din Application Gateway så att dina webbservrar kan vara webbservrarna från kostsam kryptering och dekryptering omkostnader och dina program kan fokusera på sina affärslogik. Du måste distribuera minst en SSL-certifikat för en HTTPS-lyssnare. Mer information finns i [hur du konfigurerar SSL-avslutning](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida. Application gateway kan visa en anpassad felsida när en begäran inte kan nå serverdelen. Mer information finns i [anpassade felsidor för din Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error)

### <a name="types-of-listeners"></a>Typer av lyssnare

Det finns två typer av lyssnare:

- **Basic**: Den här typen av lyssnare lyssnar på en enda domän-plats med en enda DNS-mappning till IP-adressen för Application Gateway. Den här Lyssnarkonfigurationen är obligatorisk när du är värd för en enda plats bakom en Programgateway
- **Multisite**: Den här Lyssnarkonfigurationen krävs när du konfigurerar mer än ett webbprogram på samma application gateway-instans. På så sätt kan du konfigurera en mer effektiv topologi för dina distributioner genom att lägga till upp till 100 webbplatser till en Programgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Exempel:  För tre underdomäner – abc.alpha.com, xyz.alpha.com och pqr.alpha.com som pekar på IP-adressen för Application Gateway. Skapa 3 avlyssning av typen ”multisite- och konfigurera varje lyssnare för respektive porten och protokollinställningen. 

När du har skapat en lyssnare associera du den med en routningsregel för begäran som anger hur begäran tas emot på lyssnaren ska dirigeras till serverdelen.

Lyssnare bearbetas i den ordning som de visas. Därför om en grundläggande lyssnare matchar en inkommande begäran bearbetas först. Lyssnare för flera platser bör konfigureras innan en grundläggande lyssnare så att trafik dirigeras till rätt serverdel.

## <a name="request-routing-rule"></a>Begär routningsregel

Detta är den viktigaste komponenten i Application Gateway och anger hur trafik på som är associerade med den här regeln ska dirigeras. Regeln Binder lyssnaren och backend-serverpoolen serverdelens HTTP-inställningar och definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare. En lyssnare kan kopplas till endast en regel.

Det kan finnas två typer av regler för routning av begäran:

- **Grundläggande:** Alla begäranden på den associerade lyssnaren (t.ex.: blog.contoso.com/*) vidarebefordras till den associerade serverdelspoolen genom att använda den associerade HTTP-inställningen.
- **Sökvägsbaserad:** Den här typen av regel ger dig möjlighet att dirigera begäranden på den associerade lyssnaren till en specifik serverdelspool baserat på URL i förfrågan. Om sökvägen till URL: en i en begäran matchar mönstret sökväg i en sökvägsbaserad regel, dirigeras begäran med hjälp av regeln. Sökvägsmönster tillämpas endast på sökvägen till URL-Adressen, inte till dess Frågeparametrar. 

Om sökvägen till URL: en för en begäran på en lyssnare matchar inte någon av de sökvägsbaserad reglerna kommer begäran dirigeras till den *standard* serverdelspool och *standard* HTTP-inställningar.

Regler bearbetas i den ordning som de är konfigurerade. Du rekommenderas att multisite regler konfigureras innan grundläggande regler för att minska risken att trafik dirigeras till olämplig serverdelen som grundläggande regel matchar trafik baserat på port innan multisite-regeln som utvärderas.

### <a name="redirection-support"></a>Stöd för omdirigering

En routningsregel för begäran kan du omdirigera trafik på Application Gateway. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Detta kan hjälpa dig att aktivera automatisk HTTP till HTTPS-omdirigering att se till att all kommunikation mellan program och dina användare sker via en krypterad sökväg. Du kan ange mållyssnare eller extern webbplats som omdirigering är det önskade. Konfigurationselementet har även stöd för alternativ för att lägga till URI-sökväg och fråga strängen till den omdirigerade platsen. Du kan också välja om omdirigering är en tillfällig (HTTP-statuskod 302) eller en permanent omdirigering (HTTP-statuskod 301). När du använder en grundläggande regel som är associerad med en käll-lyssnare omdirigerings-konfigurationen och är en global omdirigering. När en sökvägsbaserad regel används har omdirigerings-konfiguration definierats för Webbadress för sökvägskarta. Så gäller detta bara till området angiven sökväg för en plats. Mer information finns i [omdirigera trafik på Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>HTTP-huvuden

Application Gateway infogar x vidarebefordras för x-vidarebefordrade-protokoll och x-vidarebefordrade-port rubriker i begäran vidarebefordras till serverdelen. Formatet för x-vidarebefordrade-för-huvudet är en kommaavgränsad lista över IP:Port. Giltiga värden för x-vidarebefordrade-protokoll är http eller https. X-vidarebefordrade-port Anger den port som nått begäran vid application gateway. Application Gateway infogar också X-Original-värd-huvud som innehåller den ursprungliga värdhuvud som begäran kom. Den här rubriken är användbart i scenarier som Azure-webbplats integration, där inkommande värdhuvudet ändras innan trafiken dirigeras till serverdelen.

#### <a name="rewrite-http-headers"></a>Återskapa HTTP-huvuden

Ta bort routningsregler för begäran som du kan lägga till eller uppdatera HTTP (S)-begäran och svarshuvuden medan begäran och svar-paket som flyttar mellan klienten och serverdelen pooler via Application Gateway. Sidhuvuden kan inte bara ställas in att statiska värden utan också för andra sidhuvuden och viktiga servervariabler. Detta hjälper dig att utföra flera viktiga användningar, t.ex extrahering av IP-adressen för klienter, och som tar bort känslig information om serverdelen, som att lägga till ytterligare säkerhetsåtgärder, osv. Mer information finns i[skriva om HTTP-huvuden på Application Gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)

## <a name="http-settings"></a>HTTP-inställningar

Application Gateway dirigerar trafik till backend-servrarna med portnumret, protokoll och andra inställningar som anges i den här resursen. 

Backend-adresspooler stöder följande portar och protokoll:

### <a name="ports"></a>Portar

Det här är den port som backend-servrarna lyssnar på trafik som kommer från Application Gateway. Följande intervall tillåts för portkonfigurationen: 1-65535

### <a name="protocols"></a>Protokoll

Application Gateway stöder både HTTP och HTTPS-protokoll för dirigering av begäranden till backend-servrarna.

Om du väljer HTTP-protokollet trafik du flöden okrypterat fram till backend-servrarna. 

I sådana fall där okrypterad kommunikation till backend-servrarna inte är en acceptabel bör du välja HTTPS-protokollet. Den här inställningen kombinerade med välja HTTPS-protokollet i lyssnaren gör att du kan aktivera [slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Som gör att du kan skicka känsliga data säkert till serverdelen i krypterad. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

Du kan utnyttja flera funktioner med hjälp av HTTP-inställning:

### <a name="cookie-based-session-affinity"></a>Cookie-baserad sessionstillhörighet

Den här funktionen är användbart när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

Om programmet inte kan hantera cookie-baserad tillhörighet, kommer sedan du inte att kunna använda den här funktionen

### <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen kan tillämpas på alla medlemmar i en serverdelspool samband med regelgenereringen. När den har aktiverats ser Application Gateway till att alla avregistreringsinstanser av en serverdelspool inte får någon ny begäran samtidigt som befintliga begäranden tillåts slutföras inom en konfigurerad tid. Detta gäller såväl serverdelsinstanser som uttryckligen tas bort från serverdelspoolen med hjälp av ett API-anrop som serverdelsinstanser som rapporteras som skadade enligt hälsoavsökningarna.

### <a name="override-backend-path"></a>Åsidosätt serverdelssökväg

Den här funktionen kan du åsidosätta sökvägen i URL: en så att begäranden för en angiven sökväg kan dirigeras till en annan sökväg. Exempel: Om du avser att dirigera begäranden för contoso.com/images standard och sedan ”/” i den här textrutan och bifoga därefter den här HTTP-inställning för regeln som är associerade med contoso.com/images.

### <a name="pick-host-name-from-a-backend-address"></a>Välj värdnamnet från en backend-adress

Den här funktionen anger den *värden* huvudet i begäran till värdnamnet för serverdelspool (IP eller FQDN). Det här är användbart i scenarier där domännamnet för serverdelen skiljer sig från DNS-namnet på programmet, till exempel i ett scenario där Azure App Service används som serverdel. Det beror på att eftersom Azure App Service är en miljö med flera klientorganisationer med hjälp av en delad utrymme med en IP-adress, en App Service kan nås endast med värdnamn som konfigurerats i inställningarna för anpassad domän. Som standard är det ”example.azurewebsites.net” och om du vill komma åt din App Service med hjälp av Application Gateway med ett värdnamn som inte är registrerad i App Service eller med Application Gateway FQDN måste du åsidosätta värdnamnet i den ursprungliga begäran till appen Tjänstens värdnamn.

### <a name="host-override"></a>**Vara värd för åsidosättning**

Den här funktionen ersätter den *värden* rubrik i den inkommande begäran på Application Gateway till värdnamn som du anger här. 

När du har skapat en HTTP-inställning måste du koppla den med en eller flera be regler för routning.

## <a name="backend-pool"></a>Serverdelspool

Serverdelspoolen används för att dirigera begäranden till backend-servrarna som ska tillhandahålla begäran. Serverdelspooler kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure App Service. Medlemmar i Application Gateway serverdelspoolen är inte kopplade till en tillgänglighetsuppsättning. Medlemmar i serverdelspooler kan vara över kluster, Datacenter, eller utanför Azure så länge som de har IP-anslutning. Du kan skapa olika serverdelspooler för olika typer av begäranden. Till exempel skapa en backend-pool för allmän begäranden och andra backend-pool för begäranden till mikrotjänster för ditt program.

Du måste koppla den till en eller flera begäran routningsregler när du har skapat en serverdelspool. Du måste också konfigurera hälsotillståndsavsökningar för varje serverdelspool på Application Gateway. När en begäran om routning villkor uppfylls, vidarebefordrar trafik till felfria servrarna (som bestäms av hälsoavsökningarna) i motsvarande serverdelspoolen Application Gateway.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Azure Application Gateway som standard övervakar hälsotillståndet för alla resurser i dess backend-poolen och att ta bort automatiskt alla resurser som anses vara felaktigt från poolen. Application Gateway fortsätter att övervaka de felaktiga instanserna och lägger till dem till Felfri backend-poolen när de blir tillgängliga och svara på hälsokontroller av slutpunkter. Programgateway skickar hälsoavsökningar med samma port som definierats i backend-HTTP-inställningarna.

Förutom att använda standard hälsoövervakning för avsökning kan anpassa du också hälsoavsökningen så att det passar ditt program. Anpassade avsökningar kan du ha en mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar kan konfigurera du avsökningsintervallet, URL: en och sökväg för att testa och hur många misslyckade svar att godkänna innan du markerar backend-poolen-instans som skadad. Vi rekommenderar starkt att du konfigurerar anpassade avsökningar för att övervaka hälsotillståndet för varje serverdelspool. Mer information finns i [övervaka hälsotillståndet för Application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)

## <a name="next-steps"></a>Nästa steg

När du läst om Application Gateway-komponenter du [kan skapa en Programgateway i Azure-portalen](quick-create-portal.md) eller en [skapa en Programgateway med hjälp av PowerShell](quick-create-powershell.md) eller [skapa en Application Gateway med Azure CLI](quick-create-cli.md).