---
title: Regler för IP-brandvägg för Azure SQL Database och informations lager | Microsoft Docs
description: Lär dig hur du konfigurerar en SQL-databas eller SQL Data Warehouse brand vägg med regler för IP-brandvägg på server nivå för att hantera åtkomst och konfigurera en databas med en eller flera databaser med regler för IP-brandvägg på databas nivå.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568187"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Regler för Azure SQL Database och SQL Data Warehouse IP-brandvägg

Microsoft Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tillhandahålla en Relations databas tjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

> [!NOTE]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.
> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL Database Hanterad instans**. I följande artikel om hur [du ansluter till en hanterad instans](sql-database-managed-instance-connect-app.md) finns mer information om nätverks konfigurationen som behövs.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regler för virtuella nätverk som alternativ till IP-regler

Förutom IP-regler hanterar brand väggen också *virtuella nätverks regler*. Virtuella nätverks regler baseras på Virtual Network tjänst slut punkter. Regler för virtuella nätverk kan vara bättre för IP-regler i vissa fall. Läs mer i [Virtual Network tjänst slut punkter och regler för Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Översikt

Till en början blockeras all åtkomst till din Azure SQL-Server av SQL Database brand väggen. För att få åtkomst till en databas server måste du ange en eller flera IP-brandväggar på server nivå som ger åtkomst till din Azure SQL-Server. Använd IP-brandväggens regler för att ange vilka IP-adressintervall från Internet som tillåts och om Azure-program kan försöka ansluta till din Azure SQL-Server.

Om du bara vill bevilja åtkomst till en av databaserna på din Azure SQL-server måste du skapa en regel på databasnivå för databasen. Ange ett IP-adressintervall för databasens IP-brandväggsregel som ligger utanför det IP-adressintervall som anges i IP-brandväggens regel på server nivå och kontrol lera att klientens IP-adress ligger inom det intervall som anges i regeln på databas nivå.

> [!IMPORTANT]
> SQL Data Warehouse stöder endast IP-brandväggs regler på server nivå och stöder inte regler för IP-brandvägg på databas nivå.

Anslutningsförsök från Internet och Azure måste först passera brandväggen innan de kan nå din Azure SQL-server eller SQL Database, som du ser i följande diagram:

   ![Diagram över brandväggskonfigurationen.][1]

- **IP-brandväggs regler på server nivå:**

  Dessa regler gör det möjligt för klienter att komma åt hela Azure SQL Server, det vill säga alla databaser inom samma SQL Database-Server. Dessa regler lagras i **huvuddatabasen**. Regler för IP-brandvägg på server nivå kan konfigureras med hjälp av portalen eller med hjälp av Transact-SQL-uttryck. Om du vill skapa IP-brandvägg på server nivå med hjälp av Azure Portal eller PowerShell måste du vara prenumerations ägare eller en prenumerations deltagare. Om du vill skapa en regel för IP-brandvägg på server nivå med hjälp av Transact-SQL måste du ansluta till SQL Database-instansen som huvud inloggning på server nivå eller Azure Active Directory administratören (vilket innebär att en IP-brandväggsregel på server nivå först måste ha skapats av en användare med behörigheter på Azure-nivå).

- **IP-brandväggs regler på databas nivå:**

  Dessa regler gör att klienter kan komma åt vissa (säkra) databaser inom samma SQL Database-Server. Du kan skapa de här reglerna för varje databas (inklusive **huvud** databasen) och de lagras i de enskilda databaserna. Regler för IP-brandvägg för huvud-och användar databaser på databas nivå kan bara skapas och hanteras med hjälp av Transact-SQL-uttryck och endast efter att du har konfigurerat den första brand väggen på server nivå. Om du anger ett IP-adressintervall i IP-brandväggens regel på databas nivå som ligger utanför det intervall som anges i IP-brandväggsregel på server nivå, kan bara de klienter som har IP-adresser i intervallet på databas nivå komma åt databasen. Du kan ha högst 128 IP-brandvägg på databas nivå för en databas. Mer information om hur du konfigurerar regler för IP-brandvägg på databas nivå finns i exemplet senare i den här artikeln och se [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Rekommendation

Microsoft rekommenderar att du använder IP brand Väggs regler på databas nivå när det är möjligt för att förbättra säkerheten och göra databasen mer portabel. Använd brand Väggs regler på server nivå för administratörer och om du har många databaser med samma åtkomst krav och du inte vill lägga tid på att konfigurera varje databas individuellt.

> [!IMPORTANT]
> Windows Azure SQL Database stöder högst 128 IP brand Väggs regler.
> [!NOTE]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Ansluta från Internet

När en dator försöker ansluta till din databas server från Internet kontrollerar brand väggen först den ursprungliga IP-adressen för begäran mot reglerna på databas nivåns IP-brandvägg för den databas som anslutningen begär:

- Om IP-adressen för begäran ligger inom ett intervall som anges i IP-brandväggens regler på databas nivå, beviljas anslutningen till den SQL Database som innehåller regeln.
- Om IP-adressen för begäran inte ligger inom något av intervallen som anges i IP-brandväggens regel på databas nivå, kontrol leras IP-brandväggens regler på server nivå. Om IP-adressen för begäran är inom ett intervall som anges i IP-brandväggens regler på server nivå, beviljas anslutningen. Regler för IP-brandvägg på server nivå gäller för alla SQL-databaser på Azure SQL-servern.  
- Om IP-adressen för begäran inte ligger inom de intervall som anges i någon av reglerna på databas-eller Server nivåns IP-brandvägg, Miss lyckas anslutningsbegäran.

> [!NOTE]
> För att kunna komma åt Azure SQL Database från din lokala dator måste du kontrollera att brandväggen i nätverket och på den lokala datorn tillåter utgående kommunikation på TCP-port 1433.

### <a name="connecting-from-azure"></a>Ansluta från Azure

Om du vill tillåta att program från Azure ansluter till din Azure SQL-server måste Azure-anslutningar vara aktiverade. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. En brand Väggs inställning med start-och slut adress lika med 0.0.0.0 anger att Azure-anslutningar är tillåtna. Om anslutningsförsöket inte tillåts kommer begäran inte att nå Azure SQL Database-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.

## <a name="creating-and-managing-ip-firewall-rules"></a>Skapa och hantera IP-brandväggens regler

Den första brand Väggs inställningen på server nivå kan skapas med hjälp av [Azure Portal](https://portal.azure.com/) eller program mässigt med hjälp av [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)eller [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Efterföljande regler för IP-brandvägg på server nivå kan skapas och hanteras med hjälp av dessa metoder och via Transact-SQL.

> [!IMPORTANT]
> Det går bara att skapa och hantera regler för IP-brandvägg på databas nivå med hjälp av Transact-SQL.

För att förbättra prestanda cachelagras tillfälligt IP-brandväggens regler på server nivå på databas nivå. Information om hur du uppdaterar cacheminnet finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Du kan använda [SQL Database granskning](sql-database-auditing.md) för att granska ändringar på server nivå och databas nivå.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Hantera IP-brandväggens regler på server nivå med hjälp av Azure Portal

Om du vill ange en IP-brandväggsregel på server nivå i Azure Portal kan du antingen gå till översikts sidan för din Azure SQL-databas eller översikts sidan för din SQL Database-Server.

> [!TIP]
> En själv studie kurs finns i [skapa en databas med hjälp av Azure Portal](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Från sidan databas översikt

1. Om du vill ange en regel för IP-brandvägg på server nivå från sidan databas översikt, klickar du på **Ange server brand vägg** i verktygsfältet, som du ser i följande bild: Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

      ![regel för Server-IP-brandvägg](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Klicka på **Lägg till klient-IP** i verktygsfältet för att lägga till IP-adressen för den dator som du använder för närvarande och klicka sedan på **Spara**. En regel för IP-brandvägg på server nivå skapas för din aktuella IP-adress.

      ![Ange regel för IP-brandvägg på server nivå](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Från sidan Server översikt

Sidan översikt för servern öppnas och visar det fullständigt kvalificerade Server namnet (till exempel **mynewserver20170403.Database.Windows.net**) och innehåller alternativ för ytterligare konfiguration.

1. Om du vill ange en regel på server nivå från sidan Server Översikt klickar du på **brand vägg** i den vänstra menyn under inställningar:

2. Klicka på **Lägg till klient-IP** i verktygsfältet för att lägga till IP-adressen för den dator som du använder för närvarande och klicka sedan på **Spara**. En regel för IP-brandvägg på server nivå skapas för din aktuella IP-adress.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Hantera IP-brandväggens regler med hjälp av Transact-SQL

| Katalogvy eller lagrad procedur | Nivå | Beskrivning |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visar aktuella regler för IP-brandvägg på server nivå |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Skapar eller uppdaterar IP-brandväggens regler på server nivå |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Tar bort regler för IP-brandvägg på server nivå |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databas |Visar de aktuella IP-brandväggens regler på databas nivå |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databas |Skapar eller uppdaterar IP-brandväggens regler på databas nivå |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databaser |Tar bort regler för IP-brandvägg på databas nivå |

Följande exempel granskar de befintliga reglerna, aktiverar ett intervall med IP-adresser på servern contoso och tar bort en IP-brandvägg regel:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Lägg sedan till en regel för IP-brandvägg på server nivå.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Om du vill ta bort en regel för IP-brandvägg på server nivå kör du den lagrade proceduren sp_delete_firewall_rule. I följande exempel tar bort regeln med namnet ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Hantera IP-brandväggens regler på server nivå med hjälp av Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

| Cmdlet: | Nivå | Beskrivning |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Returnerar de aktuella brandväggsreglerna på servernivå |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Skapar en ny brandväggsregel på servernivå |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Tar bort brandväggsregler på servernivå |

I följande exempel anges en regel för IP-brandvägg på server nivå med hjälp av PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> PowerShell-exempel i kontexten för en snabb start finns i [skapa DB-PowerShell](sql-database-powershell-samples.md) och [skapa en enskild databas och konfigurera en SQL Database regel för IP-brandvägg på server nivå med hjälp av PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Hantera IP-brandväggens regler på server nivå med Azure CLI

| Cmdlet: | Nivå | Beskrivning |
| --- | --- | --- |
|[AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Skapar en server-IP-brandväggsregel|
|[AZ SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Visar en lista över IP-brandväggens regler på en server|
|[AZ för SQL Server-brandvägg – regel show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Visar information om en IP-brandväggsregel|
|[AZ SQL Server-brandvägg-regel uppdatering](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Uppdaterar en regel för IP-brandvägg|
|[AZ SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Tar bort en regel för IP-brandvägg|

I följande exempel anges en regel för IP-brandvägg på server nivå med hjälp av Azure CLI:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Ett Azure CLI-exempel i kontexten för en snabb start finns i [skapa DB-Azure CLI](sql-database-cli-samples.md) och [skapa en enskild databas och konfigurera en SQL Database IP-brandväggsregel med hjälp av Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Hantera IP-brandväggens regler på server nivå med hjälp av REST API

| API | Nivå | Beskrivning |
| --- | --- | --- |
| [Visa en lista med brandväggsregler](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Visar aktuella regler för IP-brandvägg på server nivå |
| [Skapa eller uppdatera en brandväggsregel](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Skapar eller uppdaterar IP-brandväggens regler på server nivå |
| [Ta bort brandväggsregel](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Tar bort regler för IP-brandvägg på server nivå |
| [Hämta brand Väggs regler](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Hämtar regler för IP-brandvägg på server nivå |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regler för IP-brandvägg på server nivå kontra databas nivå

F. Ska användare av en databas vara fullständigt isolerade från en annan databas?
Om ja, bevilja du åtkomst med hjälp av regler för IP-brandvägg på databas nivå. Detta förhindrar att du använder IP-brandväggs regler på server nivå, som tillåter åtkomst genom brand väggen till alla databaser, vilket minskar djupet i dina försvar.

F. Behöver användare på IP-adressen åtkomst till alla databaser?
Använd regler för IP-brandvägg på server nivå för att minska antalet gånger som du måste konfigurera IP-brandväggens regler.

F. Har den person eller det team som konfigurerar IP-brandväggens regler bara åtkomst via Azure Portal, PowerShell eller REST API?
Du måste använda regler för IP-brandvägg på server nivå. Regler för IP-brandvägg på databas nivå kan bara konfigureras med hjälp av Transact-SQL.  

F. Är den person eller det team som konfigurerar brand Väggs reglerna för IP-brandvägg förbjudna från att ha hög behörighet på databas nivå?
Använd regler för IP-brandvägg på server nivå. Att konfigurera regler för IP-brandvägg på databas nivå med hjälp av Transact- `CONTROL DATABASE` SQL, kräver minst behörighet på databas nivå.  

F. Är den person eller det team som konfigurerar eller granskar IP-brandväggens regler, centralt hanterar IP-brandväggs regler för många (kanske 100s) databaser?
Valet beror på dina behov och din miljö. Regler för IP-brandvägg på server nivå kan vara enklare att konfigurera, men skript kan konfigurera regler på databas nivå. Även om du använder IP-brandväggs regler på server nivå kan du behöva granska regler för IP-brandvägg på databas nivå för att se om användare med `CONTROL` behörighet till databasen har skapat regler för IP-brandvägg på databas nivå.

F. Kan jag använda en blandning av de båda brand Väggs reglerna på server nivå och databas nivå?
Ja. Vissa användare, t. ex. administratörer kan behöva regler för IP-brandvägg på server nivå. Andra användare, till exempel användare av ett databas program, kan behöva IP-brandväggs regler på databas nivå.

## <a name="troubleshooting-the-database-firewall"></a>Felsöka databasbrandväggen

Tänk på följande om åtkomsten till Microsoft Azure SQL Database-tjänsten inte fungerar som förväntat:

- **Lokal brand Väggs konfiguration:**

  Innan datorn kan komma åt Azure SQL Database kan du behöva skapa ett brand Väggs undantag på datorn för TCP-port 1433. Om du skapar anslutningar inom gränsen för Azure-molnet kan du behöva öppna ytterligare portar. Mer information finns i **SQL Database: Yttre vs inuti** -avsnittet av [portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **NAT (Network Address Translation):**

  På grund av NAT kan den IP-adress som används av datorn för att ansluta till Azure SQL Database skilja sig från IP-adressen som visas i datorns IP-konfigurationsinställningar. Du kan visa IP-adressen som din dator använder genom att ansluta till Azure, logga in på portalen och gå till fliken **Konfigurera** på servern som är värd för databasen. **Aktuell klient-IP-adress** visas under avsnittet **Tillåtna IP-adresser**. Klicka på **Lägg till** i **Tillåtna IP-adresser** så att den här datorn kan ansluta till servern.

- **Ändringar i listan över tillåtna har inte börjat att fungera än:**

  Det kan finnas en fördröjning på fem minuter innan ändringar i Azure SQL Database brand Väggs konfigurationen börjar gälla.

- **Inloggningen är inte auktoriserad eller så användes ett felaktigt lösen ord:**

  Om en inloggning inte har behörighet på Azure SQL Database-servern eller om det lösen ord som används är felaktigt nekas anslutningen till Azure SQL Database-servern. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser. Mer information om hur du förbereder inloggningar finns i [Hantera databaser, inloggningar och användare i Azure SQL Database](sql-database-manage-logins.md).

- **Dynamisk IP-adress:**

  Om du har en Internet anslutning med dynamisk IP-adressering och du har problem med att komma åt brand väggen kan du prova någon av följande lösningar:
  
  - Be Internet leverantören (ISP) om IP-adressintervallet som har tilldelats till de klient datorer som har åtkomst till Azure SQL Database-servern och Lägg sedan till IP-adressintervallet som en IP-brandväggsregel.
  - Hämta statiska IP-adresser i stället för dina klient datorer och Lägg sedan till IP-adresserna som IP-brandväggs regler.

## <a name="next-steps"></a>Nästa steg

- Bekräfta att företags nätverkets miljö tillåter inkommande kommunikation från beräknings-IP-adressintervall (inklusive SQL-intervall) som används av Microsoft Azure Data Center. Det kan vara nödvändigt att vitlista dessa IP-adresser, se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)  
- En snabb start för att skapa en regel för IP-brandvägg på server nivå finns i [skapa en Azure SQL-databas](sql-database-single-database-get-started.md).
- Hjälp med att ansluta till en Azure SQL-databas från öppen källkod eller program från tredje part finns i [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snabbstart med kodexempel för att ansluta klienter till SQL Database).
- Information om ytterligare portar som du kan behöva öppna finns i **SQL Database: Yttre vs inuti** -avsnittet med [portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- En översikt över Azure SQL Database säkerhet finns i [skydda databasen](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
