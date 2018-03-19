# [Dokumentation om SQL Data Warehouse](index.md)

# Översikt

## [Om SQL Data Warehouse](sql-data-warehouse-overview-what-is.md)
## [Översiktsblad](cheat-sheet.md)

# Snabbstarter

## [Skapa och anslut – portalen](create-data-warehouse-portal.md)
## Pausa och återuppta beräkning
### [Portal](pause-and-resume-compute-portal.md)
### [PowerShell](pause-and-resume-compute-powershell.md)
## Skala beräkning
### [Portal](quickstart-scale-compute-portal.md)
### [PowerShell](quickstart-scale-compute-powershell.md)
### [T-SQL](quickstart-scale-compute-tsql.md)


# Självstudier
## [1 – Läs in WideWorldImporters](load-data-wideworldimportersdw.md)

# Begrepp
## Tjänstfunktioner
### [MPP-arkitektur](massively-parallel-processing-mpp-architecture.md)
### [Prestandanivåer](performance-tiers.md)
### [Informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Skala ut, pausa och återuppta](sql-data-warehouse-manage-compute-overview.md)
### [Säkerhetskopior av informationslager](sql-data-warehouse-backups.md)
### [Granskning](sql-data-warehouse-auditing-overview.md)
### [Kapacitetsbegränsningar](sql-data-warehouse-service-capacity-limits.md)
### [Vanliga frågor och svar](sql-data-warehouse-overview-faq.md)

## Säkerhet
### [Översikt](sql-data-warehouse-overview-manage-security.md)
### [Autentisering](sql-data-warehouse-authentication.md)


## Migrera till SQL Data Warehouse
### [Översikt](sql-data-warehouse-overview-migrate.md)
### [Migrera schema](sql-data-warehouse-migrate-schema.md)
### [Migrera kod](sql-data-warehouse-migrate-code.md)
### [Migrera data](sql-data-warehouse-migrate-data.md)

## Läsa in och flytta data
### [Översikt](design-elt-data-loading.md)
### [Bästa praxis](guidance-for-loading-data.md)


## Integrera
### [Översikt](sql-data-warehouse-overview-integrate.md)
### [Elastisk fråga i SQL Database](how-to-use-elastic-query-with-sql-data-warehouse.md)


## Frågeprestanda
### [Resursklasser](resource-classes-for-workload-management.md)
### [Columnstore-komprimering](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)

## [Övervaka](sql-data-warehouse-manage-monitor.md)


## Utveckla informationslager
### [Översikt](sql-data-warehouse-overview-develop.md)
### [Komponenter för informationslager](sql-data-warehouse-overview-workload.md)

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

## [Felsöka](sql-data-warehouse-troubleshoot.md)

# Instruktionsguider
## Tjänstfunktioner
### [Återställ ett informationslager – portalen](sql-data-warehouse-restore-database-portal.md)
### [Återställ ett informationslager – PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [Återställ ett informationslager – REST API](sql-data-warehouse-restore-database-rest-api.md)

## Säkerhet
### [Aktivera kryptering – portalen](sql-data-warehouse-encryption-tde.md)
### [Aktivera kryptering – T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Hotidentifiering](sql-data-warehouse-security-threat-detection.md)


## Läsa in och flytta data
### [Data om New York-taxibilar](load-data-from-azure-blob-storage-using-polybase.md)
### [Offentliga Contoso-data](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [AzCopy](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Integrera
### [Konfigurera elastisk fråga i SQL Database](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Lägg till Azure Stream Analytics-jobb](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Använd maskininlärning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Visualisera med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Övervaka och finjustera
### [Analysera din arbetsbelastning](analyze-your-workload.md)

## Skala ut

### [Automatisera beräkningsnivåer](manage-compute-with-azure-functions.md)


# Referens

## T-SQL
### [Fullständig referens](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Språkelement för SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instruktioner för SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Systemvyer](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell-cmdletar](sql-data-warehouse-reference-powershell-cmdlets.md)
## [REST API:er](sql-data-warehouse-manage-compute-rest-api.md)

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
