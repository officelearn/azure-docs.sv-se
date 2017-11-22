# Översikt

## [Vad är SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md)
## [Arbetsbelastning i informationslager](sql-data-warehouse-overview-workload.md)

# Kom igång

## [Läsa in datasjälvstudier](load-data-from-azure-blob-storage-using-polybase.md)
## [Självstudie för elastisk fråga](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
## [Bästa praxis](sql-data-warehouse-best-practices.md)
## [Hantera](sql-data-warehouse-overview-manage.md)

# Gör så här för att

## Tjänstfunktioner
### [MPP-arkitektur](massively-parallel-processing-mpp-architecture.md)
### [Prestandanivåer](performance-tiers.md)
### [Informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Kapacitetsbegränsningar](sql-data-warehouse-service-capacity-limits.md)
### [Vanliga frågor och svar](sql-data-warehouse-overview-faq.md)

## Säkerhetskopiering och återställning

### [Översikt över Backup](sql-data-warehouse-backups.md)
### [Återställa översikt](sql-data-warehouse-restore-database-overview.md)
#### [Azure Portal](sql-data-warehouse-restore-database-portal.md)
#### [Azure PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Anslut

### [Översikt](sql-data-warehouse-connect-overview.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Installera Visual Studio](sql-data-warehouse-install-visual-studio.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [Anslutningssträngar](sql-data-warehouse-connection-strings.md)

## Skapa
### [Azure Portal](sql-data-warehouse-get-started-provision.md)
### [Azure PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Utveckla

### [Översikt](sql-data-warehouse-overview-develop.md)

### Tabeller

#### [Översikt](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Datatyper](sql-data-warehouse-tables-data-types.md)
#### [Distribuerade tabeller](sql-data-warehouse-tables-distribute.md)
#### [Index](sql-data-warehouse-tables-index.md)
#### [Identitet](sql-data-warehouse-tables-identity.md)
#### [Partitioner](sql-data-warehouse-tables-partition.md)
#### [Replikerade tabeller](design-guidance-for-replicated-tables.md)
#### [Statistik](sql-data-warehouse-tables-statistics.md)
#### [Tillfällig](sql-data-warehouse-tables-temporary.md)

### Frågor

#### [Dynamisk SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [Gruppera efter alternativ](sql-data-warehouse-develop-group-by-options.md)
#### [Etiketter](sql-data-warehouse-develop-label.md)

### Språkelement för T-SQL

#### [Loopar](sql-data-warehouse-develop-loops.md)
#### [Lagrade procedurer](sql-data-warehouse-develop-stored-procedures.md)
#### [Transaktioner](sql-data-warehouse-develop-transactions.md)
#### [Metodtips för transaktioner](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Variabeltilldelning](sql-data-warehouse-develop-variable-assignment.md)
#### [Vyer](sql-data-warehouse-develop-views.md)

## Integrera

### [Översikt](sql-data-warehouse-overview-integrate.md)
### [Data Factory](sql-data-warehouse-integrate-azure-data-factory.md)
### [Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Machine Learning, självstudier](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Power BI-visualisering](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

### [Elastisk fråga med SQL Database och SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md)

## Läsa in

### Koncept
#### [Översikt](sql-data-warehouse-overview-load.md)
#### [PolyBase-vägledning](sql-data-warehouse-load-polybase-guide.md)

### Självstudier
#### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)

### Instruktionsguider
#### [Exempeldata](sql-data-warehouse-load-sample-databases.md)
#### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
#### [BCP](sql-data-warehouse-load-with-bcp.md)
#### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
#### [PolyBase från Blob Storage](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
#### [PolyBase från SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
#### [RedGate](sql-data-warehouse-load-with-redgate.md)
#### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrera

### [Översikt](sql-data-warehouse-overview-migrate.md)
### [Migreringsverktyg](sql-data-warehouse-migrate-migration-utility.md)
### [Migrera schema](sql-data-warehouse-migrate-schema.md)
### [Migrera kod](sql-data-warehouse-migrate-code.md)
### [Migrera data](sql-data-warehouse-migrate-data.md)
### [Migrera till Premium Storage](sql-data-warehouse-migrate-to-premium-storage.md)

## Hantera beräkning

### [Översikt](sql-data-warehouse-manage-compute-overview.md)
### [Azure-portal](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [REST-API](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)
### [Automatisera beräkningsnivåer](manage-compute-with-azure-functions.md)

### Resursklasser
#### [Riktlinjer](resource-classes-for-workload-management.md)
#### [Analysera din arbetsbelastning](analyze-your-workload.md)

## Prestanda

### [Översikt](sql-data-warehouse-overview-manage-user-queries.md)
### [Columnstore-komprimering](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Övervaka](sql-data-warehouse-manage-monitor.md)

## Säkerhet

### [Översikt](sql-data-warehouse-overview-manage-security.md)
### [Granskning](sql-data-warehouse-auditing-overview.md)
### [Granskning för äldre klienter](sql-data-warehouse-auditing-downlevel-clients.md)
### [Autentisering](sql-data-warehouse-authentication.md)
### [Kryptering](sql-data-warehouse-encryption-tde.md)
### [Kryptering med T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Hotidentifiering](sql-data-warehouse-security-threat-detection.md)

## Felsöka
### [Felsöka](sql-data-warehouse-troubleshoot.md)

# Referens


## T-SQL
### [Fullständig referens](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Språkelement för SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instruktioner för SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Systemvyer](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell-cmdletar](sql-data-warehouse-reference-powershell-cmdlets.md)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=databases)
## [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Prissättning](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Funktionsbegäranden](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Support](sql-data-warehouse-get-started-create-support-ticket.md)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partner
### [Business Intelligence](sql-data-warehouse-partner-business-intelligence.md)
### [Dataintegrering](sql-data-warehouse-partner-data-integration.md)
### [Datahantering](sql-data-warehouse-partner-data-management.md)
