---
title: Azure SQL Database managed instance anslutningsarkitektur | Microsoft Docs
description: Den här artikeln innehåller Azure SQL Database managed instance kommunikationsöversikt och förklarar anslutningsarkitektur samt hur de olika komponenterna fungerar för att dirigera trafik till den hanterade instansen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 64e0444c85440a017872aa32017e7d1c47e44e89
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889789"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database managed instance anslutningsarkitektur

Den här artikeln innehåller Azure SQL Database managed instance kommunikationsöversikt och förklarar anslutningsarkitektur samt hur de olika komponenterna fungerar för att dirigera trafik till den hanterade instansen.  

Den hanterade Azure SQL Database-instansen är placerad i virtuella Azure-nätverket och undernätet för hanterade instanser. Den här distributionen gör det möjligt för följande scenarier:

- Skydda privata IP-adressen.
- Ansluter till en hanterad instans direkt från ett lokalt nätverk.
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager.
- Ansluta en hanterad instans till Azure-resurser.

## <a name="communication-overview"></a>Översikt över tjänstkommunikation

I följande diagram visar de enheter som ansluter till managed instance samt resurser som hanterad instans har kontakta för att fungera korrekt.

![anslutningen arkitektur entiteter](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Kommunikation som visas längst ned i diagrammet representerar kundprogram och verktyg ansluter till hanterad instans som datakälla.  

Eftersom managed instance är en plattform-som-en-services (PaaS) erbjuder, hanterar Microsoft den här tjänsten med hjälp av automatisk agenter (hantering, distribution och underhåll) baserat på data telemetriströmmar. Som hanterad instanshantering är enbart Microsofts ansvar, kunder har inte åtkomst till de hanterade instansen virtuellt kluster datorerna via RDP.

Vissa SQL Server-åtgärder av användare eller program kan kräva hanterade instanser för att interagera med plattformen. Ett enda fall är att skapa en hanterad databasinstans – en resurs som exponeras via Azure portal, PowerShell, Azure CLI och REST-API.

Hanterade instansen är beroende av andra Azure-tjänster för dess fungerar (till exempel Azure Storage för säkerhetskopiering, Azure Service Bus för telemetri, Azure AD för autentisering, Azure Key Vault för transparent Datakryptering och så vidare) och initierar anslutningar till dem enlighet med detta.

All kommunikation som anges ovan, är krypterat och signerat med certifikat. Om du vill se till att kommunicerande parterna är betrodd, verifierar hanterad instans ständigt dessa certifikat genom att kontakta certifikatutfärdare. Om certifikaten har återkallats eller hanterad instans kunde inte verifiera dem, stänger anslutningar för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Övergripande anslutningsarkitektur

På hög nivå är en hanterad instans en uppsättning tjänstkomponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i det virtuella undernätet för kunden och skapa ett virtuellt kluster.

Flera hanterade instanser kan finnas i ett enda virtuellt kluster. Klustret är automatiskt utökat eller om det behövs när kunden ändras antalet instanser som etablerats i undernätet.

Kundprogram kunde ansluta till hanterade instanser, fråge- och databaser endast om de körs i det virtuella nätverket eller peerkopplade virtuella nätverket eller VPN / Express Route anslutet nätverk med hjälp av slutpunkt med privat IP-adress.  

![Arkitekturdiagram för anslutning](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft-tjänster för hantering och distribution kör utanför det virtuella nätverket så att anslutningar mellan en hanterad instans och Microsoft-tjänster går över slutpunkter med offentliga IP-adresser. När hanterad instans skapar utgående anslutning, när tas emot slutet verkar det som det kommer från den här offentliga IP-Adressen på grund av Network adress Translation (NAT).

Hantering av trafik flödar genom kundens virtuella nätverk. Som innebär att element i virtuell nätverksinfrastruktur påverkar och kan skada hanteringstrafik orsaka instans att ange felaktigt tillstånd och blir otillgänglig.

> [!IMPORTANT]
> För att förbättra kundupplevelsen och tjänsttillgänglighet, tillämpar Microsoft avsikt nätverksprincip på Azure-nätverk infrastrukturelement som kan påverka fungerar som den hanterade instansen. Det här är en plattform-mekanism för att kommunicera transparent nätverkskraven för slutanvändare med huvudsakliga målet att förhindra Nätverkskonfigurationsfel och säkerställa normala hanterad instans-verksamhet. När hanterade instansen raderas bort avsikt nätverksprincip också.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuellt kluster anslutningsarkitektur

Låt oss ta en djupdykning i anslutningsarkitektur för hanterad instans. Följande diagram visar begreppsmässig layouten för det virtuella klustret.

![anslutningen arkitektur diagram virtuellt kluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienterna ansluter till managed instance med värdnamn som har ett formulär `<mi_name>.<dns_zone>.database.windows.net`. Värddatorns namn motsvarar privata IP-adressen även om den är registrerad i offentliga DNS-zon och är offentligt matchat. Den `zone-id` genereras automatiskt när klustret har skapats. Om ett nyskapat kluster värd för en sekundär hanterad instans, delar dess zons-ID med det primära klustret. Mer information finns i [automatisk redundans grupper](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Den här privata IP-adressen tillhör den hanterade instansen intern belastningsutjämnare (ILB) som dirigerar trafik till hanterad instans-gateway (GW). Eftersom flera hanterade instanser kan potentiellt körs i samma kluster, använder GW värdnamn för hanterad instans för att omdirigera trafik till rätt SQL-motor tjänst.

Hantera och distribuera tjänster ansluta till en hanterad instans med hjälp av en [hanteringsslutpunkten](#management-endpoint) att mappas till en extern belastningsutjämnare. Trafiken dirigeras till noderna endast om togs emot på en fördefinierad uppsättning portar som används uteslutande av komponenterna för hanterad instans. Inbyggda brandväggen på noderna är konfigurerad för att tillåta trafik enbart från Microsoft specifika IP-intervall. All kommunikation mellan komponenterna och Hanteringsplanet är ömsesidigt autentiserad-certifikat.

## <a name="management-endpoint"></a>Hanteringsslutpunkt

Det virtuella klustret för hanterad instans innehåller en hanteringsslutpunkt som Microsoft använder för att hantera den hanterade instansen. Hanteringsslutpunkten skyddas med inbyggda brandväggen på nätverket och det ömsesidig certifikatverifiering på programnivå. Du kan [hitta ip-adress för hantering av slutpunkten](sql-database-managed-instance-find-management-endpoint-ip-address.md).

När anslutningar öppnas från inuti den hanterade instansen (säkerhetskopiering, granskningsloggen) visas den som trafiken kommer ifrån från management endpoint offentlig IP-adress. Du kan begränsa åtkomst till offentliga tjänster från en hanterad instans genom att ställa in brandväggsregler som tillåter den hanterade instansen IP-adressen. Mer information om den metod som kan [verifiera den hanterade instans inbyggda brandväggen](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Detta gäller inte ställa in brandväggsregler för Azure-tjänster som finns i samma region som den hanterade instansen som Azure-plattformen har en optimering för trafik som går mellan de tjänster som är samordnad.

## <a name="network-requirements"></a>Nätverkskrav

Du distribuerar en hanterad instans i ett dedikerat undernät (hanterad instans-undernät) i det virtuella nätverket som uppfyller följande krav:

- **Dedikerade undernät**: Hanterad instans-undernätet får inte innehålla andra molntjänst som är associerade med det och det får inte vara ett Gateway-undernät. Du kommer inte att kunna skapa en hanterad instans i ett undernät som innehåller resurser än den hanterade instansen och du kan inte senare lägga till andra resurser i undernätet.
- **Nätverkssäkerhetsgrupp (NSG)**: En NSG som är associerade med det virtuella nätverket måste innehålla dessa definierade obligatorisk [ingående säkerhetsregler](#mandatory-inbound-security-rules) och [utgående säkerhetsregler](#mandatory-outbound-security-rules) framför andra regler. Du kan använda en NSG för att fullständigt kontrollera åtkomsten till slutpunkten för hanterad instans-data genom att filtrera trafik på port 1433.
- **En användardefinierad routningstabell (UDR)**: En användardefinierad routningstabell som är associerade med det virtuella nätverket måste ha dessa [poster](#user-defined-routes) i en användardefinierad routningstabell.
- **Tjänsten har inga slutpunkter**: Hanterad instans-undernätet får inte ha en tjänstslutpunkt som är associerade med den. Kontrollera att tjänsten slutpunkter alternativet inaktiveras när du skapar det virtuella nätverket.
- **Tillräckligt med IP-adresser**: Hanterad instans-undernätet måste ha minst 16 IP-adresser (rekommenderas minst är 32 IP-adresser). Mer information finns i [avgör storleken på undernät för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i [det befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du konfigurerar den att uppfylla [hanterad instans nätverkskrav](#network-requirements), eller skapa en [nya nätverk och undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om målundernätet inte är kompatibel med alla dessa krav. När en hanterad instans skapas en *avsikt nätverksprincip* har tillämpats på undernätet för att förhindra att icke-kompatibla ändringar till konfiguration av nätverk. När den senaste instansen har tagits bort från undernätet på *avsikt nätverksprincip* tas också bort

### <a name="mandatory-inbound-security-rules"></a>Obligatorisk inkommande säkerhetsregler

| Namn       |Port                        |Protokoll|Källa           |Mål|Åtgärd|
|------------|----------------------------|--------|-----------------|-----------|------|
|hantering  |9000, 9003, 1438, 1440, 1452|TCP     |Alla              |Alla        |Tillåt |
|mi_subnet   |Alla                         |Alla     |MI – UNDERNÄT        |Alla        |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|Alla        |Tillåt |

### <a name="mandatory-outbound-security-rules"></a>Obligatorisk utgående säkerhetsregler

| Namn       |Port          |Protokoll|Källa           |Mål|Åtgärd|
|------------|--------------|--------|-----------------|-----------|------|
|hantering  |80, 443, 12000|TCP     |Alla              |Internet   |Tillåt |
|mi_subnet   |Alla           |Alla     |Alla              |MI – UNDERNÄT *  |Tillåt |

\* MI – UNDERNÄT refererar till det IP-adressintervallet för undernätet i formuläret 10.x.x.x/y. Den här informationen kan hittas i Azure-portalen (via undernätsegenskaperna).

> [!IMPORTANT]
> Även om obligatoriska inkommande säkerhetsregler som tillåter trafik från _alla_ källa på portar 9000, 9003, 1438, 1440, 1452 portarna skyddas av inbyggda brandvägg. Detta [artikeln](sql-database-managed-instance-find-management-endpoint-ip-address.md) visar hur du kan identifiera hantering slutpunktens IP-adress och kontrollera brandväggsregler.
> [!NOTE]
> Om du använder Transaktionsreplikering i en hanterad instans och eventuella databasinstans används som en utgivare eller distributör, måste port 445 (TCP utgående) också vara öppna i säkerhetsregler för undernätet för att få åtkomst till Azure-filresursen.

### <a name="user-defined-routes"></a>Användardefinierade vägar

|Namn|Adressprefix|NET hopp|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Virtuellt nätverk|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Dessutom kan du lägga till poster till i routningstabellen för att dirigera trafik som har lokala privata IP-adressintervall som ett mål via vnet-gateway eller virtuell nätverksinstallation (NVA).

- **Valfri anpassad DNS**: Om en anpassad DNS har angetts i det virtuella nätverket, måste Azures rekursiva matchare IP-adress (till exempel 168.63.129.16) läggas till i listan. Mer information finns i [konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md). Anpassad DNS-server måste kunna matcha värdnamn i följande domäner och deras underdomäner: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, och *microsoftonline p.com*.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- Lär dig hur du [konfigurera nya VNet](sql-database-managed-instance-create-vnet-subnet.md) eller [konfigurera befintliga VNet](sql-database-managed-instance-configure-vnet-subnet.md) där du kan distribuera hanterade instanser.
- [Beräkna ut storleken på undernätet](sql-database-managed-instance-determine-size-vnet-subnet.md) där du ska distribuera hanterade instanser.
- Snabbstarter, finns i hur du skapar hanterade instansen:
  - Från den [Azure-portalen](sql-database-managed-instance-get-started.md)
  - med hjälp av [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - med hjälp av [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - med hjälp av [Azure Resource Manager-mall (jumpbox med SSMS som ingår)](https://portal.azure.com/)
