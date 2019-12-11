---
title: Transparent datakryptering
description: En översikt över transparent data kryptering för SQL Database och informations lager. Dokumentet täcker fördelarna och alternativen för konfiguration, som innehåller tjänst hanterad transparent data kryptering och Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 11/01/2019
ms.openlocfilehash: 19414a6f09f4bc61cd9b1b09ae98ea070e577d7f
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995889"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent data kryptering för SQL Database och informations lager

Transparent data kryptering (TDE) hjälper till att skydda Azure SQL Database, Azure SQL-hanterad instans och Azure Data Warehouse mot hot från skadlig offline-aktivitet genom att kryptera data i vila. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är transparent datakryptering aktiverat för alla nyligen distribuerade Azure SQL-databaser. TDE kan inte användas för att kryptera den logiska **huvud** databasen i SQL Database.  **Huvud** databasen innehåller objekt som behövs för att utföra TDE-åtgärder på användar databaserna.

TDE måste aktive ras manuellt för äldre databaser av Azure SQL Database, Azure SQL-hanterad instans eller Azure SQL Data Warehouse.
Hanterade instans databaser som skapats via återställning ärver krypterings status från käll databasen.

Transparent data kryptering krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databas krypterings nyckel. Den här databas krypterings nyckeln skyddas av transparent data krypterings skydd. Skydds komponenten är antingen ett tjänst hanterings certifikat (hanterad transparent data kryptering) eller en asymmetrisk nyckel som lagras i Azure Key Vault (Bring Your Own Key). Du ställer in transparent data krypterings skydd på server nivå för Azure SQL Database och informations lager och instans nivå för Azure SQL-hanterad instans. Termen *Server* avser både server och instans i det här dokumentet, om inget annat anges.

Vid databas start dekrypteras krypterings nyckeln för krypterad databas och används sedan för dekryptering och Omkryptering av databasfilerna i processen för SQL Server databas motor. Transparent data kryptering utför I/O-kryptering i real tid och dekryptering av data på sidnivå. Varje sida dekrypteras när de läses in i minnet och krypteras sedan innan de skrivs tillbaka till disken. En allmän beskrivning av transparent data kryptering finns i [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server som körs på en virtuell Azure-dator kan också använda en asymmetrisk nyckel från Key Vault. Konfigurations stegen skiljer sig från att använda en asymmetrisk nyckel i SQL Database-och SQL-hanterad instans. Mer information finns i [utöknings bar nyckel hantering med hjälp av Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Hanterad transparent data kryptering i tjänst

I Azure är standardinställningen för transparent data kryptering att databas krypterings nyckeln skyddas av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje server och krypteringsalgoritmen som används är AES 256. Om en databas finns i en geo-replikeringsrelation, skyddas både den primära databasen och den geo-sekundära databasen av den primära databasens överordnade server nyckel. Om två databaser är anslutna till samma server, delar de också samma inbyggda certifikat.  Microsoft roterar dessa certifikat automatiskt i enlighet med den interna säkerhets principen och rot nyckeln skyddas av en intern Microsoft-lagringsplats.  Kunder kan verifiera SQL Database efterlevnad med interna säkerhets principer i oberoende gransknings rapporter från tredje part som är tillgängliga i [Microsoft säkerhets Center](https://servicetrust.microsoft.com/).

Microsoft flyttar också sömlöst och hanterar nycklarna efter behov för geo-replikering och återställning.

> [!IMPORTANT]
> Alla nyligen skapade SQL-databaser och hanterade instans databaser krypteras som standard med hjälp av tjänstehanterad transparent data kryptering. Befintliga SQL-databaser som skapats före maj 2017 och SQL-databaser som skapats via återställning, geo-replikering och databas kopiering är inte krypterade som standard. Befintliga hanterade instans databaser som skapats före februari 2019 krypteras inte som standard. Hanterade instans databaser som skapats via återställning ärver krypterings status från källan.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Kundhanterad transparent data kryptering – Bring Your Own Key

[TDE med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) tillåta kryptering av databas krypterings nyckeln (DEK) med en kundhanterad asymmetrisk nyckel som kallas TDE-skydd.  Detta kallas även Bring Your Own Key (BYOK) stöd för transparent datakryptering. I BYOK-scenariot lagras TDE-skyddet i ett kundägda och hanterat [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Azures molnbaserade hanterings system för extern nyckel. TDE-skydd kan [genereras av nyckel valvet eller överföras till nyckel valvet](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) från en lokal HSM-enhet. TDE-DEK, som lagras på Start sidan i en databas, krypteras och dekrypteras av TDE-skyddskomponenten, som lagras i Azure Key Vault och aldrig lämnar nyckel valvet.  SQL Database måste beviljas behörighet till det kundägda nyckel valvet för att dekryptera och kryptera DEK. Om behörigheterna för den logiska SQL-servern till nyckel valvet har återkallats går det inte att komma åt databasen och alla data krypteras. För Azure SQL Database anges TDE-skydd på den logiska SQL Server-nivån och ärvs av alla databaser som är kopplade till den servern. För [Azure SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)anges TDE-skydd på instans nivå och den ärvs av alla *krypterade* databaser på den instansen. Termen *Server* avser både server och instans i det här dokumentet, om inget annat anges.

Med TDE med Azure Key Vault-integrering kan användare styra viktiga hanterings uppgifter, till exempel nyckel rotationer, nyckel valv, nyckel säkerhets kopior och aktivera granskning/rapportering på alla TDE-skydd med hjälp av Azure Key Vault-funktioner. Key Vault tillhandahåller central nyckel hantering, använder tätt övervakade HSM: er (Hardware Security modules) och gör det möjligt att dela upp uppgifter mellan hantering av nycklar och data för att möta efterlevnaden av säkerhets principer.
Om du vill veta mer om transparent data kryptering med Azure Key Vault integration (Bring Your Own Key support) för Azure SQL Database, SQL-hanterad instans och data lager, se [transparent data kryptering med Azure Key Vault integrering](transparent-data-encryption-byok-azure-sql.md).

Om du vill börja använda transparent data kryptering med Azure Key Vault integration (Bring Your Own Key support) kan du läsa instruktionen för att [Aktivera transparent data kryptering med hjälp av din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Flytta en transparent data krypterings-skyddad databas

Du behöver inte dekryptera databaser för åtgärder i Azure. De transparenta data krypterings inställningarna på käll databasen eller den primära databasen ärvs transparent på målet. Åtgärder som ingår:

- Geo-återställning
- Självbetjäning för återställning av tidpunkt
- Återställning av en borttagen databas
- Aktiv geo-replikering
- Skapandet av en databas kopia
- Återställning av säkerhets kopierings filen till Azure SQL-hanterad instans

> [!IMPORTANT]
> Att göra en manuell säkerhets kopiering av en databas som har krypterats av en hanterad TDE tillåts inte i en Azure SQL-hanterad instans eftersom certifikatet som används för kryptering inte är tillgängligt. Använd funktionen för återställning av tidpunkt för att flytta den här typen av databas till en annan hanterad instans.

När du exporterar en transparent data kryptering – skyddad databas krypteras inte det exporterade innehållet i databasen. Det exporterade innehållet lagras i okrypterade BACPAC-filer. Se till att skydda BACPAC-filerna på rätt sätt och aktivera transparent data kryptering när den nya databasen har importer ATS.

Om filen BACPAC till exempel exporteras från en lokal SQL Server instans, krypteras inte det importerade innehållet i den nya databasen automatiskt. Om filen BACPAC exporteras till en lokal SQL Server instans, krypteras inte heller den nya databasen automatiskt.

Det enda undantaget är när du exporterar till och från en SQL-databas. Transparent data kryptering är aktive rad i den nya databasen, men BACPAC-filen är fortfarande inte krypterad.


## <a name="manage-transparent-data-encryption"></a>Hantera transparent data kryptering
# <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)
Hantera transparent data kryptering i Azure Portal.

Om du vill konfigurera transparent data kryptering via Azure Portal måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

Du aktiverar och inaktiverar transparent data kryptering på databas nivå. Om du vill aktivera transparent data kryptering för en databas går du till [Azure Portal](https://portal.azure.com) och loggar in med ditt Azure-administratör eller deltagar konto. Hitta inställningarna för transparent data kryptering under din användar databas. Som standard används hanterad transparent data kryptering. Ett transparent data krypterings certifikat skapas automatiskt för den server som innehåller-databasen. För Azure SQL-hanterad instans använder du T-SQL för att aktivera och inaktivera transparent data kryptering på en databas.

![Hanterad transparent data kryptering i tjänst](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Du ställer in huvud nyckeln för transparent data kryptering, även kallat transparent data krypterings skydd på server nivå. Om du vill använda transparent data kryptering med Bring Your Own Key stöd och skydda dina databaser med en nyckel från Key Vault, öppnar du inställningarna för transparent data kryptering under din server.

![Transparent data kryptering med stöd för Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Hantera transparent data kryptering med hjälp av PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill konfigurera transparent data kryptering via PowerShell måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Cmdletar för Azure SQL Database och informations lager

Använd följande cmdlets för Azure SQL Database och informations lagret:

| Cmdlet | Beskrivning |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Aktiverar eller inaktiverar transparent data kryptering för en databas|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Hämtar transparent data krypterings tillstånd för en databas |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Kontrollerar krypterings förloppet för en databas |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Lägger till en Key Vault nyckel till en SQL Server-instans |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Hämtar Key Vault nycklar för en Azure SQL Database-Server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ställer in transparent data krypterings skydd för en SQL Server instans |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Hämtar skyddet för transparent data kryptering |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Tar bort en Key Vault nyckel från en SQL Server instans |
|  | |

> [!IMPORTANT]
> För Azure SQL-hanterad instans använder du T-SQL [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) -kommandot för att aktivera och inaktivera transparent data kryptering på en databas nivå och kontrol lera [PowerShell-skriptet](transparent-data-encryption-byok-azure-sql-configure.md) för att hantera transparent data kryptering på en instans nivå.

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Hantera transparent data kryptering med hjälp av Transact-SQL.

Anslut till databasen med en inloggning som är en administratör eller medlem av **DBManager** -rollen i huvud databasen.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | Ange kryptering vid/från krypterar eller dekrypterar en databas |
| [sys. dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Returnerar information om krypterings tillstånd för en databas och dess tillhör ande databas krypterings nycklar |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Returnerar information om krypterings status för varje data lager nod och dess tillhör ande databas krypterings nycklar |
|  | |

Du kan inte byta transparent data krypterings skydd till en nyckel från Key Vault med hjälp av Transact-SQL. Använd PowerShell eller Azure Portal.

# <a name="rest-apitabazure-restapi"></a>[REST API](#tab/azure-RESTAPI)
Hantera transparent data kryptering med hjälp av REST API.

Om du vill konfigurera transparent data kryptering via REST API måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.
Använd följande uppsättning kommandon för Azure SQL Database och informations lagret:

| Kommando | Beskrivning |
| --- | --- |
|[Skapa eller uppdatera Server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Lägger till en Azure Active Directory identitet till en SQL Server-instans (används för att bevilja åtkomst till Key Vault)|
|[Skapa eller uppdatera server nyckel](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Lägger till en Key Vault nyckel till en SQL Server-instans|
|[Ta bort server nyckel](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Tar bort en Key Vault nyckel från en SQL Server instans|
|[Hämta Server nycklar](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Hämtar en angiven Key Vault nyckel från en SQL Server instans|
|[Visa en lista med Server nycklar per server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Hämtar Key Vault nycklar för en SQL Server instans |
|[Skapa eller uppdatera krypterings skydd](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Ställer in transparent data krypterings skydd för en SQL Server instans|
|[Hämta krypterings skydd](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Hämtar det transparenta data krypterings skyddet för en SQL Server instans|
|[Visa lista över krypterings skydd per server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Hämtar skydd för transparent data kryptering för en SQL Server instans |
|[Skapa eller uppdatera transparent datakryptering konfiguration](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiverar eller inaktiverar transparent data kryptering för en databas|
|[Hämta transparent datakryptering konfiguration](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Hämtar den transparenta data krypterings konfigurationen för en databas|
|[Visa lista transparent datakryptering konfigurations resultat](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Hämtar krypterings resultatet för en databas|

## <a name="next-steps"></a>Nästa steg

- En allmän beskrivning av transparent data kryptering finns i [transparent data kryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Om du vill veta mer om transparent data kryptering med Bring Your Own Key stöd för Azure SQL Database, Azure SQL-hanterad instans och informations lager, se [transparent data kryptering med stöd för Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Om du vill börja använda transparent data kryptering med Bring Your Own Key support kan du läsa instruktionen för att [Aktivera transparent data kryptering med hjälp av din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Mer information om Key Vault finns på sidan för [Key Vault dokumentation](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
