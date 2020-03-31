---
title: Private Link
description: Översikt över funktionen Privat slutpunkt
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: ab9c5c5c1134d2e09a790a788a3b7e55f807dd9b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78945374"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse"></a>Privat länk för Azure SQL Database och Data Warehouse

Med Private Link kan du ansluta till olika PaaS-tjänster i Azure via en **privat slutpunkt**. En lista till PaaS-tjänster som stöder funktionen Privat länk finns på sidan [Privat länkdokumentation.](../private-link/index.yml) En privat slutpunkt är en privat IP-adress i ett visst [virtuella nätverk](../virtual-network/virtual-networks-overview.md) och undernät. 

> [!IMPORTANT]
> Den här artikeln gäller Azure SQL-server och både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. Den här artikeln gäller *inte* för en **hanterad instansdistribution** i Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Förebyggande av dataexfiltration

Dataexfiltration i Azure SQL Database är när en behörig användare, till exempel en databasadministratör kan extrahera data från ett system och flytta den till en annan plats eller ett annat system utanför organisationen. Användaren flyttar till exempel data till ett lagringskonto som ägs av en tredje part.

Överväg ett scenario med en användare som kör SQL Server Management Studio (SSMS) i en Azure VM-anslutning till en SQL-databas. Den här SQL-databasen finns i det västra amerikanska datacentret. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slutpunkter i SQL Database med hjälp av nätverksåtkomstkontroller.

1. Inaktivera all Azure-tjänsttrafik till SQL Database via den offentliga slutpunkten genom att ange Tillåt Azure Services att **STÄNGA .** Kontrollera att inga IP-adresser är tillåtna i brandväggsreglerna på server- och databasnivå. Mer information finns i [Azure SQL Database- och Data Warehouse-nätverksåtkomstkontroller](sql-database-networkaccess-overview.md).
1. Tillåt endast trafik till SQL-databasen med den privata IP-adressen för den virtuella datorn. Mer information finns i artiklarna om [serviceslutpunkts-](sql-database-vnet-service-endpoint-rule-overview.md) och [VNet-brandväggsregler](sql-database-firewall-configure.md).
1. På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning med hjälp av [NSG-grupper (Network Security Groups)](../virtual-network/manage-network-security-group.md) och Service Tags enligt följande
    - Ange en NSG-regel för att tillåta trafik för Service Tag = SQL. WestUs - tillåter endast anslutning till SQL Database i västra USA
    - Ange en NSG-regel (med **högre prioritet)** om du vill neka trafik för Service Tag = SQL – neka anslutningar till SQL Database i alla regioner

I slutet av den här installationen kan Azure VM endast ansluta till SQL-databaser i regionen västra USA. Anslutningen är dock inte begränsad till en enda SQL-databas. Den virtuella datorn kan fortfarande ansluta till alla SQL-databaser i regionen västra USA, inklusive databaser som inte ingår i prenumerationen. Även om vi har minskat omfattningen av dataexfiltration i ovanstående scenario till en viss region, har vi inte eliminerat det helt och hållet.

Med Private Link kan kunder nu ställa in nätverksåtkomstkontroller som NSG:er för att begränsa åtkomsten till den privata slutpunkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slutpunkter. En skadlig insider kan bara komma åt den mappade PaaS-resursen (till exempel en SQL-databas) och ingen annan resurs. 

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När kunder ansluter till den offentliga slutpunkten från lokala datorer måste deras IP-adress läggas till i den IP-baserade brandväggen med hjälp av en [brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md). Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utvecklings- eller testarbetsbelastningar, är det svårt att hantera i en produktionsmiljö.

Med Private Link kan kunder aktivera åtkomst mellan lokala och lokala resurser till den privata slutpunkten med [ExpressRoute,](../expressroute/expressroute-introduction.md)privat peering eller VPN-tunnel. Kunder kan sedan inaktivera all åtkomst via den offentliga slutpunkten och inte använda den IP-baserade brandväggen för att tillåta ip-adresser.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Konfigurera privat länk för Azure SQL Database 

### <a name="creation-process"></a>Skapandeprocess
Privata slutpunkter kan skapas med hjälp av portalen, PowerShell eller Azure CLI:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [Powershell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Godkännandeprocess
När nätverksadministratören har skapat PE (Private Endpoint) kan SQL-administratören hantera PEC (Private Endpoint Connection) till SQL Database.

1. Navigera till SQL-serverresursen i Azure-portalen enligt stegen som visas i skärmbilden nedan

    - (1) Välj de privata slutpunktsanslutningarna i den vänstra rutan
    - (2) Visar en lista över alla privata slutpunktsanslutningar (PECs)
    - (3) Motsvarande privat slutpunkt ![(PE) skapade Skärmdump av alla PECs][3]

1. Välj en enskild PEC i listan genom att välja den.
![Skärmdump vald PEC][6]

1. SQL-administratören kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort textsvar.
![Skärmbild av PEC-godkännande][4]

1. Efter godkännande eller avslag kommer listan att återspegla rätt tillstånd tillsammans med svarstexten.
![Skärmbild av alla PEC:er efter godkännande][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Användningsfall med privat länk för Azure SQL Database 

Klienter kan ansluta till den privata slutpunkten från samma virtuella nätverk, peered VNet i samma region eller via VNet-till-VNet-anslutning mellan regioner. Dessutom kan klienter ansluta från lokala med ExpressRoute, privat peering eller VPN-tunnel. Nedan finns ett förenklat diagram som visar de vanliga användningsfallen.

 ![Diagram över anslutningsalternativ][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testa anslutningen till SQL Database från en virtuell Azure-dator i samma virtuella nätverk (VNet)

Anta att du har skapat en virtuell Azure-dator (VM) som kör Windows Server 2016 i det här scenariot. 

1. [Starta en RDP-session (Remote Desktop) och anslut till den virtuella datorn](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Du kan sedan göra några grundläggande anslutningskontroller för att säkerställa att den virtuella datorn ansluter till SQL Database via den privata slutpunkten med hjälp av följande verktyg:
    1. Telnet
    1. Psping (psping)
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Kontrollera anslutningen med Telnet

[Telnet Client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) är en Windows-funktion som kan användas för att testa anslutningen. Beroende på vilken version av Windows OS är det kan du behöva aktivera den här funktionen explicit. 

Öppna ett kommandotolksfönster när du har installerat Telnet. Kör kommandot Telnet och ange IP-adressen och den privata slutpunkten för SQL-databasen.

```
>telnet 10.1.1.5 1433
```

När Telnet ansluter visas en tom skärm i kommandofönstret som bilden nedan:

 ![Diagram över telnet][2]

### <a name="check-connectivity-using-psping"></a>Kontrollera anslutningen med Psping

[Psping](/sysinternals/downloads/psping) kan användas på följande sätt för att kontrollera att den privata slutpunktsanslutningen (PEC) lyssnar efter anslutningar på port 1433.

Kör psping enligt följande genom att tillhandahålla FQDN för SQL Database-servern och port 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

Utdata visar att Psping kunde pinga den privata IP-adressen som är associerad med PEC.

### <a name="check-connectivity-using-nmap"></a>Kontrollera anslutningen med Nmap

Nmap (Network Mapper) är ett kostnadsfritt verktyg med öppen källkod som används för nätverksidentifiering och säkerhetsgranskning. För mer information och nedladdningslänken, besök https://nmap.org. Du kan använda det här verktyget för att säkerställa att den privata slutpunkten lyssnar efter anslutningar på port 1433.

Kör Nmap på följande sätt genom att ange adressintervallet för undernätet som är värd för den privata slutpunkten.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Resultatet visar att en IP-adress är uppe. som motsvarar IP-adressen för den privata slutpunkten.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Kontrollera anslutningen med SQL Server Management Studio (SSMS)
> [!NOTE]
> Använd **serverns fullständigt kvalificerade domännamn (FQDN)** i anslutningssträngar för dina klienter. Alla inloggningsförsök som görs direkt till IP-adressen ska misslyckas. Det här beteendet är avsiktligt, eftersom privata slutpunkter dirigerar trafik till SQL Gateway i regionen och FQDN måste anges för att inloggningar ska lyckas.

Följ stegen här för att använda [SSMS för att ansluta till SQL Database](sql-database-connect-query-ssms.md). När du har anslutit till SQL-databasen med SSMS kontrollerar du att du ansluter från den privata IP-adressen för den virtuella Azure-datorn genom att köra följande fråga:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>Begränsningar 
Anslutningar till privat slutpunkt stöder endast **Proxy** som [anslutningsprincip](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peered virtual network (VNet) 

Konfigurera [VNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) för att upprätta anslutning till SQL-databasen från en Virtuell Azure-dator i ett peer-virtuellt virtuellt nätverk.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö

Konfigurera [VNet-till-VNet VPN-gateway-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) för att upprätta anslutning till en SQL-databas från en Azure-virtuell dator i en annan region eller prenumeration.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN

Om du vill upprätta anslutning från en lokal miljö till SQL-databasen väljer och implementerar du något av alternativen:
- [Anslutning mellan punkt och plats](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Plats-till-plats-anslutning via VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Ansluta från ett Azure SQL Data Warehouse till Azure Storage med Polybase

PolyBase används ofta för att läsa in data i Azure SQL Data Warehouse från Azure Storage-konton. Om Azure Storage-kontot som du läser in data från begränsar åtkomsten endast till en uppsättning virtuella nätnät via privata slutpunkter, tjänstslutpunkter eller IP-baserade brandväggar bryts anslutningen från PolyBase till kontot. Följ stegen som anges [här](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)för att aktivera både PolyBase-import- och exportscenarier med Azure SQL Data Warehouse som ansluter till Azure Storage som är skyddat till ett virtuella nätverk. 



## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database-säkerhet finns i [Skydda databasen](sql-database-security-overview.md)
- En översikt över Azure SQL Database-anslutningen finns i [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
