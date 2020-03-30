---
title: Azure Files nätverksöverväganden | Microsoft-dokument
description: En översikt över nätverksalternativ för Azure-filer.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 383ad5e5063a0a207320a517c34f3b41cc57804a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067154"
---
# <a name="azure-files-networking-considerations"></a>Överväganden för Azure Files-nätverk 
Du kan ansluta till en Azure-filresurs på två sätt:

- Komma åt resursen direkt via SMB- eller FileREST-protokollen. Det här åtkomstmönstret används främst när så många lokala servrar som möjligt ska elimineras.
- Skapa en cache av Azure-filresursen på en lokal server med Azure File Sync och komma åt filresursens data från den lokala servern med ditt protokoll val (SMB, NFS, FTPS, etc.) för ditt användningsfall. Det här åtkomstmönstret är praktiskt eftersom det kombinerar det bästa av både lokala prestanda och molnskala och serverlösa attacherbara tjänster, till exempel Azure Backup.

Den här artikeln fokuserar på hur du konfigurerar nätverk för när ditt användningsfall kräver åtkomst till Azure-filresursen direkt i stället för att använda Azure File Sync. Mer information om nätverksöverväganden för en Azure File Sync-distribution finns i [konfigurera proxy- och brandväggsinställningar för Azure File Sync](storage-sync-files-firewall-and-proxy.md).

Nätverkskonfiguration för Azure-filresurser görs på Azure-lagringskontot. Ett lagringskonto är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blob-behållare eller köer. Lagringskonton visar flera inställningar som hjälper dig att skydda nätverksåtkomst till dina filresurser: nätverksslutpunkter, brandväggsinställningar för lagringskonton och kryptering under överföring. 

Vi rekommenderar att du läser [Planering för en Azure Files-distribution](storage-files-planning.md) innan du läser den här konceptuella guiden.

## <a name="accessing-your-azure-file-shares"></a>Komma åt dina Azure-filresurser
När du distribuerar en Azure-filresurs i ett lagringskonto är filresursen omedelbart tillgänglig via lagringskontots offentliga slutpunkt. Det innebär att autentiserade begäranden, till exempel begäranden som auktoriserats av en användares inloggningsidentitet, kan komma säkert inifrån eller utanför Azure. 

I många kundmiljöer misslyckas en första montering av Azure-filresursen på din lokala arbetsstation, även om fästen från virtuella Azure-datorer lyckas. Anledningen till detta är att många organisationer och Internet-leverantörer (IsPs) blockera den port som SMB använder för att kommunicera, port 445. Den här metoden kommer från säkerhetsvägledning om äldre och inaktuella versioner av SMB-protokollet. Även om SMB 3.0 är ett internetsäkert protokoll, äldre versioner av SMB, särskilt SMB 1.0 är det inte. Azure-filresurser kan endast nås externt via SMB 3.0 och FileREST-protokollet (som också är ett internetsäkert protokoll) via den offentliga slutpunkten.

Eftersom det enklaste sättet att komma åt din Azure-filresurs från lokalt är att öppna det lokala nätverket till port 445, rekommenderar Microsoft följande steg för att ta bort SMB 1.0 från din miljö:

1. Kontrollera att SMB 1.0 tas bort eller inaktiveras på organisationens enheter. Alla versioner av Windows och Windows Server som för närvarande stöds av att ta bort eller inaktivera SMB 1.0, och från och med Windows 10, version 1709, är SMB 1.0 inte installerat i Windows som standard. Mer information om hur du inaktiverar SMB 1.0 finns på våra OS-specifika sidor:
    - [Skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Säkra Linux](storage-how-to-use-files-linux.md#securing-linux)
2. Se till att inga produkter inom organisationen kräver SMB 1.0 och ta bort de som gör det. Vi har ett [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), som innehåller alla första och tredje parts produkter som microsoft känner till för att kräva SMB 1.0. 
3. (Valfritt) Använd en brandvägg från tredje part med organisationens lokala nätverk för att förhindra att SMB 1.0-trafik lämnar organisationsgränsen.

Om din organisation kräver att port 445 blockeras per princip eller förordning, eller om din organisation kräver trafik till Azure för att följa en deterministisk sökväg, kan du använda Azure VPN Gateway eller ExpressRoute för tunneltrafik till dina Azure-filresurser.

> [!Important]  
> Även om du bestämmer dig för att använda en alternativ metod för att komma åt dina Azure-filresurser rekommenderar Microsoft fortfarande att du tar bort SMB 1.0 från din miljö.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneltrafik över ett virtuellt privat nätverk eller ExpressRoute
När du upprättar en nätverkstunnel mellan det lokala nätverket och Azure peering du ditt lokala nätverk med ett eller flera virtuella nätverk i Azure. Ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md), eller virtuellt nätverk, liknar ett traditionellt nätverk som du skulle driva lokalt. Precis som ett Azure-lagringskonto eller en Virtuell Azure-dator är ett virtuellt nätverk en Azure-resurs som distribueras i en resursgrupp. 

Azure Files stöder följande mekanismer för tunneltrafik mellan lokala arbetsstationer och servrar och Azure:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): En VPN-gateway är en specifik typ av virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en alternativ plats (till exempel lokalt) via internet. En Azure VPN Gateway är en Azure-resurs som kan distribueras i en resursgrupp längs sidan av ett lagringskonto eller andra Azure-resurser. VPN-gateways exponerar två olika typer av anslutningar:
    - [P2S-vpn-gateway-anslutningar (Point-to-Site),](../../vpn-gateway/point-to-site-about.md) som är VPN-anslutningar mellan Azure och en enskild klient. Den här lösningen är främst användbar för enheter som inte ingår i organisationens lokala nätverk, till exempel distansarbetare som vill kunna montera sin Azure-filresurs hemifrån, ett kafé eller hotell på resande fot. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Information om hur du förenklar distributionen av en P2S VPN-anslutning finns i [Konfigurera en P2S-VPN (Point-to-Site) i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md) och Konfigurera ett [P2S-VPN (Point-to-Site) på Linux för användning med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Plats till plats (S2S) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), som är VPN-anslutningar mellan Azure och organisationens nätverk. Med en S2S VPN-anslutning kan du konfigurera en VPN-anslutning en gång för en VPN-server eller enhet som finns i organisationens nätverk, i stället för att göra för varje klientenhet som behöver komma åt din Azure-filresurs. Information om hur du förenklar distributionen av en S2S VPN-anslutning finns i [Konfigurera ett S2S-VPN (Site-to-Site) för användning med Azure Files](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), som gör att du kan skapa en definierad väg mellan Azure och ditt lokala nätverk som inte passerar internet. Eftersom ExpressRoute tillhandahåller en dedikerad sökväg mellan ditt lokala datacenter och Azure kan ExpressRoute vara användbart när nätverksprestanda är en faktor. ExpressRoute är också ett bra alternativ när organisationens policy eller regelkrav kräver en deterministisk väg till dina resurser i molnet.

Oavsett vilken tunnelmetod du använder för att komma åt dina Azure-filresurser behöver du en mekanism för att säkerställa att trafiken till ditt lagringskonto går över tunneln i stället för din vanliga internetanslutning. Det är tekniskt möjligt att dirigera till den offentliga slutpunkten för lagringskontot, men detta kräver hårdkodning av alla IP-adresser för Azure-lagringskluster i en region, eftersom lagringskonton kan flyttas mellan lagringskluster när som helst. Detta kräver också att ip-adressmappningar ständigt uppdateras eftersom nya kluster läggs till hela tiden.

I stället för att hårdkoda IP-adressen för dina lagringskonton i dina VPN-routningsregler rekommenderar vi att du använder privata slutpunkter som ger ditt lagringskonto en IP-adress från adressutrymmet i ett virtuellt Azure-nätverk. Eftersom skapandet av en tunnel till Azure upprättar peering mellan ditt lokala nätverk och ett eller flera virtuella nätverk, möjliggör detta rätt routning på ett hållbart sätt.

### <a name="private-endpoints"></a>Privata slutpunkter
Förutom standardslutpunkten för ett lagringskonto ger Azure Files möjlighet att ha en eller flera privata slutpunkter. En privat slutpunkt är en slutpunkt som endast är tillgänglig i ett virtuellt Azure-nätverk. När du skapar en privat slutpunkt för ditt lagringskonto får ditt lagringskonto en privat IP-adress från ditt virtuella nätverks adressutrymme, ungefär som hur en lokal filserver eller NAS-enhet tar emot en IP-adress inom den dedikerade adressen utrymme i ditt lokala nätverk. 

En enskild privat slutpunkt är associerad med ett specifikt Azure-virtuellt nätverksundernät. Ett lagringskonto kan ha privata slutpunkter i mer än ett virtuellt nätverk.

Med hjälp av privata slutpunkter med Azure Files kan du:
- Anslut till dina Azure-filresurser från lokala nätverk med hjälp av en VPN- eller ExpressRoute-anslutning med privat peering.
- Skydda dina Azure-filresurser genom att konfigurera brandväggen för lagringskonto för att blockera alla anslutningar på den offentliga slutpunkten. Som standard blockeras inte anslutningar till den offentliga slutpunkten när du skapar en privat slutpunkt.
- Öka säkerheten för det virtuella nätverket genom att du kan blockera exfiltration av data från det virtuella nätverket (och peering gränser).

Information om hur du skapar en privat slutpunkt finns i [Konfigurera privata slutpunkter för Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privata slutpunkter och DNS
När du skapar en privat slutpunkt skapar vi som standard också en (eller uppdaterar en befintlig) privat DNS-zon som motsvarar underdomänen. `privatelink` Strängt taget krävs det inte att skapa en privat DNS-zon för att använda en privat slutpunkt för ditt lagringskonto, men det rekommenderas starkt i allmänhet och krävs uttryckligen när du monterar din Azure-filresurs med ett active directory-användarobjekt eller åtkomst från FileREST API.

> [!Note]  
> Den här artikeln använder DNS-suffixet för `core.windows.net`lagringskontot för Azure Public-regionerna . Den här kommentaren gäller även Azure Sovereign-moln som Azure US Government-molnet och Azure China-molnet – bara ersätta lämpliga suffix för din miljö. 

I din privata DNS-zon skapar `storageaccount.privatelink.file.core.windows.net` vi en A-post för och en CNAME-post `storageaccount.file.core.windows.net`för det vanliga namnet på lagringskontot, som följer mönstret . Eftersom din privata Azure DNS-zon är ansluten till det virtuella nätverket som innehåller `Resolve-DnsName` den privata slutpunkten kan du observera `nslookup` DNS-konfigurationen när du anropar cmdleten från PowerShell i en Virtuell Azure -dator (växelvis i Windows och Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

I det här exemplet `storageaccount.file.core.windows.net` matchas lagringskontot till den privata IP-adressen för den privata slutpunkten, som råkar vara `192.168.0.4`.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Om du kör samma kommando från lokala ser du att samma lagringskontonamn matchas till lagringskontots offentliga IP-adress i stället. `storageaccount.file.core.windows.net` är en CNAME-post för `storageaccount.privatelink.file.core.windows.net`, som i sin tur är en CNAME-post för Azure-lagringsklustret som är värd för lagringskontot:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Detta återspeglar det faktum att lagringskontot kan exponera både den offentliga slutpunkten och en eller flera privata slutpunkter. Om du vill vara säkra på att lagringskontonamnet matchas till den privata slutpunktens privata IP-adress måste du ändra konfigurationen på dina lokala DNS-servrar. Detta kan åstadkommas på flera sätt:

- Ändra hosts-filen på dina `storageaccount.file.core.windows.net` klienter för att lösa den önskade privata slutpunktens privata IP-adress. Detta rekommenderas starkt för produktionsmiljöer, eftersom du behöver göra dessa ändringar i varje klient som vill montera dina Azure-filresurser och ändringar i lagringskontot eller den privata slutpunkten inte hanteras automatiskt.
- Skapa en A-post för `storageaccount.file.core.windows.net` i dina lokala DNS-servrar. Detta har fördelen att klienter i din lokala miljö kommer att kunna automatiskt lösa lagringskontot utan att behöva konfigurera varje klient, men den här lösningen är på samma sätt spröd till att ändra hosts-filen eftersom ändringar inte är Återspeglas. Även om denna lösning är spröd, kan det vara det bästa valet för vissa miljöer.
- Vidarebefordra `core.windows.net` zonen från dina lokala DNS-servrar till din privata AZURE DNS-zon. Azure private DNS-värden kan nås via`168.63.129.16`en särskild IP-adress ( ) som endast är tillgänglig i virtuella nätverk som är länkade till Azure private DNS-zonen. Om du vill lösa den här begränsningen kan du köra `core.windows.net` ytterligare DNS-servrar i det virtuella nätverket som vidarebefordras till azure-zonen för privat DNS. För att förenkla den här konfigurationen har vi tillhandahållit PowerShell-cmdlets som automatiskt distribuerar DNS-servrar i ditt virtuella Azure-nätverk och konfigurerar dem efter behov. Mer information om hur du konfigurerar DNS-vidarebefordran finns i [Konfigurera DNS med Azure-filer](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Brandväggsinställningar för lagringskonto
En brandvägg är en nätverksprincip som styr vilka begäranden som har åtkomst till den offentliga slutpunkten för ett lagringskonto. Med hjälp av brandväggen för lagringskonto kan du begränsa åtkomsten till lagringskontots offentliga slutpunkt till vissa IP-adresser eller-intervall eller till ett virtuellt nätverk. I allmänhet begränsar de flesta brandväggsprinciper för ett lagringskonto nätverksåtkomst till ett eller flera virtuella nätverk. 

Det finns två metoder för att begränsa åtkomsten till ett lagringskonto till ett virtuellt nätverk:
- Skapa en eller flera privata slutpunkter för lagringskontot och begränsa all åtkomst till den offentliga slutpunkten. Detta säkerställer att endast trafik som kommer inifrån önskade virtuella nätverk kan komma åt Azure-filresurserna i lagringskontot.
- Begränsa den offentliga slutpunkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter*. När du begränsar trafiken till ett lagringskonto via en tjänstslutpunkt, är du fortfarande tillgång till lagringskontot via den offentliga IP-adressen.

Mer information om hur du konfigurerar brandväggen för [lagringskonto finns i konfigurera Azure-lagringsbrand brandväggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Kryptering under överföring
Som standard har alla Azure-lagringskonton kryptering i transit aktiverat. Det innebär att när du monterar en filresurs över SMB eller kommer åt den via FileREST-protokollet (till exempel via Azure-portalen, PowerShell/CLI eller Azure SDK) tillåter Azure-filer endast anslutningen om den görs med SMB 3.0+ med kryptering eller HTTPS. Klienter som inte stöder SMB 3.0 eller klienter som stöder SMB 3.0 men inte SMB-kryptering kan inte montera Azure-filresursen om kryptering under överföring är aktiverad. Mer information om vilka operativsystem som stöder SMB 3.0 med kryptering finns i vår detaljerade dokumentation för [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md). Alla aktuella versioner av PowerShell, CLI och SDK-erna stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure-lagringskonto. När kryptering är inaktiverat tillåter Azure Files också SMB 2.1, SMB 3.0 utan kryptering och okrypterade FileREST API-anrop via HTTP. Den främsta orsaken till att inaktivera kryptering under överföring är att stödja ett äldre program som måste köras på ett äldre operativsystem, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure-filer tillåter endast SMB 2.1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2.1-klient utanför Azure-regionen i Azure-filresursen, till exempel lokalt eller i en annan Azure-region, kan inte komma åt filresursen.

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure-lagring](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Se även
- [Azure Files-översikt](storage-files-introduction.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)