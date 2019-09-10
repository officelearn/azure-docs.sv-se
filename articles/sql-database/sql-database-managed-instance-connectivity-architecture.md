---
title: Anslutnings arkitektur för en hanterad instans i Azure SQL Database | Microsoft Docs
description: Lär dig mer om Azure SQL Database Hanterad instans-och anslutnings arkitektur samt hur komponenterna dirigerar trafik till den hanterade instansen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: d539bd569eee613eb43947e5fd0e3b0614ca5d79
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858620"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Anslutnings arkitektur för en hanterad instans i Azure SQL Database

I den här artikeln beskrivs kommunikation i en Azure SQL Database Hanterad instans. Den beskriver också anslutnings arkitekturen och hur komponenterna dirigerar trafiken till den hanterade instansen.  

Den SQL Database hanterade instansen placeras inuti det virtuella Azure-nätverket och under nätet som är dedikerat till hanterade instanser. Den här distributionen ger:

- En säker privat IP-adress.
- Möjlighet att ansluta ett lokalt nätverk till en hanterad instans.
- Möjligheten att ansluta en hanterad instans till en länkad server eller till ett annat lokalt data lager.
- Möjligheten att ansluta en hanterad instans till Azure-resurser.

## <a name="communication-overview"></a>Översikt över kommunikation

Följande diagram visar entiteter som ansluter till en hanterad instans. Den visar även de resurser som måste kommunicera med den hanterade instansen. Kommunikations processen längst ned i diagrammet representerar kund program och verktyg som ansluter till den hanterade instansen som data källor.  

![Entiteter i anslutnings arkitektur](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

En hanterad instans är ett PaaS-erbjudande (Platform as a Service). Microsoft använder automatiserade agenter (hantering, distribution och underhåll) för att hantera den här tjänsten baserat på telemetridata data strömmar. Eftersom Microsoft ansvarar för hantering kan kunder inte komma åt virtuella kluster för hanterade instanser via Remote Desktop Protocol (RDP).

Vissa SQL Server åtgärder som startas av slutanvändare eller program kan behöva hanterade instanser för att interagera med plattformen. Ett fall är att skapa en hanterad instans databas. Den här resursen exponeras via Azure Portal, PowerShell, Azure CLI och REST API.

Hanterade instanser är beroende av Azure-tjänster som Azure Storage för säkerhets kopiering, Azure Event Hubs for telemetri, Azure Active Directory för autentisering, Azure Key Vault för transparent datakryptering (TDE) och ett par Azure Platform-tjänster som tillhandahåller funktioner för säkerhet och support. De hanterade instanserna gör anslutningar till de här tjänsterna.

All kommunikation krypteras och signeras med hjälp av certifikat. För att kontrol lera trovärdigheten för kommunicerande parter, verifierar hanterade instanser ständigt dessa certifikat via listor över återkallade certifikat. Om certifikaten återkallas stänger den hanterade instansen anslutningarna för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Arkitektur med hög nivå anslutning

På hög nivå är en hanterad instans en uppsättning tjänst komponenter. Dessa komponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i kundens virtuella nätverks undernät. De här datorerna utgör ett virtuellt kluster.

Ett virtuellt kluster kan vara värd för flera hanterade instanser. Vid behov expanderar klustret automatiskt eller kontrakt när kunden ändrar antalet etablerade instanser i under nätet.

Kund program kan ansluta till hanterade instanser och kan fråga efter och uppdatera databaser inuti det virtuella nätverket, peer-kopplade virtuella nätverk eller nätverk som är anslutna via VPN eller Azure ExpressRoute. Det här nätverket måste använda en slut punkt och en privat IP-adress.  

![Diagram över anslutnings arkitektur](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsofts hanterings-och distributions tjänster körs utanför det virtuella nätverket. En hanterad instans och Microsoft-tjänster ansluter över de slut punkter som har offentliga IP-adresser. När en hanterad instans skapar en utgående anslutning kommer anslutningen att se ut som om den kommer från den här offentliga IP-adressen när NAT (Network Address Translation) tas emot.

Hanterings trafik flödar genom kundens virtuella nätverk. Det innebär att element i det virtuella nätverkets infrastruktur kan skada hanterings trafiken genom att göra instansen misslyckad och bli otillgänglig.

> [!IMPORTANT]
> För att förbättra kund upplevelsen och tjänstens tillgänglighet tillämpar Microsoft en princip för nätverks avsikt på infrastruktur element för virtuella Azure-nätverk. Principen kan påverka hur den hanterade instansen fungerar. Den här plattforms mekanismen kommunicerar transparent nätverks krav till användare. Principens huvud mål är att förhindra felaktig nätverks konfiguration och se till att vanliga hanterade instans åtgärder säkerställs. När du tar bort en hanterad instans tas även principen för nätverks avsikt bort.

## <a name="virtual-cluster-connectivity-architecture"></a>Arkitektur för virtuella kluster anslutningar

Låt oss ta en djupare titt på anslutnings arkitekturen för hanterade instanser. Följande diagram visar den konceptuella layouten för det virtuella klustret.

![Anslutnings arkitektur för det virtuella klustret](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienter ansluter till en hanterad instans med hjälp av ett värdnamn som har formuläret `<mi_name>.<dns_zone>.database.windows.net`. Det här värd namnet matchar en privat IP-adress även om den är registrerad i en offentlig Domain Name System (DNS)-zon och kan matchas offentligt. `zone-id` Skapas automatiskt när du skapar klustret. Om ett nytt kluster är värd för en sekundär hanterad instans, delar den sitt zon-ID med det primära klustret. Mer information finns i [använda grupper för automatisk redundans för att aktivera transparent och koordinerad redundansväxling av flera databaser](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Den här privata IP-adressen tillhör den hanterade instansens interna belastningsutjämnare. Belastningsutjämnaren dirigerar trafiken till den hanterade instansens Gateway. Eftersom flera hanterade instanser kan köras i samma kluster använder gatewayen den hanterade instansens värdnamn för att dirigera om trafik till rätt SQL-motortjänster.

Hanterings-och distributions tjänster ansluter till en hanterad instans med hjälp av en [hanterings slut punkt](#management-endpoint) som mappar till en extern belastningsutjämnare. Trafiken dirigeras till noderna endast om den tas emot på en fördefinierad uppsättning portar som endast används av hanterings komponenter för den hanterade instansen. En inbyggd brand vägg på noderna har kon figurer ATS för att tillåta trafik enbart från Microsoft IP-intervall. Certifikat autentiserar all kommunikation mellan hanterings komponenter och hanterings planet gemensamt.

## <a name="management-endpoint"></a>Hanteringsslutpunkt

Microsoft hanterar den hanterade instansen med hjälp av en hanterings slut punkt. Den här slut punkten finns inuti instansens virtuella kluster. Hanterings slut punkten skyddas av en inbyggd brand vägg på nätverks nivå. På program nivå skyddas den av ömsesidig certifikat verifiering. Information om hur du hittar slut punktens IP-adress finns i [bestämma hanterings slut punktens IP-adress](sql-database-managed-instance-find-management-endpoint-ip-address.md).

När anslutningar börjar inuti den hanterade instansen (som med säkerhets kopiering och gransknings loggar), verkar trafiken starta från hanterings slut punktens offentliga IP-adress. Du kan begränsa åtkomsten till offentliga tjänster från en hanterad instans genom att ställa in brand Väggs regler så att endast den hanterade instansens IP-adress anges. Mer information finns i [verifiera den inbyggda brand väggen för den hanterade](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)instansen.

> [!NOTE]
> Trafik som går till Azure-tjänster som finns i den hanterade instansen är optimerad och därför inte NATed till en offentlig IP-adress för hanterad instans hantering. Av den anledningen, om du behöver använda IP-baserade brand Väggs regler, som oftast finns för lagring, måste tjänsten finnas i en annan region än den hanterade instansen.

## <a name="network-requirements"></a>Nätverkskrav

Distribuera en hanterad instans i ett dedikerat undernät i det virtuella nätverket. Under nätet måste ha följande egenskaper:

- **Dedikerat undernät:** Under nätet för den hanterade instansen får inte innehålla någon annan moln tjänst som är kopplad till den, och det får inte vara ett Gateway-undernät. Under nätet får inte innehålla någon resurs, men den hanterade instansen, och du kan inte senare lägga till andra typer av resurser i under nätet.
- **Nätverks säkerhets grupp (NSG):** En NSG som är associerad med det virtuella nätverket måste definiera [inkommande säkerhets regler](#mandatory-inbound-security-rules) och [utgående säkerhets regler](#mandatory-outbound-security-rules) före andra regler. Du kan använda en NSG för att styra åtkomsten till data slut punkten för den hanterade instansen genom att filtrera trafiken på port 1433 och portarna 11000-11999 när den hanterade instansen har kon figurer ATS för
- **Användardefinierad routningstabell (UDR):** En UDR-tabell som är associerad med det virtuella nätverket måste innehålla vissa [poster](#user-defined-routes).
- **Inga tjänst slut punkter:** Ingen tjänst slut punkt måste vara kopplad till under nätet för den hanterade instansen. Kontrol lera att alternativet tjänst slut punkter är inaktiverat när du skapar det virtuella nätverket.
- **Tillräckligt med IP-adresser:** Under nätet för hanterade instanser måste ha minst 16 IP-adresser. Det rekommenderade minimivärdet är 32 IP-adresser. Mer information finns i [bestämma storleken på under nätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i [det befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du har konfigurerat det för att uppfylla [nätverks kraven för hanterade instanser](#network-requirements). Annars skapar du ett [nytt nätverk och undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om mål under nätet saknar dessa egenskaper. När du skapar en hanterad instans tillämpas en princip för nätverks avsikt i under nätet för att förhindra inkompatibla ändringar av nätverks konfigurationen. När den sista instansen har tagits bort från under nätet tas även principen för nätverks avsikt bort.

### <a name="mandatory-inbound-security-rules"></a>Obligatoriska inkommande säkerhets regler

| Name       |Port                        |Protocol|Källa           |Mål|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|hantering  |9000, 9003, 1438, 1440, 1452|TCP     |Any              |MI-UNDERNÄT  |Allow |
|mi_subnet   |Any                         |Any     |MI-UNDERNÄT        |MI-UNDERNÄT  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|MI-UNDERNÄT  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Obligatoriska utgående säkerhets regler

| Name       |Port          |Protocol|Källa           |Mål|Action|
|------------|--------------|--------|-----------------|-----------|------|
|hantering  |80, 443, 12000|TCP     |MI-UNDERNÄT        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |MI-UNDERNÄT        |MI-UNDERNÄT  |Allow |

> [!IMPORTANT]
> Se till att det bara finns en regel för inkommande trafik för portarna 9000, 9003, 1438, 1440, 1452 och en utgående regel för portarna 80, 443, 12000. Hantering av hanterade instanser via Azure Resource Manager-distributioner Miss fungerar om inkommande och utgående regler har kon figurer ATS separat för varje port. Om de här portarna finns i separata regler Miss fungerar distributionen med felkod`VnetSubnetConflictWithIntendedPolicy`

\*MI-UNDERNÄT syftar på IP-adressintervallet för under nätet i formatet 10. x. x/y. Du hittar den här informationen i Azure Portal i under näts egenskaper.

> [!IMPORTANT]
> Även om de obligatoriska inkommande säkerhets reglerna tillåter trafik från vilken källa som _helst_ på portarna 9000, 9003, 1438, 1440 och 1452, skyddas dessa portar av en inbyggd brand vägg. Mer information finns i [ta reda på hanterings slut punktens adress](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Om du använder Transaktionsreplikering i en hanterad instans och om du använder en instans databas som utgivare eller en distributör öppnar du Port 445 (TCP utgående) i under nätets säkerhets regler. Den här porten kommer att tillåta åtkomst till Azure-filresursen.

### <a name="user-defined-routes"></a>Användardefinierade vägar

|Name|Adressprefix|Nästa hopp|
|----|--------------|-------|
|subnet_to_vnetlocal|MI-UNDERNÄT|Virtuellt nätverk|
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
|mi-207-46-16-nexthop-internet|adress blocket 207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

Dessutom kan du lägga till poster i routningstabellen för att dirigera trafik som har lokala privata IP-adressintervall som mål via den virtuella Nätverksgatewayen eller Virtual Network-apparaten (NVA).

Om det virtuella nätverket innehåller en anpassad DNS-server måste den anpassade DNS-servern kunna matcha offentliga DNS-poster. Om du använder fler funktioner som Azure AD-autentisering kan det krävas att ytterligare FQDN-namn matchas. Mer information finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [SQL Database avancerad data säkerhet](sql-database-managed-instance.md).
- Lär dig hur du [konfigurerar ett nytt virtuellt Azure-nätverk](sql-database-managed-instance-create-vnet-subnet.md) eller ett [befintligt virtuellt Azure-nätverk](sql-database-managed-instance-configure-vnet-subnet.md) där du kan distribuera hanterade instanser.
- [Beräkna storleken på det undernät](sql-database-managed-instance-determine-size-vnet-subnet.md) där du vill distribuera de hanterade instanserna.
- Lär dig hur du skapar en hanterad instans:
  - Från [Azure Portal](sql-database-managed-instance-get-started.md).
  - Med hjälp av [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Med hjälp av [en Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Genom att använda [en Azure Resource Manager mall (med hjälp av hopp, med SSMS ingår)](https://azure.microsoft.com/en-us/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
