---
title: Nätverks åtkomst kontroller
description: Översikt över nätverks åtkomst kontroller för Azure SQL Database och informations lager för att hantera åtkomst och konfigurera en databas med en eller flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16ba90aab52c00f77af590f854217cd989df53b3
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251914"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Kontroll av nätverks åtkomst för Azure SQL Database och informations lager

> [!NOTE]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL Database Hanterad instans**. Mer information om nätverks konfigurationen finns i [ansluta till en hanterad instans](sql-database-managed-instance-connect-app.md) .

När du skapar en ny Azure-SQL Server [från Azure Portal](sql-database-single-database-get-started.md), är resultatet en offentlig slut punkt i formatet *yourservername.Database.Windows.net*. Som design nekas all åtkomst till den offentliga slut punkten. Du kan sedan använda följande kontroller för nätverks åtkomst för att selektivt tillåta åtkomst till SQl-databasen via den offentliga slut punkten
- Tillåt Azure-tjänster: – när det är inställt på på, kan andra resurser inom Azure-gränser, till exempel en virtuell Azure-dator, komma åt SQL Database

- IP-brandväggs regler: – Använd den här funktionen för att uttryckligen tillåta anslutningar från en specifik IP-adress, till exempel från lokala datorer.

- Virtual Network brand Väggs regler: – Använd den här funktionen för att tillåta trafik från en speciell Virtual Network inom Azure-gränsen

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Tillåt Azure-tjänster 
När du skapar en ny Azure-SQL Server [från Azure Portal](sql-database-single-database-get-started.md)lämnas den här inställningen omarkerad.

 ![Skärm bild av ny server skapa][1]

Du kan också ändra den här inställningen via brand Väggs fönstret när Azure-SQL Server har skapats på följande sätt.
  
 ![Skärm bild av hantera Server brand vägg][2]

När det är inställt **på på** Azure SQL Server tillåta kommunikation från alla resurser inom Azure-gränser, som kan vara en del av din prenumeration.

I många fall är inställningen **för** att vara mer beviljad än vad kunderna vill ha. De kanske vill ställa in den här inställningen på **av** och ersätta den med mer restriktiva regler för IP-brandvägg eller Virtual Network brand Väggs regler. Detta påverkar följande funktioner som körs på virtuella datorer i Azure som inte ingår i ditt VNet och därmed ansluter till SQL Database via en Azure IP-adress.

### <a name="import-export-service"></a>Importera export tjänst
Import-/exportguiden fungerar inte **ge Azure-tjänster åtkomst till servern** . Du kan dock lösa problemet genom att [manuellt köra sqlpackage. exe från en virtuell Azure-dator eller utföra exporten](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) direkt i din kod med hjälp av DACFx-API: et.

### <a name="data-sync"></a>Datasynkronisering
Om du vill använda funktionen för datasynkronisering med alternativet **Tillåt att Azure-tjänster får åtkomst till servern** måste du skapa enskilda brand Väggs regel poster för att [lägga till IP-adresser](sql-database-server-level-firewall-rule.md) från **SQL-tjänstetaggen** för den region som är värd för **nav** databasen.
Lägg till dessa brand Väggs regler på server nivå på de logiska servrar som är värdar för både **nav** -och **medlems** databaser (som kan finnas i olika regioner)

Använd följande PowerShell-skript för att skapa IP-adresserna som motsvarar SQL Service-taggen för regionen Västra USA
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag returnerar det globala intervallet för SQL Service-taggen trots att du anger plats parametern. Se till att du filtrerar den till den region som är värd för NAV databasen som används av din Sync-grupp

Observera att resultatet av PowerShell-skriptet är i CIDR-notation (Classless Inter-Domain routing) och att det måste konverteras till ett format av start-och slut-IP-adress med hjälp av [Get-IPrangeStartEnd. ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) som detta
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Utför följande ytterligare steg för att konvertera alla IP-adresser från CIDR till start-och slut-IP-adress.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Nu kan du lägga till dessa som särskilda brand Väggs regler och sedan ange **Tillåt att Azure-tjänster får åtkomst till servern** .


## <a name="ip-firewall-rules"></a>Regler för IP-brandvägg
IP-baserad brand vägg är en funktion i Azure SQL Server som förhindrar all åtkomst till din databas server tills du uttryckligen [lägger till IP-adresser](sql-database-server-level-firewall-rule.md) för klient datorerna.


## <a name="virtual-network-firewall-rules"></a>Virtual Network brand Väggs regler

Förutom IP-regler kan du med Azure SQL Server-brandväggen definiera regler för *virtuella nätverk*.  
Läs mer i [Virtual Network tjänst slut punkter och regler för Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) eller titta på den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologi för Azure-nätverk  
Observera följande villkor för Azure-nätverk när du utforskar Virtual Network brand Väggs regler

**Virtuellt nätverk:** Du kan ha virtuella nätverk kopplade till din Azure-prenumeration 

**Undernät:** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella datorer i Azure (VM) som du har tilldelats till undernät. Ett undernät kan innehålla flera virtuella datorer eller andra Compute-noder. Compute-noder utanför det virtuella nätverket kan inte komma åt ditt virtuella nätverk om du inte konfigurerar din säkerhet att tillåta åtkomst.

**Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I den här artikeln är vi intresserade av typ namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database.

**Regel för virtuellt nätverk:** En regel för virtuella nätverk för din SQL Database-Server är ett undernät som listas i åtkomst kontrol listan (ACL) för din SQL Database-Server. För att det ska finnas i ACL: en för din SQL Database måste under nätet innehålla namnet **Microsoft. SQL** -typ. En regel för virtuella nätverk instruerar SQL Database servern att acceptera kommunikation från varje nod som finns på under nätet.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP vs. Virtual Network brand Väggs regler

Med Azure SQL Server-brandväggen kan du ange IP-adressintervall från vilka kommunikationen godkänns i SQL Database. Den här metoden är bra för stabila IP-adresser som ligger utanför Azures privata nätverk. Virtuella datorer (VM) i Azures privata nätverk konfigureras dock med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras när den virtuella datorn startas om och den IP-baserade brand Väggs regeln i tur är ogiltig. Det skulle vara Folly att ange en dynamisk IP-adress i en brand Väggs regel i en produktions miljö.

Du kan undvika den här begränsningen genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure Portal](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Den statiska IP-metoden kan dock bli svår att hantera, och den är kostsam när den görs i stor skala. 

Regler för virtuella nätverk är enklare att upprätta och hantera åtkomst från ett särskilt undernät som innehåller dina virtuella datorer.

> [!NOTE]
> Du kan inte ha SQL Database i ett undernät än. Om din Azure SQL Database-Server var en nod i ett undernät i det virtuella nätverket kan alla noder i det virtuella nätverket kommunicera med din SQL Database. I det här fallet kan de virtuella datorerna kommunicera med SQL Database utan att behöva några regler för virtuella nätverk eller IP-regler.

## <a name="next-steps"></a>Nästa steg

- En snabb start för att skapa en regel för IP-brandvägg på server nivå finns i [skapa en Azure SQL-databas](sql-database-single-database-get-started.md).

- En snabb start för att skapa en brand Väggs regel på server nivå finns i [Virtual Network tjänst slut punkter och regler för Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- För hjälp med att ansluta till en Azure SQL-databas från öppna käll-eller tredjepartsprogram, se [kod exempel för klient snabb start för att SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Mer information om ytterligare portar som du kan behöva öppna finns i avsnittet **SQL Database: utanför vs i** [portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- En översikt över Azure SQL Database-anslutningar finns i [arkitektur för Azure SQL-anslutning](sql-database-connectivity-architecture.md)

- En översikt över Azure SQL Database säkerhet finns i [skydda databasen](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
