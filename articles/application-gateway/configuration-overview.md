---
title: Azure Application Gateway configuration-översikt
description: Den här artikeln innehåller information om hur du konfigurerar de olika komponenterna i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/04/2019
ms.author: absha
ms.openlocfilehash: 702101039c03b30bb8883ef0308fe68c5567a0c4
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733321"
---
# <a name="application-gateway-configuration-overview"></a>Översikt över Application Gateway-konfiguration

Programgateway består av flera komponenter som kan konfigureras på olika sätt för att utföra olika scenarier. Den här artikeln beskriver hur varje komponent som ska konfigureras.

![application-gateway-components](.\media\configuration-overview\configuration-overview1.png)

På bilden ovan illustrerar konfigurationen av ett program med 3 lyssnare. Första två är lyssnare för flera platser för http://acme.com/* och http://fabrikam.com/* respektive. Båda lyssnar på port 80. Den tredje lyssnaren är en grundläggande lyssnare med från slutpunkt till slutpunkt SSL-avslutning. 

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure-nätverk och dedikerade undernät

Application gateway är en särskild distribution i det virtuella nätverket. I det virtuella nätverket krävs ett dedikerat undernät för application gateway. Du kan ha flera instanser av en angiven programdistribution gateway i det här undernätet. Du kan även distribuera andra application gatewayer i undernätet, men du kan inte distribuera någon annan resurs i application gateway-undernätet.  

> [!NOTE]   
> Blanda Standard_v2 och Standard Application Gateway i samma undernät stöds inte.

#### <a name="size-of-the-subnet"></a>Storleken på undernätet

När det gäller v1-SKU förbrukar application Gateway en privat IP-adress per instans, plus en annan privat IP-adress om en privat klientdels-IP-konfiguration har konfigurerats. Dessutom Azure reserverar fyra första och sista IP-adress i varje undernät för intern användning. Exempel: om en application gateway är inställd på tre instanser och ingen privat klientdels-IP, sedan ett/29 undernät storlek eller högre krävs. I det här fallet använder application gateway tre IP-adresser. Om du har tre instanser och en IP-adress för privata klientdelens IP-konfiguration, sedan en/28 undernät storlek eller högre krävs eftersom det krävs fyra IP-adresser.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Nätverkssäkerhetsgrupper som stöds på Application Gateway-undernät

Nätverkssäkerhetsgrupper (NSG) stöds i Application Gateway-undernät med följande begränsningar: 

- Undantag måste placeras i inkommande trafik på portarna 65503 65534 för Application Gateway v1-SKU och portar 65200 – 65535 för v2-SKU. Den här portintervall krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer går externa entiteter, inklusive kunderna till dessa gateways, inte att initiera alla ändringar på dessa slutpunkter.

- Det går inte att blockera utgående internet-anslutning.

- Trafik från taggen AzureLoadBalancer måste tillåtas.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Lista över tillåtna Application Gateway-åtkomst till några käll-IP-adresser

Det här scenariot kan göras med NSG: er på application gateway-undernätet. Följande begränsningar försätts i undernät i listan prioritetsordning:

1. Tillåt inkommande trafik från källa IP/IP-adressintervall.
2. Tillåt inkommande begäranden från alla källor till portar 65503 65534 för [kommunikation för backend-hälsotillstånd](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Intervallet måste anges för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte initiera alla ändringar på dessa slutpunkter.
3. Tillåt inkommande Azure belastningsutjämnare avsökningar (taggen AzureLoadBalancer) och inkommande trafik i virtuella nätverk (taggen VirtualNetwork) på den [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blockera alla andra inkommande trafik med en neka alla-regeln.
5. Tillåt utgående trafik till internet för alla mål.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Användardefinierade vägar som stöds på Application Gateway-undernät

Vid v1-SKU stöds användardefinierade vägar (Udr) på application gateway-undernätet, så länge de inte ändrar slutpunkt till slutpunkt begäran/svar-kommunikation.

Exempel: du kan ställa in en UDR i application gateway-undernätet så att den pekar till en brandväggsinstallation för paketinspektion, men måste du kontrollera att paketet kan nå den avsedda mål post granskar. I annat fall kan leda till felaktig hälsotillstånd avsökning eller SNMP-trafiken routning beteende. Detta inkluderar inlärda eller 0.0.0.0/0 standardvägar sprids av ExpressRoute eller VPN-gatewayer i det virtuella nätverket.

När det gäller v2 stöds-SKU, udr: er i application gateway-undernätet inte. Mer information finns i [automatisk skalning och zonredundant Application Gateway (offentlig förhandsversion)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

## <a name="frontend-ip"></a>Frontend-IP

Du kan konfigurera programgatewayen så att den antingen har en offentlig IP-adress eller en privat IP-adress eller båda. Offentlig IP-adress krävs när du är värd för en serverdel som krävs för att användas av klienter via internet via en internetuppkopplad VIP. Offentlig IP-adress krävs inte för en intern slutpunkt som inte exponeras till Internet, även känd som en intern belastningsutjämnare (ILB) slutpunkt. Det kan vara praktiskt att konfigurera gatewayen med en ILB för interna affärsprogram som inte är exponerade för Internet. Det är också användbart för tjänster och nivåer i ett affärsprogram med flera nivåer som finns vid en säkerhetsgräns som inte är exponerad för Internet men som fortfarande kräver distribution med resursallokering (round-robin), sessionsvaraktighet eller SSL-avslut (Secure Sockets Layer).

Endast en offentlig IP-adress eller en privat IP-adress stöds. Du kan välja frontend-IP för när du skapar Programgatewayen. 

- Vid en offentlig IP-adress kan du välja att skapa en ny offentlig IP-adress eller Använd en befintlig offentlig IP på samma plats som Application Gateway. Om du skapar en ny offentlig IP-adress, kan inte IP-adresstypen som valts (statisk eller dynamisk) ändras senare. Mer information finns i [Statiska och dynamiska offentliga IP-adress](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- Du kan välja att ange en privat IP-adress från det undernät som Application Gateway har skapats vid en privat IP-adress. Om inte anges explicit väljs en skadlig IP-adress automatiskt från undernätet. Mer information finns i [skapa en Programgateway med en intern belastningsutjämnare (ILB) slutpunkt.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

En Frontend IP är kopplad till *lyssnare* som söker efter inkommande begäranden på Frontend-IP.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk enhet som söker efter inkommande anslutningsbegäranden, använder port, protokoll, värddator och IP-adress. Därför när du konfigurerar lyssnaren måste du ange dessa värden för port, protokoll, värd och IP-som är samma som de motsvarande värdena i den inkommande begäran på gatewayen. När du skapar en Programgateway med hjälp av Azure portal kan skapa du också en standard-lyssnare genom att välja det protokoll och port för lyssnaren. Du kan också välja om du vill aktivera stöd för HTTP2 på lyssnaren. När Application Gateway har skapats kan du redigera inställningen för den här standard-lyssnaren (*appGatewayHttpListener*/*appGatewayHttpsListener*) och/eller skapa nya lyssnare.

### <a name="listener-type"></a>Lyssnaren typ

Du kan välja mellan [grundläggande eller multisite lyssnare](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) när du skapar en ny lyssnare. 

- Om du är värd för en enda plats bakom en Programgateway, väljer du grundläggande lyssnare. Lär dig [hur du skapar en Programgateway med med grundläggande lyssnare](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Om du konfigurerar mer än ett webbprogram eller flera underdomäner i samma överordnade domän på samma application gateway-instans, väljer du lyssnare för flera platser. För lyssnare för flera platser behöver du dessutom ange ett värdnamn. Det beror på att Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att vara värd för flera webbplatser på samma offentliga IP-adress och port.![1551057450710](C:\Users\absha\AppData\Roaming\Typora\typora-user-images\1551057450710.png)


> [!NOTE]
> När det gäller v1-SKU: er bearbetas lyssnare i den ordning som de visas. Därför om en grundläggande lyssnare matchar en inkommande begäran bearbetas först. Lyssnare för flera platser bör därför konfigureras innan en grundläggande lyssnare så att trafik dirigeras till rätt serverdel.
>
> När det gäller v2-SKU: er bearbetas lyssnare för flera platser innan du grundläggande lyssnare.

### <a name="frontend-ip"></a>Frontend-IP

Välj den frontend IP-Adressen som du vill associera med den här lyssnaren. Lyssnaren lyssnar på den inkommande begäran på denna IP-adress.

### <a name="frontend-port"></a>Frontend-Port

Välj frontend-port. Du kan välja från befintliga portar eller skapa en ny. Du kan välja ett värde mellan den [tillåtna portintervall](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). På så sätt kan du inte bara använda välkända portar, till exempel 80 och 443, men någon tillåtet anpassad port lämplig för din användning. En port kan antingen användas för offentliga internetuppkopplade lyssnare eller privata riktade lyssnare.

### <a name="protocol"></a>Protokoll

Du måste välja mellan HTTP och HTTPS-protokollet. 

- Om du väljer HTTP flödar trafiken mellan klienten och programmet gatewayen okrypterade.

- Välj HTTPS om du är intresserad av [Secure Sockets Layer (SSL)-avslutning](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) eller [från slutpunkt till slutpunkt SSL-kryptering](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Om du väljer HTTPS trafik mellan klienten och programmet gateway krypteras och SSL-anslutningen avslutas vid application gateway.  Om du vill från slutpunkt till slutpunkt SSL-kryptering, måste du även välja HTTPS-protokollet när du konfigurerar *serverdelens HTTP-inställningen*. Det säkerställer att trafiken krypteras igen när de överförs från Programgatewayen till serverdelen.

  Om du vill konfigurera Secure Sockets Layer (SSL)-avslutning och SSL-kryptering från slutpunkt till slutpunkt, krävs ett certifikat som ska läggas till lyssnaren för att aktivera Application Gateway att härleda en symmetrisk nyckel enligt specifikationen för SSL-protokollet. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. Gateway-certifikatet måste ha formatet Personal Information Exchange (PFX). Det här filformatet kan du exportera den privata nyckeln som krävs av application gateway för att utföra kryptering och dekryptering av trafik. 

#### <a name="supported-certs"></a>Certifikat som stöds

Självsignerade certifikat, CA-certifikat, jokertecken certifikat och EV certifikat stöds.

### <a name="additional-protocol-support"></a>Stöd för ytterligare protokoll

#### <a name="http2-support"></a>Stöd för HTTP2

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels-serverpooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverad som standard. Följande Azure PowerShell-kodfragmentet kodexempel visar hur du kan aktivera den:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Websocket-stöd

Websocket-stöd är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. Du kan använda WebSockets med både HTTP och HTTPS-lyssnare. 

### <a name="custom-error-page"></a>Anpassad felsida

Anpassade felsidor kan definieras på global nivå samt lyssnare-nivå, men skapar anpassade felsidor för global nivå från Azure-portalen stöds inte för tillfället. Du kan konfigurera en anpassad felsida för ett 403 WAF-fel eller en 502 underhållssidan på nivån lyssnare. Du måste också ange en offentligt tillgänglig blob-URL för den angivna felkoden. Mer information finns i [Skapa anpassad felsida](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Felkoder för Application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Använd för att konfigurera en global anpassad felsida [Azure PowerShell för konfiguration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>SSL-princip

Du kan centralisera hanteringen av SSL-certifikat och minska omkostnader för kryptering och dekryptering från en backend-servergrupp. Den här centraliserade SSL även hanterar kan du ange en central SSL-princip som är anpassade till din organisations säkerhetskrav.  Du kan välja mellan standard fördefinierade och anpassade SSL-princip. 

Du kan konfigurera SSL-princip för att styra SSL-protokoll versioner. Du kan konfigurera programgatewayen för att neka TLS 1.0 och TLS1.1 TLS1.2. SSL 2.0 och 3.0 är redan inaktiverad som standard och kan inte konfigureras. Mer information finns i [översikt över Application Gateway SSL-princip](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

När du har skapat en lyssnare associera du den med en routningsregel för begäran som anger hur begäran tas emot på lyssnaren ska dirigeras till serverdelen.

## <a name="request-routing-rule"></a>Begär routningsregel

När du skapar programgatewayen med hjälp av Azure-portalen måste du skapa en standardregel (*1*), vilket Binder lyssnaren standard (*appGatewayHttpListener*) med Standardpool för serverdelen (*appGatewayBackendPool*) och standard serverdelens HTTP-inställningar (*appGatewayBackendHttpSettings*). När application gateway har skapats kan du kan redigera inställningen för den här Standardregeln och/eller skapa nya regler.

### <a name="rule-type"></a>Regeltyp

Du kan välja mellan [grundläggande eller sökvägsbaserad regel](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) när du skapar en ny regel. 

- Om du vill vidarebefordra alla begäranden på den associerade lyssnaren (t.ex.: blog.contoso.com/*) till en enda backend-pool väljer du grundläggande lyssnare. 
- Välj-baserad lyssnare om du vill dirigera begäranden med specifika sökvägar till specifika serverdelspooler. Sökvägsmönster tillämpas endast på sökvägen till URL-Adressen, inte till dess Frågeparametrar.


> [!NOTE]
>
> Vid v1-SKU: er bearbetas matchning av mönster för inkommande begäran i ordning där sökvägarna visas i Webbadress för sökvägskarta av sökvägsbaserad regel. Därför, om en begäran matchar mönstret i två eller flera sökvägar i Webbadress för sökvägskarta, sedan den sökväg som visas först matchas och begäran vidarebefordras till serverdelen som är associerade med samma sökväg.
>
> När det gäller v2-SKU: er innehåller en exakt matchning högre prioritet över den ordning som sökvägarna visas i Webbadress för sökvägskarta. För detta innebär att om en begäran matchar mönstret i två eller flera sökvägar, och sedan begäran vidarebefordras till serverdelen som associeras med samma sökväg som matchar exakt med begäran. Om sökvägen i den inkommande begäranden inte matchar någon sökväg i Webbadress för sökvägskarta, bearbetas sedan matchar mönstret för inkommande begäran i ordning där sökvägarna visas i Webbadress för sökvägskarta av sökvägsbaserad regel.

### <a name="associated-listener"></a>Tillhörande lyssnare

Du vill koppla en lyssnare för regeln så att den *begär routningsregel* som är associerade med den *lyssnare* utvärderas för att fastställa den *serverdelspool* som den förfrågan är ska dirigeras.

### <a name="associated-backend-pool"></a>Associerade backend-Pool

Associera serverdelspoolen som innehåller backend-mål som kommer att fungera begäranden tas emot av lyssnaren. Vid en grundläggande regel tillåts bara en serverdelspool eftersom alla begäranden på den associerade lyssnaren vidarebefordras till den här serverdelspoolen. Lägga till flera serverdelspooler som motsvarar varje URL-sökväg vid en sökvägsbaserad regel. Begäranden som matchar den webbsökväg som anges här, vidarebefordras till motsvarande serverdelspoolen. Lägg även till en Standardpool för serverdelen eftersom de förfrågningar som inte matchar alla URL-sökvägen som angetts i den här regeln ska vidarebefordras till den.

### <a name="associated-backend-http-setting"></a>Associerade serverdelens HTTP-inställning

Lägg till en backend-HTTP-inställning för varje regel. Förfrågningar kommer att dirigeras från Programgatewayen till backend-mål med portnumret, protokoll och andra inställningar som anges i den här inställningen. Vid en grundläggande regel tillåts endast en serverdelens HTTP-inställning eftersom alla begäranden på den associerade lyssnaren vidarebefordras till de motsvarande backend-mål som använder den här inställningen för HTTP. Lägg till flera serverdelens HTTP-inställningar för varje URL-sökväg vid en sökvägsbaserad regel. Begäranden som matchar den webbsökväg som anges här, vidarebefordras till de motsvarande backend-mål med hjälp av HTTP-inställningarna för varje URL-sökväg. Lägg även till en HTTP-standardinställningarna eftersom de förfrågningar som inte matchar alla URL-sökvägen som angetts i den här regeln ska vidarebefordras till Standardpool för serverdelen med HTTP-standardinställningarna.

### <a name="redirection-setting"></a>Omdirigeringsinställningen

Om omdirigeringen har konfigurerats för en grundläggande regel, kommer alla begäranden på den associerade lyssnaren omdirigeras till omdirigering av mål, vilket innebär att globala omdirigering. Om omdirigeringen har konfigurerats för en sökvägsbaserad regel begäranden endast på en viss plats, till exempel en kundvagn området enligt/kundvagn / *, kommer att omdirigeras till omdirigering av mål, vilket innebär att sökvägsbaserad omdirigering. 

Information om funktionen för omdirigering finns i [översikt över Mappomdirigering](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Omdirigeringstyp

  Välj typ av omdirigering av krävs från: Permanenta är tillfällig, hitta eller se andra.

- #### <a name="redirection-target"></a>Omdirigering av mål

  Du kan välja mellan en annan lyssnare eller en extern webbplats som omdirigering av mål. 

  - ##### <a name="listener"></a>Lyssnare

    Välja lyssnare som omdirigering av mål underlättar vid omdirigering från en lyssnare till en annan lyssnaren på gatewayen. Den här inställningen är obligatorisk när du vill aktivera HTTP till HTTPS-omdirigering, d.v.s. omdirigerings-trafik från käll-lyssnaren som söker efter de inkommande HTTP-begäranden till lyssnare inom ramen för mål som söker efter inkommande HTTPS-begäranden. Du kan också välja frågesträngen och sökväg i den ursprungliga begäran som ska ingå i begäran vidarebefordras till målet för omdirigering.![application-gateway-components](.\media\configuration-overview\configure-redirection.png)

    Mer information om HTTP till HTTPS-omdirigering finns i [HTTP till HTTP-omdirigering med hjälp av portalen](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [HTTP till HTTP-omdirigering med hjälp av PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [HTTP till HTTP-omdirigering med CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Extern webbplats

    Välj extern webbplats om du vill omdirigera trafik på lyssnaren kopplad till regeln därför att omdirigeras till en extern webbplats. Du kan välja frågesträngen i den ursprungliga begäran som ska ingå i begäran vidarebefordras till målet för omdirigering. Du kan inte vidarebefordra sökvägen i den ursprungliga begäran till den externa platsen.

    Mer information om omdirigering till extern webbplats finns i [omdirigera trafik till extern webbplats med hjälp av PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) och [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Skriv om HTTP-huvud-inställning

Den här funktionen kan du lägga till, ta bort eller uppdatera HTTP-begäran och svarshuvuden när begäran och svarspaket flytta mellan klienten och serverdelen pooler.    Du kan konfigurera den här funktionen endast via PowerShell. Portal och CLI-stöd är inte tillgängligt ännu. Mer information finns i [skriva om HTTP-huvuden](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) översikt och [konfigurera HTTP-huvud omskrivning](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>HTTP-inställningar

Application gateway dirigerar trafik till backend-servrarna med den konfiguration som har angetts i den här komponenten. När du har skapat en HTTP-inställning måste du koppla den med en eller flera be regler för routning.

### <a name="cookie-based-affinity"></a>Cookiebaserad tillhörighet

Den här funktionen är användbart när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan programgatewayen dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession. Om programmet inte kan hantera cookie-baserad tillhörighet, kommer sedan du inte att kunna använda den här funktionen. Om du vill använda cookie-baserad sessionstillhörighet, bör du kontrollera att klienterna måste ha stöd för cookies. 

### <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen kan tillämpas på alla medlemmar i en serverdelspool samband med regelgenereringen. När du har aktiverat, Programgateway ser du till att ta bort registrera instanser av en serverdelspool inte tar emot nya begäranden samtidigt som befintliga begäranden ska slutföras inom en konfigurerad tid. Detta gäller såväl serverdelsinstanser som uttryckligen tas bort från serverdelspoolen med hjälp av ett API-anrop som serverdelsinstanser som rapporteras som skadade enligt hälsoavsökningarna.

### <a name="protocol"></a>Protokoll

Application gateway stöder både HTTP och HTTPS-protokoll för dirigering av begäranden till backend-servrarna. Om du väljer HTTP-protokollet trafik du flöden okrypterat fram till backend-servrarna. I sådana fall där okrypterad kommunikation till backend-servrarna inte är en acceptabel bör du välja HTTPS-protokollet. Den här inställningen kombinerade med välja HTTPS-protokollet i lyssnaren kan du aktivera [slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Det låter dig skicka känsliga data säkert till serverdelen i krypterad. Varje serverdels-server i serverdels-poolen som har slutpunkt-till-slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

### <a name="port"></a>Port

Det här är den port som backend-servrarna lyssnar på trafik som kommer från Application Gateway. Du kan konfigurera portar mellan 1 och 65535.

### <a name="request-timeout"></a>Timeout för begäran

Hur många sekunder som application gateway som väntar på svar från backend-poolen innan det returneras ett fel som ”tidsgränsen uppnåddes för anslutningen”.

### <a name="override-backend-path"></a>Åsidosätt serverdelssökväg

Den här inställningen kan du konfigurera ett valfritt anpassat vidarebefordran sökväg som ska användas när begäran vidarebefordras till serverdelen. Detta kommer att kopiera någon del av den inkommande sökvägen som matchar den anpassade sökväg som angavs i den **åsidosätta sökvägen till serverdelen** fältet vidarebefordrade sökvägen. Se tabellen nedan för att förstå hur funktionen fungerar.

- När HTTP-inställningarna är kopplad till en regel för vidarebefordran av grundläggande begäran:

  | Ursprungliga begäran  | Åsidosätt serverdelssökväg | Begäran vidarebefordras till serverdelen |
  | ----------------- | --------------------- | ---------------------------- |
  | /Startsida/            | /override/            | / åsidosätta/home /              |
  | / home/secondhome / | /override/            | / åsidosättning/home/secondhome /   |

- När HTTP-inställningarna är kopplad till en regel för vidarebefordran av sökvägsbaserad begäran:

  | Ursprungliga begäran           | Sökvägsregeln       | Åsidosätt serverdelssökväg | Begäran vidarebefordras till serverdelen |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / åsidosätta/home /              |
  | / pathrule/home/secondhome / | /pathrule*      | /override/            | / åsidosättning/home/secondhome /   |
  | /Startsida/                     | /pathrule*      | /override/            | / åsidosätta/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / åsidosättning/home/secondhome /   |
  | /pathrule/home /            | / pathrule/startsidan * | /override/            | /override/                   |
  | / pathrule/home/secondhome / | / pathrule/startsidan * | /override/            | / åsidosätta/secondhome /        |

### <a name="use-for-app-service"></a>Använd för apptjänst

Det här är en UI-genväg som väljer de två inställningarna som krävs för App service-serverdelen – aktiverar Välj värdnamnet från serverdelsadressen och skapar en ny anpassad avsökning. Orsaken till varför f.d. görs beskrivs i avsnittet för **Välj värdnamnet från serverdelsadressen** inställningen. En ny avsökning skapas där avsökningen huvudet också plockas upp från serverdelen medlems-adress.

### <a name="use-custom-probe"></a>Använd anpassad avsökning

Den här inställningen används för att associera en [anpassad avsökning](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) med den här inställningen för HTTP. Du kan associera endast en anpassad avsökning med en HTTP-inställning. Om du inte uttryckligen associera en anpassad avsökning sedan [standard avsökningen](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) används för att övervaka hälsotillståndet för serverdelen. Du rekommenderas att du skapar en anpassad avsökning om du vill ha mer detaljerad kontroll över hälsoövervakning av serverdelen.

> [!NOTE]   
> Anpassad avsökning kommer inte att börja övervaka hälsotillståndet för serverdelspoolen, såvida inte den motsvarande HTTP-inställningen är uttryckligen associerad med en lyssnare.

### <a name="pick-host-name-from-backend-address"></a>Välj värdnamn från serverdelsadress

Den här funktionen dynamiskt anger den *värden* huvudet i begäran till värdnamnet för serverdelspoolen genom att använda en IP-adress eller fullständigt kvalificerat domännamn (FQDN). Det här är användbart i scenarier där domännamnet för serverdelen skiljer sig från DNS-namnet på application gateway och serverdelen som förlitar sig på en specifik värdrubrik eller ett SNI-tillägg för att matcha mot rätt slutpunkt, som vid tjänster med flera innehavare som serverdelen. Eftersom App service är en tjänst för flera klienter som använder en delad utrymme med en IP-adress, kan en App service nås endast med värdnamn som konfigurerats i inställningarna för anpassad domän. Det anpassade domännamnet är som standard *example.azurewebsites.net*. Därför, om du vill komma åt din App service med application gateway med antingen ett värdnamn som inte uttryckligen har registrerats i App service eller med Application gateway FQDN du behöver åsidosätta värdnamnet i den ursprungliga begäran till App service-värdnamn, genom att Aktivera **Välj värdnamnet från serverdelsadressen** inställningen.

Om du äger en anpassad domän och har mappat befintligt anpassat DNS-namn till App service, behöver du inte aktivera den här inställningen.

> [!NOTE]   
> Den här inställningen behövs inte för App Service Environment (ASE) eftersom ASE är en särskild distribution. 

### <a name="host-name-override"></a>Åsidosätt för värd-namn

Den här funktionen ersätter den *värden* rubrik i den inkommande begäran på application gateway till värdnamn som du anger här. Exempel: om www.contoso.com har angetts som den **värdnamn** inställningen, den ursprungliga begäran https://appgw.eastus.cloudapp.net/path1 kommer att ändras till https://www.contoso.com/path1 när begäran vidarebefordras till backend-servern. 

## <a name="backend-pool"></a>Serverdelspool

En serverdelspool kan vara visade fyra typer av serverdelen medlemmar: en specifik virtuell dator, virtual machine scale Sets, en IP-adressen/FQDN eller en app service. Varje serverdelspool kan peka på flera medlemmar av samma typ. Peka medlemmar av olika typer i samma serverdelspool stöds inte. 

När du skapar en serverdelspool kan behöva du associera det med en eller flera begäran routningsregler. Du måste också konfigurera hälsotillståndsavsökningar för varje serverdelspool på application gateway. När en begäran om routning villkor uppfylls, vidarebefordrar trafik till felfria servrarna (som bestäms av hälsoavsökningarna) i motsvarande serverdelspoolen application gateway.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Även om application gateway övervakar hälsotillståndet för alla resurser i serverdelen som standard, bör du skapa en anpassad avsökning för varje serverdelens HTTP-inställning för att få en mer detaljerad kontroll över övervakning av hälsotillstånd. Läs hur du konfigurerar anpassade hälsoavsökning i [anpassad avsökning hälsomiljöer](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> När du skapar en anpassad hälsoavsökning, måste du koppla den till en serverdel HTTP-inställning. Anpassad avsökning kommer inte att börja övervaka hälsotillståndet för serverdelspoolen, såvida inte den motsvarande HTTP-inställningen är uttryckligen associerad med en lyssnare.

## <a name="next-steps"></a>Nästa steg

När du läst om Application Gateway-komponenter kan du:

- [Skapa en Programgateway i Azure portal](quick-create-portal.md)
- [Skapa en Programgateway med hjälp av PowerShell](quick-create-powershell.md)
- [Skapa en Programgateway med hjälp av Azure CLI](quick-create-cli.md)