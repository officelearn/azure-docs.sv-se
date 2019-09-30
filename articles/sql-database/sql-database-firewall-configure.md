---
title: Regler för Azure SQL Database och Azure SQL Data Warehouse IP-brandvägg | Microsoft Docs
description: Konfigurera IP-brandväggs regler på server nivå för en SQL-databas eller SQL Data Warehouse-brandvägg. Hantera åtkomst och konfigurera regler för IP-brandvägg på databas nivå för en databas med en eller flera databaser.
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
ms.openlocfilehash: e3e65a6deadfbcad563a6b64c0a9f48182cdd571
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686474"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Regler för Azure SQL Database och Azure SQL Data Warehouse IP-brandvägg

> [!NOTE]
> Den här artikeln gäller för Azure SQL-servrar och för både Azure SQL Database-och Azure SQL Data Warehouse-databaser på en Azure SQL-Server. För enkelhetens skull används *SQL Database* för att referera till både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för *Azure SQL Database Hanterad instans*. Information om nätverks konfiguration finns i [ansluta ditt program till Azure SQL Database Hanterad instans](sql-database-managed-instance-connect-app.md).

När du skapar en ny Azure SQL-Server med namnet *unsqlserver*blockerar SQL Database-brandväggen all åtkomst till den offentliga slut punkten för servern (som är tillgänglig på *MySQLServer.Database.Windows.net*).

> [!IMPORTANT]
> SQL Data Warehouse endast stöd för IP-brandväggs regler på server nivå. Den har inte stöd för IP-brandväggs regler på databas nivå.

## <a name="how-the-firewall-works"></a>Så här fungerar brand väggen
Anslutnings försök från Internet och Azure måste passera brand väggen innan de når din SQL Server eller SQL-databas, som i följande diagram visas.

   ![Konfigurations diagram för brand vägg][1]

### <a name="server-level-ip-firewall-rules"></a>IP-brandväggsregler på servernivå

  Dessa regler gör det möjligt för klienter att komma åt hela Azure SQL Server, det vill säga alla databaser inom samma SQL Database-Server. Reglerna lagras i *huvud* databasen. Du kan ha högst 128 IP brand Väggs regler på server nivå för en Azure-SQL Server.
  
  Du kan konfigurera regler för IP-brandvägg på server nivå med hjälp av Azure Portal-, PowerShell-eller Transact-SQL-uttryck.
  - Om du vill använda portalen eller PowerShell måste du vara prenumerations ägare eller en prenumerations deltagare.
  - Om du vill använda Transact-SQL måste du ansluta till SQL Database-instansen som huvudsaklig inloggning på server nivå eller som Azure Active Directorys administratör. (En regel för IP-brandvägg på server nivå måste först skapas av en användare som har behörighet på Azure-nivå.)

### <a name="database-level-ip-firewall-rules"></a>Regler för IP-brandvägg på databas nivå

  Dessa regler gör att klienter kan komma åt vissa (säkra) databaser inom samma SQL Database-Server. Du skapar reglerna för varje databas (inklusive *huvud* databasen) och de lagras i den enskilda databasen.
  
  Du kan bara skapa och hantera IP-brandväggs regler på databas nivå för huvud-och användar databaser med hjälp av Transact-SQL-uttryck och bara när du har konfigurerat den första brand väggen på server nivå.
  
  Om du anger ett IP-adressintervall i IP-brandväggens regel på databas nivå som ligger utanför intervallet i IP-brandväggsregel på server nivå, kan bara de klienter som har IP-adresser i intervallet på databas nivå komma åt databasen.
  
  Du kan ha högst 128 IP-brandvägg på databas nivå för en databas. Mer information om hur du konfigurerar regler för IP-brandvägg på databas nivå finns i exemplet senare i den här artikeln och se [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Rekommendationer för att ställa in brand Väggs regler

Vi rekommenderar att du använder IP brand Väggs regler på databas nivå när det är möjligt. Den här övningen förbättrar säkerheten och gör databasen mer portabel. Använd regler för IP-brandvägg för administratörer på server nivå. Använd dem även när du har många databaser med samma åtkomst krav och du inte vill konfigurera varje databas individuellt.

> [!NOTE]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regler för IP-brandvägg på server nivå kontra databas nivå

*Ska användare av en databas vara fullständigt isolerade från en annan databas?*

Om *Ja*, Använd regler för IP-brandvägg på databas nivå för att bevilja åtkomst. Med den här metoden undviker du att använda regler för IP-brandvägg på server nivå, som tillåter åtkomst genom brand väggen till alla databaser. Det skulle minska djupet i dina försvar.

*Behöver användare på IP-adresserna åtkomst till alla databaser?*

Om *Ja*, Använd IP brand Väggs regler på server nivå för att minska antalet gånger som du måste konfigurera IP-brandväggens regler.

*Har den person eller det team som konfigurerar IP-brandväggens regler bara åtkomst via Azure Portal, PowerShell eller REST API?*

I så fall måste du använda regler för IP-brandvägg på server nivå. Regler för IP-brandvägg på databas nivå kan bara konfigureras via Transact-SQL.  

*Är den person eller det team som konfigurerar brand Väggs reglerna för IP-brandvägg förbjudna från att ha hög behörighet på databas nivå?*

I så fall använder du regler för IP-brandvägg på server nivå. Du behöver minst behörigheten *kontrol lera databas* på databas nivå för att konfigurera IP-brandvägg på databas nivå via Transact-SQL.  

*Hanterar den person eller det team som konfigurerar eller granskar IP-brandväggens regler centralt IP-brandväggar för många (kanske hundratals) databaser?*

I det här scenariot bestäms bästa praxis av dina behov och din miljö. Regler för IP-brandvägg på server nivå kan vara enklare att konfigurera, men skript kan konfigurera regler på databas nivå. Även om du använder IP-brandväggs regler på server nivå kan du behöva granska regler för IP-brandvägg på databas nivå för att se om användare med *kontroll* behörighet för databasen skapar regler för IP-brandvägg på databas nivå.

*Kan jag använda en blandning av regler för IP-brandvägg på server nivå och databas nivå?*

Ja. Vissa användare, till exempel Administratörer, kan behöva regler för IP-brandvägg på server nivå. Andra användare, till exempel användare av ett databas program, kan behöva IP-brandväggs regler på databas nivå.

### <a name="connections-from-the-internet"></a>Anslutningar från Internet

När en dator försöker ansluta till din databas server från Internet kontrollerar brand väggen först den ursprungliga IP-adressen för begäran mot databas nivåns IP-brandvägg regler för den databas som anslutningsbegäran begär.

- Om adressen är inom ett intervall som anges i IP-brandväggens regler på databas nivå, beviljas anslutningen till den SQL-databas som innehåller regeln.
- Om adressen inte ligger inom ett intervall i IP-brandväggens regler på databas nivå, kontrollerar brand väggen IP-brandväggens regler på server nivå. Om adressen ligger inom ett intervall som finns i IP-brandväggens regler på server nivå, beviljas anslutningen. Regler för IP-brandvägg på server nivå gäller för alla SQL-databaser på Azure SQL-servern.  
- Om adressen inte är inom ett intervall som finns i någon av reglerna på databas-eller Server nivåns IP-brandvägg, Miss lyckas anslutningsbegäran.

> [!NOTE]
> För att få åtkomst till SQL Database från den lokala datorn måste du kontrol lera att brand väggen i nätverket och den lokala datorn tillåter utgående kommunikation på TCP-port 1433.

### <a name="connections-from-inside-azure"></a>Anslutningar inifrån Azure

Om du vill att program som finns i Azure ska kunna ansluta till din SQL Server måste Azure-anslutningar vara aktiverade. När ett program från Azure försöker ansluta till din databas server, kontrollerar brand väggen att Azure-anslutningar är tillåtna. En brand Väggs inställning som har start-och slut-IP-adresser lika med *0.0.0.0* indikerar att Azure-anslutningar är tillåtna. Om anslutningen inte är tillåten når inte begäran SQL Database servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brand väggen så att den tillåter alla anslutningar från Azure, inklusive anslutningar från andra kunders prenumerationer. Om du väljer det här alternativet ser du till att dina inloggnings-och användar behörigheter begränsar åtkomsten till behöriga användare.

## <a name="create-and-manage-ip-firewall-rules"></a>Skapa och hantera IP-brandväggens regler

Du kan skapa den första brand Väggs inställningen på server nivå med hjälp av [Azure Portal](https://portal.azure.com/) eller program mässigt genom att använda [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)eller en Azure- [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Du skapar och hanterar ytterligare IP-brandvägg på server nivå med hjälp av dessa metoder eller Transact-SQL.

> [!IMPORTANT]
> Det går bara att skapa och hantera regler för IP-brandvägg på databas nivå med hjälp av Transact-SQL.

För att förbättra prestanda cachelagras tillfälligt IP-brandväggens regler på server nivå på databas nivå. Information om hur du uppdaterar cacheminnet finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Du kan använda [SQL Database granskning](sql-database-auditing.md) för att granska ändringar på server nivå och databas nivå.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Använd Azure Portal för att hantera IP-brandväggs regler på server nivå

Om du vill ange en regel för IP-brandvägg på server nivå i Azure Portal går du till översikts sidan för din Azure SQL-databas eller din SQL Database-Server.

> [!TIP]
> En själv studie kurs finns i [skapa en databas med hjälp av Azure Portal](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>På sidan databas översikt

1. Om du vill ange en regel för IP-brandvägg på server nivå från sidan databas översikt väljer du **Ange server brand vägg** i verktygsfältet, som följande bild visar. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

      ![Regel för Server-IP-brandvägg](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Välj **Lägg till klient-IP** i verktygsfältet för att lägga till IP-adressen för den dator som du använder och välj sedan **Spara**. En regel för IP-brandvägg på server nivå skapas för din aktuella IP-adress.

      ![Ange regel för IP-brandvägg på server nivå](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

#### <a name="from-the-server-overview-page"></a>På sidan Server översikt

Sidan översikt för servern öppnas. Det visar det fullständigt kvalificerade Server namnet (till exempel *mynewserver20170403.Database.Windows.net*) och innehåller alternativ för ytterligare konfiguration.

1. Om du vill ange en regel på server nivå från den här sidan väljer du **brand vägg** på menyn **Inställningar** på vänster sida.

2. Välj **Lägg till klient-IP** i verktygsfältet för att lägga till IP-adressen för den dator som du använder och välj sedan **Spara**. En regel för IP-brandvägg på server nivå skapas för din aktuella IP-adress.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Använd Transact-SQL för att hantera IP-brandväggens regler

| Katalogvy eller lagrad procedur | Nivå | Beskrivning |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visar aktuella regler för IP-brandvägg på server nivå |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Skapar eller uppdaterar IP-brandväggens regler på server nivå |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Tar bort regler för IP-brandvägg på server nivå |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databas |Visar de aktuella IP-brandväggens regler på databas nivå |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databas |Skapar eller uppdaterar IP-brandväggens regler på databas nivå |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databaser |Tar bort regler för IP-brandvägg på databas nivå |

Följande exempel granskar befintliga regler, aktiverar ett intervall med IP-adresser på servern *contoso*och tar bort en regel för IP-brandvägg:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Lägg sedan till en regel för IP-brandvägg på server nivå.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Om du vill ta bort en regel för IP-brandvägg på server nivå kör du den lagrade proceduren *sp_delete_firewall_rule* . I följande exempel tar bort regeln *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Använd PowerShell för att hantera IP-brandväggs regler på server nivå 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all utveckling är nu för AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-och AzureRm-modulerna är i stort sett identiska.

| Cmdlet: | Nivå | Beskrivning |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Returnerar de aktuella brandväggsreglerna på servernivå |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Skapar en ny brandväggsregel på servernivå |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Tar bort brandväggsregler på servernivå |

I följande exempel används PowerShell för att ange en regel för IP-brandvägg på server nivå:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> PowerShell-exempel i kontexten för en snabb start finns i [skapa DB-PowerShell](sql-database-powershell-samples.md) och [skapa en enskild databas och konfigurera en SQL Database regel för IP-brandvägg på server nivå med hjälp av PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Använd CLI för att hantera IP-brandväggs regler på server nivå

| Cmdlet: | Nivå | Beskrivning |
| --- | --- | --- |
|[AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Skapar en server-IP-brandväggsregel|
|[AZ SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Visar en lista över IP-brandväggens regler på en server|
|[AZ för SQL Server-brandvägg – regel show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Visar information om en IP-brandväggsregel|
|[AZ SQL Server-brandvägg-regel uppdatering](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Uppdaterar en regel för IP-brandvägg|
|[AZ SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Tar bort en regel för IP-brandvägg|

I följande exempel används CLI för att ange en regel för IP-brandvägg på server nivå:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Ett CLI-exempel i kontexten för en snabb start finns i [skapa DB-Azure CLI](sql-database-cli-samples.md) och [skapa en enskild databas och konfigurera en SQL Database IP-brandväggsregel med hjälp av Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Använd en REST API för att hantera IP-brandvägg på server nivå

| API | Nivå | Beskrivning |
| --- | --- | --- |
| [Visa lista över brand Väggs regler](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Visar aktuella regler för IP-brandvägg på server nivå |
| [Skapa eller uppdatera brand Väggs regler](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Skapar eller uppdaterar IP-brandväggens regler på server nivå |
| [Ta bort brand Väggs regler](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Tar bort regler för IP-brandvägg på server nivå |
| [Hämta brand Väggs regler](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Hämtar regler för IP-brandvägg på server nivå |

## <a name="troubleshoot-the-database-firewall"></a>Felsöka databas brand väggen

Tänk på följande när åtkomsten till SQL Database tjänsten inte fungerar som förväntat.

- **Lokal brand Väggs konfiguration:**

  Innan datorn kan komma åt SQL Database kan du behöva skapa ett brand Väggs undantag på datorn för TCP-port 1433. Du kan behöva öppna ytterligare portar för att kunna göra anslutningar inom molnets gränser i Azure. Mer information finns i avsnittet "SQL Database: Utanför vs inuti avsnittet av [portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Översättning av nätverks adresser:**

  På grund av Network Address Translation (NAT) kan IP-adressen som används av datorn för att ansluta till SQL Database skilja sig från IP-adressen i datorns IP-konfigurationsinställningar. Så här visar du IP-adressen som datorn använder för att ansluta till Azure:
    1. Logga in på portalen.
    1. Gå till fliken **Konfigurera** på den server som är värd för din databas.
    1. Den **aktuella klientens IP-adress** visas i avsnittet **tillåtna IP-adresser** . Välj **Lägg till** för **tillåtna IP-adresser** så att den här datorn kan komma åt servern.

- **Ändringar i listan över tillåtna har inte börjat användas än:**

  Det kan vara upp till fem minuter innan ändringarna i SQL Database brand Väggs konfigurationen börjar gälla.

- **Inloggningen är inte auktoriserad eller ett felaktigt lösen ord användes:**

  Om en inloggning inte har behörighet på SQL Database servern eller om lösen ordet är felaktigt nekas anslutningen till servern. Om du skapar en brand Väggs inställning får klienterna *möjlighet* att försöka ansluta till servern. Klienten måste ändå ange nödvändiga säkerhets referenser. Mer information om hur du förbereder inloggningar finns i [kontrol lera och bevilja databas åtkomst till SQL Database och SQL Data Warehouse](sql-database-manage-logins.md).

- **Dynamisk IP-adress:**

  Om du har en Internet anslutning som använder dynamisk IP-adressering och du har problem med att komma genom brand väggen kan du prova någon av följande lösningar:
  
  - Fråga Internet leverantören efter IP-adressintervall som är tilldelade till de klient datorer som har åtkomst till SQL Database-servern. Lägg till IP-adressintervallet som en regel för IP-brandvägg.
  - Hämta statiska IP-adresser i stället för klient datorerna. Lägg till IP-adresserna som IP-brandväggs regler.

## <a name="next-steps"></a>Nästa steg

- Bekräfta att din företags nätverks miljö tillåter inkommande kommunikation från beräknings-IP-adressintervall (inklusive SQL-intervall) som används av Azure-datacentren. Du kanske måste lägga till dessa IP-adresser i listan över tillåtna. Se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).  
- En snabb start om hur du skapar en regel för IP-brandvägg på server nivå finns i [skapa en Azure SQL-databas](sql-database-single-database-get-started.md).
- För hjälp med att ansluta till en Azure SQL-databas från program med öppen källkod eller från tredje part, se [kod exempel för klient snabb start för att SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Information om ytterligare portar som du kan behöva öppna finns i avsnittet "SQL Database: Utanför vs inuti avsnittet av [portar utöver 1433 för ADO.NET 4,5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- En översikt över Azure SQL Database säkerhet finns i [skydda databasen](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
