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
ms.date: 07/09/2018
ms.openlocfilehash: 935c54cf04854e75c39df55d8063138086be73a6
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309492"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent datakryptering för SQL Database och Data Warehouse

Transparent datakryptering (TDE) kan du skydda Azure SQL Database och Azure Data Warehouse mot skadlig aktivitet. Den utför i realtid kryptering och dekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande utan ändringar i programmet. Som standard är TDE aktiverat för alla nyligen distribuerade Azure SQL-databaser. TDE kan inte användas för att kryptera den logiska **master** databas i SQL Database.  Den **master** databasen innehåller objekt som behövs för att utföra TDE-åtgärder på användardatabaser.

TDE måste aktiveras manuellt för äldre databaser eller Azure SQL Data Warehouse.  

Transparent datakryptering krypteras lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln. Den här databaskrypteringsnyckeln skyddas av krypteringsskyddet transparent data. Skyddets är antingen en tjänsthanterad certifikat (tjänsthanterad transparent datakryptering) eller en asymmetrisk nyckel som lagras i Azure Key Vault (Bring Your Own Key). Du kan ställa in transparent data krypteringsskyddet på servernivå. 

När du startar för databasen är krypterad databaskrypteringsnyckeln dekrypteras och sedan användas för dekryptering och omkryptering av databasfilerna i SQL Server Database Engine-processen. Transparent datakryptering utför i realtid i/o-kryptering och dekryptering av data på sidnivå. Varje sida dekrypteras när den har läst in i minnet och sedan krypteras innan de skrivs till disk. En allmän beskrivning av transparent datakryptering, se [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server som körs på virtuella Azure-datorer även kan använda en asymmetrisk nyckel från Key Vault. Konfigurationsstegen skiljer sig från att använda en asymmetrisk nyckel i SQL-databas. Mer information finns i [utökningsbar nyckelhantering med hjälp av Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Tjänsthanterad transparent datakryptering

I Azure är standardinställningen för transparent datakryptering att databaskrypteringsnyckeln skyddas av ett certifikat för inbyggda. Inbyggda servercertifikatet är unikt för varje server. Om en databas finns i en relation för geo-replikering, både primär och geo-sekundära databasen skyddas av den primära databasen överordnade servernyckeln. Om två databaser är ansluten till samma server kan dela de samma inbyggda certifikat. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag.

Microsoft även sömlöst flyttar hanterar nycklar som behövs för geo-replikering och återställer. 

> [!IMPORTANT]
> Alla nyligen skapade SQL-databaser krypteras som standard med hjälp av tjänsthanterad transparent datakryptering. Befintliga databaser tidigare maj 2017 och databaser som har skapats via återställning, geo-replikering och databaskopian inte är krypterad som standard.
>

## <a name="bring-your-own-key"></a>Ta med din egen nyckel

Med stöd för Bring Your Own Key kan du ta kontrollen över dina nycklar för kryptering av data transparent och kontrollera vem som kan komma åt dem och när. Key Vault, som är det Azure-molnbaserade externa nyckelhanteringssystemet, är den första nyckelhanteringstjänst att transparent datakryptering har integrerats med stöd för Bring Your Own Key. Med stöd för Bring Your Own Key skyddas databaskrypteringsnyckeln av en asymmetrisk nyckel som lagras i Key Vault. En asymmetrisk nyckel lämnar aldrig Key Vault. När servern har behörighet till key vault, skickar servern grundläggande nyckelåtgärd begäranden till den via Key Vault. Du anger en asymmetrisk nyckel på servernivå och alla databaser under den här servern ärver den.

Med stöd för Bring Your Own Key kan kan du nu styra viktiga hanteringsuppgifter, till exempel nyckelrotationer och nyckelvalvet behörigheter. Du kan också ta bort nycklar och aktivera granskning/rapportering på alla krypteringsnycklar. Key Vault ger central nyckelhantering och använder nära övervakade HSM: er. Key Vault främjar uppdelning av hantering av nycklar och data för att uppfylla regelefterlevnad. Läs mer om Key Vault i den [dokumentationssidan för Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Läs mer om transparent datakryptering med Bring Your Own Key-stöd för SQL-databasen och informationslagret i [Transparent datakryptering med stöd för Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).

Om du vill börja använda transparent datakryptering med stöd för Bring Your Own Key, finns i den här guiden [aktivera transparent datakryptering med hjälp av en egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Flytta en transparent data encryption-skyddad databas

Du behöver inte dekryptera databaser för åtgärder i Azure. Transparent data encryption inställningarna på källdatabasen eller primära databasen ärvs transparent på målet. Det innefattar åtgärder som ingår:
- GEO-återställning.
- Självbetjänad point-in-time-återställning.
- Återställa en borttagen databas.
- Aktiv geo-replikering.
- Skapa en databaskopia.

När du exporterar en transparent data encryption-skyddad databas, inte är det exporterade innehållet på databasen krypterad. Det här exporterade innehållet lagras i okrypterade BACPAC-filer. Var noga med att skydda BACPAC-filer på rätt sätt och aktivera transparent datakryptering efter import av den nya databasen har slutförts.

Till exempel om BACPAC-fil har exporterats från en lokal SQL Server-instans, krypteras importerade innehållet i den nya databasen inte automatiskt. På samma sätt, om BACPAC-fil har exporterats till en lokal SQL Server-instans, den nya databasen också inte krypteras automatiskt.

Det enda undantaget är när du exporterar till och från en SQL-databas. Transparent datakryptering är aktiverat i den nya databasen, men själva BACPAC-filen fortfarande inte är krypterad.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Hantera transparent datakryptering i Azure portal

Om du vill konfigurera transparent datakryptering via Azure-portalen måste du vara ansluten som Azure ägare, deltagare eller SQL Security Manager. 

Du kan ställa in transparent datakryptering på databasnivå. Om du vill aktivera transparent datakryptering på en databas, gå till den [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-administratör eller bidragsgivare. Hitta transparent data encryption inställningarna för databasen. Som standard används tjänsthanterad transparent datakryptering. Ett krypteringscertifikat för transparent data genereras automatiskt för den server där databasen. 

![Tjänsthanterad transparent datakryptering](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Du anger transparent data encryption huvudnyckeln, även kallat transparent data encryption skyddets, på servernivå. Om du vill använda transparent datakryptering med stöd för Bring Your Own Key och skydda dina databaser med en nyckel från Key Vault, se krypteringsinställningar transparent data under din server. 

![Transparent datakryptering med stöd för Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Hantera transparent datakryptering med hjälp av PowerShell

Om du vill konfigurera transparent datakryptering via PowerShell, måste du vara ansluten som Azure ägare, deltagare eller SQL Security Manager. 

| Cmdlet | Beskrivning |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Aktiverar eller inaktiverar transparent datakryptering för en databas|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Hämtar transparent data encryption tillståndet för en databas |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Kontrollerar kryptering förloppet för en databas |
| [Lägg till AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Lägger till en Key Vault-nyckel till en SQL Server-instans |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Hämtar Key Vault-nycklar för en SQL server-instans  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Anger krypteringsskyddet transparent data för en SQL Server-instans |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Hämtar krypteringsskyddet transparent data |
| [Ta bort AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Tar bort en nyckel med Key Vault från en SQL Server-instans |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Hantera transparent datakryptering med hjälp av Transact-SQL

Ansluta till databasen med hjälp av en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) | Ställ in kryptering på/av krypterar eller dekrypterar en databas |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Returnerar information om krypteringsstatus för en databas och dess associerade databasen krypteringsnycklar |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Returnerar information om krypteringsstatus för varje data warehouse-nod och dess associerade databasen krypteringsnycklar | 
|  | |

Du kan inte växla krypteringsskyddet transparent data till en nyckel från Key Vault med hjälp av Transact-SQL. Använd PowerShell eller Azure-portalen.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Hantera transparent datakryptering med hjälp av REST-API
 
Om du vill konfigurera transparent datakryptering via REST API, måste du vara ansluten som Azure ägare, deltagare eller SQL Security Manager. 

| Kommando | Beskrivning |
| --- | --- |
|[Skapa eller uppdatera Server](/rest/api/sql/servers/createorupdate)|Lägger till en Azure Active Directory-identitet till en SQL Server-instans (används för att bevilja åtkomst till Key Vault)|
|[Skapa eller uppdatera servernyckel](/rest/api/sql/serverkeys/createorupdate)|Lägger till en Key Vault-nyckel till en SQL Server-instans|
|[Ta bort servernyckeln](/rest/api/sql/serverkeys/delete)|Tar bort en nyckel med Key Vault från en SQL Server-instans|
|[Hämta för Server](/rest/api/sql/serverkeys/get)|Hämtar en specifik Key Vault-nyckel från en SQL Server-instans|
|[Lista servernycklar av servern](/rest/api/sql/serverkeys/listbyserver)|Hämtar Key Vault-nycklar för en SQL Server-instans |
|[Skapa eller uppdatera Krypteringsskyddet](/rest/api/sql/encryptionprotectors/createorupdate)|Anger krypteringsskyddet transparent data för en SQL Server-instans|
|[Hämta Krypteringsskyddet](/rest/api/sql/encryptionprotectors/get)|Hämtar krypteringsskyddet transparent data för en SQL Server-instans|
|[Lista kryptering skydd av servern](/rest/api/sql/encryptionprotectors/listbyserver)|Hämtar transparent data encryption skydd för en SQL Server-instans |
|[Skapa eller uppdatera Transparent Data Encryption konfiguration](/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiverar eller inaktiverar transparent datakryptering för en databas|
|[Hämta konfiguration för Transparent kryptering](/rest/api/sql/transparentdataencryptions/get)|Hämtar transparent data encryption konfigurationen för en databas|
|[Transparent Data Encryption Configuration Listresultaten](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|Hämtar kryptering resultatet för en databas|

## <a name="next-steps"></a>Nästa steg

- En allmän beskrivning av transparent datakryptering, se [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Läs mer om transparent datakryptering med Bring Your Own Key-stöd för SQL-databasen och informationslagret i [Transparent datakryptering med stöd för Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Om du vill börja använda transparent datakryptering med stöd för Bring Your Own Key, finns i den här guiden [aktivera transparent datakryptering med hjälp av en egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Läs mer om Key Vault, den [dokumentationssidan för Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
