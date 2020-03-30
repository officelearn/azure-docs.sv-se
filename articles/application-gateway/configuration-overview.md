---
title: Konfigurationsöversikt för Azure Application Gateway
description: I den hÃ¤r artikeln beskrivs hur du konfigurerar komponenterna i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: f31c24c96732ec3311ea904fc9c63344e2d14109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371242"
---
# <a name="application-gateway-configuration-overview"></a>Översikt över konfigurationen för Application Gateway

Azure Application Gateway består av flera komponenter som du kan konfigurera på olika sätt för olika scenarier. Den här artikeln visar hur du konfigurerar varje komponent.

![Flödesschema för komponenter för programgateway](./media/configuration-overview/configuration-overview1.png)

Den här bilden illustrerar ett program som har tre lyssnare. De två första är lyssnare `http://acme.com/*` `http://fabrikam.com/*`på flera webbplatser för respektive . Båda lyssnar på port 80. Den tredje är en grundläggande lyssnare som har slutpunkt till slutpunkt Transport Layer Security (TLS) avslutning, tidigare känd som SSL-avslutning (Secure Sockets Layer).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuellt Azure-nätverk och dedikerat undernät

En programgateway är en dedikerad distribution i ditt virtuella nätverk. I det virtuella nätverket krävs ett dedikerat undernät för programgatewayen. Du kan ha flera instanser av en viss programgatewaydistribution i ett undernät. Du kan också distribuera andra programgateways i undernätet. Men du kan inte distribuera någon annan resurs i undernätet för programgateway.

> [!NOTE]
> Du kan inte blanda Standard_v2 och Standard Azure Application Gateway på samma undernät.

#### <a name="size-of-the-subnet"></a>Storleken på undernätet

Application Gateway använder en privat IP-adress per instans, plus en annan privat IP-adress om en privat klient-TV-ip har konfigurerats.

Azure reserverar också fem IP-adresser i varje undernät för internt bruk: de första fyra och de senaste IP-adresserna. Tänk dig till exempel 15 programgatewayinstanser utan privat klient-TV-ip. Du behöver minst 20 IP-adresser för det här undernätet: fem för internt bruk och 15 för instanser av programgateway. Så du behöver en /27-undernätsstorlek eller större.

Överväg ett undernät som har 27 programgatewayinstanser och en IP-adress för en privat klient-TV-ip.Consider a subnet that has 27 application gateway instances and an IP address for a private front-end IP. I det här fallet behöver du 33 IP-adresser: 27 för instanser av programgateway, en för den privata klientdelen och fem för internt bruk. Så du behöver en /26 undernätsstorlek eller större.

Vi rekommenderar att du använder en undernätsstorlek på minst /28. Denna storlek ger dig 11 användbara IP-adresser. Om programbelastningen kräver fler än 10 Application Gateway-instanser bör du tänka på en undernätsstorlek på /27 eller /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Nätverkssäkerhetsgrupper i undernätet Application Gateway

Nätverkssäkerhetsgrupper (NSG) stöds på Application Gateway. Men det finns vissa begränsningar:

- Du måste tillåta inkommande Internet-trafik på TCP-portar 65503-65534 för Application Gateway v1 SKU och TCP-portar 65200-65535 för v2 SKU med målundernätet som **Alla** och källa som **GatewayManager-tjänsttag.** Det här portintervallet krävs för Azure-infrastrukturkommunikation. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter, inklusive kunderna för dessa gateways, kan inte kommunicera på dessa slutpunkter.

- Det går inte att blockera utgående internetanslutning. Standardregler för utgående trafik i NSG tillåter internetanslutning. Vi rekommenderar att du gör följande:

  - Ta inte bort standardreglerna för utgående.
  - Skapa inte andra utgående regler som nekar utgående anslutning.

- Trafik från **AzureLoadBalancer-taggen** måste tillåtas.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Tillåt åtkomst till programgateway till några käll-IP-adresser

I det här scenariot använder du NSG:er i undernätet Application Gateway. Placera följande begränsningar för undernätet i den här prioritetsordningen:

1. Tillåt inkommande trafik från ett käll-IP- eller IP-intervall med målet som hela application gateway-undernätsadressintervallet och målporten som inkommande åtkomstport, till exempel port 80 för HTTP-åtkomst.
2. Tillåt inkommande begäranden från källan som **GatewayManager-tjänsttagg** och mål som **Alla** och målportar som 65503-65534 för Application Gateway v1 SKU och portarna 65200-65535 för v2 SKU för kommunikation med [slutskyddsstatus](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Det här portintervallet krävs för Azure-infrastrukturkommunikation. Dessa portar är skyddade (låsta) av Azure-certifikat. Utan lämpliga certifikat kan externa entiteter inte initiera ändringar på dessa slutpunkter.
3. Tillåt inkommande Azure Load Balancer-avsökningar *(AzureLoadBalancer-tagg)* och inkommande virtuell nätverkstrafik *(VirtualNetwork-tagg)* i [nätverkssäkerhetsgruppen](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blockera all annan inkommande trafik med hjälp av en regel neka alla.
5. Tillåt utgående trafik till internet för alla destinationer.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Användardefinierade vägar som stöds på Application Gateway-undernätet

> [!IMPORTANT]
> Om du använder UDR:er i undernätet Application Gateway kan hälsostatusen i hälsovyn för [serverdelen](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) visas som **Okänd**. Det kan också orsaka generering av Application Gateway loggar och mått misslyckas. Vi rekommenderar att du inte använder UDR:er i undernätet Application Gateway så att du kan visa backend-hälsa, loggar och mått.

- **v1 (på)**

   För v1 SKU stöds användardefinierade vägar (UDRs) i undernätet Application Gateway, så länge de inte ändrar kommunikation mellan på begäran/svar. Du kan till exempel ställa in en UDR i undernätet Application Gateway så att den pekar på en brandväggsinstallation för paketbesiktning. Men du måste se till att paketet kan nå sin avsedda destination efter inspektion. Om du inte gör det kan det leda till felaktigt hälsoavsöknings- eller trafikroutningsbeteende. Detta inkluderar inlärda vägar eller standardvägar 0.0.0.0/0 som sprids av Azure ExpressRoute- eller VPN-gateways i det virtuella nätverket.

- **v2**

   För v2 SKU finns det scenarier som stöds och inte stöds:

   **v2 scenarier som stöds**
   > [!WARNING]
   > En felaktig konfiguration av flödestabellen kan resultera i asymmetrisk routning i Application Gateway v2. Se till att all lednings-/kontrollflygtrafik skickas direkt till Internet och inte via en virtuell installation. Loggning och mått kan också påverkas.


  **Scenario 1**: UDR för att inaktivera BGP-vägspridning (Border Gateway Protocol) till undernätet Application Gateway

   Ibland annonseras standardgatewayruwayvägen (0.0.0.0/0) via ExpressRoute- eller VPN-gateways som är associerade med det virtuella nätverket Application Gateway. Detta bryter ledningens flygtrafik, vilket kräver en direkt väg till Internet. I sådana fall kan en UDR användas för att inaktivera BGP-vägspridning. 

   Så här inaktiverar du BGP-vägspridning:

   1. Skapa en flödestabellresurs i Azure.
   2. Inaktivera parametern för spridning av dirigerar för **virtuell nätverksgateway.** 
   3. Associera flödestabellen till lämpligt undernät. 

   Om du aktiverar UDR för det här scenariot bör du inte bryta några befintliga inställningar.

  **Scenario 2**: UDR att rikta 0.0.0.0/0 till Internet

   Du kan skapa en UDR för att skicka 0.0.0.0/0-trafik direkt till Internet. 

  **Scenario 3**: UDR för Azure Kubernetes Service kubenet

  Om du använder kubenet med Azure Kubernetes Service (AKS) och AGIC (Application Gateway Ingress Controller) måste du ställa in en vägtabell så att trafik som skickas till poddar dirigeras till rätt nod. Detta är inte nödvändigt om du använder Azure CNI. 

   Så här ställer du in flödestabellen så att kubenet kan fungera:

  1. Skapa en flödestabellresurs i Azure. 
  2. När den har skapats går du till sidan **Rutter.** 
  3. Lägg till en ny rutt:
     - Adressprefix bör vara IP-intervallet för de poddar du vill nå i AKS. 
     - Nästa hopptyp ska vara **Virtual Appliance**. 
     - Nästa hoppadress ska vara IP-adressen för den nod som är värd för poddar inom IP-intervallet som definierats i adressprefixfältet. 
    
  **v2 scenarier som inte stöds**

  **Scenario 1:** UDR för virtuella apparater

  Alla scenarier där 0.0.0.0/0 behöver omdirigeras via en virtuell installation, ett virtuellt nätverk för nav/eker eller lokalisering (påtvingad tunnelning) stöds inte för den offentliga förhandsversionen av v2. 

## <a name="front-end-ip"></a>Ip-adress i fronten

Du kan konfigurera programgatewayen så att den har en offentlig IP-adress, en privat IP-adress eller båda. En offentlig IP krävs när du är värd för en back end som klienter måste komma åt via Internet via en internet-facing virtuell IP (VIP). 

En offentlig IP krävs inte för en intern slutpunkt som inte är exponerad för internet. Det kallas en *intern load-balancer* (ILB) slutpunkt eller privat frontend IP. En programgateway ILB är användbar för interna affärsprogram som inte är exponerade för Internet. Det är också användbart för tjänster och nivåer i ett flernivåprogram inom en säkerhetsgräns som inte är exponerade för internet men som kräver lastdistribution av round-robin, sessionssnasighet eller TLS-avslutning.

Endast en offentlig IP-adress eller en privat IP-adress stöds. Du väljer front-end IP när du skapar programgatewayen.

- För en offentlig IP kan du skapa en ny offentlig IP-adress eller använda en befintlig offentlig IP-adress på samma plats som programgatewayen. Mer information finns i [statisk kontra dynamisk offentlig IP-adress](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- För en privat IP-adress kan du ange en privat IP-adress från undernätet där programgatewayen skapas. Om du inte anger en väljs en godtycklig IP-adress automatiskt från undernätet. Ip-adresstypen som du väljer (statisk eller dynamisk) kan inte ändras senare. Mer information finns i [Skapa en programgateway med en intern belastningsutjämnare](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

En front-end IP-adress är associerad med en *lyssnare*, som söker efter inkommande begäranden på frontend IP.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk entitet som söker efter inkommande anslutningsbegäranden med hjälp av porten, protokollet, värden och IP-adressen. När du konfigurerar lyssnaren måste du ange värden för dessa som matchar motsvarande värden i den inkommande begäran på gatewayen.

När du skapar en programgateway med hjälp av Azure-portalen skapar du också en standardlyssnare genom att välja protokoll och port för lyssnaren. Du kan välja om http2-stöd ska aktiveras på lyssnaren. När du har skapat programgatewayen kan du redigera inställningarna för den standardlyssnaren (*appGatewayHttpListener*) eller skapa nya lyssnare.

### <a name="listener-type"></a>Text för lyssnare

När du skapar en ny lyssnare väljer du mellan [ *grundläggande* och *flera webbplatser*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Om du vill att alla dina förfrågningar (för alla domäner) ska accepteras och vidarebefordras till backend-pooler väljer du grundläggande. Lär dig hur du [skapar en programgateway med en grundläggande lyssnare](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Om du vill vidarebefordra begäranden till olika serverdpooler baserat på *värdhuvudet* eller värdnamnet väljer du lyssnare på flera webbplatser, där du också måste ange ett värdnamn som matchar den inkommande begäran. Detta beror på att Application Gateway förlitar sig på HTTP 1.1-värdhuvuden för att vara värd för mer än en webbplats på samma offentliga IP-adress och port.

#### <a name="order-of-processing-listeners"></a>Ordning för bearbetning av lyssnare

För v1 SKU matchas begäranden enligt reglernas ordning och typen av lyssnare. Om en regel med grundläggande lyssnare kommer först i ordningen bearbetas den först och accepterar alla förfrågningar om den porten och IP-kombinationen. För att undvika detta konfigurerar du reglerna med lyssnare på flera webbplatser först och skickar regeln med den grundläggande lyssnaren till den sista i listan.

För v2 SKU bearbetas lyssnare på flera webbplatser före grundläggande lyssnare.

### <a name="front-end-ip"></a>Ip-adress i fronten

Välj den frontend-IP-adress som du planerar att associera med den här lyssnaren. Lyssnaren lyssnar på inkommande begäranden på den här IP-adressen.

### <a name="front-end-port"></a>Front-end-port

Välj frontend-port. Välj en befintlig port eller skapa en ny. Välj ett värde från det [tillåtna portintervallet](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Du kan använda inte bara välkända portar, till exempel 80 och 443, men alla tillåtna anpassade portar som är lämpliga. En port kan användas för lyssnare som vetter mot allmänheten eller lyssnare med privat håll.

### <a name="protocol"></a>Protokoll

Välj HTTP eller HTTPS:

- Om du väljer HTTP är trafiken mellan klienten och programgatewayen okrypterad.

- Välj HTTPS om du vill att [TLS-avslutning](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) eller [TLS-kryptering ska avslutas](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Trafiken mellan klienten och programgatewayen krypteras. Och TLS-anslutningen avslutas vid programgatewayen. Om du vill ha slutpunkt till slutpunkt TLS-kryptering måste du välja HTTPS och konfigurera **http-inställningen för serverdelen.** Detta säkerställer att trafiken krypteras om när den färdas från programgatewayen till den bakre delen.

- Välj HTTPS om du vill att [TLS-avslutning](features.md#secure-sockets-layer-ssltls-termination) eller [TLS-kryptering ska avslutas](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Trafiken mellan klienten och programgatewayen krypteras. Och TLS-anslutningen avslutas vid programgatewayen. Om du vill ha slutpunkt till slutpunkt TLS-kryptering måste du välja HTTPS och konfigurera **http-inställningen för serverdelen.** Detta säkerställer att trafiken krypteras om när den färdas från programgatewayen till den bakre delen.


Om du vill konfigurera TLS-avslutning och end-to-end TLS-kryptering måste du lägga till ett certifikat till lyssnaren för att programgatewayen ska kunna härleda en symmetrisk nyckel. Detta dikteras av TLS-protokollspecifikationen. Den symmetriska nyckeln används för att kryptera och dekryptera den trafik som skickas till gatewayen. Gateway-certifikatet måste vara i PFX-format (Personal Information Exchange). Med det här formatet kan du exportera den privata nyckeln som gatewayen använder för att kryptera och dekryptera trafik.

#### <a name="supported-certificates"></a>Certifikat som stöds

Se [certifikat som stöds för TLS-avslutning](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Stöd för tilläggsprotokoll

#### <a name="http2-support"></a>HTTP2-stöd

HTTP/2-protokollstöd är endast tillgängligt för klienter som endast ansluter till lyssnare för programgateway. Kommunikationen till serverpooler för backend är över HTTP/1.1. Som standard är HTTP/2-stöd inaktiverat. Följande Azure PowerShell-kodavsnitt visar hur du aktiverar detta:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket-stöd

WebSocket-stöd är aktiverat som standard. Det finns ingen inställning för användaren som kan konfigureras för att aktivera eller inaktivera den. Du kan använda WebSockets med både HTTP- och HTTPS-lyssnare.

### <a name="custom-error-pages"></a>Anpassade felsidor

Du kan definiera anpassade fel på global nivå eller lyssnarnivå. Men det går inte att skapa anpassade felsidor på global nivå från Azure-portalen. Du kan konfigurera en anpassad felsida för ett brandväggsfel för 403-webbprogram eller en 502-underhållssida på lyssnarnivå. Du måste också ange en offentligt tillgänglig blob-URL för den angivna felstatuskoden. Mer information finns i [Skapa anpassade felsidor i Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Felkoder för programgateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Information om hur du konfigurerar en global anpassad felsida finns i [Azure PowerShell-konfiguration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>TLS-princip

Du kan centralisera TLS/SSL-certifikathantering och minska krypteringsdekrypteringskostnaderna för en servergrupp för server. Med centraliserad TLS-hantering kan du också ange en central TLS-princip som passar dina säkerhetskrav. Du kan välja *standardprincip,* *fördefinierad*eller *anpassad* TLS-princip.

Du konfigurerar TLS-principen för att styra TLS-protokollversioner. Du kan konfigurera en programgateway för att använda en minsta protokollversion för TLS-handslag från TLS1.0, TLS1.1 och TLS1.2. Som standard är SSL 2.0 och 3.0 inaktiverade och kan inte konfigureras. Mer information finns i [TLS-principöversikt för Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

När du har skapat en lyssnare associerar du den med en regel för begärandirigering. Den regeln avgör hur begäranden som tas emot på lyssnaren dirigeras till den bakre delen.

## <a name="request-routing-rules"></a>Regler för begär routning

När du skapar en programgateway med hjälp av Azure-portalen skapar du en standardregel (*regel1*). Den här regeln binder standardlyssnaren (*appGatewayHttpListener*) med standardpoolen för backend *(appGatewayBackendPool)* och standardbaserade HTTP-inställningar *(appGatewayBackendHttpSettings*). När du har skapat gatewayen kan du redigera inställningarna för standardregeln eller skapa nya regler.

### <a name="rule-type"></a>Typ av regel

När du skapar en regel väljer du mellan [ *grundläggande* och *sökvägsbaserad*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Välj grundläggande om du vill vidarebefordra alla begäranden på den associerade lyssnaren (till exempel *blogg<i></i>\*.contoso.com/)* till en enda backend-pool.
- Välj sökvägsbaserad om du vill dirigera begäranden från specifika URL-sökvägar till specifika backend-pooler. Banmönstret tillämpas endast på URL:ens sökväg, inte på frågeparametrarna.

#### <a name="order-of-processing-rules"></a>Regler för bearbetning av bearbetning

För v1 SKU bearbetas mönstermatchning av inkommande begäranden i den ordning som sökvägarna visas i URL-sökvägen för den sökvägsbaserade regeln. Om en begäran matchar mönstret i två eller flera sökvägar i sökvägskartan matchas sökvägen som visas först. Och begäran vidarebefordras till den bakre änden som är associerad med den sökvägen.

För v2 SKU är en exakt matchning högre prioritet än banordningen i URL-sökvägen. Om en begäran matchar mönstret i två eller flera sökvägar vidarebefordras begäran till den bakre änden som är associerad med sökvägen som exakt matchar begäran. Om sökvägen i den inkommande begäran inte exakt matchar någon sökväg i kartan bearbetas mönstermatchning av begäran i sökvägsordningslistan för den sökvägsbaserade regeln.

### <a name="associated-listener"></a>Associerad lyssnare

Associera en lyssnare till regeln så att *regeln om begärandirigering* som är associerad med lyssnaren utvärderas för att bestämma backend-poolen att dirigera begäran till.

### <a name="associated-back-end-pool"></a>Associerad backend-pool

Associera till regeln backend-poolen som innehåller backend-mål som betjänar begäranden som lyssnaren tar emot.

 - För en grundregel är endast en backend-pool tillåten. Alla begäranden på den associerade lyssnaren vidarebefordras till den backend-poolen.

 - För en sökvägsbaserad regel lägger du till flera backend-pooler som motsvarar varje URL-sökväg. De begäranden som matchar url-sökvägen som har angetts vidarebefordras till motsvarande backend-pool. Lägg också till en standardpool för backend.Also, add a default back-end pool. Begäranden som inte matchar någon URL-sökväg i regeln vidarebefordras till poolen.

### <a name="associated-back-end-http-setting"></a>Associerad HTTP-inställning för backend

Lägg till en http-inställning för backend för varje regel. Begäranden dirigeras från programgatewayen till backend-målen med hjälp av portnummer, protokoll och annan information som anges i den här inställningen.

För en grundregel tillåts endast en http-inställning för backend. Alla begäranden på den associerade lyssnaren vidarebefordras till motsvarande backend-mål med den här HTTP-inställningen.

För en sökvägsbaserad regel lägger du till flera HTTP-inställningar för backend som motsvarar varje URL-sökväg. Begäranden som matchar URL-sökvägen i den här inställningen vidarebefordras till motsvarande backend-mål med hjälp av HTTP-inställningarna som motsvarar varje URL-sökväg. Lägg också till en standard-HTTP-inställning. Begäranden som inte matchar någon URL-sökväg i den här regeln vidarebefordras till standardpoolen för backend-funktioner med hjälp av standard-HTTP-inställningen.

### <a name="redirection-setting"></a>Inställning för omdirigering

Om omdirigering har konfigurerats för en grundregel omdirigeras alla begäranden på den associerade lyssnaren till målet. Detta är *global* omdirigering. Om omdirigering är konfigurerad för en sökvägsbaserad regel omdirigeras endast begäranden i ett visst platsområde. Ett exempel är ett kundvagnsområde som betecknas med */cart/\**. Detta är *sökvägsbaserad* omdirigering.

Mer information om omdirigeringar finns i [översikt över omdirigering av Programgateway](redirect-overview.md).

#### <a name="redirection-type"></a>Typ av omdirigering

Välj den typ av omdirigering som krävs: *Permanent(301),* *Tillfällig(307)*, *Found(302)* eller *Se andra(303)*.

#### <a name="redirection-target"></a>Mål för omdirigering

Välj en annan lyssnare eller en extern webbplats som omdirigeringsmål.

##### <a name="listener"></a>Lyssnare

Välj lyssnare som omdirigeringsmål för att omdirigera trafik från en lyssnare till en annan på gatewayen. Den här inställningen krävs när du vill aktivera HTTP-till-HTTPS-omdirigering. Den omdirigerar trafik från källavlyssnaren som söker efter inkommande HTTP-begäranden till målavlyssnaren som söker efter inkommande HTTPS-begäranden. Du kan också välja att inkludera frågesträngen och sökvägen från den ursprungliga begäran i begäran som vidarebefordras till omdirigeringsmålet.

![Dialogrutan Komponenter för Programgateway](./media/configuration-overview/configure-redirection.png)

Mer information om omdirigering av HTTP-till-HTTPS finns i:
- [HTTP-till-HTTPS-omdirigering med hjälp av Azure-portalen](redirect-http-to-https-portal.md)
- [HTTP-till-HTTPS-omdirigering med PowerShell](redirect-http-to-https-powershell.md)
- [HTTP-till-HTTPS-omdirigering med hjälp av Azure CLI](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Extern webbplats

Välj extern webbplats när du vill omdirigera trafiken på lyssnaren som är associerad med den här regeln till en extern webbplats. Du kan välja att inkludera frågesträngen från den ursprungliga begäran i begäran som vidarebefordras till omdirigeringsmålet. Du kan inte vidarebefordra sökvägen till den externa platsen som fanns i den ursprungliga begäran.

Mer information om omdirigering finns i:
- [Omdirigera trafik till en extern webbplats med hjälp av PowerShell](redirect-external-site-powershell.md)
- [Omdirigera trafik till en extern plats med hjälp av CLI](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>Skriva om HTTP-huvudinställningen

Den här inställningen lägger till, tar bort eller uppdaterar HTTP-begäran och svarshuvuden medan begäran och svarspaket flyttas mellan klient- och backend-poolerna. Mer information finns i:

 - [Skriv om HTTP-huvuden översikt](rewrite-http-headers.md)
 - [Konfigurera omskrivning av HTTP-huvud](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP-inställningar

Programgatewayen dirigerar trafik till backend-servrarna med hjälp av den konfiguration som du anger här. När du har skapat en HTTP-inställning måste du associera den med en eller flera regler för routning av begäranden.

### <a name="cookie-based-affinity"></a>Cookie-baserad tillhörighet

Azure Application Gateway använder gateway-hanterade cookies för att underhålla användarsessioner. När en användare skickar den första begäran till Application Gateway, anger den en tillhörighetscookie i svaret med ett hash-värde som innehåller sessionsinformationen, så att de efterföljande begärandena som bär tillhörighetscookien dirigeras till samma server för serverdelsservern för upprätthålla klibbighet. 

Den här funktionen är användbar när du vill behålla en användarsession på samma server och när sessionstillstånd sparas lokalt på servern för en användarsession. Om programmet inte kan hantera cookie-baserad tillhörighet kan du inte använda den här funktionen. För att använda den, se till att klienterna stöder cookies.

[Chromium browser](https://www.chromium.org/Home) [v80-uppdateringen](https://chromiumdash.appspot.com/schedule) medförde ett mandat där HTTP-cookies utan [SameSite-attribut](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) måste behandlas som SameSite=Lax. När det gäller CORS-begäranden (Cross-Origin Resource Sharing) måste cookien, om cookien måste skickas i en tredjepartskontext, använda *SameSite=None. Säkra* attribut och det bör endast skickas via HTTPS. Annars, i ett HTTP-scenario, skickar webbläsaren inte cookies i tredjepartskontexten. Målet med den här uppdateringen från Chrome är att öka säkerheten och undvika CSRF-attacker (Cross-Site Request Forgery). 

För att stödja denna ändring, från och med 17 februari 2020, kommer Application Gateway (alla SKU-typer) att injicera en annan cookie som heter *ApplicationGatewayAffinityCORS* utöver den befintliga *ApplicationGatewayAffinity-cookien.* *ApplicationGatewayAffinityCORS-cookien* har ytterligare två attribut tillagda i den (*"SameSite=None; Säker"*) så att klibbiga session upprätthålls även för begäranden med flera ursprung.

Observera att standardnamnet för tillhörighetscookien är *ApplicationGatewayAffinity* och du kan ändra det. Om du använder ett anpassat affinitetscookinamn läggs ytterligare en cookie till med CORS som suffix. Till exempel *CustomCookieNameCORS*.

> [!NOTE]
> Om attributet *SameSite=None* är inställt är det obligatoriskt att cookien också innehåller flaggan *Säker* och måste skickas via HTTPS.  Om sessionstillhörighet krävs via CORS måste du migrera arbetsbelastningen till HTTPS. Se TLS-avlastning och End-to-End TLS-dokumentation för Application Gateway här – [Översikt](ssl-overview.md), [Konfigurera en programgateway med TLS-avslutning med Azure-portalen](create-ssl-portal.md), [Konfigurera end-to-end TLS med hjälp av Application Gateway med portalen](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Anslutningstömning

Anslutningsdränning hjälper dig att smidigt ta bort backend-poolmedlemmar under planerade serviceuppdateringar. Du kan använda den här inställningen på alla medlemmar i en backend-pool när regeln skapas. Det säkerställer att alla avregistreringsinstanser av en backend-pool fortsätter att underhålla befintliga anslutningar och betjäna pågående begäranden om en konfigurerbar timeout och inte får några nya begäranden eller anslutningar. Det enda undantaget till detta är begäranden som är bundna för avregistrering av instanser på grund av gateway-hanterad sessionstillhörighet och kommer att fortsätta att vidarebefordras till avregistreringsinstanserna. Anslutningsdränning gäller backend-instanser som uttryckligen tas bort från backend-poolen.

### <a name="protocol"></a>Protokoll

Application Gateway stöder både HTTP och HTTPS för routningsbegäranden till backend-servrarna. Om du väljer HTTP är trafiken till backend-servrarna okrypterad. Om okrypterad kommunikation inte är acceptabel väljer du HTTPS.

Den här inställningen i kombination med HTTPS i lyssnaren stöder [TLS från slutna till slutna.](ssl-overview.md) På så sätt kan du på ett säkert sätt överföra känsliga data som krypterats till baksidan. Varje backend-server i backend-poolen som har TLS-uppbacknings-till-slut måste konfigureras med ett certifikat för att möjliggöra säker kommunikation.

### <a name="port"></a>Port

Den här inställningen anger porten där backend-servrarna lyssnar på trafik från programgatewayen. Du kan konfigurera portar från 1 till 65535.

### <a name="request-timeout"></a>Tidsgränsen för begäran

Den här inställningen är det antal sekunder som programgatewayen väntar på att få ett svar från backend-servern.

### <a name="override-back-end-path"></a>Åsidosätt backend-bana

Med den här inställningen kan du konfigurera en valfri anpassad vidarebefordringssökväg som ska användas när begäran vidarebefordras till serverdelen. Alla delar av den inkommande sökvägen som matchar den anpassade sökvägen i **sökvägen för åsidosättningsbakdel** kopieras till den vidarebefordrade sökvägen. Följande tabell visar hur den här funktionen fungerar:

- När HTTP-inställningen är kopplad till en grundläggande regel för routning av begäran:

  | Ursprunglig begäran  | Åsidosätt backend-bana | Begäran vidarebefordrad till backend |
  | ----------------- | --------------------- | ---------------------------- |
  | /hem/            | /åsidosättning/            | /override/home/              |
  | /home/secondhome/ | /åsidosättning/            | /override/home/secondhome/   |

- När HTTP-inställningen är kopplad till en sökvägsbaserad routningsregel för begäran:

  | Ursprunglig begäran           | Regel för bana       | Åsidosätt backend-bana | Begäran vidarebefordrad till backend |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /åsidosättning/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /åsidosättning/            | /override/home/secondhome/   |
  | /hem/                     | /pathrule*      | /åsidosättning/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /åsidosättning/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /åsidosättning/            | /åsidosättning/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /åsidosättning/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /åsidosättning/            | /åsidosättning/                   |

### <a name="use-for-app-service"></a>Används för apptjänst

Det här är en kortkommando som bara väljer de två nödvändiga inställningarna för Azure App Service-backend. Det gör det möjligt **att välja värdnamn från backend-adress**, och det skapar en ny anpassad avsökning om du inte redan har en. (Mer information finns i avsnittet [Välj värdnamn från backend-adressinställning](#pick) i den här artikeln.) En ny avsökning skapas och avsökningshuvudet plockas från serverdelsmedlemmens adress.

### <a name="use-custom-probe"></a>Använd anpassad avsökning

Den här inställningen associerar en [anpassad avsökning](application-gateway-probe-overview.md#custom-health-probe) med en HTTP-inställning. Du kan bara associera en anpassad avsökning med en HTTP-inställning. Om du inte uttryckligen associerar en anpassad avsökning används [standardavsökningen](application-gateway-probe-overview.md#default-health-probe-settings) för att övervaka hälsotillståndet för serverdelen. Vi rekommenderar att du skapar en anpassad avsökning för större kontroll över hälsoövervakningen av dina serverdelar.

> [!NOTE]
> Den anpassade avsökningen övervakar inte hälsotillståndet för backend-poolen om inte motsvarande HTTP-inställning uttryckligen associeras med en lyssnare.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Välj värdnamn från backend-adress

Den här funktionen anger *värdhuvudet* dynamiskt i begäran till värdnamnet för backend-poolen. Den använder en IP-adress eller FQDN.

Den här funktionen hjälper när domännamnet för serverdelen skiljer sig från DNS-namnet för programgatewayen och serverdelen är beroende av ett visst värdhuvud för att matcha till rätt slutpunkt.

Ett exempel fall är flera innehavare tjänster som backend. En apptjänst är en tjänst med flera innehavare som använder ett delat utrymme med en enda IP-adress. Så kan en apptjänst endast nås via de värdnamn som är konfigurerade i de anpassade domäninställningarna.

Som standard är det anpassade domännamnet *example.azurewebsites.net*. Om du vill komma åt din apptjänst med hjälp av en programgateway via ett värdnamn som inte uttryckligen är registrerat i apptjänsten eller via programgatewayens FQDN åsidosätter du värdnamnet i den ursprungliga begäran till apptjänstens värdnamn. Det gör du genom att aktivera **plockvärdnamnet från inställningen för backend-adress.**

För en anpassad domän vars befintliga anpassade DNS-namn mappas till apptjänsten behöver du inte aktivera den här inställningen.

> [!NOTE]
> Den här inställningen krävs inte för App Service Environment, som är en dedikerad distribution.

### <a name="host-name-override"></a>Åsidosättning av värdnamn

Den här funktionen ersätter *värdhuvudet* i den inkommande begäran på programgatewayen med det värdnamn som du anger.

Om *www.contoso.com* till exempel anges i inställningen **Värdnamn** ändras den`https://appgw.eastus.cloudapp.azure.com/path1` ursprungliga begäran`https://www.contoso.com/path1` * till * när begäran vidarebefordras till serverdelsservern.

## <a name="back-end-pool"></a>Serverdelspool

Du kan peka en backend-pool till fyra typer av serverdelsmedlemmar: en viss virtuell dator, en skalbar för den virtuella datorn, en IP-adress/FQDN eller en apptjänst. 

När du har skapat en backend-pool måste du associera den med en eller flera regler för routning av begäranden. Du måste också konfigurera hälsoavsökningar för varje backend-pool på programgatewayen. När ett villkor för routningsfördirigering av begäran är uppfyllt vidarebefordrar programgatewayen trafiken till de felfria servrarna (enligt hälsoavsökningarna) i motsvarande backend-pool.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En programgateway övervakar hälsotillståndet för alla resurser i dess backend som standard. Men vi rekommenderar starkt att du skapar en anpassad avsökning för varje http-inställning för serverdel för att få större kontroll över hälsoövervakning. Mer information om hur du konfigurerar en anpassad avsökning finns i [Anpassade inställningar för hälsoavsökning](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> När du har skapat en anpassad hälsoavsökning måste du koppla den till en HTTP-inställning för serverdel. En anpassad avsökning övervakar inte hälsotillståndet för backend-poolen om inte motsvarande HTTP-inställning uttryckligen associeras med en lyssnare med hjälp av en regel.

## <a name="next-steps"></a>Nästa steg

Nu när du känner till Application Gateway-komponenter kan du:

- [Skapa en programgateway i Azure-portalen](quick-create-portal.md)
- [Skapa en programgateway med PowerShell](quick-create-powershell.md)
- [Skapa en programgateway med hjälp av Azure CLI](quick-create-cli.md)
