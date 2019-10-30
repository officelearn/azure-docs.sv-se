---
title: Azure Files nätverks överväganden | Microsoft Docs
description: En översikt över nätverks alternativ för Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141796"
---
# <a name="azure-files-networking-considerations"></a>Azure Files nätverks överväganden 
Du kan ansluta till en Azure-filresurs på två sätt:

- Åtkomst till resursen direkt via SMB eller fileraste protokollen. Detta åtkomst mönster används i första hand för att eliminera så många lokala servrar som möjligt.
- Skapa ett cacheminne för Azure-filresursen på en lokal server med Azure File Sync och få åtkomst till fil resursens data från den lokala servern med ditt protokoll (SMB, NFS, FTPS osv.) för ditt användnings fall. Det här åtkomst mönstret är användbart eftersom det kombinerar det bästa av både lokala prestanda och moln skalning och tjänster som kan anslutas utan server, till exempel Azure Backup.

Den här artikeln fokuserar på hur du konfigurerar nätverk för när ditt användnings fall anropar åtkomst till Azure-filresursen direkt i stället för att använda Azure File Sync. Mer information om nätverks överväganden för en Azure File Sync distribution finns i [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Inställningar för lagrings konto
Ett lagrings konto är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. Azure Storage-konton exponerar två grundläggande uppsättningar med inställningar för att skydda nätverket: kryptering vid överföring och brand väggar och virtuella nätverk (virtuella nätverk).

### <a name="encryption-in-transit"></a>Kryptering under överföring
Som standard har alla Azure Storage-konton kryptering under överföring aktiverat. Det innebär att när du monterar en fil resurs över SMB eller åtkomst till den via det fileraste protokollet (till exempel via Azure Portal, PowerShell/CLI eller Azure SDK: er), tillåter Azure Files bara anslutningen om den görs med SMB 3.0 + med kryptering eller HTTPS. Klienter som inte har stöd för SMB 3,0 eller klienter som stöder SMB 3,0 men inte SMB-kryptering kommer inte att kunna montera Azure-filresursen om kryptering i överföring är aktiverat. Mer information om vilka operativ system som stöder SMB 3,0 med kryptering finns i vår detaljerade dokumentation för [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md). Alla aktuella versioner av PowerShell, CLI och SDK: er stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure Storage-konto. När krypteringen är inaktive rad kan Azure Files också tillåta SMB 2,1, SMB 3,0 utan kryptering och okrypterade filer för API-anrop via HTTP. Den främsta anledningen till att inaktivera kryptering vid överföring är att stödja ett äldre program som måste köras på ett äldre operativ system, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure Files tillåter endast SMB 2,1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2,1-klient utanför Azure-filresursens region, till exempel lokalt eller i en annan Azure-region, kommer inte att kunna komma åt fil resursen.

Mer information om kryptering i överföring finns i [krav på säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Brandväggar och virtuella nätverk 
En brand vägg är en nätverks princip som begär att få åtkomst till Azure-filresurser och andra lagrings resurser i ditt lagrings konto. När ett lagrings konto skapas med standard nätverks inställningarna är det inte begränsat till ett särskilt nätverk och är därför tillgängligt för Internet. Det innebär inte att vem som helst på Internet kan komma åt de data som lagras på Azure-filresurser som finns i ditt lagrings konto, men i stället kommer lagrings kontot att acceptera auktoriserade begär Anden från alla nätverk. Begär Anden kan auktoriseras med en lagrings konto nyckel, en signatur för delad åtkomst (SAS) (endast för filer) eller med en Active Directory användarens huvud namn. 

Brand Väggs principen för ett lagrings konto kan användas för att begränsa åtkomsten till vissa IP-adresser eller intervall, eller till ett virtuellt nätverk. I allmänhet kommer de flesta brand Väggs principer för ett lagrings konto att begränsa nätverks åtkomsten till ett virtuellt nätverk. 

Ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md), eller VNet, liknar ett traditionellt nätverk som du kommer att använda i ditt eget Data Center. Du kan skapa en säker kommunikations kanal för dina Azure-resurser, till exempel Azure-filresurser, virtuella datorer, SQL-databaser osv. för att kommunicera med varandra. Precis som ett Azure Storage-konto eller en virtuell Azure-dator är ett VNet en Azure-resurs som distribueras i en resurs grupp. Med ytterligare nätverks konfiguration kan Azure virtuella nätverk också anslutas till dina lokala nätverk.

När resurser, till exempel en virtuell Azure-dator läggs till i ett virtuellt nätverk, är ett virtuellt nätverks gränssnitt (NIC) som är kopplat till den virtuella datorn begränsat till ett specifikt VNet. Detta är möjligt eftersom virtuella Azure-datorer är virtualiserade datorer, vilket i kursen har nätverkskort. Virtuella datorer erbjuds som en del av Azures infrastruktur som en tjänst, eller IaaS, produkt utbud. Eftersom Azure-filresurser är fil resurser utan server, har de inte något nätverkskort som du kan lägga till i ett VNet. På ett annat sätt erbjuds Azure Files som en del av Azures plattform som en tjänst eller PaaS produkt utbud. Om du vill aktivera ett lagrings konto för att kunna vara en del av ett VNet, stöder Azure ett koncept för PaaS Services som kallas tjänst slut punkter. En tjänst slut punkt gör att PaaS-tjänster kan ingå i ett virtuellt nätverk. Mer information om tjänst slut punkter finns i [tjänst slut punkter för virtuella nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md).

Ett lagrings konto kan läggas till i ett eller flera virtuella nätverk. Mer information om hur du lägger till ditt lagrings konto i ett virtuellt nätverk eller konfigurerar andra brand Väggs inställningar finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Azure-nätverk
Som standard kan Azure-tjänster inklusive Azure Files nås via Internet. Eftersom standard trafik till ditt lagrings konto är krypterad (och SMB 2,1-monteringar tillåts aldrig utanför en Azure-region), är det inget osäkert att komma åt dina Azure-filresurser via Internet. Baserat på din organisations policy eller unika myndighets krav kan du behöva mer begränsad kommunikation med Azure, och därför erbjuder Azure flera sätt att begränsa hur trafik från utanför Azure får Azure Files. Du kan skydda nätverket ytterligare när du använder Azure-filresursen med hjälp av följande tjänst erbjudanden:

- [Azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): en VPN-gateway är en speciell typ av virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en annan plats (till exempel lokalt) via Internet. En Azure-VPN Gateway är en Azure-resurs som kan distribueras i en resurs grupp tillsammans med ett lagrings konto eller andra Azure-resurser. VPN-gatewayer exponerar två olika typer av anslutningar:
    - [Punkt-till-plats (P2s) VPN gateway-](../../vpn-gateway/point-to-site-about.md) anslutningar, som är VPN-anslutningar mellan Azure och en enskild klient. Den här lösningen är främst användbar för enheter som inte är en del av organisationens lokala nätverk, t. ex. kunder som vill kunna montera sin Azure-filresurs från hemmet, ett kafé eller ett hotell på resan. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. 
    - [VPN för plats-till-plats (S2S)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), som är VPN-anslutningar mellan Azure och din organisations nätverk. Med en S2S VPN-anslutning kan du konfigurera en VPN-anslutning en gång, för en VPN-server eller en enhet som finns i din organisations nätverk, i stället för att göra för varje klient enhet som behöver åtkomst till Azure-filresursen.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), som gör att du kan skapa en definierad väg mellan Azure och ditt lokala nätverk som inte passerar Internet. Eftersom ExpressRoute tillhandahåller en dedikerad sökväg mellan ditt lokala data Center och Azure, kan ExpressRoute vara användbart när nätverks prestanda är ett övervägande. ExpressRoute är också ett användbart alternativ när din organisations policy eller myndighets krav kräver en deterministisk sökväg till dina resurser i molnet.

## <a name="securing-access-from-on-premises"></a>Skydda åtkomsten lokalt 
När du migrerar fil resurser för generell användning (till exempel Office-dokument, PDF-dokument, CAD-dokument osv.) till Azure Files, kommer användarna normalt att fortsätta att ha åtkomst till sina filer från lokala enheter, till exempel arbets stationer, bärbara datorer och surfplattor. Det främsta syftet med en allmän fil resurs är hur lokala användare på ett säkert sätt kan komma åt sina fil resurser via Internet eller WAN.

Det enklaste sättet att komma åt din Azure-filresurs från lokalt är att öppna det lokala nätverket till port 445, porten som SMB använder och montera UNC-sökvägen som tillhandahålls av Azure Portal. Detta kräver inget särskilt nätverk krävs. Många kunder är ovilliga för att öppna port 445 på grund av inaktuella säkerhets guider kring SMB 1,0, som Microsoft inte anser vara ett säkert protokoll för Internet. Azure Files implementerar inte SMB 1,0. 

SMB 3,0 har utformats med ett explicit krav på att vara säkra fildelnings protokoll för Internet. När du använder SMB 3.0 +, från dator nätverkets perspektiv, är det därför inte något annat att öppna port 445 än att öppna port 443, porten som används för HTTPS-anslutningar. I stället för att blockera port 445 för att förhindra osäker SMB 1,0-trafik rekommenderar Microsoft följande steg:

> [!Important]  
> Även om du väljer att lämna port 445 stängd till utgående trafik rekommenderar Microsoft fortfarande följande steg för att ta bort SMB 1,0 från din miljö.

1. Se till att SMB 1,0 tas bort eller inaktive ras på din organisations enheter. Alla Windows-och Windows Server-versioner som stöds för närvarande stöder borttagning eller inaktive ring av SMB 1,0 och från och med Windows 10, version 1709, SMB 1,0 installeras inte i Windows som standard. Mer information om hur du inaktiverar SMB 1,0 finns i våra OS-/regionsspecifika sidor:
    - [Skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Skydda Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Se till att inga produkter i organisationen kräver SMB 1,0 och ta bort de som gör. Vi underhåller en [SMB1 produkt-Clearinghouse](https://aka.ms/stillneedssmb1)som innehåller alla de första och tredje parts produkter som är kända för att Microsoft ska kräva SMB 1,0. 
1. Valfritt Använd en brand vägg från en tredje part med din organisations lokala nätverk för att förhindra SMB 1,0-trafik.

Om din organisation kräver att port 445 ska blockeras per princip eller regel kan du använda Azure VPN Gateway eller ExpressRoute för tunnel trafik över port 443. Om du vill veta mer om de olika stegen för att distribuera dessa kan du läsa mer om hur du använder sidor:
- [Konfigurera ett VPN för plats-till-plats (S2S) som ska användas med Azure Files](storage-files-configure-s2s-vpn.md)
- [Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md)

Din organisation kan ha det ytterligare krav som trafik utgående från den lokala platsen måste följa en deterministisk sökväg till dina resurser i molnet. I så fall kan ExpressRoute uppfylla detta krav.

## <a name="securing-access-from-cloud-resources"></a>Skydda åtkomst från moln resurser
När ett lokalt program lyfts upp och flyttas till molnet flyttas programmet och programmets data på samma gång. Det innebär att den primära beräkningen för en hiss och Shift-migrering låser upp åtkomsten till Azure-filresursen till de virtuella datorer eller Azure-tjänster som kräver åtkomst till fil resursen för att fungera. 

Du kanske vill använda virtuella nätverk för att begränsa vilka virtuella datorer eller andra Azure-resurser som tillåts upprätta nätverks anslutningar (SMB-monteringar eller REST API anrop till Azure-filresursen). Vi rekommenderar alltid att du placerar Azure-filresursen i ett VNet om du tillåter okrypterad trafik till ditt lagrings konto. I annat fall, oavsett om du använder virtuella nätverk, är ett beslut som ska drivas av dina verksamhets krav och organisations principer.

Huvud orsaken till att tillåta okrypterad trafik till Azure-filresursen är att stödja Windows Server 2008 R2, Windows 7 eller andra äldre operativ system som använder Azure-filresursen med SMB 2,1 (eller SMB 3,0 utan kryptering för vissa Linux-distributioner). Vi rekommenderar inte att du använder SMB 2,1 eller SMB 3,0 utan kryptering på operativ system som stöder SMB 3.0 + med kryptering.

## <a name="see-also"></a>Se också
- [Azure Files-översikt](storage-files-introduction.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)