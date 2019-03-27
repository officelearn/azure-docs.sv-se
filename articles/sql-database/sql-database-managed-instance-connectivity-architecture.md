---
title: Anslutningsarkitektur för en hanterad instans i Azure SQL Database | Microsoft Docs
description: Läs mer om Azure SQL Database-hanterad instans kommunikation och anslutningsarkitektur samt hur komponenterna dirigera trafik till den hanterade instansen.
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
ms.openlocfilehash: ad005ff879ef5e4c0fb2fb72ce3062a5dd25d99a
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486792"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Anslutningsarkitektur för en hanterad instans i Azure SQL Database 

Den här artikeln förklarar kommunikation i en hanterad Azure SQL Database-instans. Här beskrivs också anslutningsarkitektur och hur komponenterna dirigera trafik till den hanterade instansen.  

Den hanterade instansen för SQL-databas är placerad i Azure-nätverk och undernät som är dedikerad för hanterade instanser. Den här distributionen innehåller:

- En säker privata IP-adress.
- Möjligheten att ansluta ett lokalt nätverk till en hanterad instans.
- Möjligheten att ansluta en hanterad instans till en länkad server eller en annan lokala datalager.
- Möjlighet att ansluta en hanterad instans till Azure-resurser.

## <a name="communication-overview"></a>Översikt över tjänstkommunikation

Följande diagram visar de enheter som ansluter till en hanterad instans. Den visar även de resurser som behöver kommunicera med den hanterade instansen. Kommunikationsprocessen längst ned i diagrammet representerar kundprogram och verktyg som ansluter till den hanterade instansen som datakällor.  

![Entiteter i anslutningsarkitektur](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

En hanterad instans är en plattform-tjänst (PaaS). Microsoft använder automatiska agenter (hantering, distribution och underhåll) för att hantera den här tjänsten baserat på data telemetriströmmar. Eftersom Microsoft ansvarar för hantering, kunder inte åtkomst till de hanterade instansen virtuellt kluster datorerna via Remote Desktop Protocol (RDP).

Vissa SQL-Server som är åtgärder som startas av användare eller program kan kräva hanterade instanser för att interagera med plattformen. Ett enda fall är att skapa en hanterad instans-databas. Den här resursen är tillgängliga via Azure portal, PowerShell, Azure CLI och REST-API.

Hanterade instanser beror på Azure-tjänster som Azure Storage för säkerhetskopiering, Azure Service Bus för telemetri, Azure Active Directory för autentisering och Azure Key Vault för Transparent datakryptering (TDE). Hanterade instanser gör ansluta till dessa tjänster.

All kommunikation använda certifikat för kryptering och signering. Om du vill kontrollera trovärdigheten för kommunicerande enheterna, hanterade Kontrollera instanser ständigt dessa certifikat genom att kontakta en certifikatutfärdare. Om certifikaten har återkallats eller inte går att verifiera, stängs den hanterade instansen anslutningar för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Övergripande anslutningsarkitektur

På en hög nivå är en uppsättning tjänstkomponenter i en hanterad instans. Dessa komponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i kundens virtuella nätverkets undernät. De här datorerna utgör ett virtuellt kluster.

Ett virtuellt kluster kan ha flera hanterade instanser. Om det behövs kan klustret automatiskt att visas eller döljs när kunden ändras antalet instanser som etablerats i undernätet.

Kundprogram kan ansluta till hanterade instanser och kan hämta och uppdatera databaser endast om de körs i det virtuella nätverket, peer-kopplat virtuellt nätverk eller nätverk som är anslutna via VPN eller Azure ExpressRoute. Det här nätverket måste använda en slutpunkt och en privat IP-adress.  

![Arkitekturdiagram för anslutning](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft-tjänster för hantering och distribution kör utanför det virtuella nätverket. En hanterad instans och Microsoft-tjänster ansluta via slutpunkter som har offentliga IP-adresser. När en hanterad instans skapar en utgående anslutning på mottagande sidan Network Address Translation (NAT) gör anslutningen ser ut som den kommer från den här offentliga IP-adressen.

Hantering av trafik flödar genom kundens virtuella nätverk. Det innebär att element i det virtuella nätverkets infrastruktur kan skada hanteringstrafik genom att göra instansen misslyckas och blir otillgänglig.

> [!IMPORTANT]
> För att förbättra kundupplevelsen och tjänsttillgänglighet, tillämpar Microsoft en avsiktlig nätverksprincip på infrastrukturelement för Azure-nätverk. Principen kan påverka hur den hanterade instansen fungerar. Den här plattformen mekanismen kommunicerar transparent nätverkskraven för användare. Principens huvudsakliga målet är att förhindra Nätverkskonfigurationsfel och säkerställa normala hanterad instans-åtgärder. När du tar bort en hanterad instans tas nätverk avsikt principen också bort.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuellt kluster anslutningsarkitektur

Låt oss ta en djupdykning i anslutningsarkitektur för hanterade instanser. Följande diagram visar begreppsmässig layouten för det virtuella klustret.

![Anslutningsarkitektur för det virtuella klustret](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienterna ansluter till en hanterad instans med hjälp av ett värdnamn som har formatet `<mi_name>.<dns_zone>.database.windows.net`. Värddatorns namn matchas till en privat IP-adress, även om den är registrerad i en zon som offentliga Domain Name System (DNS) och är offentligt matchat. Den `zone-id` genereras automatiskt när du skapar klustret. Om ett nyskapat kluster är värd för en sekundär hanterad instans, delar dess zons-ID med det primära klustret. Mer information finns i [använda automatisk växling vid fel grupper för att aktivera transparent och samordnad redundans för flera databaser](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Den här privata IP-adressen tillhör den hanterade instansen intern belastningsutjämnare. Belastningsutjämnaren dirigerar trafik till den hanterade instansen gateway. Eftersom flera hanterade instanser kan köras i samma kluster, använder gatewayen värdnamnet för den hanterade instansen för att omdirigera trafik till rätt tjänst för SQL-motor.

Hantera och distribuera tjänster ansluta till en hanterad instans med hjälp av en [hanteringsslutpunkten](#management-endpoint) att mappas till en extern belastningsutjämnare. Trafiken dirigeras till noderna endast om den tas emot på en fördefinierad uppsättning portar som använder bara den hanterade instansen komponenterna. En inbyggd brandvägg på noderna har ställts in så att endast trafik från Microsoft IP-intervall. Certifikat autentisera ömsesidigt all kommunikation mellan komponenterna och Hanteringsplanet.

## <a name="management-endpoint"></a>Hanteringsslutpunkt

Microsoft hanterar den hanterade instansen med hjälp av en hanteringsslutpunkt. Den här slutpunkten som finns i den instansen virtuellt kluster. Hanteringsslutpunkten skyddas av en inbyggd brandvägg på nätverksnivå. Den är skyddad med ömsesidig certifikatverifiering på applikationsnivå. Du hittar slutpunkten IP-adress [fastställa IP-adressen för den hanteringsslutpunkten](sql-database-managed-instance-find-management-endpoint-ip-address.md).

När anslutningar starta i den hanterade instansen (precis som med säkerhetskopior och granskningsloggar), visas trafik för att starta från den hanteringsslutpunkten offentlig IP-adress. Du kan begränsa åtkomsten till offentliga tjänster från en hanterad instans genom att ställa in brandväggsregler som tillåter bara den hanterade instansen IP-adress. Mer information finns i [verifiera den hanterade instansen inbyggda brandväggen](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Till skillnad från brandväggen för anslutningar som startar i den hanterade instansen har en brandvägg som är optimerad för trafik som går mellan de här tjänsterna i Azure-tjänster som är i regionen för den hanterade instansen.

## <a name="network-requirements"></a>Nätverkskrav

Distribuera en hanterad instans i ett dedikerat undernät i virtuella nätverk. Undernätet måste ha följande egenskaper:

- **Dedikerat undernät:** Undernät för den hanterade instansen får inte innehålla andra molntjänst som är associerat med det och den kan inte vara ett gateway-undernät. Undernätet får inte innehålla någon resurs men den hanterade instansen och du senare lägga till inte resurser i undernätet.
- **Nätverkssäkerhetsgrupp (NSG):** En NSG som är associerat med det virtuella nätverket måste definiera [ingående säkerhetsregler](#mandatory-inbound-security-rules) och [utgående säkerhetsregler](#mandatory-outbound-security-rules) innan andra regler. Du kan använda en NSG för att styra åtkomsten till slutpunkten för den hanterade instansen data genom att filtrera trafik på port 1433.
- **Användartabell användardefinierad väg (UDR):** En UDR-tabell som är associerat med det virtuella nätverket måste innehålla specifika [poster](#user-defined-routes).
- **Tjänsten har inga slutpunkter:** Ingen tjänstslutpunkt ska vara associerat med den hanterade instansen undernät. Kontrollera att tjänsten slutpunkter alternativet inaktiveras när du skapar det virtuella nätverket.
- **Tillräckligt med IP-adresser:** Hanterad instans-undernätet måste ha minst 16 IP-adresser. Rekommenderad minsta är 32 IP-adresser. Mer information finns i [avgör storleken på undernätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i [det befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du har konfigurerat den för att uppfylla [nätverkskrav för hanterade instanser](#network-requirements). Annars skapar du en [nya nätverk och undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om målundernätet saknar följande egenskaper. När du skapar en hanterad instans, har en avsiktlig princip tillämpats på undernätet för att förhindra att nätverksinställningarna för icke-kompatibla ändras. När den senaste instansen tas bort från undernätet, tas avsiktlig nätverksprincipen också bort.

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

> Kontrollera att det finns endast en regel för inkommande trafik för portar 9000, 9003, 1438, 1440, 1452 och en regel för utgående portarna 80, 443, 12000. Hanterad instans etablering via ARM-distributioner kan misslyckas om inkommande och utgående regler konfigureras separat för varje portar. 

\* MI – UNDERNÄT refererar till IP-adressintervall för undernätet i formuläret 10.x.x.x/y. Du hittar den här informationen i Azure-portalen i undernätsegenskaperna för.

> [!IMPORTANT]
> Även om nödvändiga inkommande säkerhetsregler som tillåter trafik från _alla_ källa på portar 9000, 9003, 1438, 1440 och 1452, portarna skyddas av en inbyggd brandvägg. Mer information finns i [fastställa slutpunktsadress management](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Om du vill använda Transaktionsreplikering i en hanterad instans, och om du använder en instans-databas som en utgivare eller distributör, kan du öppna port 445 (TCP utgående) i undernätets säkerhetsregler. Den här porten kommer att tillåta åtkomst till Azure-filresursen.

### <a name="user-defined-routes"></a>Användardefinierade vägar

|Namn|Adressprefix|Nästa hopp|
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

Dessutom kan du lägga till poster till i routningstabellen för att dirigera trafik som har lokala privata IP-adressintervall som mål i vnet-gateway eller en virtuell nätverksinstallation (NVA).

Om det virtuella nätverket innehåller en anpassad DNS kan du lägga till en post för Azures rekursiva matchare IP-adress (till exempel 168.63.129.16). Mer information finns i [ställa in en anpassad DNS](sql-database-managed-instance-custom-dns.md). Anpassad DNS-server måste kunna matcha värdnamn i dessa domäner och deras underdomäner: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, och *microsoftonline p.com*.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [SQL-databas som avancerade datasäkerhet](sql-database-managed-instance.md).
- Lär dig hur du [ställa in ett nytt Azure virtuellt nätverk](sql-database-managed-instance-create-vnet-subnet.md) eller en [befintliga Azure-nätverk](sql-database-managed-instance-configure-vnet-subnet.md) där du kan distribuera hanterade instanser.
- [Beräkna storleken på undernätet](sql-database-managed-instance-determine-size-vnet-subnet.md) där du vill distribuera de hanterade instanserna.
- Lär dig hur du skapar en hanterad instans:
  - Från den [Azure-portalen](sql-database-managed-instance-get-started.md).
  - Med hjälp av [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Med hjälp av [en Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Med hjälp av [en Azure Resource Manager-mall (med JumpBox, med SSMS som ingår)](https://portal.azure.com/).
