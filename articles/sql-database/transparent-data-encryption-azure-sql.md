---
title: Transparent datakryptering för Azure SQL Database och Data Warehouse | Microsoft Docs
description: En översikt över transparent datakryptering för SQL Database och Data Warehouse. Dokumentet omfattar alternativ för konfiguration, vilket inkluderar tjänsthanterad transparent datakryptering och Bring Your Own Key och dess fördelar.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3333355b742d531cc814484bdefbd47e7ab6c75c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878014"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent datakryptering för SQL Database och Data Warehouse

Transparent datakryptering (TDE) kan du skydda Azure SQL Database, Azure SQL Managed Instance och Azure Data Warehouse mot skadlig aktivitet. Den utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet. Som standard är TDE aktiverat för alla nyligen distribuerade Azure SQL-databaser. TDE kan inte användas för att kryptera den logiska **master** databas i SQL Database.  Den **master** databasen innehåller objekt som behövs för att utföra TDE-åtgärder på användardatabaser.

TDE måste aktiveras manuellt för Azure SQL Managed Instance, äldre databaser på Azure SQL Database eller Azure SQL Data Warehouse.  

Transparent datakryptering krypteras lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Den här databaskrypteringsnyckeln skyddas av krypteringsskyddet transparent data. Skyddets är antingen en tjänsthanterad certifikat (tjänsthanterad transparent datakryptering) eller en asymmetrisk nyckel som lagras i Azure Key Vault (Bring Your Own Key). Du kan ställa in transparent data krypteringsskyddet på servernivå för Azure SQL Database och Data Warehouse och instansnivå för Azure SQL Managed Instance. Termen *server* refererar både till servern och instansen i hela dokumentet, om inte anges på olika sätt.

När du startar för databasen är krypterad databaskrypteringsnyckeln dekrypteras och sedan användas för dekryptering och omkryptering av databasfilerna i SQL Server Database Engine-processen. Transparent datakryptering utför i realtid i/o-kryptering och dekryptering av data på sidnivå. Varje sida dekrypteras när den har läst in i minnet och sedan krypteras innan de skrivs till disk. En allmän beskrivning av transparent datakryptering, se [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server som körs på virtuella Azure-datorer även kan använda en asymmetrisk nyckel från Key Vault. Konfigurationsstegen skiljer sig från att använda en asymmetrisk nyckel i SQL Database och SQL-hanterad instans. Mer information finns i [utökningsbar nyckelhantering med hjälp av Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Tjänsthanterad transparent datakryptering

I Azure är standardinställningen för transparent datakryptering att databaskrypteringsnyckeln skyddas av ett certifikat för inbyggda. Inbyggda servercertifikatet är unikt för varje server. Om en databas finns i en relation för geo-replikering, både primär och geo-sekundära databasen skyddas av den primära databasen överordnade servernyckeln. Om två databaser är ansluten till samma server kan dela de också samma inbyggda certifikat. Microsoft roterar automatiskt dessa certifikat i enlighet med den interna säkerhetsprincipen och rotnyckeln skyddas av en Microsoft interna hemliga store.

Microsoft även sömlöst flyttar hanterar nycklar som behövs för geo-replikering och återställer.

> [!IMPORTANT]
> Alla nyligen skapade SQL-databaser krypteras som standard med hjälp av tjänsthanterad transparent datakryptering. Azure SQL-hanterad instans-databaser, befintliga SQL-databaser som skapats före maj 2017 och SQL-databaser som skapats via återställning, geo-replikering och databaskopiering krypteras inte som standard.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Kundhanterad transparent datakryptering, Bring Your Own Key

[TDE med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) tillåter för att kryptera den databasen Datakrypteringsnyckeln (DEK) med en kundhanterad asymmetrisk nyckel kallas TDE-skydd.  Detta kallas också i allmänhet som ta med din egen nyckel (BYOK) som har stöd för Transparent datakryptering. I BYOK-scenariot TDE-skyddet lagras i en kundägda och hanteras [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Azures molnbaserade externa nyckelhanteringssystem. TDE-skyddet kan vara [genereras av key vault eller överföras till nyckelvalvet](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) från en on premises HSM-enhet. TDE DEK, som finns på startsidan av en databas, krypteras och dekrypteras av TDE-skyddet, som lagras i Azure Key Vault och lämnar aldrig nyckelvalvet.  SQL-databasen måste ha behörighet till kundägda nyckelvalvet för att dekryptera och kryptera DEK. Om behörigheterna för den logiska SQL-servern till nyckelvalvet har återkallats, en databas kan inte nås och alla data krypteras. För Azure SQL Database, TDE-skyddet är inställd på den logiska SQL-servernivån och ärvs av alla databaser som är associerade med den här servern. För [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), TDE-skyddet är inställd på instansnivå och den ärvs av alla *krypterade* databaser på instansen. Termen *server* refererar både till servern och instansen i hela dokumentet, om inte anges på olika sätt.

Med transparent Datakryptering med Azure Key Vault-integrering, kan användare styra viktiga hanteringsaktiviteter, inklusive nyckelrotationer, nyckelvalvet behörigheter, nyckelsäkerhetskopior och aktivera granskning/reporting på alla TDE-skydd med hjälp av Azure Key Vault-funktioner. Key Vault ger central nyckelhantering, utnyttjar nära övervakade maskinvarusäkerhetsmoduler (HSM) och låter uppdelning av uppgifter mellan hantering av nycklar och data för att uppfylla efterlevnad med säkerhetsprinciper.
Läs mer om transparent datakryptering med Azure Key Vault-integrering (stöd för Bring Your Own Key) för Azure SQL Database, SQL-hanterad instans och Data Warehouse i [Transparent datakryptering med Azure Key Vault-integrering](transparent-data-encryption-byok-azure-sql.md).

Om du vill börja använda transparent datakryptering med Azure Key Vault-integrering (stöd för Bring Your Own Key), finns i den här guiden [aktivera transparent datakryptering med hjälp av en egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Flytta en transparent data encryption-skyddad databas

Du behöver inte dekryptera databaser för åtgärder i Azure. Transparent data encryption inställningarna på källdatabasen eller primära databasen ärvs transparent på målet. Det innefattar åtgärder som ingår:

- Geo-återställning
- Självbetjänad point-in-time-återställning
- Återställa en borttagen databas
- Aktiv geo-replikering
- Skapa en databaskopia
- Återställning av säkerhetskopierade filen till Azure SQL Managed Instance

> [!IMPORTANT]
> Med manuell säkerhetskopiering med endast kopiering av en databas som har krypterats av tjänsthanterad TDE tillåts inte i Azure SQL Managed Instance, eftersom certifikatet som används för kryptering inte är tillgänglig. Använd punkt i tiden återställning funktion för att flytta den här typen av databasen till en annan hanterad instans.

När du exporterar en transparent data encryption-skyddad databas, inte är det exporterade innehållet på databasen krypterad. Det här exporterade innehållet lagras i icke krypterade BACPAC-filer. Var noga med att skydda BACPAC-filer på rätt sätt och aktivera transparent datakryptering efter import av den nya databasen har slutförts.

Till exempel om BACPAC-fil har exporterats från en lokal SQL Server-instans, krypteras importerade innehållet i den nya databasen inte automatiskt. På samma sätt, om BACPAC-fil har exporterats till en lokal SQL Server-instans, den nya databasen också inte krypteras automatiskt.

Det enda undantaget är när du exporterar till och från en SQL-databas. Transparent datakryptering är aktiverat i den nya databasen, men själva BACPAC-filen fortfarande inte är krypterad.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Hantera transparent datakryptering i Azure portal

Om du vill konfigurera transparent datakryptering via Azure-portalen måste du vara ansluten som Azure ägare, deltagare eller SQL Security Manager.

Du aktiverar transparent datakryptering och sätts på databasnivå. Om du vill aktivera transparent datakryptering på en databas, gå till den [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-administratör eller bidragsgivare. Hitta transparent data encryption inställningarna för databasen. Som standard används tjänsthanterad transparent datakryptering. Ett krypteringscertifikat för transparent data genereras automatiskt för den server där databasen. Använd T-SQL aktivera transparent datakryptering och inaktivera en databas för Azure SQL Managed Instance.

![Tjänsthanterad transparent datakryptering](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Du anger transparent data encryption huvudnyckeln, även kallat transparent data encryption skyddets, på servernivå. Om du vill använda transparent datakryptering med stöd för Bring Your Own Key och skydda dina databaser med en nyckel från Key Vault, öppna krypteringsinställningar transparent data under din server.

![Transparent datakryptering med stöd för Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Hantera transparent datakryptering med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

Om du vill konfigurera transparent datakryptering via PowerShell, måste du vara ansluten som Azure ägare, deltagare eller SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Cmdletar för Azure SQL Database och Data Warehouse

Använd följande cmdlets för Azure SQL Database och Data Warehouse:

| Cmdlet | Beskrivning |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Aktiverar eller inaktiverar transparent datakryptering för en databas|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Hämtar transparent data encryption tillståndet för en databas |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Kontrollerar kryptering förloppet för en databas |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Lägger till en Key Vault-nyckel till en SQL Server-instans |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Hämtar Key Vault-nycklar för en Azure SQL Database-server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Anger krypteringsskyddet transparent data för en SQL Server-instans |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Hämtar krypteringsskyddet transparent data |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Tar bort en nyckel med Key Vault från en SQL Server-instans |
|  | |

> [!IMPORTANT]
> Använd T-SQL för Azure SQL Managed Instance [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) kommando för att aktivera transparent datakryptering och inaktivera en databasnivå och kontrollera [exempel på PowerShell-skript](transparent-data-encryption-byok-azure-sql-configure.md) att hantera transparent data kryptering på en instansnivå.

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Hantera transparent datakryptering med hjälp av Transact-SQL

Ansluta till databasen med hjälp av en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL-databas)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | Ställ in kryptering på/av krypterar eller dekrypterar en databas |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Returnerar information om krypteringsstatus för en databas och dess associerade databasen krypteringsnycklar |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Returnerar information om krypteringsstatus för varje data warehouse-nod och dess associerade databasen krypteringsnycklar |
|  | |

Du kan inte växla krypteringsskyddet transparent data till en nyckel från Key Vault med hjälp av Transact-SQL. Använd PowerShell eller Azure-portalen.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Hantera transparent datakryptering med hjälp av REST-API

Om du vill konfigurera transparent datakryptering via REST API, måste du vara ansluten som Azure ägare, deltagare eller SQL Security Manager.
Använd följande kommandon för Azure SQL Database och Data Warehouse:

| Kommando | Beskrivning |
| --- | --- |
|[Skapa eller uppdatera Server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Lägger till en Azure Active Directory-identitet till en SQL Server-instans (används för att bevilja åtkomst till Key Vault)|
|[Skapa eller uppdatera servernyckel](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Lägger till en Key Vault-nyckel till en SQL Server-instans|
|[Ta bort servernyckeln](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Tar bort en nyckel med Key Vault från en SQL Server-instans|
|[Hämta för Server](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Hämtar en specifik Key Vault-nyckel från en SQL Server-instans|
|[Lista servernycklar av servern](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Hämtar Key Vault-nycklar för en SQL Server-instans |
|[Skapa eller uppdatera Krypteringsskyddet](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Anger krypteringsskyddet transparent data för en SQL Server-instans|
|[Hämta Krypteringsskyddet](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Hämtar krypteringsskyddet transparent data för en SQL Server-instans|
|[Lista kryptering skydd av servern](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Hämtar transparent data encryption skydd för en SQL Server-instans |
|[Skapa eller uppdatera Transparent Data Encryption konfiguration](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiverar eller inaktiverar transparent datakryptering för en databas|
|[Hämta konfiguration för Transparent kryptering](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Hämtar transparent data encryption konfigurationen för en databas|
|[Transparent Data Encryption Configuration Listresultaten](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Hämtar kryptering resultatet för en databas|

## <a name="next-steps"></a>Nästa steg

- En allmän beskrivning av transparent datakryptering, se [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Läs mer om transparent datakryptering med Bring Your Own Key-stöd för Azure SQL Database, Azure SQL Managed Instance och Data Warehouse i [Transparent datakryptering med stöd för Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Om du vill börja använda transparent datakryptering med stöd för Bring Your Own Key, finns i den här guiden [aktivera transparent datakryptering med hjälp av en egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Läs mer om Key Vault, den [dokumentationssidan för Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
