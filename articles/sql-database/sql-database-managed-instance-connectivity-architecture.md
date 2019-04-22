---
title: Anslutningsarkitektur för en hanterad instans i Azure SQL Database | Microsoft Docs
description: Läs mer om Azure SQL Database-hanterad instans kommunikation och anslutningsarkitektur samt hur komponenterna dirigera trafik till den hanterade instansen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 82b533f7293e00469a5b92b02e8d58967379a585
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59497074"
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

Hanterade instanser beror på Azure-tjänster som Azure Storage för säkerhetskopiering, Azure Event Hubs för telemetri, Azure Active Directory för autentisering, Azure Key Vault för Transparent datakryptering (TDE) och ett par Azure plattformstjänster som tillhandahåller funktioner för säkerhet och support. Hanterade instanser är anslutningar till dessa tjänster.

All kommunikation är krypterat och signerat med certifikat. Om du vill kontrollera trovärdigheten för kommunicerande enheterna som hanteras Kontrollera instanser ständigt dessa certifikat via listor över återkallade certifikat. Om certifikaten har återkallats, stängs den hanterade instansen anslutningar för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Övergripande anslutningsarkitektur

På en hög nivå är en uppsättning tjänstkomponenter i en hanterad instans. Dessa komponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i kundens virtuella nätverkets undernät. De här datorerna utgör ett virtuellt kluster.

Ett virtuellt kluster kan ha flera hanterade instanser. Om det behövs kan klustret automatiskt att visas eller döljs när kunden ändras antalet instanser som etablerats i undernätet.

Kundprogram kan ansluta till hanterade instanser, och kan fråga efter och uppdatera databaserna på det virtuella nätverket, peer-kopplade virtuella nätverket, eller nätverk som är anslutna via VPN eller Azure ExpressRoute. Det här nätverket måste använda en slutpunkt och en privat IP-adress.  

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
> Traffice som leder till Azure-tjänster som är i regionen för den hanterade instansen är optimerad och anledning inte NATed till hanterade instans-hantering endpoint offentliga IP-adressen för den. Därför om du vill använda IP-baserade brandväggsregler, oftast för lagring, service måste vara i en annan region från hanterad instans.

## <a name="network-requirements"></a>Nätverkskrav

Distribuera en hanterad instans i ett dedikerat undernät i virtuella nätverk. Undernätet måste ha följande egenskaper:

- **Dedikerat undernät:** Undernät för den hanterade instansen får inte innehålla andra molntjänst som är associerat med det och den kan inte vara ett gateway-undernät. Undernätet får inte innehålla någon resurs men den hanterade instansen och du senare lägga till inte resurser i undernätet.
- **Nätverkssäkerhetsgrupp (NSG):** En NSG som är associerat med det virtuella nätverket måste definiera [ingående säkerhetsregler](#mandatory-inbound-security-rules) och [utgående säkerhetsregler](#mandatory-outbound-security-rules) innan andra regler. Du kan använda en NSG för att styra åtkomsten till slutpunkten för den hanterade instansen data genom att filtrera trafik på port 1433 och portar 11000 11999 när hanterade instansen har konfigurerats för omdirigera anslutningar.
- **Användartabell användardefinierad väg (UDR):** En UDR-tabell som är associerat med det virtuella nätverket måste innehålla specifika [poster](#user-defined-routes).
- **Tjänsten har inga slutpunkter:** Ingen tjänstslutpunkt ska vara associerat med den hanterade instansen undernät. Kontrollera att tjänsten slutpunkter alternativet inaktiveras när du skapar det virtuella nätverket.
- **Tillräckligt med IP-adresser:** Hanterad instans-undernätet måste ha minst 16 IP-adresser. Rekommenderad minsta är 32 IP-adresser. Mer information finns i [avgör storleken på undernätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i [det befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du har konfigurerat den för att uppfylla [nätverkskrav för hanterade instanser](#network-requirements). Annars skapar du en [nya nätverk och undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om målundernätet saknar följande egenskaper. När du skapar en hanterad instans, har en avsiktlig princip tillämpats på undernätet för att förhindra att nätverksinställningarna för icke-kompatibla ändras. När den senaste instansen tas bort från undernätet, tas avsiktlig nätverksprincipen också bort.

### <a name="mandatory-inbound-security-rules"></a>Obligatorisk inkommande säkerhetsregler

| Namn       |Port                        |Protokoll|Källa           |Mål|Åtgärd|
|------------|----------------------------|--------|-----------------|-----------|------|
|hantering  |9000, 9003, 1438, 1440, 1452|TCP     |Alla              |MI – UNDERNÄT  |Tillåt |
|mi_subnet   |Alla                         |Alla     |MI – UNDERNÄT        |MI – UNDERNÄT  |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|MI – UNDERNÄT  |Tillåt |

### <a name="mandatory-outbound-security-rules"></a>Obligatorisk utgående säkerhetsregler

| Namn       |Port          |Protokoll|Källa           |Mål|Åtgärd|
|------------|--------------|--------|-----------------|-----------|------|
|hantering  |80, 443, 12000|TCP     |MI – UNDERNÄT        |AzureCloud |Tillåt |
|mi_subnet   |Alla           |Alla     |MI – UNDERNÄT        |MI – UNDERNÄT  |Tillåt |

> [!IMPORTANT]
> Se till att det finns bara en inkommande regel för portar 9000, 9003, 1438, 1440, 1452 och en regel för utgående portarna 80, 443, 12000. Hanterade instans etablering via Azure Resource Manager distributioner kommer att misslyckas om inkommande och utgående regler konfigureras separat för varje port. Om dessa portar finns i separata regler kan misslyckas distributionen med felkoden `VnetSubnetConflictWithIntendedPolicy`

\* MI – UNDERNÄT refererar till IP-adressintervall för undernätet i formuläret 10.x.x.x/y. Du hittar den här informationen i Azure-portalen i undernätsegenskaperna för.

> [!IMPORTANT]
> Även om nödvändiga inkommande säkerhetsregler som tillåter trafik från _alla_ källa på portar 9000, 9003, 1438, 1440 och 1452, portarna skyddas av en inbyggd brandvägg. Mer information finns i [fastställa slutpunktsadress management](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Om du vill använda Transaktionsreplikering i en hanterad instans, och om du använder en instans-databas som en utgivare eller distributör, kan du öppna port 445 (TCP utgående) i undernätets säkerhetsregler. Den här porten kommer att tillåta åtkomst till Azure-filresursen.

### <a name="user-defined-routes"></a>Användardefinierade vägar

|Namn|Adressprefix|Nästa hopp|
|----|--------------|-------|
|subnet_to_vnetlocal|MI – UNDERNÄT|Virtuellt nätverk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

Dessutom kan du lägga till poster till i routningstabellen för att dirigera trafik som har lokala privata IP-adressintervall som mål i vnet-gateway eller en virtuell nätverksinstallation (NVA).

Om det virtuella nätverket innehåller en anpassad DNS, anpassad DNS-server måste kunna matcha värdnamn i \*. core.windows.net zon. Med hjälp av ytterligare funktioner som Azure AD-autentisering kan behöva lösa ytterligare FQDN-namn. Mer information finns i [ställa in en anpassad DNS](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [SQL-databas som avancerade datasäkerhet](sql-database-managed-instance.md).
- Lär dig hur du [ställa in ett nytt Azure virtuellt nätverk](sql-database-managed-instance-create-vnet-subnet.md) eller en [befintliga Azure-nätverk](sql-database-managed-instance-configure-vnet-subnet.md) där du kan distribuera hanterade instanser.
- [Beräkna storleken på undernätet](sql-database-managed-instance-determine-size-vnet-subnet.md) där du vill distribuera de hanterade instanserna.
- Lär dig hur du skapar en hanterad instans:
  - Från den [Azure-portalen](sql-database-managed-instance-get-started.md).
  - Med hjälp av [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Med hjälp av [en Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Med hjälp av [en Azure Resource Manager-mall (med JumpBox, med SSMS som ingår)](https://portal.azure.com/). 
