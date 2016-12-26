# Översikt
## [Vad är SQL Database?](sql-database-technical-overview.md)
### [Tjänstnivåer](sql-database-service-tiers.md)
### [DTU:er och eDTU:er](sql-database-what-is-a-dtu.md)
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
### Läs mer
#### [Servrar](sql-database-server-overview.md)
#### [Enkla databaser](sql-database-overview.md)
#### [Flera databaser](sql-database-elastic-scale-introduction.md)
##### Mappning av klienter
###### [Elastiskt klientbibliotek](sql-database-elastic-database-client-library.md)
###### [Karthanterare för shard](sql-database-elastic-scale-shard-map-management.md)
###### [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md)
###### [Hantera autentiseringsuppgifter](sql-database-elastic-scale-manage-credentials.md)
###### [Frågor med flera shards](sql-database-elastic-scale-multishard-querying.md)
##### Elastiska pooler (resurspooler)
###### [Vad är en elastisk pool?](sql-database-elastic-pool.md)
###### [När elastisk pooler används](sql-database-elastic-pool-guidance.md)
###### [Priser för elastisk pool](sql-database-elastic-pool-price.md)
##### Shardade databaser
###### [Ordlista över elastiska verktyg](sql-database-elastic-scale-glossary.md)
###### [Flytta data mellan shard](sql-database-elastic-scale-overview-split-and-merge.md)
###### [Vanliga frågor och svar om elastiska verktyg](sql-database-elastic-scale-faq.md)
##### Elastisk förfrågan (frågor över flera databaser)
###### [Vad är en elastisk förfrågan?](sql-database-elastic-query-overview.md)
##### Elastiska transaktioner (distribuerade transaktioner)
###### [Transaktioner över molndatabaser](sql-database-elastic-transactions-overview.md)
##### Elastiska jobb (jobb över flera databaser)
###### [Vad är ett elastiskt jobb?](sql-database-elastic-jobs-overview.md)
#### [Använda Azure RemoteApp för att ansluta till SQL Database](sql-database-ssms-remoteapp.md)
#### [Hantera SQL-databaser med Azure Automation-tjänsten](sql-database-manage-automation.md)
### Gör följande
#### [Skapa en enskild databas med Azure-portalen](sql-database-get-started.md)
#### [Skapa en enskild databas med PowerShell](sql-database-get-started-powershell.md)
#### [Skapa en enskild databas med C#](sql-database-get-started-csharp.md)
#### [Skapa delat program](sql-database-elastic-scale-get-started.md)
#### [Flytta data mellan shard](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Kom igång med elastiska jobb](sql-database-elastic-jobs-getting-started.md)
#### [Kom igång med elastiska förfrågningar](sql-database-elastic-query-getting-started-vertical.md)
#### [Skapa rapporter med hjälp av elastisk förfrågan](sql-database-elastic-query-getting-started.md)
#### [Frågedatabaser med olika scheman](sql-database-elastic-query-vertical-partitioning.md)
#### [Rapportering över utskalade databaser](sql-database-elastic-query-horizontal-partitioning.md)
## Migrera och flytta data
### Läs mer
#### [Migrera en databas](sql-database-cloud-migrate.md)
#### [Transaktionsreplikering](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Datasynkronisering](sql-database-get-started-sql-data-sync.md)
#### [Kopiera en SQL-databas](sql-database-copy.md)
## Brandväggsregler, autentisering och auktorisering
### Läs mer
#### [Översikt](sql-database-security.md)
#### [Riktlinjer för säkerhet](sql-database-security-guidelines.md)
#### [Brandvägg](sql-database-firewall-configure.md)
#### [Hantera inloggningar](sql-database-manage-logins.md)
### Gör följande
#### [SQL autentisering och auktorisering](sql-database-get-started-security.md)
## Säkra och skydda data
### Läs mer
#### Granskning
##### [Granska](sql-database-auditing-get-started.md)
##### [Stöd för tidigare klientversioner och IP-slutpunktsändringar för granskning](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Hotidentifiering](sql-database-threat-detection-get-started.md)
#### Kryptera data
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Transparent datakryptering](https://msdn.microsoft.com/library/azure/dn948096)
##### [Kolumnkryptering](https://msdn.microsoft.com/library/azure/ms179331)
#### Maskera data
##### Dynamisk datamaskning
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### Gör följande
#### [Dynamisk datamaskning i Azure Portal](sql-database-dynamic-data-masking-get-started.md)
##### [Alltid krypterad med Windows certifikatarkiv](sql-database-always-encrypted.md)
## Verksamhetskontinuitet
### Läs mer
#### [Översikt](sql-database-business-continuity.md)
#### [Säkerhetskopior av databasen](sql-database-automated-backups.md)
#### [Långsiktig kvarhållning](sql-database-long-term-retention.md)
#### [Återställning av databas med hjälp av säkerhetskopior](sql-database-recovery-using-backups.md)
#### [Återställa en enskild tabell](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Återställa från ett strömavbrott i ett datacenter](sql-database-disaster-recovery.md)
#### [Autentiseringskrav för haveriberedskap](sql-database-geo-replication-security-config.md)
#### [Designscenarier för företagskontinuitet](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Strategier för haveriberedskap med elastiska pooler](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Löpande uppgraderingar](sql-database-manage-application-rolling-upgrade.md)
#### [Utföra programåterställningstest](sql-database-disaster-recovery-drills.md)
#### [Översikt över aktiv geo-replikering](sql-database-geo-replication-overview.md)
### Gör följande
#### [Kom igång med SQL Database säkerhetskopiering och återställning](sql-database-get-started-backup-recovery.md)
## Apputveckling
### Läs mer
#### [Översikt över utveckling av databasprogram](sql-database-develop-overview.md)
#### [Anslutningsbibliotek](sql-database-libraries.md)
#### [SaaS-program för flera innehavare](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Skala SaaS-program för flera innehavare med säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Använda andra portar än 1433 för ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Hämta de värden som krävs för att autentisera ett program](sql-database-client-id-keys.md)
### Gör följande
#### Ansluta ett program
##### [.NET](sql-database-develop-dotnet-simple.md)
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
#### [Använda Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Använd klientbibliotek med Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Kundimplementeringar
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
#### [Minnesintern optimering](sql-database-in-memory.md)
### Gör följande
#### [SQL Server-utveckling](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Använda minnesintern OLTP](sql-database-in-memory-oltp-migration.md)
## Övervakning och justering
### Läs mer
#### [Enkla databaser](sql-database-single-database-monitor.md)
#### [Översikt över SQL Database Advisor](sql-database-advisor.md)
#### [Vägledning för enkel databas](sql-database-performance-guidance.md)
#### [Arbetsbelastningsinformation i Azure Portal](sql-database-performance.md)
#### [Använda batchbearbetning](sql-database-use-batching-to-improve-performance.md)
#### [Utökade händelser](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Webb- och företagsversioner sunset](sql-database-web-business-sunset-faq.md)
### [Tjänstnivå-rådgivaren](sql-database-service-tier-advisor.md)
### [Utvärderingsverktyg för elastiska pooler](sql-database-elastic-pool-database-assessment-powershell.md)
### [Uppgradera till V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Uppgradera med Azure Portal](sql-database-upgrade-server-portal.md)
#### [Uppgradera med PowerShell](sql-database-upgrade-server-powershell.md)
# Gör så här för att
## Skapa och hantera
### [Hantera en SQL-databas med hjälp av Azure Portal](sql-database-manage-portal.md)
### [Hantera en SQL-databas med hjälp av PowerShell](sql-database-manage-powershell.md)
### [Hantera en SQL-databas med hjälp av SSMS](sql-database-manage-azure-ssms.md)
### Servrar
#### [Skapa servrar](sql-database-create-servers.md)
#### [Visa eller uppdatera serverinställningar](sql-database-view-update-server-settings.md)
### Enkla databaser
#### [Skapa enkla databaser](sql-database-create-databases.md)
#### [Visa eller uppdatera databasinställningar](sql-database-view-update-database-settings.md)
### Brandväggsregler
#### [Skapa brandväggsregler med hjälp av Azure-portalen](sql-database-configure-firewall-settings.md)
#### [Skapa brandväggsregler med hjälp av PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Skapa brandväggsregler med hjälp av REST-API](sql-database-configure-firewall-settings-rest.md)
#### [Skapa brandväggsregler med hjälp av T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Flera databaser
#### [Uppgradera klientbibliotek i klientprogram](sql-database-elastic-scale-upgrade-client-library.md)
#### Shardade databaser
##### [Säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Lägg till en shard](sql-database-elastic-scale-add-a-shard.md)
##### [Lösa problem i shardkarta](sql-database-elastic-database-recovery-manager.md)
##### [Migrera befintliga utskalade databaser till shardade databaser ](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Skapa prestandaräknare för karthanteraren för shard](sql-database-elastic-database-perf-counters.md)
#### Elastiska jobb
##### [Hur installerar jag tjänsten elastiska jobb?](sql-database-elastic-jobs-service-installation.md)
##### [Skapa och hantera elastiska jobb med PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Skapa och hantera elastiska jobb med hjälp av Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
##### [Hur avinstallerar jag elastiska jobb?](sql-database-elastic-jobs-uninstall.md)
#### Elastiska pooler
##### [Skapa med Azure Portal](sql-database-elastic-pool-create-portal.md)
##### [Skapa med PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Skapa med C#](sql-database-elastic-pool-create-csharp.md)
##### [Hantera med Azure Portal](sql-database-elastic-pool-manage-portal.md)
##### [Hantera med PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Hantera med C#](sql-database-elastic-pool-manage-csharp.md)
##### [Hantera med T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Autentisera och auktorisera
### [Azure AD-autentisering](sql-database-aad-authentication.md)
### [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md)
## Kryptera data
### [Transparent datakryptering](https://msdn.microsoft.com/library/azure/dn948096)
### [Kolumnkryptering](https://msdn.microsoft.com/library/azure/ms179331)
## Migrera databaser
### Fastställa kompatibilitet
#### [Fastställa kompatibilitet med hjälp av verktyg för SQL-paket](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Fastställa kompatibilitet med hjälp av SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Åtgärda kompatibilitetsproblem
#### [Åtgärda kompatibilitetsproblem med hjälp av SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Åtgärda kompatibilitetsproblem med hjälp av SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Åtgärda kompatibilitetsproblem med hjälp av SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Migrera med hjälp av SSMS migreringsguide](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Övervaka och finjustera
### [Query Performance Insight](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV](sql-database-monitoring-with-dmvs.md)
### [Efterlevnadsnivåer](sql-database-compatibility-level-query-performance-130.md)
### [Tips vid prestandajustering](sql-database-troubleshoot-performance.md)
### Ändra tjänst- och prestandanivåer
#### [Ändra tjänstnivåer med hjälp av Azure-portalen](sql-database-scale-up.md)
#### [Ändra tjänstnivåer med hjälp av PowerShell](sql-database-scale-up-powershell.md)
### [Skapa aviseringar](sql-database-insights-alerts-portal.md)
#### [Övervaka minnesintern OLTP-lagring](sql-database-in-memory-oltp-monitoring.md)
### Query Store
#### [Övervakning av prestanda med hjälp av Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Query Store-användningsscenarier](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Använda Query Store](sql-database-operate-query-store.md)
### Utökade händelser
#### [Händelsefilens målkod](sql-database-xevent-code-event-file.md)
#### [Ringbuffertens målkod](sql-database-xevent-code-ring-buffer.md)
## Flytta data
### Kopiera en SQL-databas
#### [Kopiera med Azure Portal](sql-database-copy-portal.md)
#### [Kopiera med PowerShell](sql-database-copy-powershell.md)
#### [Kopiera med T-SQL](sql-database-copy-transact-sql.md)
### Exportera databasen till en BACPAC-fil
#### [Exportera med hjälp av Azure-portalen](sql-database-export.md)
#### [Exportera med hjälp av SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Exportera med hjälp av verktyg för SQL-paket](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Exportera med hjälp av PowerShell](sql-database-export-powershell.md)
### Importera databasen från en BACPAC-fil
#### [Importera med hjälp av Azure-portalen](sql-database-import.md)
#### [Importera med hjälp av PowerShell](sql-database-import-powershell.md)
#### [Importera med hjälp av SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Importera med hjälp av verktyg för SQL-paket](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Läsa in från CSV-filen med BCP](sql-database-load-from-csv-with-bcp.md)
## Fråga
### [Fråga med hjälp av SSMS](sql-database-connect-query-ssms.md)
## Säkerhetskopiera och återställ
### Långsiktig kvarhållning av säkerhetskopior
#### [Konfigurera långsiktig kvarhållning av säkerhetskopior](sql-database-configure-long-term-retention.md)
#### [Visa säkerhetskopior i ett Recovery Services-valv](sql-database-view-backups-in-vault.md)
#### [Återställa från långsiktig kvarhållning av säkerhetskopior](sql-database-restore-from-long-term-retention.md)
### Återställa borttagen databas
#### [Återställa borttagna med hjälp av Azure-portalen](sql-database-restore-deleted-database-portal.md)
#### [Återställa borttagna med hjälp av PowerShell](sql-database-restore-deleted-database-powershell.md)
### Återställning till tidpunkt
#### [Återställa till en tidpunkt](sql-database-point-in-time-restore.md)
#### [Visa äldsta återställningspunkt](sql-database-view-oldest-restore-point.md)
### Geo-återställning
#### [Geo-återställa med hjälp av Azure-portalen](sql-database-geo-restore-portal.md)
#### [Geo-återställa med hjälp av PowerShell](sql-database-geo-restore-powershell.md)
## Aktiv geo-replikering
### [Konfigurera med hjälp av Azure-portalen](sql-database-geo-replication-portal.md)
### [Konfigurera med hjälp av PowerShell](sql-database-geo-replication-powershell.md)
### [Konfigurera med hjälp av T-SQL](sql-database-geo-replication-transact-sql.md)
### [Redundans med hjälp av Azure-portalen](sql-database-geo-replication-failover-portal.md)
### [Redundans med hjälp av PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Redundans med hjälp av T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Felsöka
### [Anslutningsproblem](sql-database-troubleshoot-common-connection-issues.md)
### [Tillfälligt anslutningsfel](sql-database-troubleshoot-connection.md)
### [Diagnostisera och förhindra](sql-database-connectivity-issues.md)
### [Behörigheter](sql-database-troubleshoot-permissions.md)
### [Flytta databaser](sql-database-troubleshoot-moving-data.md)
# Referens
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (Elastic Database)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Relaterat
## Bibliotek för SQL Database-hantering
### [Hämta bibliotekspaketet för SQL Database-hanteringen](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server-drivrutiner](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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


<!--HONumber=Dec16_HO3-->


