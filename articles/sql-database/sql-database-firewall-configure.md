---
title: REGLER för IP-brandväggen
description: Konfigurera IP-brandväggsregler på servernivå för en SQL-databas eller SQL Data Warehouse-brandvägg. Hantera åtkomst och konfigurera IP-brandväggsregler på databasnivå för en enda eller poolad databas.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531285"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database och Azure SQL Data Warehouse IP-brandväggsregler

> [!NOTE]
> Den här artikeln gäller Azure SQL-servrar och både Azure SQL Database- och Azure SQL Data Warehouse-databaser på en Azure SQL-server. För enkelhetens skull används *SQL Database* för att referera till både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* *för Azure SQL Database Managed Instance*. Information om nätverkskonfiguration finns i [Ansluta ditt program till Azure SQL Database Managed Instance](sql-database-managed-instance-connect-app.md).

När du skapar en ny Azure SQL-server med namnet *mysqlserver,* till exempel, blockerar SQL Database-brandväggen all åtkomst till den offentliga slutpunkten för servern (som är tillgänglig på *mysqlserver.database.windows.net*).

> [!IMPORTANT]
> SQL Data Warehouse stöder endast IP-brandväggsregler på servernivå. Det stöder inte ip-brandväggsregler på databasnivå.

## <a name="how-the-firewall-works"></a>Så här fungerar brandväggen
Anslutningsförsök från internet och Azure måste passera genom brandväggen innan de når din SQL-server eller SQL-databas, vilket visas i följande diagram.

   ![Konfigurationsdiagram för brandvägg][1]

### <a name="server-level-ip-firewall-rules"></a>IP-brandväggsregler på servernivå

  Dessa regler gör det möjligt för klienter att komma åt hela Din Azure SQL-server, det vill än alla databaser i samma SQL Database-server. Reglerna lagras i *huvuddatabasen.* Du kan ha högst 128 IP-brandväggsregler på servernivå för en Azure SQL Server. Om du har **aktiverat Tillåt Azure-tjänster och resurser för att komma åt den här serverinställningen** räknas detta som en enda brandväggsregel för Azure SQL Server.
  
  Du kan konfigurera IP-brandväggsregler på servernivå med hjälp av Azure-portal-, PowerShell- eller Transact-SQL-uttryck.
  - Om du vill använda portalen eller PowerShell måste du vara prenumerationsägare eller prenumerationsdeltagare.
  - Om du vill använda Transact-SQL måste du ansluta till SQL Database-instansen som huvudinloggning på servernivå eller som Azure Active Directory-administratör. (En IP-brandväggsregel på servernivå måste först skapas av en användare som har behörighet på Azure-nivå.)

### <a name="database-level-ip-firewall-rules"></a>IP-brandväggsregler på databasnivå

  Dessa regler gör det möjligt för klienter att komma åt vissa (säkra) databaser inom samma SQL Database-server. Du skapar reglerna för varje *master* databas (inklusive huvuddatabasen) och de lagras i den enskilda databasen.
  
  Du kan bara skapa och hantera IP-brandväggsregler på databasnivå för huvud- och användardatabaser med hjälp av Transact-SQL-uttryck och först när du har konfigurerat den första brandväggen på servernivå.
  
  Om du anger ett IP-adressintervall i IP-brandväggsregeln på databasnivå som ligger utanför intervallet i IP-brandväggsregeln på servernivå kan endast de klienter som har IP-adresser i intervallet på databasnivå komma åt databasen.
  
  Du kan ha högst 128 IP-brandväggsregler på databasnivå för en databas. Mer information om hur du konfigurerar IP-brandväggsregler på databasnivå finns i exemplet senare i den här artikeln och finns [i sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Rekommendationer för hur brandväggsregler ska fastställas

Vi rekommenderar att du använder IP-brandväggsregler på databasnivå när det är möjligt. Den här metoden ökar säkerheten och gör databasen mer portabel. Använd IP-brandväggsregler på servernivå för administratörer. Använd dem också när du har många databaser som har samma åtkomstkrav, och du inte vill konfigurera varje databas individuellt.

> [!NOTE]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>IP-brandväggsregler på servernivå kontra databasnivå

*Ska användare av en databas vara helt isolerade från en annan databas?*

Om *ja*använder du IP-brandväggsregler på databasnivå för att bevilja åtkomst. Den här metoden undviker att använda IP-brandväggsregler på servernivå, vilket tillåter åtkomst genom brandväggen till alla databaser. Det skulle minska djupet av ditt försvar.

*Behöver användare på IP-adresserna tillgång till alla databaser?*

Om *ja*använder du IP-brandväggsregler på servernivå för att minska antalet gånger som du måste konfigurera IP-brandväggsregler.

*Har den person eller det team som konfigurerar IP-brandväggsreglerna bara åtkomst via Azure-portalen, PowerShell eller REST API?*

Om så är fallet måste du använda IP-brandväggsregler på servernivå. IP-brandväggsregler på databasnivå kan endast konfigureras via Transact-SQL.  

*Är den person eller det team som konfigurerar IP-brandväggsreglerna förbjuden att ha behörighet på hög nivå på databasnivå?*

Använd i så fall IP-brandväggsregler på servernivå. Du behöver minst *behörigheten CONTROL DATABASE* på databasnivå för att konfigurera IP-brandväggsregler på databasnivå via Transact-SQL.  

*Hanterar den person eller det team som konfigurerar eller granskar IP-brandväggsreglerna centralt IP-brandväggsregler för många (kanske hundratals) databaser?*

I det här fallet bestäms bästa praxis av dina behov och din miljö. IP-brandväggsregler på servernivå kan vara enklare att konfigurera, men skript kan konfigurera regler på databasnivå. Och även om du använder IP-brandväggsregler på servernivå kan du behöva granska IP-brandväggsregler på databasnivå för att se om användare med *behörighet för CTRL* i databasen skapar IP-brandväggsregler på databasnivå.

*Kan jag använda en blandning av IP-brandväggsregler på servernivå och databasnivå?*

Ja. Vissa användare, till exempel administratörer, kan behöva IP-brandväggsregler på servernivå. Andra användare, till exempel användare av ett databasprogram, kan behöva IP-brandväggsregler på databasnivå.

### <a name="connections-from-the-internet"></a>Anslutningar från internet

När en dator försöker ansluta till databasservern från internet kontrollerar brandväggen först den ursprungliga IP-adressen för begäran mot IP-brandväggsreglerna på databasnivå för databasen som anslutningen begär.

- Om adressen ligger inom ett intervall som anges i IP-brandväggsreglerna på databasnivå beviljas anslutningen till SQL-databasen som innehåller regeln.
- Om adressen inte ligger inom ett intervall i IP-brandväggsreglerna på databasnivå kontrollerar brandväggen IP-brandväggsreglerna på servernivå. Om adressen ligger inom ett intervall som finns i IP-brandväggsreglerna på servernivå beviljas anslutningen. IP-brandväggsregler på servernivå gäller för alla SQL-databaser på Azure SQL-servern.  
- Om adressen inte ligger inom ett intervall som finns i någon av IP-brandväggsreglerna på databasnivå eller servernivå misslyckas anslutningsbegäran.

> [!NOTE]
> Om du vill komma åt SQL Database från den lokala datorn kontrollerar du att brandväggen i nätverket och den lokala datorn tillåter utgående kommunikation på TCP-port 1433.

### <a name="connections-from-inside-azure"></a>Anslutningar inifrån Azure

Om du vill att program som finns i Azure ska kunna ansluta till din SQL-server måste Azure-anslutningar vara aktiverade. När ett program från Azure försöker ansluta till databasservern verifierar brandväggen att Azure-anslutningar är tillåtna. En brandväggsinställning som har start- och slut-IP-adresser som är lika med *0.0.0.0* anger att Azure-anslutningar är tillåtna. Detta kan aktiveras direkt från Azure Portal-bladet genom att ange brandväggsregler, samt växla **till Tillåt Azure-tjänster och resurser för att komma åt den här servern** till **ON** i **inställningarna för brandväggar och virtuella nätverk.** Om anslutningen inte är tillåten når begäran inte SQL Database-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer från andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till behöriga användare.

## <a name="create-and-manage-ip-firewall-rules"></a>Skapa och hantera IP-brandväggsregler

Du skapar den första brandväggsinställningen på servernivå med hjälp av [Azure-portalen](https://portal.azure.com/) eller programmässigt med hjälp av [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)eller ett Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Du skapar och hanterar ytterligare IP-brandväggsregler på servernivå med hjälp av dessa metoder eller Transact-SQL.

> [!IMPORTANT]
> IP-brandväggsregler på databasnivå kan bara skapas och hanteras med hjälp av Transact-SQL.

För att förbättra prestanda cachelagras IP-brandväggsregler på servernivå tillfälligt på databasnivå. Information om hur du uppdaterar cacheminnet finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Du kan använda [SQL Database Auditing](sql-database-auditing.md) för att granska brandväggsändringar på servernivå och brandvägg på databasnivå.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Använda Azure-portalen för att hantera IP-brandväggsregler på servernivå

Om du vill ange en IP-brandväggsregel på servernivå i Azure-portalen går du till översiktssidan för din Azure SQL-databas eller SQL Database-servern.

> [!TIP]
> En självstudiekurs finns i [Skapa en DB med Azure-portalen](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Från databasöversiktssidan

1. Om du vill ange en IP-brandväggsregel på servernivå från databasöversiktssidan väljer du **Ange serverbrandvägg** i verktygsfältet, vilket visas i följande bild. 

    ![Regel för server-IP-brandvägg](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

2. Välj **Lägg till klient-IP i** verktygsfältet om du vill lägga till IP-adressen för den dator som du använder och välj sedan **Spara**. En IP-brandväggsregel på servernivå skapas för din aktuella IP-adress.

    ![Ange IP-brandväggsregel på servernivå](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Från serveröversiktssidan

Översiktssidan för servern öppnas. Den visar det fullständigt kvalificerade servernamnet (till exempel *mynewserver20170403.database.windows.net)* och innehåller alternativ för ytterligare konfiguration.

1. Om du vill ange en regel på servernivå från den här sidan väljer du **Brandvägg** på **menyn Inställningar** till vänster.

2. Välj **Lägg till klient-IP i** verktygsfältet om du vill lägga till IP-adressen för den dator som du använder och välj sedan **Spara**. En IP-brandväggsregel på servernivå skapas för din aktuella IP-adress.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Använda Transact-SQL för att hantera IP-brandväggsregler

| Katalogvy eller lagrad procedur | Nivå | Beskrivning |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visar de aktuella IP-brandväggsreglerna på servernivå |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Skapar eller uppdaterar IP-brandväggsregler på servernivå |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Tar bort IP-brandväggsregler på servernivå |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databas |Visar de aktuella IP-brandväggsreglerna på databasnivå |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databas |Skapar eller uppdaterar IP-brandväggsregler på databasnivå |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databaser |Tar bort IP-brandväggsregler på databasnivå |

I följande exempel granskas de befintliga reglerna, ett intervall med IP-adresser på servern *Contoso*tas bort och en IP-brandväggsregel tas bort:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Lägg sedan till en IP-brandväggsregel på servernivå.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Om du vill ta bort en IP-brandväggsregel på servernivå kör *du den sp_delete_firewall_rule* lagrade proceduren. I följande exempel tas regeln *ContosoFirewallRule*bort:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Använda PowerShell för att hantera IP-brandväggsregler på servernivå 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all utveckling är nu för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az- och AzureRm-modulerna är i stort sett identiska.

| Cmdlet | Nivå | Beskrivning |
| --- | --- | --- |
| [Få-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Returnerar de aktuella brandväggsreglerna på servernivå |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Skapar en ny brandväggsregel på servernivå |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Ta bort-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Tar bort brandväggsregler på servernivå |

I följande exempel används PowerShell för att ange en IP-brandväggsregel på servernivå:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> För $servername ange servernamnet och inte det fullständigt kvalificerade DNS-namnet t.ex **mysqldbserver.database.windows.net.** **mysqldbserver**

> [!TIP]
> Exempel på PowerShell i samband med en snabbstart finns i [Skapa DB - PowerShell](sql-database-powershell-samples.md) och [Skapa en enda databas och konfigurera en IP-brandväggsregel för SQL Database-servernivå med PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Använda CLI för att hantera IP-brandväggsregler på servernivå

| Cmdlet | Nivå | Beskrivning |
| --- | --- | --- |
|[az sql server brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Skapar en server-IP-brandväggsregel|
|[az sql server brandvägg-regel lista](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Visar IP-brandväggsregler på en server|
|[az sql server brandvägg-regel visa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Visar detaljerna i en IP-brandväggsregel|
|[az sql server brandvägg-regel uppdatering](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Uppdaterar en IP-brandväggsregel|
|[az sql server brandvägg-regel bort](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Tar bort en IP-brandväggsregel|

I följande exempel används CLI för att ange en IP-brandväggsregel på servernivå:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> För $servername ange servernamnet och inte det fullständigt kvalificerade DNS-namnet t.ex **mysqldbserver.database.windows.net.** **mysqldbserver**

> [!TIP]
> Ett CLI-exempel i samband med en snabbstart finns i [Skapa DB - Azure CLI](sql-database-cli-samples.md) och Skapa en enda databas och konfigurera en SQL Database [IP-brandväggsregel med Hjälp av Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Använda ett REST API för att hantera IP-brandväggsregler på servernivå

| API | Nivå | Beskrivning |
| --- | --- | --- |
| [Lista brandväggsregler](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Visar de aktuella IP-brandväggsreglerna på servernivå |
| [Skapa eller uppdatera brandväggsregler](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Skapar eller uppdaterar IP-brandväggsregler på servernivå |
| [Ta bort brandväggsregler](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Tar bort IP-brandväggsregler på servernivå |
| [Hämta brandväggsregler](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Hämtar IP-brandväggsregler på servernivå |

## <a name="troubleshoot-the-database-firewall"></a>Felsöka databasbrandväggen

Tänk på följande punkter när åtkomsten till SQL Database-tjänsten inte fungerar som förväntat.

- **Lokal brandväggskonfiguration:**

  Innan datorn kan komma åt SQL Database kan du behöva skapa ett brandväggsundantag på datorn för TCP-port 1433. Om du vill skapa anslutningar innanför Azure-molngränsen kan du behöva öppna ytterligare portar. Mer information finns i avsnittet "SQL Database: Outside vs inside" i [Portar över 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Översättning av nätverksadresser:**

  På grund av NAT (Network Address Translation) kan DEN IP-adress som används av datorn för att ansluta till SQL Database skilja sig från IP-adressen i datorns IP-konfigurationsinställningar. Så här visar du den IP-adress som datorn använder för att ansluta till Azure:
    1. Logga in på portalen.
    1. Gå till fliken **Konfigurera** på servern som är värd för databasen.
    1. Den **aktuella klientens IP-adress** visas i avsnittet **Tillåtna IP-adresser.** Välj **Lägg till** för **tillåtna IP-adresser** så att den här datorn kan komma åt servern.

- **Ändringarna i tillåt-listan har inte trätt i kraft ännu:**

  Det kan ta upp till fem minuters fördröjning för ändringar i SQL Database-brandväggskonfigurationen att börja gälla.

- **Inloggningen är inte auktoriserad eller så användes ett felaktigt lösenord:**

  Om en inloggning inte har behörighet på SQL Database-servern eller om lösenordet är felaktigt nekas anslutningen till servern. Om du skapar en *opportunity* brandväggsinställning kan klienterna bara försöka ansluta till servern. Klienten måste fortfarande tillhandahålla nödvändiga säkerhetsreferenser. Mer information om hur du förbereder inloggningar finns i [Kontrollera och bevilja databasåtkomst till SQL Database och SQL Data Warehouse](sql-database-manage-logins.md).

- **Dynamisk IP-adress:**

  Om du har en internetanslutning som använder dynamisk IP-adressering och du har problem med att ta dig igenom brandväggen kan du prova någon av följande lösningar:
  
  - Fråga internetleverantören om det IP-adressintervall som har tilldelats klientdatorerna som ansluter till SQL Database-servern. Lägg till det IP-adressintervallet som en IP-brandväggsregel.
  - Få statisk IP-adressering i stället för dina klientdatorer. Lägg till IP-adresserna som IP-brandväggsregler.

## <a name="next-steps"></a>Nästa steg

- Bekräfta att företagets nätverksmiljö tillåter inkommande kommunikation från beräknings-IP-adressintervallen (inklusive SQL-intervall) som används av Azure-datacenter. Du kanske måste lägga till dessa IP-adresser i listan över tillåtna. Se [IP-intervall för Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653).  
- En snabbstart om hur du skapar en IP-brandväggsregel på servernivå finns i [Skapa en Azure SQL-databas](sql-database-single-database-get-started.md).
- Mer information om hur du ansluter till en Azure SQL-databas från program med öppen källkod eller tredje part finns i [snabbstartskodexempel för klient till SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Information om ytterligare portar som du kan behöva öppna finns i avsnittet "SQL Database: Outside vs inside" i [Portar över 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- En översikt över Azure SQL Database-säkerhet finns i [Skydda databasen](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
