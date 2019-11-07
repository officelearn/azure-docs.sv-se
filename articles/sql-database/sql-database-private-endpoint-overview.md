---
title: Privat länk för Azure SQL Database och informations lager
description: Översikt över funktionen för privat slut punkt
author: rohitnayakmsft
ms.author: rohitna
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: a056c4beeeb05886d4b4ed492a05d1963372b366
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687606"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Privat länk för Azure SQL Database och informations lager (för hands version)

Med privat länk kan du ansluta till olika PaaS-tjänster i Azure via en **privat slut punkt**. Om du vill ha en lista över PaaS Services som stöder funktionen för privat länk går du till [dokumentations sidan för privat länk](../private-link/index.yml) . En privat slut punkt är en privat IP-adress inom ett särskilt [VNet](../virtual-network/virtual-networks-overview.md) och undernät. 

> [!IMPORTANT]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. Den här artikeln gäller *inte* för distribution av **hanterade instanser** i Azure SQL Database.

## <a name="data-exfiltration-prevention"></a>Data exfiltrering skydd

Data exfiltrering i Azure SQL Database är när en behörig användare, till exempel en databas administratör, kan extrahera data från ett system och flytta den till en annan plats eller ett system utanför organisationen. Användaren kan till exempel flytta data till ett lagrings konto som ägs av en tredje part.

Överväg ett scenario med en användare som kör SQL Server Management Studio (SSMS) i en virtuell Azure-dator som ansluter till en SQL Database. Den här SQL Database är i data centret västra USA. Exemplet nedan visar hur du begränsar åtkomsten med offentliga slut punkter på SQL Database med hjälp av nätverks åtkomst kontroller.

1. Inaktivera all Azure Service-trafik för att SQL Database via den offentliga slut punkten genom att ställa in Tillåt att Azure-tjänster **stängs**av. Se till att inga IP-adresser är tillåtna i brand Väggs reglerna för Server och databas nivå. Mer information finns i [Azure SQL Database-och data lager nätverks åtkomst kontroller](sql-database-networkaccess-overview.md).
1. Tillåt endast trafik till SQL Database med den virtuella datorns privata IP-adress. Mer information finns i artikeln om [service Endpoint](sql-database-vnet-service-endpoint-rule-overview.md) och [VNet brand Väggs regler](sql-database-firewall-configure.md).
1. På den virtuella Azure-datorn begränsar du omfattningen av utgående anslutning genom att använda [nätverks säkerhets grupper (NSG: er)](../virtual-network/manage-network-security-group.md) och service märken enligt följande
    - Ange en NSG-regel för att tillåta trafik för service tag = SQL. Västra USA – endast tillåta anslutning till SQL Database i västra USA
    - Ange en NSG-regel (med en **högre prioritet**) om du vill neka trafik för service tag = SQL-neka anslutningar till SQL Database i alla regioner

I slutet av den här installationen kan den virtuella Azure-datorn bara ansluta till SQL-databaser i regionen USA, västra. Anslutningen är dock inte begränsad till en enda SQL Database. Den virtuella datorn kan fortfarande ansluta till alla SQL-databaser i regionen Västra USA, inklusive de databaser som inte ingår i prenumerationen. Vi har minskat omfattningen av data exfiltrering i scenariot ovan till en speciell region, men vi har inte eliminerat det helt.

Med privat länk kan kunder nu konfigurera nätverks åtkomst kontroller som NSG: er för att begränsa åtkomsten till den privata slut punkten. Enskilda Azure PaaS-resurser mappas sedan till specifika privata slut punkter. En skadlig Insider kan bara komma åt den mappade PaaS-resursen (till exempel en SQL Database) och ingen annan resurs. 

## <a name="on-premises-connectivity-over-private-peering"></a>Lokal anslutning via privat peering

När kunderna ansluter till den offentliga slut punkten från lokala datorer måste deras IP-adress läggas till i den IP-baserade brand väggen med hjälp av en [brand Väggs regel på server nivå](sql-database-server-level-firewall-rule.md). Även om den här modellen fungerar bra för att tillåta åtkomst till enskilda datorer för utveckling eller testning av arbets belastningar, är det svårt att hantera i en produktions miljö.

Med privat länk kan kunder aktivera åtkomst till lokala slut punkter via [ExpressRoute](../expressroute/expressroute-introduction.md), privat peering eller VPN-tunnlar. Kunder kan sedan inaktivera all åtkomst via den offentliga slut punkten och inte använda den IP-baserade brand väggen för att tillåta alla IP-adresser.

Med privat länk kan kunder aktivera åtkomst över hela nätverket till den privata slut punkten med hjälp av en privat peering-eller VPN-tunnel från Express Route (ER). De kan sedan inaktivera all åtkomst via offentlig slut punkt och inte använda den IP-baserade brand väggen.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Så här konfigurerar du en privat länk för Azure SQL Database 

### <a name="creation-process"></a>Skapande process
Privata slut punkter kan skapas med hjälp av portalen, PowerShell eller Azure CLI:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Godkännande process
När nätverks administratören skapar den privata slut punkten (PE) kan SQL-administratören hantera den privata slut punkts anslutningen (PEC) för att SQL Database.

1. Gå till SQL Server-resursen i Azure Portal.

    - (1) Välj de privata slut punkts anslutningarna i det vänstra fönstret
    - (2) visar en lista över alla anslutningar för privata slut punkter (PECs)
    - (3) motsvarande privata slut punkt (PE) skapade ![skärm bild av alla PECs][3]

1. Välj en enskild PEC från listan genom att markera den.
![skärm bild vald PEC][6]

1. SQL-administratören kan välja att godkänna eller avvisa en PEC och eventuellt lägga till ett kort text svar.
![skärm bild av PEC-godkännande][4]

1. Efter godkännande eller avvisande visar listan lämpligt tillstånd tillsammans med svars texten.
![skärm bild av alla PECs efter godkännande][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Använd fall av privat länk för Azure SQL Database 

Klienter kan ansluta till den privata slut punkten från samma VNet, peer-kopplat VNet i samma region eller via VNet-till-VNet-anslutning mellan regioner. Dessutom kan klienter ansluta lokalt med ExpressRoute, privat peering eller VPN-tunnlar. Nedan visas ett förenklat diagram som visar vanliga användnings fall.

 ![Diagram över anslutnings alternativ][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testa anslutningen till SQL Database från en virtuell Azure-dator i samma Virtual Network (VNet)

I det här scenariot förutsätter vi att du har skapat en virtuell Azure-dator (VM) som kör Windows Server 2016. 

1. [Starta en fjärr skrivbords-session (RDP) och Anslut till den virtuella datorn](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Du kan sedan utföra vissa grundläggande anslutnings kontroller för att säkerställa att den virtuella datorn ansluter till SQL Database via den privata slut punkten med hjälp av följande verktyg:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Kontrol lera anslutningen med Telnet

[Telnet-klienten](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) är en Windows-funktion som kan användas för att testa anslutningen. Beroende på vilken version av Windows OS du har kan du behöva aktivera den här funktionen explicit. 

Öppna ett kommando tolks fönster när du har installerat telnet. Kör Telnet-kommandot och ange IP-adressen och den privata slut punkten för SQL Database.

```
>telnet 10.1.1.5 1433
```

När Telnet ansluter ser du en tom skärm i kommando fönstret som bilden nedan:

 ![Diagram över Telnet][2]

### <a name="check-connectivity-using-psping"></a>Kontrol lera anslutningen med Psping

[Psping](/sysinternals/downloads/psping) kan användas på följande sätt för att kontrol lera att den privata slut punkts anslutningen (PEC) lyssnar efter anslutningar på port 1433.

Kör psping enligt följande genom att ange det fullständiga domän namnet för din SQL Database-Server och port 1433:

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

Utdata visar att Psping kan pinga den privata IP-adress som är associerad med PEC.

### <a name="check-connectivity-using-nmap"></a>Kontrol lera anslutningen med nmap

Nmap (Network Mapper) är ett kostnads fritt verktyg med öppen källkod som används för nätverks identifiering och säkerhets granskning. Mer information och nedladdnings länken finns på https://nmap.org. Du kan använda det här verktyget för att se till att den privata slut punkten lyssnar efter anslutningar på port 1433.

Kör nmap enligt följande genom att tillhandahålla adress intervallet för under nätet som är värd för den privata slut punkten.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Resultatet visar att en IP-adress är upp. som motsvarar IP-adressen för den privata slut punkten.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Kontrol lera anslutningen med SQL Server Management Studio (SSMS)

Det sista steget är att använda [SSMS för att ansluta till SQL Database](sql-database-connect-query-ssms.md). När du har anslutit till SQL Database med SSMS kontrollerar du att du ansluter från den privata IP-adressen för den virtuella Azure-datorn genom att köra följande fråga:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> I för hands versionen stöder anslutningar till privata slut punkter **proxy** som [anslutnings princip](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ansluta från en virtuell Azure-dator i peer-Virtual Network (VNet) 

Konfigurera [VNet-peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) för att upprätta anslutning till SQL Database från en virtuell Azure-dator i ett peer-kopplat VNet.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ansluta från en virtuell Azure-dator i VNet-till-VNet-miljö

Konfigurera [VPN gateway-anslutning mellan virtuella](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) nätverk för att upprätta anslutning till en SQL Database från en virtuell Azure-dator i en annan region eller prenumeration.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ansluta från en lokal miljö via VPN

Om du vill upprätta en anslutning från en lokal miljö till SQL Database väljer du och implementerar något av alternativen:
- [Punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Plats-till-plats-VPN-anslutning](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Ansluta från en Azure SQL Data Warehouse till Azure Storage med PolyBase

PolyBase används ofta för att läsa in data i Azure SQL Data Warehouse från Azure Storage-konton. Om Azure Storage konto som du läser in data från begränsar åtkomsten till en uppsättning VNet-undernät via privata slut punkter, tjänst slut punkter eller IP-baserade brand väggar, kommer anslutningen från PolyBase till kontot att avbrytas. Följ stegen nedan om du vill aktivera både PolyBase import-och export scenarier med Azure SQL Data Warehouse ansluta till Azure Storage som är skyddade till ett [VNet.](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 



## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database säkerhet finns i [skydda databasen](sql-database-security-overview.md)
- En översikt över Azure SQL Database-anslutningar finns i [arkitektur för Azure SQL-anslutning](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
