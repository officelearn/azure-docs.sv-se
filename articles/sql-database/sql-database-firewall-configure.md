---
title: "Översikt över SQL Database-brandväggsregler | Microsoft Docs"
description: "Lär dig hur du konfigurerar en SQL Database-brandvägg med brandväggsregler på servernivå och databasnivå för att hantera åtkomst."
keywords: "databasbrandvägg"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/23/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: b5417e02f2c4e50c89afcfa007ccdd208775f374


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Översikt över Azure SQL Database-brandväggsregler 
> [!div class="op_single_selector"]
> * [Översikt](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST-API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Microsoft Azure SQL Database tillhandahåller en relationsdatabastjänst för Azure och andra Internetbaserade program. För att skydda dina data förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet. Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brandväggsregler på server- och databasnivå.

* **Brandväggsregler på servernivå:** Dessa regler gör att klienterna kan komma åt hela Azure SQL-servern, det vill säga alla databaser som finns på samma logiska server. Dessa regler lagras i **huvuddatabasen**. Brandväggsregler på servernivå kan konfigureras via portalen eller med hjälp av Transact-SQL-instruktioner.
* **Brandväggsregler på databasnivå:** Dessa regler gör att klienterna kan komma åt enskilda databaser på din Azure SQL Database-server. Du kan skapa dessa regler för varje databas. Reglerna lagras i de enskilda databaserna. (Du kan skapa brandväggsregler på databasnivå för **huvuddatabasen**.) Med dessa regler kan du begränsa åtkomsten till vissa (säkra) databaser på samma logiska server. Brandväggsregler på databasnivå kan endast konfigureras med hjälp av Transact-SQL-instruktioner.

**Rekommendation:** Microsoft rekommenderar att du använder brandväggsregler på databasnivå när det är möjligt för att förbättra säkerheten och göra databasen mer portabel. Använd brandväggsregler på servernivå för administratörer och om du har många databaser med samma åtkomstkrav och du inte vill lägga tid på att konfigurera varje databas individuellt.

> [!Note]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md).
>

## <a name="firewall-overview"></a>Översikt över brandväggar
Till en början blockeras all Transact-SQL-åtkomst till din Azure SQL-server av brandväggen. För att börja använda din Azure SQL-server måste du gå till Azure Portal och ange en eller flera brandväggsregler på servernivå som ger åtkomst till din Azure SQL-server. Använd brandväggsreglerna för att ange vilka IP-adressintervall från Internet som tillåts samt för att ange om Azure-program kan försöka ansluta till din Azure SQL Database-server eller inte.

Om du bara vill bevilja åtkomst till en av databaserna på din Azure SQL-server måste du skapa en regel på databasnivå för databasen. Ange ett IP-adressintervall för brandväggsregeln för databasen som ligger utanför det IP-adressintervall som angetts i brandväggsregeln på servernivå och kontrollera att klientens IP-adress ligger inom intervallet som angetts i regeln på databasnivå.

Anslutningsförsök från Internet och Azure måste först passera brandväggen innan de kan nå din Azure SQL-server eller SQL-databas, som du ser i följande diagram:

   ![Diagram över brandväggskonfigurationen.][1]

## <a name="connecting-from-the-internet"></a>Ansluta från Internet
När en dator försöker ansluta till din databasserver från Internet kontrollerar brandväggen först vilken IP-adress som begäran kommer från och jämför den med den fullständiga uppsättningen brandväggsregler:

* Om IP-adressen för begäran ligger inom ett intervall som anges i brandväggsreglerna på servernivå godkänns anslutningen till din Azure SQL Database-server.
* Om IP-adressen för begäran inte ligger inom något av intervallen som anges i brandväggsregeln på servernivå kontrollerar brandväggen brandväggsreglerna på databasnivå. Om IP-adressen för begäran ligger inom ett av intervallen som angetts i brandväggsreglerna på databasnivå godkänns anslutningen bara för databasen som har en matchande regel på databasnivå.
* Om IP-adressen för begäran inte ligger inom intervallen som angetts i brandväggsreglerna på server- eller databasnivå misslyckas anslutningsbegäran.

> [!NOTE]
> För att kunna komma åt Azure SQL Database från din lokala dator måste du kontrollera att brandväggen i nätverket och på den lokala datorn tillåter utgående kommunikation på TCP-port 1433.
> 

## <a name="connecting-from-azure"></a>Ansluta från Azure
Om du vill tillåta att program från Azure ansluter till din Azure SQL-server måste Azure-anslutningar vara aktiverade. När ett program från Azure försöker ansluta till databasservern kontrollerar brandväggen att Azure-anslutningar tillåts. En brandväggsinställning med start- och slutadresser som är 0.0.0.0 anger att dessa anslutningar tillåts. Om anslutningsförsöket inte tillåts kommer begäran inte att nå Azure SQL Database-servern.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

> [!NOTE]
>  Mer information finns i avsnittet **SQL Database: Outside vs inside** (SQL Database: utanför eller inuti) i [Ports beyond 1433 for ADO.NET 4.5 and SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md) (Portar utöver 1433 för ADO.NET 4.5 och SQL Database)
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>Skapa den första brandväggsregeln på servernivå
Du kan skapa den första brandväggsinställningen på servernivå via [Azure Portal](https://portal.azure.com/) eller genom programmering med hjälp av REST-API:et eller Azure PowerShell. Efterföljande brandväggsregler på servernivå kan skapas och hanteras med hjälp av dessa metoder samt via Transact-SQL. För att förbättra prestanda cachelagras brandväggsregler på servernivå tillfälligt på databasnivå. Information om hur du uppdaterar cacheminnet finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Mer information om brandväggsregler på servernivå finns i [How to: Configure an Azure SQL server firewall using the Azure portal](sql-database-configure-firewall-settings.md) (Så här gör du: Konfigurera en Azure SQL-serverbrandvägg med hjälp av Azure Portal).

## <a name="creating-database-level-firewall-rules"></a>Skapa brandväggsregler på databasnivå
När du har konfigurerat den första brandväggen på servernivå kanske du vill begränsa åtkomsten till vissa databaser. Om du anger ett IP-adressintervall i brandväggsregeln på databasnivå som ligger utanför det intervall som angetts i brandväggsregeln på servernivå kan endast klienter som har IP-adresser som ligger i intervallet som angetts för databasnivån komma åt databasen. Du kan skapa upp till 128 brandväggsregler på databasnivå för en databas. Brandväggsregler på databasnivå för huvud- och användardatabaser kan skapas och hanteras via Transact-SQL. Mer information om hur du konfigurerar brandväggsregler på databasnivå finns i [sp_set_database_firewall_rule (Azure SQL-databaser)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Hantera brandväggsregler via programmering
Förutom att använda Azure Portal kan du hantera brandväggsregler via programmering med hjälp av Transact-SQL, REST-API:et och Azure PowerShell. Följande tabeller beskriver den uppsättning kommandon som är tillgänglig för varje metod.

### <a name="transact-sql"></a>Transact-SQL
| Katalogvy eller lagrad procedur | Nivå | Beskrivning |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visar de aktuella brandväggsreglerna på servernivå |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Skapar eller uppdaterar brandväggsregler på servernivå |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Tar bort brandväggsregler på servernivå |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databas |Visar de aktuella brandväggsreglerna på databasnivå |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databas |Skapar eller uppdaterar brandväggsreglerna på databasnivå |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databaser |Tar bort brandväggsregler på databasnivå |

### <a name="rest-api"></a>REST API
| API | Nivå | Beskrivning |
| --- | --- | --- |
| [Visa en lista med brandväggsregler](https://msdn.microsoft.com/library/azure/dn505715.aspx) |Server |Visar de aktuella brandväggsreglerna på servernivå |
| [Skapa brandväggsregel](https://msdn.microsoft.com/library/azure/dn505712.aspx) |Server |Skapar eller uppdaterar brandväggsregler på servernivå |
| [Konfigurera brandväggsregel](https://msdn.microsoft.com/library/azure/dn505707.aspx) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Ta bort brandväggsregel](https://msdn.microsoft.com/library/azure/dn505706.aspx) |Server |Tar bort brandväggsregler på servernivå |

### <a name="azure-powershell"></a>Azure PowerShell
| Cmdlet | Nivå | Beskrivning |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |Server |Returnerar de aktuella brandväggsreglerna på servernivå |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |Server |Skapar en ny brandväggsregel på servernivå |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |Server |Tar bort brandväggsregler på servernivå |

> [!NOTE]
> Det kan ta upp till fem minuter innan ändringarna av brandväggsinställningarna börjar gälla.
> 
> 

## <a name="troubleshooting-the-database-firewall"></a>Felsöka databasbrandväggen
Tänk på följande om åtkomsten till Microsoft Azure SQL Database-tjänsten inte fungerar som förväntat:

* **Lokal brandväggskonfiguration:** Innan din dator kan komma åt Azure SQL Database kan du behöva skapa ett brandväggsundantag på din dator för TCP-port 1433. Om du skapar anslutningar inom gränsen för Azure-molnet kan du behöva öppna ytterligare portar. Mer information finns i avsnittet **V12 of SQL Database: Outside vs inside** (V12 av SQL Database: utanför eller inuti) i [Ports beyond 1433 for ADO.NET 4.5 and SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md) (Portar utöver 1433 för ADO.NET 4.5 och SQL Database V12)
* **NAT (Network Address Translation):** På grund av NAT kanske IP-adressen som används av din dator för att ansluta till Azure SQL Database inte är samma som IP-adressen som visas i datorns IP-konfigurationsinställningar. Du kan visa IP-adressen som din dator använder genom att ansluta till Azure, logga in på portalen och gå till fliken **Konfigurera** på servern som är värd för databasen. **Aktuell klient-IP-adress** visas under avsnittet **Tillåtna IP-adresser**. Klicka på **Lägg till** i **Tillåtna IP-adresser** så att den här datorn kan ansluta till servern.
* **Ändringarna i listan över tillåtna adresser har inte börjat gälla ännu:** Det kan ta upp till fem minuter innan ändringarna i brandväggskonfigurationen för Azure SQL Database börjar gälla.
* **Inloggningen har inte behörighet eller så har ett felaktigt lösenord använts:** Om en inloggning inte har behörighet på Azure SQL Database-servern eller om lösenordet som användes är fel så nekas anslutningen till Azure SQL Database-servern. En brandväggsinställning ger endast klienter möjlighet att försöka ansluta till din server. Varje klient måste fortfarande ange nödvändiga säkerhetsreferenser. Mer information om hur du förbereder inloggningar finns i avsnittet Managing Databases, Logins, and Users in Azure SQL Database (Hantera databaser, inloggningar och användare i Azure SQL Database).
* **Dynamisk IP-adress:** Om du har en Internetanslutning med dynamisk IP-adressering och du har problem med att passera brandväggen kan du prova någon av följande lösningar:
  
  * Fråga din Internetleverantör efter IP-adressintervallet som tilldelats klientdatorer som ansluter till Azure SQL Database-servern och lägg sedan till IP-adressintervallet som en brandväggsregel.
  * Använd statisk IP-adressering i stället för dina klientdatorer och lägg sedan till IP-adresserna som brandväggsregler.

## <a name="next-steps"></a>Nästa steg
Artiklar som beskriver hur du skapar brandväggsregler på server- och databasnivå finns här:

* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av Azure Portal](sql-database-configure-firewall-settings.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå och databasnivå med hjälp av T-SQL](sql-database-configure-firewall-settings-tsql.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av REST-API:et](sql-database-configure-firewall-settings-rest.md)

En självstudiekurs om hur du skapar en databas finns i [Skapa en SQL-databas på bara några minuter med hjälp av Azure Portal](sql-database-get-started.md).
Hjälp med att ansluta till en Azure SQL-databas från öppen källkod eller program från tredje part finns i [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snabbstart med kodexempel för att ansluta klienter till SQL Database).
Information om hur du navigerar till databaser finns i [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Hantera databasåtkomst och inloggningssäkerhet).

## <a name="additional-resources"></a>Ytterligare resurser
* [Skydda en databas](sql-database-security.md)
* [Security Center för SQL Server Database Engine och Azure SQL Database](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png



<!--HONumber=Dec16_HO1-->


