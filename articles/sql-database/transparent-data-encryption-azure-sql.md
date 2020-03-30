---
title: Transparent datakryptering
description: En översikt över transparent datakryptering för SQL Database och SQL Analytics i Azure Synapse. Dokumentet täcker dess fördelar och alternativ för konfiguration, som inkluderar tjänsthanterad transparent datakryptering och Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/06/2020
ms.openlocfilehash: 5bbb537ef6545852423bf5315b7636671c598fdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255644"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparent datakryptering för SQL Database och Azure Synapse

Transparent datakryptering (TDE) hjälper till att skydda Azure SQL Database, Azure SQL Managed Instance och Azure Synapse mot hotet om skadlig offlineaktivitet genom att kryptera data i vila. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är transparent datakryptering aktiverat för alla nyligen distribuerade Azure SQL-databaser. Det går inte att använda TDE för att kryptera den logiska **huvuddatabasen** i SQL Database.  **Huvuddatabasen** innehåller objekt som behövs för att utföra TDE-åtgärderna i användardatabaserna.

TDE måste aktiveras manuellt för äldre databaser i Azure SQL Database, Azure SQL Managed Instance eller Azure Azure Synapse.
Hanterade instansdatabaser som skapats genom återställning ärver krypteringsstatus från källdatabasen.

Transparent datakryptering krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel som kallas databaskrypteringsnyckeln. Den här krypteringsnyckeln för databasen skyddas av det genomskinliga datakrypteringsskyddet. Protector är antingen ett tjänsthanterade certifikat (tjänsthanterad transparent datakryptering) eller en asymmetrisk nyckel som lagras i Azure Key Vault (Bring Your Own Key). Du anger det transparenta datakrypteringsskyddet på servernivå för Azure SQL Database och Azure Synapse och instansnivå för Azure SQL Managed Instance. Termen *server* refererar både till server och instans i hela det här dokumentet, om inte annat anges.

Vid start av databasen dekrypteras den krypterade databaskrypteringsnyckeln och används sedan för dekryptering och återkryptering av databasfilerna i SQL Server Database Engine-processen. Transparent datakryptering utför I/O-kryptering i realtid och dekryptering av data på sidnivå. Varje sida dekrypteras när de läses in i minnet och krypteras sedan innan de skrivs tillbaka till disken. En allmän beskrivning av transparent datakryptering finns i [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server som körs på en virtuell Azure-dator kan också använda en asymmetrisk nyckel från Key Vault. Konfigurationsstegen skiljer sig från att använda en asymmetrisk nyckel i SQL Database och SQL Managed Instance. Mer information finns i [Extensible key management med hjälp av Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Tjänsthanterad transparent datakryptering

I Azure är standardinställningen för transparent datakryptering att databaskrypteringsnyckeln skyddas av ett inbyggt servercertifikat. Det inbyggda servercertifikatet är unikt för varje server och krypteringsalgoritmen som används är AES 256. Om en databas är i en geo-replikeringsrelation skyddas både den primära och geo-sekundära databasen av den primära databasens överordnade servernyckel. Om två databaser är anslutna till samma server delar de också samma inbyggda certifikat.  Microsoft roterar automatiskt dessa certifikat i enlighet med den interna säkerhetsprincipen och rotnyckeln skyddas av ett internt microsoft-arkiv.  Kunder kan verifiera SQL Database-efterlevnaden av interna säkerhetsprinciper i oberoende granskningsrapporter från tredje part som är tillgängliga i [Microsoft Trust Center](https://servicetrust.microsoft.com/).

Microsoft flyttar och hanterar också nycklarna sömlöst efter behov för georeplikering och återställningar.

> [!IMPORTANT]
> Alla nyligen skapade SQL-databaser och databaser med hanterade instans krypteras som standard med hjälp av tjänsthanterad transparent datakryptering. Befintliga SQL-databaser som skapats före maj 2017 och SQL-databaser som skapats genom återställning, georeplikering och databaskopia krypteras inte som standard. Befintliga databaser för hanterade instans som skapats före februari 2019 krypteras inte som standard. Hanterade instansdatabaser som skapats genom återställning ärver krypteringsstatus från källan.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Kundhanterad transparent datakryptering - Ta med din egen nyckel

[TDE med kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) gör det möjligt att kryptera dek (Database Encryption Key) med en kundhanterad asymmetrisk nyckel som kallas TDE Protector.  Detta kallas också i allmänhet för BYOK-stöd (Bring Your Own Key) för transparent datakryptering. I BYOK-scenariot lagras TDE Protector i ett kundägt och hanterat [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Azures molnbaserade externa nyckelhanteringssystem. TDE Protector kan [genereras av nyckelvalvet eller överföras till nyckelvalvet](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) från en lokal HSM-enhet. TDE DEK, som lagras på startsidan i en databas, krypteras och dekrypteras av TDE Protector, som lagras i Azure Key Vault och lämnar aldrig nyckelvalvet.  SQL Database måste beviljas behörigheter till det kundägda nyckelvalvet för att dekryptera och kryptera DEK. Om behörigheterna för den logiska SQL-servern till nyckelvalvet återkallas, kommer en databas att vara otillgänglig och alla data krypteras. För Azure SQL Database anges TDE-protector på den logiska SQL-servernivån och ärvs av alla databaser som är associerade med den servern. För [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)anges TDE-protectorn på instansnivå och ärvs av alla *krypterade* databaser i den instansen. Termen *server* refererar både till server och instans i hela det här dokumentet, om inte annat anges.

Med TDE med Azure Key Vault-integrering kan användare styra nyckelhanteringsuppgifter, inklusive nyckelrotationer, nyckelvalvsbehörigheter, nyckelsäkerhetskopior och aktivera granskning/rapportering på alla TDE-skydd med Azure Key Vault-funktioner. Key Vault ger central nyckelhantering, utnyttjar noggrant övervakade maskinvarusäkerhetsmoduler (HSM) och möjliggör åtskillnad mellan arbetsuppgifter mellan hantering av nycklar och data för att uppfylla efterlevnaden av säkerhetsprinciper.
Mer information om transparent datakryptering med Azure Key Vault-integrering (Ta med eget nyckelstöd) för Azure SQL Database, SQL Managed Instance och Azure Synapse finns i [Transparent datakryptering med Azure Key Vault-integrering](transparent-data-encryption-byok-azure-sql.md).

Om du vill börja använda transparent datakryptering med Azure Key Vault-integrering (Ta med din egen nyckel)se hur du [guidar Aktivera transparent datakryptering med hjälp av din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Flytta en transparent datakrypteringsskyddad databas

Du behöver inte dekryptera databaser för åtgärder inom Azure. De genomskinliga datakrypteringsinställningarna i källdatabasen eller den primära databasen ärvs transparent på målet. Åtgärder som ingår omfattar:

- Geo-återställning
- Återställning av självbetjäning i tid
- Återställande av en borttagen databas
- Aktiv geo-replikering
- Skapa en databaskopia
- Återställning av säkerhetskopian till Azure SQL Managed Instance

> [!IMPORTANT]
> Det är inte tillåtet att ta manuell COPY-ONLY-säkerhetskopiering av en databas krypterad av tjänsthanterad TDE i Azure SQL Managed Instance, eftersom certifikat som används för kryptering inte är tillgängligt. Använd funktionen point-in-time-restore för att flytta den här typen av databas till en annan hanterad instans.

När du exporterar en transparent datakrypteringsskyddad databas krypteras inte det exporterade innehållet i databasen. Det här exporterade innehållet lagras i okrypterade BACPAC-filer. Var noga med att skydda BACPAC-filerna på rätt sätt och aktivera transparent datakryptering när importen av den nya databasen är klar.

Om BACPAC-filen till exempel exporteras från en lokal SQL Server-instans krypteras inte det importerade innehållet i den nya databasen automatiskt. Om BACPAC-filen exporteras till en lokal SQL Server-instans krypteras inte heller den nya databasen automatiskt.

Det enda undantaget är när du exporterar till och från en SQL-databas. Transparent datakryptering är aktiverad i den nya databasen, men BACPAC-filen i sig är fortfarande inte krypterad.


## <a name="manage-transparent-data-encryption"></a>Hantera transparent datakryptering
# <a name="portal"></a>[Portal](#tab/azure-portal)
Hantera transparent datakryptering i Azure-portalen.

Om du vill konfigurera transparent datakryptering via Azure-portalen måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

Du aktiverar och inaktiverar transparent datakryptering på databasnivå. Om du vill aktivera transparent datakryptering i en databas går du till [Azure-portalen](https://portal.azure.com) och loggar in med ditt Azure-administratörs- eller deltagarkonto. Hitta de genomskinliga inställningarna för datakryptering under användardatabasen. Som standard används tjänsthanterad transparent datakryptering. Ett transparent datakrypteringscertifikat genereras automatiskt för den server som innehåller databasen. För Azure SQL Managed Instance använder T-SQL för att aktivera och inaktivera transparent datakryptering i en databas.

![Tjänsthanterad transparent datakryptering](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

Du anger den genomskinliga nyckeln för datakryptering, även känd som det genomskinliga datakrypteringsskyddet, på servernivå. Om du vill använda transparent datakryptering med stöd för Bring Your Own Key och skydda dina databaser med en nyckel från Key Vault öppnar du de genomskinliga datakrypteringsinställningarna under servern.

![Transparent datakryptering med stöd för Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Hantera transparent datakryptering med powershell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill konfigurera transparent datakryptering via PowerShell måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets för Azure SQL Database och Azure Synapse

Använd följande cmdlets för Azure SQL Database och Azure Synapse:

| Cmdlet | Beskrivning |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Aktiverar eller inaktiverar transparent datakryptering för en databas|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Hämtar transparent datakrypteringstillstånd för en databas |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Kontrollerar krypteringsstatus för en databas |
| [Tillägg AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Lägger till en Key Vault-nyckel i en SQL Server-instans |
| [Hämta-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Hämtar nyckelvalvsnycklarna för en Azure SQL Database-server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Ställer in transparent datakrypteringsskydd för en SQL Server-instans |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Hämtar det transparenta datakrypteringsskyddet |
| [Ta bort-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Tar bort en Key Vault-nyckel från en SQL Server-instans |
|  | |

> [!IMPORTANT]
> För Azure SQL Managed Instance använder du kommandot T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) för att aktivera och inaktivera transparent datakryptering på databasnivå och kontrollera exempel [på PowerShell-skript](transparent-data-encryption-byok-azure-sql-configure.md) för att hantera transparent datakryptering på instansnivå.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Hantera transparent datakryptering med hjälp av Transact-SQL.

Anslut till databasen med hjälp av en inloggning som är administratör eller medlem i **dbmanager-rollen** i huvuddatabasen.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | ANGE KRYPTERING PÅ/AV krypterar eller dekrypterar en databas |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Returnerar information om krypteringstillståndet för en databas och dess associerade databaskrypteringsnycklar |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Returnerar information om krypteringstillståndet för varje informationslagernod och tillhörande databaskrypteringsnycklar |
|  | |

Du kan inte växla det genomskinliga datakrypteringsskyddet till en nyckel från Key Vault med hjälp av Transact-SQL. Använd PowerShell eller Azure-portalen.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
Hantera transparent datakryptering med hjälp av REST API.

Om du vill konfigurera transparent datakryptering via REST API måste du vara ansluten som Azure-ägare, deltagare eller SQL Security Manager.
Använd följande uppsättning kommandon för Azure SQL Database och Azure Synapse:

| Kommando | Beskrivning |
| --- | --- |
|[Skapa eller uppdatera server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Lägger till en Azure Active Directory-identitet i en SQL Server-instans (används för att bevilja åtkomst till Key Vault)|
|[Skapa eller uppdatera servernyckel](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Lägger till en Key Vault-nyckel i en SQL Server-instans|
|[Ta bort servernyckel](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Tar bort en Key Vault-nyckel från en SQL Server-instans|
|[Hämta servernycklar](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Hämtar en specifik Key Vault-nyckel från en SQL Server-instans|
|[Lista servernycklar efter server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Hämtar key vault-nycklarna för en SQL Server-instans |
|[Skapa eller uppdatera krypteringsskydd](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Ställer in transparent datakrypteringsskydd för en SQL Server-instans|
|[Hämta krypteringsskydd](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Hämtar det transparenta datakrypteringsskyddet för en SQL Server-instans|
|[Lista krypteringsskydd efter server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Hämtar transparenta datakrypteringsskydd för en SQL Server-instans |
|[Skapa eller uppdatera transparent datakrypteringskonfiguration](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiverar eller inaktiverar transparent datakryptering för en databas|
|[Hämta konfiguration för transparent datakryptering](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Hämtar den transparenta datakrypteringskonfigurationen för en databas|
|[Lista transparenta konfigurationsresultat för datakryptering](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Hämtar krypteringsresultatet för en databas|

## <a name="next-steps"></a>Nästa steg

- En allmän beskrivning av transparent datakryptering finns i [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Mer information om transparent datakryptering med stöd för Ta med din egen nyckel för Azure SQL Database, Azure SQL Managed Instance och Azure Synapse finns i [Transparent datakryptering med stöd för Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Om du vill börja använda transparent datakryptering med stöd för Bring Your Own Key läser du hur du [guidar Aktivera transparent datakryptering med hjälp av din egen nyckel från Key Vault med powershell](transparent-data-encryption-byok-azure-sql-configure.md).
- Mer information om Key Vault finns på [dokumentationssidan](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)för Key Vault .
