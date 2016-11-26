# Översikt
## [Vad är SQL Database?](sql-database-technical-overview.md)
### [Tjänstnivåer](sql-database-service-tiers.md)
### [Databastransaktionsenheter](sql-database-what-is-a-dtu.md)
### [Benchmark-översikt för DTU](sql-database-benchmark-overview.md)
### [Resursbegränsningar](sql-database-resource-limits.md)
### [Funktioner](sql-database-features.md)
### [Vanliga frågor om SQL Database](sql-database-faq.md)
## Fördelar
### [Lärdomar och tips](sql-database-learn-and-adapt.md)
### [Skalningar i farten](sql-database-scale-on-the-fly.md)
### [Bygger appar för flera innehavare](sql-database-build-multi-tenant-apps.md)
### [Säkrar och skyddar](sql-database-helps-secures-and-protects.md)
### [Fungerar i din miljö](sql-database-works-in-your-environment.md)
## Jämförelser
### [SQL Database jämfört med SQL på en virtuell dator](sql-database-paas-vs-sql-server-iaas.md)
### [T-SQL-skillnader](sql-database-transact-sql-information.md)
### [SQL kontra NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [SQL Database-verktyg](sql-database-manage-overview.md)
## [SQL Database-självstudier](sql-database-explore-tutorials.md)
## [Snabbstarter för lösningar](sql-database-solution-quick-starts.md)
## Säkerhet
### [Azure Security Center för Azure SQL Database](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL-säkerhetscenter](https://msdn.microsoft.com/library/azure/bb510589)
# Kom igång
## Databaser och servrar
### Enkla databaser
#### Läs mer
##### [Självstudie för Azure Portal: Skapa en Azure SQL-databas med Azure Portal](sql-database-get-started.md)
#### Gör följande
##### [Självstudie för Azure Portal: Skapa en Azure SQL-databas med Azure Portal](sql-database-get-started.md)
##### [Självstudie för Azure Portal: Skapa en Azure SQL-databas med PowerShell](sql-database-get-started-powershell.md)
##### [Självstudie för Azure Portal: Skapa en Azure SQL-databas med C#](sql-database-get-started-csharp.md)
### Elastic Database-pooler
#### Läs mer
##### [Elastic Database-pooler](sql-database-elastic-pool.md)
##### [När du ska använda en Elastic Database-pool](sql-database-elastic-pool-guidance.md)
##### [Priser för elastisk pool](sql-database-elastic-pool-price.md)
#### Gör följande
##### [Skapa med Azure Portal](sql-database-elastic-pool-create-portal.md)
##### [Skapa med PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Skapa med C#](sql-database-elastic-pool-create-csharp.md)
### Skala ut
#### Läs mer
##### [Skala ut](sql-database-elastic-scale-introduction.md)
##### [Elastisk skalning](sql-database-elastic-scale-get-started.md)
##### [Skapa skalbara molndatabaser](sql-database-elastic-database-client-library.md)
##### [Korsdatabasjobb](sql-database-elastic-jobs-overview.md)
##### [Vanliga frågor och svar om elastiska verktyg](sql-database-elastic-scale-faq.md)
##### [Ordlista för verktyg i elastiska databaser](sql-database-elastic-scale-glossary.md)
##### [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md)
#### Gör följande
##### [Elastiska jobb](sql-database-elastic-jobs-getting-started.md)
##### [Rapporter mellan databaser](sql-database-elastic-query-getting-started.md)
##### [Frågor mellan databaser](sql-database-elastic-query-getting-started-vertical.md)
##### [Avinstallera elastiska jobb](sql-database-elastic-jobs-uninstall.md)
## Migrera och flytta data
### Läs mer
#### [Migrera en databas](sql-database-cloud-migrate.md)
#### [Transaktionsreplikering](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Datasynkronisering](sql-database-get-started-sql-data-sync.md)
#### [Kopiera en SQL-databas](sql-database-copy.md)
### Gör följande
#### Fastställ databaskompatibilitet
##### [Verktyg för SQL-paket](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Åtgärda kompatibilitetsproblem med databasen
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure migreringsguide](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [SQL Server Management Studio-migreringsguide](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Kopiera en databas
##### [Kopiera med Azure Portal](sql-database-copy-portal.md)
##### [Kopiera med PowerShell](sql-database-copy-powershell.md)
##### [Kopiera med T-SQL](sql-database-copy-transact-sql.md)
#### Exportera databasen till en BACPAC-fil
##### [Azure-portal](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [Verktyg för SQL-paket](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Importera databasen från en BACPAC-fil
##### [Azure-portal](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [Verktyg för SQL-paket](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Läsa in från CSV-filen med BCP](sql-database-load-from-csv-with-bcp.md)
## Autentisera och bevilja åtkomst
### Läs mer
#### [Översikt](sql-database-security.md)
#### [Riktlinjer för säkerhet](sql-database-security-guidelines.md)
#### [Hantera inloggningar](sql-database-manage-logins.md)
### Gör följande
### [Självstudie: SQL-autentisering och åtkomst](sql-database-get-started-security.md)
## Säkra och skydda data
### Läs mer
#### Granskning
##### [Granska](sql-database-auditing-get-started.md)
##### [Stöd för tidigare klientversioner och IP-slutpunktsändringar för granskning](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Hotidentifiering](sql-database-threat-detection-get-started.md)
#### Kryptera data
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Windows-certifikatarkiv](sql-database-always-encrypted.md)
##### [Transparent datakryptering](https://msdn.microsoft.com/library/azure/dn948096)
##### [Kolumnkryptering](https://msdn.microsoft.com/library/azure/ms179331)
#### Maskera data
##### Dynamisk datamaskning
###### [Azure-portal](sql-database-dynamic-data-masking-get-started.md)
### Gör följande
#### [Dynamisk datamaskning i Azure-portalen](sql-database-dynamic-data-masking-get-started.md)
## Verksamhetskontinuitet
### Läs mer
#### [Översikt](sql-database-business-continuity.md)
#### [Säkerhetskopior av databasen](sql-database-automated-backups.md)
#### [Långsiktig kvarhållning](sql-database-long-term-retention.md) 
#### [Återställning av databas med hjälp av säkerhetskopior](sql-database-recovery-using-backups.md)
#### [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md)
#### [Designscenarier för företagskontinuitet](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Strategier för haveriberedskap med elastiska pooler](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Löpande uppgraderingar](sql-database-manage-application-rolling-upgrade.md)
### Gör följande
#### Återställa borttagen databas
##### [Azure-portal](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Återställning från tidpunkt
##### [Azure-portal](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Geo-återställ databaser
##### [Azure-portal](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Enkel tabell](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Återställa från ett strömavbrott i ett datacenter](sql-database-disaster-recovery.md)
#### [Utföra programåterställningstest](sql-database-disaster-recovery-drills.md)
### Replikera databaser
#### [Översikt över aktiv geo-replikering](sql-database-geo-replication-overview.md)
#### Konfigurera aktiv geo-replikering
##### [Azure-portal](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Redundans med aktiv geo-replikering
##### [Azure-portal](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Apputveckling
### Läs mer
#### [Översikt över utveckling av databasprogram](sql-database-develop-overview.md)
#### [Anslutningsbibliotek](sql-database-libraries.md)
#### [SaaS-program för flera innehavare](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Använda andra portar än 1433 för ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Hämta de värden som krävs för att autentisera ett program](sql-database-client-id-keys.md)
### Gör följande
#### Ansluta ett program
##### [NET](sql-database-develop-dotnet-simple.md)
##### [C och C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Anslut med Visual Studio](sql-database-connect-query.md)
#### [Skapa ett klientprogram](https://www.microsoft.com/sql-server/developer-get-started)
#### [Arbeta med felmeddelanden](sql-database-develop-error-messages.md)
### Kundimplementeringar av programvara som en tjänst
#### [Daxko/CSI Software](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Databasutveckling
### Läs mer
#### Temporala tabeller
##### [Temporala tabeller](sql-database-temporal-tables.md)
##### [Principer för kvarhållning](sql-database-temporal-tables-retention-policy.md)
#### [JSON-data](sql-database-json-features.md)
#### [Minnesintern](sql-database-in-memory.md)
### Gör följande
#### [SQL Server-utveckling](https://msdn.microsoft.com/library/ms179422.aspx)
## Övervakning och justering
### Läs mer
#### [Enkla databaser](sql-database-single-database-monitor.md)
#### [Översikt över SQL Database Advisor](sql-database-advisor.md)
#### [Vägledning för enkel databas](sql-database-performance-guidance.md)
#### [Arbetsbelastningsinformation i Azure-portalen](sql-database-performance.md)
#### [Använda batchbearbetning](sql-database-use-batching-to-improve-performance.md)
## SQL Database V11
### [Webb- och företagsversioner sunset](sql-database-web-business-sunset-faq.md)
### [Tjänstnivå-rådgivaren](sql-database-service-tier-advisor.md)
### [Utvärderingsverktyg för elastiska pooler](sql-database-elastic-pool-database-assessment-powershell.md)
### [Uppgradera till V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Uppgradera med Azure Portal](sql-database-upgrade-server-portal.md)
#### [Uppgradera med PowerShell](sql-database-upgrade-server-powershell.md)
#### [Prisnivårekommendationer](sql-database-service-tier-advisor.md)
# Gör så här för att
## Skapa och hantera
### Servrar och databaser
#### [Enkla databaser](sql-database-manage-portal.md)
#### [Azure-portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Elastic Database-pooler
#### [Skapa med Azure Portal](sql-database-elastic-pool-create-portal.md)
#### [Skapa med PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [Skapa med C#](sql-database-elastic-pool-create-csharp.md)
#### [Hantera med Azure Portal](sql-database-elastic-pool-manage-portal.md)
#### [Hantera med PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [Hantera med C#](sql-database-elastic-pool-manage-csharp.md)
#### [Hantera med T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Shardade databaser
#### [Använd karthanteraren för shard](sql-database-elastic-scale-shard-map-management.md)
#### [Dela sammanslagen säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Arbeta med Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Använda Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
#### [Säkerhet på radnivå för flera innehavare](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Hantera autentiseringsuppgifter](sql-database-elastic-scale-manage-credentials.md)
#### [Distribuera en tjänst för att dela/sammanslå](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Lägg till en shard](sql-database-elastic-scale-add-a-shard.md)
#### [Korrigera shard-kartproblem med RecoveryManager-klassen](sql-database-elastic-database-recovery-manager.md)
###  Autentisering
#### [Azure AD-autentisering](sql-database-aad-authentication.md)
#### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
### Brandväggsregler
#### [Azure Portal](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [REST-API](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Jobb och automatisering
#### [Tjänstinstallation](sql-database-elastic-jobs-service-installation.md)
#### [Skapa och hantera elastiska jobb i Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [Skapa och hantera elastiska jobb med PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Uppgradera klientbibliotek](sql-database-elastic-scale-upgrade-client-library.md)
#### [Hantera SQL-databaser med Azure Automation-tjänsten](sql-database-manage-automation.md)
### [Brandvägg](sql-database-firewall-configure.md)
## Kryptera data
### [Alltid krypterad översikt](sql-database-always-encrypted.md)
### [Transparent datakryptering](https://msdn.microsoft.com/library/azure/dn948096)
### [Kolumnkryptering](https://msdn.microsoft.com/library/azure/ms179331)
## Migrera
### Fastställa kompatibilitet
#### [Verktyg för SQL-paket](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Åtgärda kompatibilitetsproblem
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure migreringsguide](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Använd guiden för SQL Server Management Studio-migrering](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Använd transaktionsreplikering](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Migrera befintliga utskalade databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
## Övervaka och finjustera
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Efterlevnadsnivåer](sql-database-compatibility-level-query-performance-130.md)
### [Prestandaräknare för karthanteraren för shard](sql-database-elastic-database-perf-counters.md)
### [Tips vid prestandajustering](sql-database-troubleshoot-performance.md)
### Ändra tjänst- och prestandanivåer
#### [Använd Azure Portal](sql-database-scale-up.md)
#### [Använda PowerShell](sql-database-scale-up-powershell.md)
### [Skapa aviseringar](sql-database-insights-alerts-portal.md)
### Minnesintern OLTP
#### [Använda minnesintern OLTP](sql-database-in-memory-oltp-migration.md)
#### [Övervaka minnesintern OLTP-lagring](sql-database-in-memory-oltp-monitoring.md)
### Query Store
#### [Övervakning av prestanda med hjälp av Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Query Store-användningsscenarier](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Använda Query Store](sql-database-operate-query-store.md)
### Utökade händelser
#### [Utökade händelser](sql-database-xevent-db-diff-from-svr.md)
#### [Händelsefilens målkod](sql-database-xevent-code-event-file.md)
#### [Ringbuffertens målkod](sql-database-xevent-code-ring-buffer.md)
## Flytta data
### [Kopiera en SQL-databas](sql-database-copy.md)
#### [Azure-portal](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exportera databasen till en BACPAC-fil
#### [Azure-portal](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Verktyg för SQL-paket](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importera databasen från en BACPAC-fil
#### [Azure-portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Verktyg för SQL-paket](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Läsa in från CSV-filen med BCP](sql-database-load-from-csv-with-bcp.md)
### [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md)
## Fråga
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Frågor med flera shards](sql-database-elastic-scale-multishard-querying.md)
### Frågor över flera databaser
#### [Översikt](sql-database-elastic-query-overview.md)
#### [Frågor med olika scheman mellan databaser](sql-database-elastic-query-vertical-partitioning.md)
#### [Rapportering mellan databaser](sql-database-elastic-query-horizontal-partitioning.md)
#### [Distribuerade transaktioner över molndatabaser](sql-database-elastic-transactions-overview.md)
## Återställ
### Återställa borttagen databas
#### [Azure-portal](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Återställning till tidpunkt
#### [Azure-portal](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Geo-återställning
#### [Azure-portal](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Enkel tabell](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Återställa från ett strömavbrott i ett datacenter](sql-database-disaster-recovery.md)
### [Utföra programåterställningstest](sql-database-disaster-recovery-drills.md)
## Replikera
### [Översikt över aktiv geo-replikering](sql-database-geo-replication-overview.md)
### Konfigurera
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Redundans
#### [Azure-portal](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Felsöka
### [Anslutningsproblem](sql-database-troubleshoot-common-connection-issues.md)
### [Tillfälligt anslutningsfel](sql-database-troubleshoot-connection.md)
### [Diagnostisera och förhindra](sql-database-connectivity-issues.md)
### [Behörigheter](sql-database-troubleshoot-permissions.md)
### [Flytta databaser](sql-database-troubleshoot-moving-data.md)


# Referens
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [PowerShell – Klassisk](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Cmdlet:ar för Azure SQL-databas](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
## [SQL Server-cmdletar](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## Bibliotek för SQL Database-hantering
### [Biblioteksreferens för SQL Database-hantering](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Hämta bibliotekspaketet för SQL Database-hanteringen](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server-drivrutiner](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Resurser
## [Prissättning](https://azure.microsoft.com/pricing/details/sql-database/)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Server-verktyg](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)




<!--HONumber=Nov16_HO4-->


