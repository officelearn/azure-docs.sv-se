---
title: Azure SQL Database och Data Warehouse-brandväggsregler | Microsoft Docs
description: Lär dig hur du konfigurerar SQL-databas och SQL Data Warehouse-brandvägg med brandväggsregler på servernivå att hantera åtkomst och konfigurera brandväggsregler för SQL-databas på databasnivå.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: bc246031e57fd8e28cddf9f4e6e170b0695d7579
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166766"
---
# <a name="azure-sql-database-and-sql-data-warehouse-firewall-rules"></a>Azure SQL Database och SQL Data Warehouse brandväggsregler 

Microsoft Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Virtual network-regler som alternativ till IP-regler

Förutom IP-regler i brandväggen hanterar också *virtuella Nätverksregler*. Virtuella Nätverksregler baseras på Vnet-tjänstslutpunkter. Regler för virtuellt nätverk kan vara bättre att IP-regler i vissa fall. Mer information finns i [tjänstslutpunkter i virtuella nätverk och regler för Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Översikt

Till en början blockeras all Transact-SQL-åtkomst till din Azure SQL-server av brandväggen. Om du vill börja använda Azure SQL-servern, måste du ange en eller flera-brandväggsregler på servernivå som ger åtkomst till Azure SQL-servern. Använd brandväggsreglerna för att ange vilka IP-adressintervall från Internet som tillåts samt för att ange om Azure-program kan försöka ansluta till din Azure SQL Database-server eller inte.

Om du bara vill bevilja åtkomst till en av databaserna på din Azure SQL-server måste du skapa en regel på databasnivå för databasen. Ange ett IP-adressintervall för brandväggsregeln för databasen som ligger utanför det IP-adressintervall som angetts i brandväggsregeln på servernivå och kontrollera att klientens IP-adress ligger inom intervallet som angetts i regeln på databasnivå.

> [!IMPORTANT]
> SQL Data Warehouse bara stöder brandväggsregler på servernivå och stöder inte brandväggsregler på databasnivå.

Anslutningsförsök från Internet och Azure måste först passera brandväggen innan de kan nå din Azure SQL-server eller SQL Database, som du ser i följande diagram:

   ![Diagram över brandväggskonfigurationen.][1]

* **Brandväggsregler på servernivå:** Dessa regler gör att klienterna kan komma åt hela Azure SQL-servern, det vill säga alla databaser som finns på samma logiska server. Dessa regler lagras i **huvuddatabasen**. Brandväggsregler på servernivå kan konfigureras via portalen eller med hjälp av Transact-SQL-instruktioner. Du måste vara prenumerationsägare eller prenumerationsdeltagare för att skapa brandväggsregler på servernivå med Azure Portal eller PowerShell. För att skapa en brandväggsregel på servernivå med hjälp av Transact-SQL, måste du ansluta till SQL Database-instansen med huvudsaklig inloggning på servernivå eller som Azure Active Directory-administratör (vilket innebär att en brandväggsregel på servernivå först måste ha skapats av en användare med Azure-behörighet).
* **Databasnivå brandväggsregler:** dessa regler gör att klienterna kan komma åt vissa (säkra) databaser inom samma logiska server. Du kan skapa dessa regler för varje databas (inklusive den **master** databas) och de lagras i de enskilda databaserna. Brandväggsregler på databasnivå för huvud- och användardatabaser kan bara skapas och hanteras med hjälp av Transact-SQL-instruktioner och bara när du har konfigurerat den första brandväggen på servernivå. Om du anger ett IP-adressintervall i brandväggsregeln på databasnivå som ligger utanför det intervall som angetts i brandväggsregeln på servernivå kan endast klienter som har IP-adresser som ligger i intervallet som angetts för databasnivån komma åt databasen. Du kan skapa upp till 128 brandväggsregler på databasnivå för en databas. Mer information om hur du konfigurerar brandväggsregler på databasnivå finns i exemplet senare i den här artikeln och se [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

**Rekommendation:** Microsoft rekommenderar att du använder brandväggsregler på databasnivå när det är möjligt för att förbättra säkerheten och göra databasen mer portabel. Använd brandväggsregler på servernivå för administratörer och om du har många databaser med samma åtkomstkrav och du inte vill lägga tid på att konfigurera varje databas individuellt.

> [!Important]
> Windows Azure SQL Database stöder maximalt 128 brandväggsregler.
>

> [!Note]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Ansluta från Internet

När en dator försöker ansluta till databasservern från Internet kontrollerar brandväggen den ursprungliga IP-adressen för begäran mot brandväggsreglerna på databasnivå för den databas anslutningen begär:

* Om IP-adressen för begäran ligger inom ett intervall som anges i brandväggsreglerna på databasnivå godkänns anslutningen till din SQL Database som innehåller regeln.
* Om IP-adressen för begäran inte ligger inom något av intervallen som anges i brandväggsregeln på databasnivå kontrollerar brandväggen brandväggsreglerna på servernivå. Om IP-adressen för begäran ligger inom ett intervall som anges i brandväggsreglerna på servernivå godkänns anslutningen. Brandväggsregler på servernivå gäller för alla SQL-databaser på Azure SQL-servern.  
* Om IP-adressen för begäran inte ligger inom intervallen som angetts i brandväggsreglerna på databasnivå eller servernivå misslyckas anslutningsbegäran.

> [!NOTE]
> För att kunna komma åt Azure SQL Database från din lokala dator måste du kontrollera att brandväggen i nätverket och på den lokala datorn tillåter utgående kommunikation på TCP-port 1433.
> 

### <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure SQL-server måste Azure-anslutningar vara aktiverade. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Om anslutningsförsöket inte tillåts kommer begäran inte att nå Azure SQL Database-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

## <a name="creating-and-managing-firewall-rules"></a>Skapa och hantera brandväggsregler
Första brandväggsinställningen på servernivå kan skapas med den [Azure-portalen](https://portal.azure.com/) eller programmässigt med [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), eller [ REST-API](https://docs.microsoft.com/rest/api/sql/firewallrules). Efterföljande brandväggsregler på servernivå kan skapas och hanteras med hjälp av dessa metoder samt via Transact-SQL. 

> [!IMPORTANT]
> Brandväggsregler på databasnivå kan bara skapas och hanteras med hjälp av Transact-SQL. 
>

För att förbättra prestanda cachelagras brandväggsregler på servernivå tillfälligt på databasnivå. Information om hur du uppdaterar cacheminnet finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Du kan använda [SQL Database Auditing](sql-database-auditing.md) att granska ändringar av brandvägg på servernivå och databasnivå.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Hantera brandväggsregler med hjälp av Azure-portalen

Om du vill ställa in en brandväggsregel på servernivå på Azure-portalen, går antingen du till sidan Översikt för Azure SQL database eller översiktssidan för din logiska Azure Database-server.

> [!TIP]
> En självstudiekurs finns i [skapa en databas med Azure portal](sql-database-get-started-portal.md).
>

**Från översiktssidan för databasen**

1. Om du vill ställa in en brandväggsregel på servernivå från översiktssidan för databasen, klickar du på **konfigurera serverbrandvägg** i verktygsfältet, som visas i följande bild: den **brandväggsinställningar** för SQL Database-server öppnas.

      ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Klicka på **klientens IP-adress** i verktygsfältet för att lägga till IP-adressen för datorn du använder för närvarande och klicka sedan på **spara**. En brandväggsregel på servernivå skapas för din aktuella IP-adress.

      ![ange brandväggsregel för server](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Från server-översiktssidan**

Översiktssidan för servern öppnas, där du kan se det fullständigt kvalificerade servernamnet (till exempel **mynewserver20170403.database.windows.net**) och alternativ för ytterligare konfiguration.

1. Om du vill ange en regel för på servernivå från server översiktssidan, klickar du på **brandväggen** på den vänstra menyn under inställningar: 

2. Klicka på **klientens IP-adress** i verktygsfältet för att lägga till IP-adressen för datorn du använder för närvarande och klicka sedan på **spara**. En brandväggsregel på servernivå skapas för din aktuella IP-adress.

## <a name="manage-firewall-rules-using-transact-sql"></a>Hantera brandväggsregler med hjälp av Transact-SQL
| Katalogvy eller lagrad procedur | Nivå | Beskrivning |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visar de aktuella brandväggsreglerna på servernivå |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Skapar eller uppdaterar brandväggsregler på servernivå |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Tar bort brandväggsregler på servernivå |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databas |Visar de aktuella brandväggsreglerna på databasnivå |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databas |Skapar eller uppdaterar brandväggsreglerna på databasnivå |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databaser |Tar bort brandväggsregler på databasnivå |


Granska befintliga regler i följande exempel, aktivera ett intervall med IP-adresser på Contoso-servern och tar bort en brandväggsregel:
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
Lägg sedan till en brandväggsregel.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Kör den lagrade proceduren sp_delete_firewall_rule om du vill ta bort en brandväggsregel på servernivå. I följande exempel tas regeln med namnet ContosoFirewallRule:
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>Hantera brandväggsregler med hjälp av Azure PowerShell
| Cmdlet | Nivå | Beskrivning |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Returnerar de aktuella brandväggsreglerna på servernivå |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Skapar en ny brandväggsregel på servernivå |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Tar bort brandväggsregler på servernivå |


I följande exempel anger en regel för brandvägg på servernivå med hjälp av PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> PowerShell-exempel i kontexten för en Snabbstart finns [skapa DB – PowerShell](sql-database-powershell-samples.md) och [skapar en enkel databas och konfigurerar en brandväggsregel med hjälp av PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Hantera brandväggsregler med hjälp av Azure CLI
| Cmdlet | Nivå | Beskrivning |
| --- | --- | --- |
|[Skapa AZ sql server firewall-rule](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Skapar en brandväggsregel|
|[AZ sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Visar en lista över brandväggsreglerna på en server|
|[AZ sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Visar information om en brandväggsregel|
|[AZ sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Uppdaterar en brandväggsregel|
|[AZ sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Tar bort en brandväggsregel|

I följande exempel anger en regel för brandvägg på servernivå med hjälp av Azure-CLI: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Azure CLI exempelvis i kontexten för en Snabbstart Se [skapa DB – Azure CLI](sql-database-cli-samples.md) och [skapar en enkel databas och konfigurerar en brandväggsregel med hjälp av Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
>

## <a name="manage-firewall-rules-using-rest-api"></a>Hantera brandväggsregler med hjälp av REST API
| API | Nivå | Beskrivning |
| --- | --- | --- |
| [Visa en lista med brandväggsregler](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |Server |Visar de aktuella brandväggsreglerna på servernivå |
| [Skapa eller uppdatera en brandväggsregel](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |Server |Skapar eller uppdaterar brandväggsregler på servernivå |
| [Ta bort brandväggsregel](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |Server |Tar bort brandväggsregler på servernivå |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Brandväggsregel på servernivå jämfört med en brandväggsregel på databasnivå
F. Bör användare av en databas är helt isolerade från en annan databas?   
  Om Ja, kan du bevilja åtkomst med hjälp av brandväggsregler på databasnivå. Detta förhindrar med brandväggen på servernivå regler som tillåter åtkomst genom brandväggen till alla databaser, dina försvar djup.   
 
F. Användare på IP-adressen måste ha åtkomst till alla databaser?   
  Använd brandväggsregler på servernivå för att minska antalet gånger som du måste konfigurera brandväggens regler.   

F. Har du åtkomst via Azure portal, PowerShell eller REST-API genom att den person eller grupp att konfigurera brandväggsregler endast?   
  Du måste använda brandväggsregler på servernivå. Brandväggsregler på databasnivå kan bara konfigureras med hjälp av Transact-SQL.  

F. Är den person eller grupp att konfigurera brandväggsregler som är förbjudna att ha på hög nivå behörighet på databasnivå?   
  Använd brandväggsregler på servernivå. Konfigurera brandväggsregler på databasnivå med hjälp av Transact-SQL, måste minst `CONTROL DATABASE` behörighet på databasnivå.  

F. Är den person eller grupp konfigurering eller granskning brandväggsregler, centralt hantera brandväggsregler för många (kanske 100-tal) databaser?   
  Det här valet beror på dina behov och miljö. Brandväggsregler på servernivå kan vara enklare att konfigurera, men skript kan konfigurera regler på databasnivå. Och även om du använder brandväggsregler på servernivå kan du behöva granska database-brandväggsregler, att se om användare med `CONTROL` -behörighet på databasen har skapat brandväggsregler på databasnivå.   

F. Kan jag använda en blandning av båda brandväggsregler på servernivå och databasnivå?   
  Ja. Vissa användare, till exempel Administratörer behöva brandväggsregler på servernivå. Andra användare, t.ex användare av ett databasprogram, behöva brandväggsregler på databasnivå.   

## <a name="troubleshooting-the-database-firewall"></a>Felsöka databasbrandväggen
Tänk på följande om åtkomsten till Microsoft Azure SQL Database-tjänsten inte fungerar som förväntat:

* **Lokal brandväggskonfiguration:** Innan din dator kan komma åt Azure SQL Database kan du behöva skapa ett brandväggsundantag på din dator för TCP-port 1433. Om du skapar anslutningar inom gränsen för Azure-molnet kan du behöva öppna ytterligare portar. Mer information finns i den **SQL Database: utanför eller inuti** delen av [portar utöver 1433 för ADO.NET 4.5 och SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* **NAT (Network Address Translation):** På grund av NAT kanske IP-adressen som används av din dator för att ansluta till Azure SQL Database inte är samma som IP-adressen som visas i datorns IP-konfigurationsinställningar. Du kan visa IP-adressen som din dator använder genom att ansluta till Azure, logga in på portalen och gå till fliken **Konfigurera** på servern som är värd för databasen. **Aktuell klient-IP-adress** visas under avsnittet **Tillåtna IP-adresser**. Klicka på **Lägg till** i **Tillåtna IP-adresser** så att den här datorn kan ansluta till servern.
* **Ändringarna i listan över tillåtna adresser har inte börjat gälla ännu:** Det kan ta upp till fem minuter innan ändringarna i brandväggskonfigurationen för Azure SQL Database börjar gälla.
* **Inloggningen har inte behörighet eller så har ett felaktigt lösenord använts:** Om en inloggning inte har behörighet på Azure SQL Database-servern eller om lösenordet som användes är fel så nekas anslutningen till Azure SQL Database-servern. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser. Mer information om hur du förbereder inloggningar finns i [hantera databaser, inloggningar och användare i Azure SQL Database](sql-database-manage-logins.md).
* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:
  
  * Fråga din Internetleverantör efter IP-adressintervallet som tilldelats klientdatorer som ansluter till Azure SQL Database-servern och lägg sedan till IP-adressintervallet som en brandväggsregel.
  * Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

## <a name="next-steps"></a>Nästa steg

- En Snabbstart om hur du skapar en databas och en brandväggsregel på servernivå finns [skapa en Azure SQL database](sql-database-get-started-portal.md).
- Hjälp med att ansluta till en Azure SQL-databas från öppen källkod eller program från tredje part finns i [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snabbstart med kodexempel för att ansluta klienter till SQL Database).
- Information om ytterligare portar som du kan behöva öppna finns i den **SQL Database: utanför eller inuti** delen av [portar utöver 1433 för ADO.NET 4.5 och SQL-databas](sql-database-develop-direct-route-ports-adonet-v12.md)
- En översikt över Azure SQL Database-säkerhet finns i [skydda databasen](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
