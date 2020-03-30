---
title: Anslutningsarkitektur för en hanterad instans
description: Lär dig mer om Azure SQL Database hanterad instanskommunikation och anslutningsarkitektur samt hur komponenterna dirigerar trafik till den hanterade instansen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: f30ccd498b79c36c8892ae38a3e26d169249621a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481107"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Anslutningsarkitektur för en hanterad instans i Azure SQL Database

I den här artikeln beskrivs kommunikation i en hanterad Azure SQL-databas-hanterad instans. Den beskriver också anslutningsarkitektur och hur komponenterna dirigerar trafik till den hanterade instansen.  

Den hanterade SQL-databasinstansen placeras i det virtuella Azure-nätverket och undernätet som är dedikerat till hanterade instanser. Den här distributionen innehåller:

- En säker privat IP-adress.
- Möjligheten att ansluta ett lokalt nätverk till en hanterad instans.
- Möjligheten att ansluta en hanterad instans till en länkad server eller ett annat lokalt datalager.
- Möjligheten att ansluta en hanterad instans till Azure-resurser.

## <a name="communication-overview"></a>Översikt över kommunikation

I följande diagram visas entiteter som ansluter till en hanterad instans. Den visar också de resurser som behöver kommunicera med den hanterade instansen. Kommunikationsprocessen längst ned i diagrammet representerar kundprogram och verktyg som ansluter till den hanterade instansen som datakällor.  

![Entiteter i anslutningsarkitektur](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

En hanterad instans är en plattform som ett PaaS-erbjudande (Service). Microsoft använder automatiserade agenter (hantering, distribution och underhåll) för att hantera den här tjänsten baserat på telemetridataströmmar. Eftersom Microsoft ansvarar för hanteringen kan kunderna inte komma åt de hanterade instansen virtuella klusterdatorer via Remote Desktop Protocol (RDP).

Vissa SQL Server-åtgärder som startades av slutanvändare eller program kan kräva hanterade instanser för att interagera med plattformen. Ett fall är att skapa en hanterad instansdatabas. Den här resursen exponeras via Azure-portalen, PowerShell, Azure CLI och REST API.

Hanterade instanser är beroende av Azure-tjänster som Azure Storage för säkerhetskopior, Azure Event Hubs för telemetri, Azure Active Directory för autentisering, Azure Key Vault for Transparent Data Encryption (TDE) och ett par Azure-plattformstjänster som tillhandahåller säkerhets- och supportfunktioner. Hanterade instanser gör anslutningar till dessa tjänster.

All kommunikation krypteras och signeras med certifikat. För att kontrollera förtroende för kommunicerande parter verifierar hanterade instanser kontinuerligt dessa certifikat via listor över återkallade certifikat. Om certifikaten återkallas stänger den hanterade instansen anslutningarna för att skydda data.

## <a name="high-level-connectivity-architecture"></a>Anslutningsarkitektur på hög nivå

På en hög nivå är en hanterad instans en uppsättning tjänstkomponenter. Dessa komponenter finns på en dedikerad uppsättning isolerade virtuella datorer som körs i kundens virtuella nätverksundernät. Dessa datorer bildar ett virtuellt kluster.

Ett virtuellt kluster kan vara värd för flera hanterade instanser. Om det behövs utökas eller kontrakteras klustret automatiskt när kunden ändrar antalet etablerade instanser i undernätet.

Kundprogram kan ansluta till hanterade instanser och kan fråga och uppdatera databaser i det virtuella nätverket, peered virtuellt nätverk eller nätverk som är anslutna via VPN eller Azure ExpressRoute. Det här nätverket måste använda en slutpunkt och en privat IP-adress.  

![Arkitekturdiagram för anslutning](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsofts hanterings- och distributionstjänster körs utanför det virtuella nätverket. En hanterad instans och Microsoft-tjänster ansluter över slutpunkter som har offentliga IP-adresser. När en hanterad instans skapar en utgående anslutning får anslutningen ser utgående utleveransanslutning (NAT) ut som om den kommer från den här offentliga IP-adressen.

Hanteringstrafik flödar genom kundens virtuella nätverk. Det innebär att delar av det virtuella nätverkets infrastruktur kan skada hanteringstrafiken genom att instansen misslyckas och blir otillgänglig.

> [!IMPORTANT]
> För att förbättra kundupplevelsen och tjänstens tillgänglighet tillämpar Microsoft en princip för nätverksavsikt på Azure-element för virtuell nätverksinfrastruktur. Principen kan påverka hur den hanterade instansen fungerar. Den här plattformsmekanismen kommunicerar nätverkskrav öppet till användarna. Principens främsta mål är att förhindra felkonfiguration av nätverk och att säkerställa normala hanterade instansåtgärder. När du tar bort en hanterad instans tas även principen för nätverksavsikten bort.

## <a name="virtual-cluster-connectivity-architecture"></a>Arkitektur för anslutning till virtuellt kluster

Låt oss ta en djupare inblick i anslutningsarkitektur för hanterade instanser. I följande diagram visas den konceptuella layouten för det virtuella klustret.

![Anslutningsarkitektur för det virtuella klustret](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienter ansluter till en hanterad instans med `<mi_name>.<dns_zone>.database.windows.net`hjälp av ett värdnamn som har formuläret . Det här värdnamnet matchas till en privat IP-adress även om det är registrerat i en DNS-zon (Domain Name System) och är offentligt löst. Genereras `zone-id` automatiskt när du skapar klustret. Om ett nyskapat kluster är värd för en sekundär hanterad instans delar det sitt zon-ID med det primära klustret. Mer information finns i [Använda automatisk redundansgrupper för att aktivera genomskinlig och samordnad redundans för flera databaser](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Den här privata IP-adressen tillhör den hanterade instansens interna belastningsutjämnar. Belastningsutjämnaren dirigerar trafik till den hanterade instansens gateway. Eftersom flera hanterade instanser kan köras i samma kluster använder gatewayen den hanterade instansens värdnamn för att omdirigera trafik till rätt SQL-motortjänst.

Hanterings- och distributionstjänster ansluter till en hanterad instans med hjälp av en [hanteringsslutpunkt](#management-endpoint) som mappar till en extern belastningsutjämnare. Trafiken dirigeras bara till noderna om den tas emot på en fördefinierad uppsättning portar som bara den hanterade instansens hanteringskomponenter använder. En inbyggd brandvägg på noderna är inställd så att trafik endast tillåts från Microsofts IP-intervall. Certifikat autentiserar all kommunikation mellan hanteringskomponenter och hanteringsplanet.

## <a name="management-endpoint"></a>Hanteringsslutpunkt

Microsoft hanterar den hanterade instansen med hjälp av en hanteringsslutpunkt. Den här slutpunkten finns i instansens virtuella kluster. Slutpunkten för hantering skyddas av en inbyggd brandvägg på nätverksnivå. På programnivå skyddas den av ömsesidig certifikatverifiering. Information om hur du hittar slutpunktens IP-adress finns [i Fastställa hanteringsslutpunktens IP-adress](sql-database-managed-instance-find-management-endpoint-ip-address.md).

När anslutningar startar inuti den hanterade instansen (som med säkerhetskopior och granskningsloggar) verkar trafiken starta från hanteringsslutpunktens offentliga IP-adress. Du kan begränsa åtkomsten till offentliga tjänster från en hanterad instans genom att ange brandväggsregler så att endast den hanterade instansens IP-adress tillåts. Mer information finns [i Verifiera den hanterade instansens inbyggda brandvägg](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Trafik som går till Azure-tjänster som finns i den hanterade instansens region är optimerad och därför inte NATed till hanterad instanshanteringsslutpunkts offentlig IP-adress. Därför måste tjänsten vara i en annan region än hanterad instans om du behöver använda IP-baserade brandväggsregler, oftast för lagring.

## <a name="service-aided-subnet-configuration"></a>Konfiguration av servicestödda undernät

För att åtgärda kundsäkerhet och hanterbarhetskrav Hanterad instans övergår från manuell till servicestödd undernätskonfiguration.

Med service-aided undernät konfiguration användare är i full kontroll över data (TDS) trafik medan managed instance tar ansvar för att säkerställa oavbrutet flöde av hantering trafik för att uppfylla SLA.

Tjänststödd undernätskonfiguration bygger på funktionen för [delegering](../virtual-network/subnet-delegation-overview.md) av virtuella nätverk för att tillhandahålla automatisk hantering av nätverkskonfiguration och aktivera tjänstslutpunkter. Tjänstslutpunkter kan användas för att konfigurera regler för virtuella nätverksbrandvägger på lagringskonton som håller säkerhetskopior/granskningsloggar.

### <a name="network-requirements"></a>Nätverkskrav 

Distribuera en hanterad instans i ett dedikerat undernät i det virtuella nätverket. Undernätet skall ha följande egenskaper:

- **Dedikerat undernät:** Den hanterade instansens undernät kan inte innehålla någon annan molntjänst som är associerad med den och det kan inte vara ett gateway-undernät. Undernätet kan inte innehålla någon resurs utan den hanterade instansen och du kan inte lägga till andra typer av resurser i undernätet senare.
- **Delegering av undernät:** Den hanterade instansens undernät måste `Microsoft.Sql/managedInstances` delegeras till resursprovidern.
- **Nätverkssäkerhetsgrupp (NSG):** En NSG måste associeras med den hanterade instansens undernät. Du kan använda en NSG för att styra åtkomsten till den hanterade instansens dataslutpunkt genom att filtrera trafik på port 1433 och portarna 11000-11999 när hanterad instans konfigureras för omdirigeringsanslutningar. Tjänsten etablerar och behåller gällande [regler](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) som krävs för att tillåta oavbrutet flöde av hanteringstrafik.
- **Tabell för användardefinierad väg (UDR):** En UDR-tabell måste associeras med den hanterade instansens undernät. Du kan lägga till poster i flödestabellen för att dirigera trafik som har lokala privata IP-intervall som mål via den virtuella nätverksgatewayen eller den virtuella nätverksinstallationen (NVA). Tjänsten etablerar automatiskt och behåller aktuella [poster](#user-defined-routes-with-service-aided-subnet-configuration) som krävs för att tillåta oavbrutet flöde av hanteringstrafik.
- **Tillräckliga IP-adresser:** Undernätet för hanterade instanser måste ha minst 16 IP-adresser. Det rekommenderade minimumet är 32 IP-adresser. Mer information finns [i Bestämma storleken på undernätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i det [befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du har konfigurerat det för att uppfylla [nätverkskraven för hanterade instanser](#network-requirements). Annars skapar du ett [nytt nätverk och ett nytt undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> När du skapar en hanterad instans tillämpas en princip för nätverksavsikter i undernätet för att förhindra inkompatibla ändringar i nätverksinställningarna. När den sista instansen har tagits bort från undernätet tas även principen för nätverksavsikten bort.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatoriska regler för inkommande säkerhet med servicestödd undernätskonfiguration 

| Namn       |Port                        |Protokoll|Källa           |Mål|Åtgärd|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement (SQLManage)    |MI-UNDERNÄT  |Tillåt |
|            |9000, 9003                  |TCP     |CorpnetSaw (2000)-0       |MI-UNDERNÄT  |Tillåt |
|            |9000, 9003                  |TCP     |CorpnetPublic (på andra sätt)    |MI-UNDERNÄT  |Tillåt |
|mi_subnet   |Alla                         |Alla     |MI-UNDERNÄT        |MI-UNDERNÄT  |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|MI-UNDERNÄT  |Tillåt |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Obligatoriska utgående säkerhetsregler med servicestödd undernätskonfiguration 

| Namn       |Port          |Protokoll|Källa           |Mål|Åtgärd|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI-UNDERNÄT        |AzureCloud |Tillåt |
|mi_subnet   |Alla           |Alla     |MI-UNDERNÄT        |MI-UNDERNÄT  |Tillåt |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Användardefinierade vägar med servicestödd undernätskonfiguration 

|Namn|Adressprefix|Nästa hopp|
|----|--------------|-------|
|subnät-till-vnetlokal|MI-UNDERNÄT|Virtuellt nätverk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*MI SUBNET refererar till IP-adressintervallet för undernätet i formuläret x.x.x.x/y. Du hittar den här informationen i Azure-portalen i undernätsegenskaper.

Dessutom kan du lägga till poster i flödestabellen för att dirigera trafik som har lokala privata IP-intervall som mål via den virtuella nätverksgatewayen eller den virtuella nätverksinstallationen (NVA).

Om det virtuella nätverket innehåller en anpassad DNS måste den anpassade DNS-servern kunna matcha offentliga DNS-poster. Om du använder ytterligare funktioner som Azure AD-autentisering kan det krävas att ytterligare FQDN:er åtgärdas. Mer information finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Begränsningar för nätverk

**TLS 1.2 tillämpas på utgående anslutningar:** I januari 2020 verkställs Microsoft TLS 1.2 för trafik inom tjänsten i alla Azure-tjänster. För azure SQL Database-hanterad instans resulterade detta i att TLS 1.2 tillämpades på utgående anslutningar som används för replikering och länkade serveranslutningar till SQL Server. Om du använder versioner av SQL Server äldre sedan 2016 med Hanterad instans, se till att [TLS 1.2 specifika uppdateringar](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) har tillämpats.

Följande virtuella nätverksfunktioner stöds för närvarande inte med hanterad instans:
- **Microsoft-peering:** Aktivera [Microsoft-peering](../expressroute/expressroute-faqs.md#microsoft-peering) på expressrännliga vägkretsar som peeras direkt eller transitivt med virtuellt nätverk där hanterad instans finns påverkar trafikflödet mellan komponenter i hanterade instanser i virtuellt nätverk och tjänster som det beror på att orsaka tillgänglighetsproblem. Hanterade instansdistributioner till virtuellt nätverk med Microsoft-peering som redan är aktiverat förväntas misslyckas.
- **Global virtuell nätverks peering:** [Virtual network peering-anslutning](../virtual-network/virtual-network-peering-overview.md) över Azure-regioner fungerar inte för hanterad instans på grund av [dokumenterade belastningsutjämnadbegränsningar](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: Om du använder AzurePlatformDNS-tjänsttaggen för att blockera plattforms-DNS-lösning skulle hanterad instans inte tillgänglig. [service tag](../virtual-network/service-tags-overview.md) Även om hanterad instans stöder kunddefinierad DNS för DNS-upplösning i motorn finns det ett beroende av plattforms-DNS för plattformsåtgärder.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>-Jag har inte varit med om det. Nätverkskrav utan servicestödd undernätskonfiguration

Distribuera en hanterad instans i ett dedikerat undernät i det virtuella nätverket. Undernätet skall ha följande egenskaper:

- **Dedikerat undernät:** Den hanterade instansens undernät kan inte innehålla någon annan molntjänst som är associerad med den och det kan inte vara ett gateway-undernät. Undernätet kan inte innehålla någon resurs utan den hanterade instansen och du kan inte lägga till andra typer av resurser i undernätet senare.
- **Nätverkssäkerhetsgrupp (NSG):** En NSG som är associerad med det virtuella nätverket måste definiera [inkommande säkerhetsregler](#mandatory-inbound-security-rules) och [utgående säkerhetsregler](#mandatory-outbound-security-rules) före andra regler. Du kan använda en NSG för att styra åtkomsten till den hanterade instansens dataslutpunkt genom att filtrera trafik på port 1433 och portarna 11000-11999 när hanterad instans konfigureras för omdirigeringsanslutningar.
- **Tabell för användardefinierad väg (UDR):** En UDR-tabell som är associerad med det virtuella nätverket måste innehålla specifika [poster](#user-defined-routes).
- **Inga tjänstslutpunkter:** Ingen tjänstslutpunkt ska associeras med den hanterade instansens undernät. Kontrollera att alternativet för tjänstslutpunkter är inaktiverat när du skapar det virtuella nätverket.
- **Tillräckliga IP-adresser:** Undernätet för hanterade instanser måste ha minst 16 IP-adresser. Det rekommenderade minimumet är 32 IP-adresser. Mer information finns [i Bestämma storleken på undernätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md). Du kan distribuera hanterade instanser i det [befintliga nätverket](sql-database-managed-instance-configure-vnet-subnet.md) när du har konfigurerat det för att uppfylla [nätverkskraven för hanterade instanser](#network-requirements). Annars skapar du ett [nytt nätverk och ett nytt undernät](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Du kan inte distribuera en ny hanterad instans om målundernätet saknar dessa egenskaper. När du skapar en hanterad instans tillämpas en princip för nätverksavsikter i undernätet för att förhindra inkompatibla ändringar i nätverksinställningarna. När den sista instansen har tagits bort från undernätet tas även principen för nätverksavsikten bort.

### <a name="mandatory-inbound-security-rules"></a>Obligatoriska regler för inkommande säkerhet

| Namn       |Port                        |Protokoll|Källa           |Mål|Åtgärd|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Alla              |MI-UNDERNÄT  |Tillåt |
|mi_subnet   |Alla                         |Alla     |MI-UNDERNÄT        |MI-UNDERNÄT  |Tillåt |
|health_probe|Alla                         |Alla     |AzureLoadBalancer|MI-UNDERNÄT  |Tillåt |

### <a name="mandatory-outbound-security-rules"></a>Obligatoriska regler för utgående säkerhetsregler

| Namn       |Port          |Protokoll|Källa           |Mål|Åtgärd|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI-UNDERNÄT        |AzureCloud |Tillåt |
|mi_subnet   |Alla           |Alla     |MI-UNDERNÄT        |MI-UNDERNÄT  |Tillåt |

> [!IMPORTANT]
> Se till att det bara finns en ingående regel för portarna 9000, 9003, 1438, 1440, 1452 och en utgående regel för portarna 443, 12000. Etablerade hanterade instansering via Azure Resource Manager-distributioner misslyckas om inkommande och utgående regler konfigureras separat för varje port. Om dessa portar finns i separata regler misslyckas distributionen med felkod`VnetSubnetConflictWithIntendedPolicy`

\*MI SUBNET refererar till IP-adressintervallet för undernätet i formuläret x.x.x.x/y. Du hittar den här informationen i Azure-portalen i undernätsegenskaper.

> [!IMPORTANT]
> Även om obligatoriska regler för inkommande säkerhetsregler tillåter trafik från _valfri_ källa på portarna 9000, 9003, 1438, 1440 och 1452, skyddas dessa portar av en inbyggd brandvägg. Mer information finns i [Fastställa hanteringsslutpunktsadressen](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Om du använder transaktionsreplikering i en hanterad instans och om du använder någon instansdatabas som utgivare eller distributör öppnar du port 445 (TCP-utgående) i undernätets säkerhetsregler. Den här porten ger åtkomst till Azure-filresursen.

### <a name="user-defined-routes"></a>Användardefinierade vägar

|Namn|Adressprefix|Nästa hopp|
|----|--------------|-------|
|subnet_to_vnetlocal|MI-UNDERNÄT|Virtuellt nätverk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
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
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
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
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [avancerad datasäkerhet i SQL Database](sql-database-managed-instance.md).
- Lär dig hur du [konfigurerar ett nytt virtuellt Azure-nätverk](sql-database-managed-instance-create-vnet-subnet.md) eller ett [befintligt virtuellt Azure-nätverk](sql-database-managed-instance-configure-vnet-subnet.md) där du kan distribuera hanterade instanser.
- [Beräkna storleken på undernätet](sql-database-managed-instance-determine-size-vnet-subnet.md) där du vill distribuera hanterade instanser.
- Lär dig hur du skapar en hanterad instans:
  - Från [Azure-portalen](sql-database-managed-instance-get-started.md).
  - Genom att använda [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Med hjälp av [en Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Genom att använda [en Azure Resource Manager-mall (med JumpBox, med SSMS inkluderat)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
