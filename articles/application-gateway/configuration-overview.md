---
title: Azure Application Gateway configuration-översikt
description: Den här artikeln beskriver hur du konfigurerar komponenterna i Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 40c5444a54f4e483a9dcacb958c18f66da45019a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58906131"
---
# <a name="application-gateway-configuration-overview"></a>Översikt över Application Gateway-konfiguration

Azure Application Gateway består av flera komponenter som du kan konfigurera på olika sätt för olika scenarier. Den här artikeln visar hur du konfigurerar varje komponent.

![Flödesschema för Application Gateway-komponenter](./media/configuration-overview/configuration-overview1.png)

Den här bilden illustrerar ett program som har tre lyssnare. De första två är lyssnare för flera platser för `http://acme.com/*` och `http://fabrikam.com/*`respektive. Båda lyssna på port 80. Tredje är en grundläggande lyssnare som har slutpunkt till slutpunkt Secure Sockets Layer (SSL)-avslutning.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure-nätverk och dedikerade undernät

En application gateway är en särskild distribution i det virtuella nätverket. I det virtuella nätverket krävs ett dedikerat undernät för application gateway. Du kan ha flera instanser av en viss application gateway-distribution i ett undernät. Du kan även distribuera andra application gatewayer i undernätet. Men du kan inte distribuera någon annan resurs i application gateway-undernätet.

> [!NOTE]
> Du kan inte blanda Standard_v2 och Standard Azure Application Gateway i samma undernät.

#### <a name="size-of-the-subnet"></a>Storleken på undernätet

Application Gateway förbrukar 1 privat IP-adress per instans, plus en annan privat IP-adress om en privat frontend IP-adress har konfigurerats.

Azure också reserverar 5 IP-adresser i varje undernät för intern användning: 4 första och sista IP-adresser. Anta exempelvis att 15 application gateway-instanser med ingen privat frontend-IP-adress. Du behöver minst 20 IP-adresser för det här undernätet: 5 för intern användning och 15 för application gateway-instanser. Så du behöver en/27 undernät eller större.

Överväg ett undernät som har 27 application gateway-instanser och en IP-adress för en privat frontend IP-adress. I det här fallet behöver du 33 IP-adresser: 27 för application gateway-instanserna, 1 för privata klientdelen och 5 för internt bruk. Så du behöver en /26 undernät eller större.

Vi rekommenderar att du använder undernätets storlek på minst/28. Den här storleken ger dig 11 användbara IP-adresser. Om programbelastningen kräver mer än 10 IP-adresser, Överväg en/27 eller/26 undernätets storlek.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Nätverkssäkerhetsgrupper i Application Gateway-undernät

Nätverkssäkerhetsgrupper (NSG) stöds på Application Gateway. Men det finns flera begränsningar:

- Du måste ta med undantag för inkommande trafik på portar 65503 65534 för Application Gateway v1-SKU och portar 65200 – 65535 för v2-SKU. Intervallet måste anges för Azures infrastrukturkommunikation. De här portarna är skyddade (låsta) med Azure-certifikat. Externa entiteter, inklusive kunderna till dessa gateways kan inte initiera ändringar på dessa slutpunkter utan rätt certifikat på plats.

- Det går inte att blockera utgående internet-anslutning. Utgående standardregler i NSG tillåta anslutning till internet. Vi rekommenderar att du:

  - Ta inte bort utgående standardregler.
  - Skapa inte andra utgående regler som nekar utgående internet-anslutning.

- Trafik från den **AzureLoadBalancer** taggen måste tillåtas.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Lista över tillåtna Application Gateway-åtkomst till några käll-IP-adresser

Använd NSG: er på Application Gateway-undernät i det här scenariot. Placera följande begränsningar i undernät i det här prioritetsordning:

1. Tillåt inkommande trafik från en källa IP/IP-adressintervall.
2. Tillåt inkommande begäranden från alla källor till portar 65503 65534 för [backend-hälsotillstånd kommunikation](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Intervallet måste anges för Azures infrastrukturkommunikation. De här portarna är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat på plats, kan inte externa enheter initiera ändringar på dessa slutpunkter.
3. Tillåt inkommande Azure Load Balancer kontrollerar (*AzureLoadBalancer* tagg) och inkommande trafik för virtuellt nätverk (*VirtualNetwork* tagg) på den [nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blockera all annan inkommande trafik med hjälp av en regel för neka alla.
5. Tillåt utgående trafik till internet för alla mål.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Användardefinierade vägar som stöds på Application Gateway-undernät

V1-SKU stöds användardefinierade vägar (Udr) för i Application Gateway-undernät, så länge de inte ändrar slutpunkt till slutpunkt begäran/svar-kommunikation. Du kan till exempel skapa en UDR i Application Gateway-undernät för att peka mot en brandväggsinstallation för paketinspektion. Men du måste kontrollera att paketet kan nå sin destination efter granskning. I annat fall kan leda till felaktig hälsoavsökning eller beteende för routning av nätverkstrafik. Detta inkluderar inlärda eller standardvägar för 0.0.0.0/0 som sprids via Azure ExpressRoute eller VPN-gatewayer i det virtuella nätverket.

Udr: er stöds inte på Application Gateway-undernät för v2-SKU. Mer information finns i [automatisk skalning och zonredundans för Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> Udr: er i Application Gateway-undernät orsakar hälsostatus i den [backend-hälsotillstånd visa](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) ska visas som ”okänt”. Det gör även generation av Application Gateway-loggar och mått misslyckas. Vi rekommenderar att du inte använder udr: er på Application Gateway-undernät så att du kan visa backend-hälsotillstånd, loggar och mått.

## <a name="front-end-ip"></a>Frontend-IP

Du kan konfigurera programgatewayen om du vill ha en offentlig IP-adress, en privat IP-adress eller båda. En offentlig IP-adress krävs om du agerar värd för en serverdel som klienter måste ha åtkomst via internet via en internet-ansluten virtuell IP (VIP). 

En offentlig IP-adress krävs inte för en intern slutpunkt som inte exponeras till internet. Som kallas en *interna belastningsutjämnare* (ILB) slutpunkt. En Programgateway ILB är användbart för interna line-of-business-program som inte är exponerad för internet. Det är också användbart för tjänster och nivåer i ett flerskiktat program inom en säkerhetsgräns som inte är exponerad för internet men som kräver resursallokering läsa in distribution, sessionsvaraktighet eller SSL-avslutning.

Endast 1 offentlig IP-adress eller 1 privat IP-adress stöds. Du kan välja frontend IP-Adressen när du skapar programgatewayen.

- Du kan skapa en ny offentlig IP-adress eller använda en befintlig offentlig IP på samma plats som application gateway för en offentlig IP-adress. Om du skapar en ny offentlig IP-adress i IP-adresstyp som du väljer (statisk eller dynamisk) kan inte ändras senare. Mer information finns i [Statiska eller dynamiska offentliga IP-adressen](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Du kan ange en privat IP-adress från det undernät där programgatewayen skapas för en privat IP-adress. Om du inte anger något, väljs en skadlig IP-adress automatiskt från undernätet. Mer information finns i [skapa en Programgateway med en intern belastningsutjämnare](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

En frontend IP-adress är kopplad till en *lyssnare*, som söker efter inkommande begäranden på frontend IP-Adressen.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk enhet som söker efter inkommande anslutningsbegäranden genom att använda port, protokoll, värddator och IP-adress. När du konfigurerar lyssnaren, måste du ange värden för dessa som matchar de motsvarande värdena i den inkommande begäran på gatewayen.

När du skapar en Programgateway med hjälp av Azure portal kan skapa du också en standard-lyssnare genom att välja det protokoll och port för lyssnaren. Du kan välja om du vill aktivera stöd för HTTP2 på lyssnaren. När du har skapat application gateway kan du redigera inställningarna för den standard-lyssnaren (*appGatewayHttpListener*/*appGatewayHttpsListener*) eller skapa nya lyssnare.

### <a name="listener-type"></a>Lyssnaren typ

När du skapar en ny lyssnare kan du välja mellan [ *grundläggande* och *multisite*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Om du är värd för en enda plats bakom en Programgateway, väljer du grundläggande. Lär dig [hur du skapar en Programgateway med en grundläggande lyssnare](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Om du konfigurerar mer än ett webbprogram eller flera underdomäner i samma överordnade domän på samma application gateway-instans, väljer du lyssnare för flera platser. För en lyssnare för flera platser, måste du också ange ett värdnamn. Det beror på att Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att vara värd för flera webbplatser på samma offentliga IP-adress och port.

#### <a name="order-of-processing-listeners"></a>Bearbetningsordning för lyssnare

I v1-SKU: n bearbetas lyssnare i angiven ordning. Om en grundläggande lyssnare matchar en inkommande begäran, bearbetar lyssnaren begäran först. Konfigurera lyssnare för flera platser innan du grundläggande lyssnare för att se till att trafiken dirigeras till rätt serverdel.

I v2-SKU: n bearbetas lyssnare för flera platser innan du grundläggande lyssnare.

### <a name="front-end-ip"></a>Frontend-IP

Välj den frontend IP-adress som du vill associera med den här lyssnaren. Lyssnaren ska lyssna på inkommande begäranden på denna IP-adress.

### <a name="front-end-port"></a>Frontend-port

Välj frontend-porten. Välj en befintlig port eller skapa en ny. Välj ett värde mellan den [tillåtna portintervall](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Du kan använda inte bara välkända portar, till exempel 80 och 443, men alla tillåtna anpassade portar som är lämpligt. En port kan användas för lyssnare för offentliga eller privata-ansluten lyssnare.

### <a name="protocol"></a>Protokoll

Välj HTTP eller HTTPS:

- Om du väljer HTTP är trafiken mellan klienten och application gateway okrypterad.

- Välj HTTPS om du vill [SSL-avslutning](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) eller [slutpunkt till slutpunkt SSL-kryptering](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Trafik mellan klienten och application gateway är krypterad. Och SSL-anslutningen avslutas vid application gateway. Om du vill slutpunkt till slutpunkt SSL-kryptering, du måste välja HTTPS och konfigurera den **backend-HTTP** inställningen. Detta säkerställer att trafik krypteras igen när de överförs från programgatewayen till backend-server.

Om du vill konfigurera SSL-avslutning och slutpunkt till slutpunkt SSL-kryptering, måste du lägga till ett certifikat i lyssnaren för att aktivera application gateway att härleda en symmetrisk nyckel. Detta styrs av specifikationen för SSL-protokollet. Den symmetriska nyckeln som används för att kryptera och dekryptera den trafik som skickas till gatewayen. Gateway-certifikatet måste ha formatet Personal Information Exchange (PFX). Det här formatet kan du exportera den privata nyckeln som används av gatewayen för att kryptera och dekryptera trafiken.

#### <a name="supported-certificates"></a>Certifikat som stöds

Se [certifikat som stöds för SSL-avslutning](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Stöd för ytterligare protokoll

#### <a name="http2-support"></a>Stöd för HTTP2

Stöd för HTTP/2-protokollet är tillgängligt för klienter som ansluter till application gateway lyssnare. Kommunikation till serverdels serverpooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverad som standard. Följande Azure PowerShell-kodfragmentet visar hur du aktiverar detta:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket-stöd

WebSocket-stöd är aktiverat som standard. Det finns ingen användarangiven inställning för att aktivera eller inaktivera den. Du kan använda WebSockets med både HTTP och HTTPS-lyssnare.

### <a name="custom-error-pages"></a>Anpassade felsidor

Du kan definiera anpassade fel på global nivå eller lyssnare-nivå. Men att skapa anpassade felsidor för global nivå från Azure portal för närvarande stöds inte. Du kan konfigurera en anpassad felsida för ett 403 web application firewall fel eller en 502 underhållssidan på nivån lyssnare. Du måste även ange en offentligt tillgänglig blob-URL för den angivna felkoden. Mer information finns i [Skapa anpassade felsidor i Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Felkoder för Application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

För att konfigurera en global anpassad felsida Se [Azure PowerShell-konfigurationen](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>SSL-princip

Du kan centralisera hanteringen av SSL-certifikat och minska kryptering-dekryptering användning för en backend-server-grupp. Centraliserade SSL-hantering kan du ange en central SSL-princip som passar dina säkerhetsbehov. Du kan välja *standard*, *fördefinierade*, eller *anpassade* SSL-princip.

Du kan konfigurera SSL-princip att kontrollen SSL-protokollsversioner. Du kan konfigurera en Programgateway för att neka TLS 1.0 och TLS1.1 TLS1.2. SSL 2.0 och 3.0 inaktiveras som standard och inte kan konfigureras. Mer information finns i [översikt över Application Gateway SSL-princip](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

När du skapar en lyssnare kan associera du den med en regel för routning av begäran. Regeln anger hur begäranden som tas emot på lyssnaren dirigeras till backend-servern.

## <a name="request-routing-rules"></a>Routningsregler för begäran

När du skapar en Programgateway med hjälp av Azure portal kan du skapa en standardregel (*1*). Den här regeln Binder lyssnaren standard (*appGatewayHttpListener*) med den förvalda backend-adresspoolen (*appGatewayBackendPool*) och standard backend-HTTP-inställningarna ( *appGatewayBackendHttpSettings*). När du har skapat gatewayen, kan du redigera inställningarna för Standardregeln eller skapa nya regler.

### <a name="rule-type"></a>Regeltyp

När du skapar en regel kan du välja mellan [ *grundläggande* och *sökvägsbaserad*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule).

- Välj basic om du vill vidarebefordra alla begäranden på den associerade lyssnaren (till exempel *blogg<i></i>.contoso.com/\*)* till en enda backend-pool.
- Välj sökvägsbaserad om du vill dirigera förfrågningar från specifika URL-sökvägar till specifika serverdelspooler. Sökvägsmönster tillämpas endast på sökvägen till URL-Adressen, inte till dess Frågeparametrar.

#### <a name="order-of-processing-rules"></a>Ordningen på bearbetar regler

I v1-SKU: n bearbetas matchning av inkommande begäranden i den ordning som sökvägarna visas i Webbadress för sökvägskarta av sökvägsbaserad regel. Om en begäran matchar mönstret i två eller flera sökvägar i path-map, matchas den sökväg som visas först. Och begäran vidarebefordras till backend-server som är associerat med denna sökväg.

I v2-SKU: n är en exakt matchning högre prioritet än sökväg ordning i Webbadress för sökvägskarta. Om en begäran matchar mönstret i två eller flera sökvägar, vidarebefordras begäran till backend-server som är associerat med den sökväg som exakt matchar begäran. Om sökvägen i den inkommande begäranden inte matchar exakt valfri sökväg på kartan, bearbetas mönstermatchning för begäran i i orderlistan för sökvägen för sökvägsbaserad regel.

### <a name="associated-listener"></a>Tillhörande lyssnare

Associera en lyssnare för regeln så att den *regeln för routning av begäran* som är associerad med lyssnaren utvärderas för att fastställa backend-poolen för att dirigera begäran till.

### <a name="associated-back-end-pool"></a>Tillhörande backend-adresspool

Associera för regeln backend-poolen som innehåller de backend-mål som betjänar förfrågningar som tar emot lyssnaren.

 - För en grundläggande regel tillåts bara en backend-poolen. Alla begäranden på den associerade lyssnaren vidarebefordras till backend-poolen.

 - Lägg till flera backend-adresspooler som relaterar till varje URL-sökvägen för en sökvägsbaserad regel. Begäranden som matchar URL-sökvägen som anges vidarebefordras till motsvarande backend-poolen. Lägg även till en standard backend-poolen. Begäranden som inte matchar alla URL-sökvägen i regeln vidarebefordras till poolen.

### <a name="associated-back-end-http-setting"></a>Associerade backend-HTTP-inställning

Lägg till en backend-HTTP-inställning för varje regel. Begäranden dirigeras från programgatewayen till backend-mål med hjälp av portnumret, protokoll och annan information som anges i den här inställningen.

För en grundläggande regel tillåts bara en backend-HTTP-inställning. Alla begäranden på den associerade lyssnaren vidarebefordras till motsvarande backend-mål med hjälp av den här HTTP-inställning.

Lägg till en backend-HTTP-inställning för varje regel. Begäranden dirigeras från programgatewayen till backend-mål med hjälp av portnumret, protokoll och annan information som anges i den här inställningen.

För en grundläggande regel tillåts bara en backend-HTTP-inställning. Alla begäranden på den associerade lyssnaren vidarebefordras till motsvarande backend-mål med hjälp av den här HTTP-inställning.

Lägg till flera backend-HTTP-inställningarna som motsvarar varje URL-sökvägen för en sökvägsbaserad regel. Begäranden som matchar en URL-sökväg i den här inställningen vidarebefordras till motsvarande backend-mål med hjälp av HTTP-inställningarna för varje URL-sökväg. Lägg även till en standardinställning för HTTP. Begäranden som inte matchar alla URL-sökvägen i den här regeln vidarebefordras till den förvalda backend-adresspoolen genom att använda standardinställningen för HTTP.

### <a name="redirection-setting"></a>Omdirigeringsinställningen

Om omdirigering har konfigurerats för en grundläggande regel, omdirigeras alla begäranden på den associerade lyssnaren till målet. Det här är *globala* omdirigering. Om omdirigering har konfigurerats för en sökvägsbaserad regel, omdirigeras endast begäranden under en viss plats. Ett exempel är en i kundvagnen område som markerats med */cart/\**. Det här är *sökvägsbaserad* omdirigering.

Läs mer om omdirigeringar [översikt för omdirigering i Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Omdirigeringstyp

Välj typ av omdirigering av krävs: *Permanent(301)*, *Temporary(307)*, *Found(302)*, eller *finns i other(303)*.

#### <a name="redirection-target"></a>Omdirigering av mål

Välj en annan lyssnare eller en extern webbplats som mål för omdirigering.

##### <a name="listener"></a>Lyssnare

Välj lyssnare som omdirigering av mål att omdirigera trafik från en lyssnare till en annan på gatewayen. Den här inställningen är obligatorisk när du vill aktivera HTTP till HTTPS-omdirigering. Den dirigerar trafik från käll-lyssnaren som söker efter inkommande HTTP-begäranden till mål-lyssnare som söker efter inkommande HTTPS-begäranden. Du kan också välja att inkludera frågesträngen och sökväg från den ursprungliga begäran i begäran som vidarebefordras till målet för omdirigering.

![Dialogrutan för Application Gateway-komponenter](./media/configuration-overview/configure-redirection.png)

Mer information om HTTP till HTTPS-omdirigering finns:
- [HTTP-HTTP-omdirigering med hjälp av Azure portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [HTTP-HTTP-omdirigering med hjälp av PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [HTTP till HTTP-omdirigering med hjälp av Azure CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Extern webbplats

Välj extern webbplats om du vill omdirigera trafik på som är associerat med den här regeln till en extern plats. Du kan välja att inkludera frågesträngen från den ursprungliga begäran i begäran som vidarebefordras till målet för omdirigering. Du kan inte vidarebefordra sökvägen till den externa platsen som fanns i den ursprungliga begäran.

Mer information om omdirigering finns:
- [Omdirigera trafik till en extern plats med hjälp av PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Omdirigera trafik till en extern plats med hjälp av CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Skriv om HTTP-huvud-inställning

Den här inställningen lägger till, tar bort eller uppdaterar HTTP-begäran och svarshuvuden när begäran och svarspaket flytta mellan klienten och backend-adresspooler. Du kan bara konfigurera den här funktionen via PowerShell. Azure portal och CLI-stöd är inte ännu tillgängliga. Mer information finns i:

 - [Skriv om HTTP-huvuden översikt](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Konfigurera HTTP-huvud-omskrivning](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP-inställningar

Application gateway dirigerar trafik till backend servrarna med den konfiguration som du anger här. När du har skapat en HTTP-inställning måste du associera den med en eller flera routning av begäran regler.

### <a name="cookie-based-affinity"></a>Cookie-baserad tillhörighet

Den här funktionen är användbart när du vill behålla en användarsession på samma server. Gatewayhanterade cookies kan application gateway direkt efterföljande trafiken från en användarsession till samma server för bearbetning. Detta är viktigt när sessionstillstånd har sparats lokalt på servern för en användarsession. Om programmet inte kan hantera cookie-baserad tillhörighet kan använda du inte den här funktionen. Se till att klienterna stöder cookies för att använda den.

### <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att smidigt ta bort serverdelspool medlemmar under planerade service uppdateringar. Du kan använda den här inställningen för alla medlemmar i en serverdelspool samband med regelgenereringen. Det innebär att ta bort registrera instanser av en backend-poolen inte får alla nya begäranden. Under tiden kan ska befintliga begäranden kunna slutföras inom en konfigurerad tid. Anslutningstömning gäller för backend-instanser som uttryckligen tas bort från backend poolen med ett API-anrop. Det gäller även för backend-instanser som har rapporterats som *feltillstånd* av hälsotillståndet avsökningar.

### <a name="protocol"></a>Protokoll

Application Gateway stöder både HTTP och HTTPS för dirigering av begäranden till backend servrarna. Om du väljer HTTP är-trafik till backend servrarna okrypterad. Om dekrypterade kommunikation inte är acceptabel kan du välja HTTPS.

Den här inställningen tillsammans med HTTPS i lyssnaren stöder [slutpunkt till slutpunkt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). På så sätt kan du säkert överföra känsliga data krypteras till backend-server. Varje backend-server i backend-poolen som har slutpunkt till slutpunkt SSL aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

### <a name="port"></a>Port

Den här inställningen anger den port där backend servrarna lyssnar på trafik från programgatewayen. Du kan konfigurera portar mellan 1 och 65535.

### <a name="request-timeout"></a>Timeout för begäran

Den här inställningen är antalet sekunder som application gateway som väntar på svar från backend poolen innan den returnerar felmeddelandet ”anslutning uppnåddes”.

### <a name="override-back-end-path"></a>Åsidosätt backend-sökväg

Den här inställningen kan du konfigurera ett valfritt anpassat vidarebefordran sökväg som ska användas när begäran vidarebefordras till backend-servern. Någon del av den inkommande sökvägen som matchar den anpassad sökvägen i den **åsidosätta sökvägen till serverdelen** kopieras till den vidarebefordrade sökvägen. I följande tabell visar hur den här funktionen fungerar:

- När HTTP-inställning är kopplad till en grundläggande regel som routning av begäran:

  | Ursprungliga begäran  | Åsidosätt backend-sökväg | Begäran vidarebefordras till serverdel |
  | ----------------- | --------------------- | ---------------------------- |
  | /Startsida/            | /override/            | / åsidosätta/home /              |
  | / home/secondhome / | /override/            | / åsidosättning/home/secondhome /   |

- När HTTP-inställning är kopplad till en sökvägsbaserad routning av begäran regel:

  | Ursprungliga begäran           | Sökvägsregeln       | Åsidosätt backend-sökväg | Begäran vidarebefordras till serverdel |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / åsidosätta/home /              |
  | / pathrule/home/secondhome / | /pathrule*      | /override/            | / åsidosättning/home/secondhome /   |
  | /Startsida/                     | /pathrule*      | /override/            | / åsidosätta/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / åsidosättning/home/secondhome /   |
  | /pathrule/home /            | / pathrule/startsidan * | /override/            | /override/                   |
  | / pathrule/home/secondhome / | / pathrule/startsidan * | /override/            | / åsidosätta/secondhome /        |

### <a name="use-for-app-service"></a>Använd för app service

Det här är en UI-genväg som väljer de två inställningarna som krävs för Azure App Service-serverdelen. Det gör att **Välj värdnamnet från backend-adress**, och skapar en ny anpassad avsökning. (Mer information finns i den [Välj värdnamnet från backend-adress](#pick) inställning i den här artikeln.) En ny avsökning skapas och rubriken avsökningen hämtas från medlemmen backend-adress.

### <a name="use-custom-probe"></a>Använd anpassad avsökning

Den här inställningen associerar en [anpassad avsökning](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) med en HTTP-inställning. Du kan associera endast en anpassad avsökning med en HTTP-inställning. Om du inte uttryckligen associera en anpassad avsökning i [standard avsökningen](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) används för att övervaka hälsotillståndet för backend-servern. Vi rekommenderar att du skapar en anpassad avsökning för bättre kontroll över hälsoövervakning av dina servrar.

> [!NOTE]
> Anpassad avsökning övervaka inte hälsotillståndet för backend poolen, såvida inte den motsvarande HTTP-inställningen är uttryckligen associerad med en lyssnare.

### <a id="pick"/></a>Välj värdnamnet från backend-adress

Den här funktionen dynamiskt anger den *värden* huvudet i begäran till värdnamnet för backend poolen. Den använder en IP-adress eller fullständigt domännamn.

Den här funktionen gör att domännamnet för serverdelen skiljer sig från DNS-namnet på application gateway och backend-servern förlitar sig på en specifik värdrubrik eller Server Name Indication (SNI)-tillägg kunna matcha mot rätt slutpunkt.

Ett exempel är tjänster med flera innehavare som backend-servern. En app service är en tjänst för flera klienter som använder en delad utrymme med en enda IP-adress. Därför kan en app service endast nås via värdnamn som konfigureras i inställningarna för anpassad domän.

Det anpassade domännamnet är som standard *example.azurewebsites.<i> </i>net*. För att komma åt din app service med hjälp av en Programgateway via ett värdnamn som inte uttryckligen har registrerats i app service eller FQDN för application gateway kan du åsidosätta värdnamnet i den ursprungliga begäran till värdnamn för app service. Om du vill göra detta måste du aktivera den **Välj värdnamnet från serverdelsadressen** inställningen.

För en anpassad domän vars befintligt anpassat DNS-namn är mappad till app service, har du inte aktivera den här inställningen.

> [!NOTE]
> Den här inställningen behövs inte för App Service Environment för PowerApps, vilket är en särskild distribution.

### <a name="host-name-override"></a>Åsidosätt för värd-namn

Den här funktionen ersätter den *värden* rubrik i den inkommande begäran på application gateway med värdnamn som du anger.

Till exempel om *www.contoso<i></i>.com* har angetts i den **värdnamn** inställningen, den ursprungliga begäran *https:/<i></i>/appgw.eastus.cloudapp.net/path1* ändras till *https:/<i></i>/www.contoso.com/path1* när begäran vidarebefordras till backend-servern.

## <a name="back-end-pool"></a>Backend-poolen

Du kan peka en backend-poolen på fyra typer av serverdelen medlemmar: en specifik virtuell dator, en skalningsuppsättning för virtuell dator, en IP-adressen/FQDN eller en app service. Varje serverdelspool kan peka på flera medlemmar av samma typ. Peka på medlemmar av olika typer i samma backend-pool stöds inte.

När du har skapat en backend pool måste du associera den med en eller flera routning av begäran regler. Du måste också konfigurera hälsoavsökningar för varje serverdelspool på application gateway. När ett villkor för routning av begäran är uppfyllt, vidarebefordrar application gateway trafik till felfria servrarna (som bestäms av hälsoavsökningarna) i motsvarande backend-poolen.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En application gateway övervakar hälsotillståndet för alla resurser i serverdelen som standard. Men vi rekommenderar starkt att du skapar en anpassad avsökning för varje backend-HTTP-inställning att få bättre kontroll över hälsoövervakning. Läs hur du konfigurerar en anpassad avsökning i [anpassad avsökning hälsomiljöer](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> När du skapar en anpassad hälsoavsökning, måste du koppla den till en backend-HTTP-inställning. En anpassad avsökning övervaka inte hälsotillståndet för backend poolen, såvida inte den motsvarande HTTP-inställningen är uttryckligen associerad med en lyssnare.

## <a name="next-steps"></a>Nästa steg

Nu när du vet om Application Gateway-komponenter kan du:

- [Skapa en Programgateway i Azure portal](quick-create-portal.md)
- [Skapa en Programgateway med hjälp av PowerShell](quick-create-powershell.md)
- [Skapa en Programgateway med hjälp av Azure CLI](quick-create-cli.md)
