---
title: Kontroller för nätverksåtkomst
description: Översikt över nätverksåtkomstkontroller för Azure SQL Database och Data Warehouse för att hantera åtkomst och konfigurera en enda eller poolad databas.
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
ms.date: 03/09/2020
ms.openlocfilehash: 822fab5c00501d415c3c184587141e869523e417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945382"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database- och Data Warehouse-nätverksåtkomstkontroller

> [!NOTE]
> Den här artikeln gäller Azure SQL-server och både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* **för Azure SQL Database Managed Instance**. Mer information om nätverkskonfigurationen finns i [ansluta till en hanterad instans](sql-database-managed-instance-connect-app.md) .

När du skapar en ny Azure SQL Server från [Azure-portalen](sql-database-single-database-get-started.md)är resultatet en offentlig slutpunkt i *formatet, yourservername.database.windows.net*.

Du kan använda följande nätverksåtkomstkontroller för att selektivt tillåta åtkomst till SQl-databasen via den offentliga slutpunkten:
- Tillåt Azure-tjänster: När de är inställda på ON kan andra resurser inom Azure-gränsen, till exempel en virtuell Azure-dator, komma åt SQL Database

- IP-brandväggsregler: Använd den här funktionen för att uttryckligen tillåta anslutningar från en viss IP-adress, till exempel från lokala datorer

Du kan också tillåta privat åtkomst till SQL-databasen från [virtuella nätverk](../virtual-network/virtual-networks-overview.md) via:
- Regler för brandvägg för virtuellt nätverk: Använd den här funktionen för att tillåta trafik från ett visst virtuellt nätverk inom Azure-gränsen

- Privat länk: Använd den här funktionen för att skapa en privat slutpunkt för Azure SQL Server i ett visst virtuellt nätverk



Se videon nedan för en förklaring på hög nivå av dessa åtkomstkontroller och vad de gör:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Tillåt Azure-tjänster 
När du skapar en ny Azure SQL Server [från Azure-portalen](sql-database-single-database-get-started.md)lämnas den här inställningen avmarkerad.



Du kan också ändra den här inställningen via brandväggsfönstret när Azure SQL Server har skapats på följande sätt.
  
 ![Skärmbild av hantera serverbrandväggen][2]

När den är inställd **på ON** Azure SQL Server tillåts kommunikation från alla resurser inom Azure-gränsen, som kanske eller kanske inte är en del av din prenumeration.

I många fall är **ON-inställningen** mer tillåtande än vad de flesta kunder vill ha. De kanske vill ställa in den här inställningen till **AV** och ersätta den med mer restriktiva IP-brandväggsregler eller brandväggsregler för virtuellt nätverk. Detta påverkar följande funktioner som körs på virtuella datorer i Azure som inte ingår i ditt virtuella nätverk och därmed ansluter till Sql Database via en Azure IP-adress.

### <a name="import-export-service"></a>Importera exporttjänst
Importexporttjänsten fungerar inte **Tillåt Azure-tjänster att komma åt serveruppsättningen** till OFF. Du kan dock komma runt problemet [genom att manuellt köra sqlpackage.exe från en Virtuell Azure eller utföra exporten](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) direkt i koden med hjälp av DACFx API.

### <a name="data-sync"></a>Datasynkronisering
Om du vill använda datasynkroniseringsfunktionen med **Tillåt Azure-tjänster för att komma åt serveruppsättningen** till AV måste du skapa enskilda brandväggsregelposter för att lägga till [IP-adresser](sql-database-server-level-firewall-rule.md) från **Sql-tjänsttaggen** för regionen som är värd för **Hub-databasen.**
Lägg till dessa brandväggsregler på servernivå till de logiska servrarna som är värdar för både **Hub-** och **Member-databaser** (som kan finnas i olika regioner)

Använd följande PowerShell-skript för att generera IP-adresser som motsvarar Sql-tjänsttaggen för region Västra USA
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
> Get-AzNetworkServiceTag returnerar det globala intervallet för Sql Service Tag trots att parametern Plats anges. Var noga med att filtrera den till den region som är värd för Hub-databasen som används av din synkroniseringsgrupp

Observera att utdata för PowerShell-skriptet är i klasslös CIDR-notation (Inter-Domain Routing) och att detta måste konverteras till ett format med Start- och slut-IP-adress med [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) så här
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Gör följande ytterligare steg för att konvertera alla IP-adresser från CIDR till Start och End IP-adressformat.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
Du kan nu lägga till dessa som olika brandväggsregler och sedan ange **Tillåt Azure-tjänster för att komma åt servern** till OFF.


## <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen
IP-baserad brandvägg är en funktion i Azure SQL Server som förhindrar all åtkomst till databasservern tills du uttryckligen [lägger till IP-adresser](sql-database-server-level-firewall-rule.md) för klientdatorerna.


## <a name="virtual-network-firewall-rules"></a>Regler för brandvägg för virtuellt nätverk

Förutom IP-regler kan du med Azure SQL Server-brandväggen definiera *virtuella nätverksregler*.  
Mer information finns i [slutpunkter och regler för tjänsten Virtuellt nätverk för Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) eller titta på den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologi för Azure Networking  
Tänk på följande Villkor för Azure Networking när du utforskar brandväggsregler för virtuellt nätverk

**Virtuellt nätverk:** Du kan ha virtuella nätverk som är associerade med din Azure-prenumeration 

**Undernät:** Ett virtuellt nätverk innehåller **undernät**. Alla virtuella Azure-datorer (VIRTUELLA datorer) som du har tilldelats undernät. Ett undernät kan innehålla flera virtuella datorer eller andra beräkningsnoder. Beräkningsnoder som finns utanför det virtuella nätverket kan inte komma åt det virtuella nätverket om du inte konfigurerar säkerheten så att åtkomst tillåts.

**Slutpunkt för tjänsten Virtuellt nätverk:** Slutpunkten [för tjänsten Virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller ett eller flera formella Azure-tjänsttypsnamn. I den här artikeln är vi intresserade av typnamnet för **Microsoft.Sql**, som refererar till Azure-tjänsten SOM heter SQL Database.

**Regel för virtuellt nätverk:** En virtuell nätverksregel för SQL Database-servern är ett undernät som visas i åtkomstkontrollistan (ACL) på SQL Database-servern. Om du vill vara i åtkomstkontrollistan för SQL-databasen måste undernätet innehålla namnet på **typen Microsoft.Sql.** En virtuell nätverksregel talar om för SQL Database-servern att acceptera kommunikation från alla noder som finns i undernätet.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP vs. Regler för brandvägg för virtuella nätverk

Med Azure SQL Server-brandväggen kan du ange IP-adressintervall från vilka kommunikation accepteras i SQL Database. Den här metoden är bra för stabila IP-adresser som ligger utanför Azure privata nätverk. Virtuella datorer (VMs) i Det privata Azure-nätverket konfigureras dock med *dynamiska* IP-adresser. Dynamiska IP-adresser kan ändras när den virtuella datorn startas om och i sin tur ogiltigförklara den IP-baserade brandväggsregeln. Det skulle vara dårskap att ange en dynamisk IP-adress i en brandväggsregel, i en produktionsmiljö.

Du kan kringgå den här begränsningen genom att skaffa en *statisk* IP-adress för den virtuella datorn. Mer information finns i [Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure-portalen](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Den statiska IP-metoden kan dock bli svår att hantera, och den är kostsam när den görs i stor skala. 

Virtuella nätverksregler är enklare att upprätta och hantera åtkomst från ett visst undernät som innehåller dina virtuella datorer.

> [!NOTE]
> Du kan ännu inte ha SQL Database i ett undernät. Om din Azure SQL Database-server var en nod i ett undernät i det virtuella nätverket kan alla noder i det virtuella nätverket kommunicera med din SQL-databas. I det här fallet kan dina virtuella datorer kommunicera med SQL Database utan att behöva några virtuella nätverksregler eller IP-regler.

## <a name="private-link"></a>Private Link 
Med Private Link kan du ansluta till Azure SQL Server via en **privat slutpunkt**. En privat slutpunkt är en privat IP-adress i ett visst [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) och undernät.

## <a name="next-steps"></a>Nästa steg

- En snabbstart för att skapa en IP-brandväggsregel på servernivå finns i [Skapa en Azure SQL-databas](sql-database-single-database-get-started.md).

- En snabbstart för att skapa en Vnet-brandväggsregel på servernivå finns i [slutpunkter och regler för tjänsten Virtuellt nätverk för Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

- Mer information om hur du ansluter till en Azure SQL-databas från program med öppen källkod eller från tredje part finns i [snabbstartskodexempel för klient till SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Information om ytterligare portar som du kan behöva öppna finns i **avsnittet SQL Database: Outside vs inside** of Ports beyond [1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)

- En översikt över Azure SQL Database Connectivity finns i [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)

- En översikt över Azure SQL Database-säkerhet finns i [Skydda databasen](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

