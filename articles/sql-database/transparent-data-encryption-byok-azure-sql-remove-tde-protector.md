---
title: Ta bort TDE-skydd - PowerShell
description: Så här kan du styra för att svara på ett potentiellt komprometterat TDE-skydd för en Azure SQL-databas eller datalager med hjälp av TDE med BYOK-stöd (Bring YOur Own Key).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067199"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Ta bort ett TDE-skydd (Transparent Data Encryption) med PowerShell

## <a name="prerequisites"></a>Krav

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen
- Du måste ha Azure PowerShell installerat och körs.
- Den här hjälpguiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för en Azure SQL-databas eller datalager. Läs [transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md) om du vill veta mer.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

 Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För specifika cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Installation finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Översikt

Den här guiden beskriver hur du svarar på ett eventuellt komprometterat TDE-skydd för en Azure SQL-databas eller data warehouse som använder TDE med kundhanterade nycklar i Azure Key Vault - Bring Your Own Key (BYOK). Mer information om BYOK-stöd för TDE finns på [översiktssidan](transparent-data-encryption-byok-azure-sql.md).

Följande förfaranden bör endast utföras i extrema fall eller i testmiljöer. Granska den vägledning som används noggrant, eftersom ta bort aktivt använda TDE-skydd från Azure Key Vault kommer att resultera i **databas otillgänglighet**.

Om en nyckel någonsin misstänks vara komprometterade, så att en tjänst eller användare hade obehörig åtkomst till nyckeln, är det bäst att ta bort nyckeln.

Tänk på att när TDE-skydd tas bort i Key Vault, i upp till 10 minuter alla krypterade databaser kommer att börja neka alla anslutningar med motsvarande felmeddelande och ändra dess tillstånd till [Otillgänglig](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

I följande steg beskrivs hur du kontrollerar tumavtrycken för TDE Protector som fortfarande används av VLF (Virtual Log Files) i en viss databas.
Tumavtrycket för det aktuella TDE-skydd för databasen och databas-ID kan hittas genom att köra:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

Följande fråga returnerar de VLFs och krypteraren respektive tumavtryck som används. Varje annat tumavtryck refererar till olika nyckel i Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

**PowerShell-kommandot Get-AzureRmSqlServerKeyVaultKey ger** tumavtrycket för TDE Protector som används i frågan, så att du kan se vilka nycklar som ska behållas och vilka nycklar som ska tas bort i AKV. Endast nycklar som inte längre används av databasen kan tas bort på ett säkert sätt från Azure Key Vault.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell-kommandot **az sql server key show** ger tumavtryck för TDE Protector som används i frågan, så att du kan se vilka nycklar som ska behållas och vilka nycklar som ska tas bort i AKV. Endast nycklar som inte längre används av databasen kan tas bort på ett säkert sätt från Azure Key Vault.

* * *

Denna hur-till guide går över två metoder beroende på önskat resultat efter incidenten svar:

- Så här håller du Azure SQL-databaser/datalager **tillgängliga**
- Så här gör du Azure SQL-databaser / datalager **otillgängliga**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Så här håller du de krypterade resurserna tillgängliga

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Skapa en [ny nyckel i Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Kontrollera att den här nya nyckeln skapas i ett separat nyckelvalv från det potentiellt komprometterade TDE-skydd, eftersom åtkomstkontrollen har etablerats på valvnivå.

2. Lägg till den nya nyckeln på servern med hjälp av [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) och [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets och uppdatera den som serverns nya TDE-beskyddare.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Kontrollera att servern och eventuella repliker har uppdaterats till det nya TDE-skyddet med [get-azsqlservertransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet.

   > [!NOTE]
   > Det kan ta några minuter innan det nya TDE-skyddet sprids till alla databaser och sekundära databaser under servern.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Gör en [säkerhetskopia av den nya nyckeln](/powershell/module/az.keyvault/backup-azkeyvaultkey) i Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Ta bort den komprometterade nyckeln från Key Vault med cmdleten [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Så här återställer du en nyckel till Key Vault i framtiden med cmdleten [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kommandoreferens finns i [Azure CLI keyvault](/cli/azure/keyvault/key).

1. Skapa en [ny nyckel i Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Kontrollera att den här nya nyckeln skapas i ett separat nyckelvalv från det potentiellt komprometterade TDE-skydd, eftersom åtkomstkontrollen har etablerats på valvnivå.

2. Lägg till den nya nyckeln på servern och uppdatera den som serverns nya TDE-skydd.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Kontrollera att servern och eventuella repliker har uppdaterats till det nya TDE-skyddet.

   > [!NOTE]
   > Det kan ta några minuter innan det nya TDE-skyddet sprids till alla databaser och sekundära databaser under servern.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Gör en säkerhetskopia av den nya nyckeln i Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Ta bort den komprometterade nyckeln från Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Så här återställer du en nyckel till Key Vault i framtiden.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Så här gör du de krypterade resurserna otillgängliga

1. Släpp de databaser som krypteras av den potentiellt komprometterade nyckeln.

   Databasen och loggfilerna säkerhetskopieras automatiskt, så en point-in-time-återställning av databasen kan göras när som helst (så länge du anger nyckeln). Databaserna måste tas bort innan ett aktivt TDE-skydd tas bort för att förhindra potentiell dataförlust på upp till 10 minuter av de senaste transaktionerna.

2. Säkerhetskopiera nyckelmaterialet i TDE-skydd i Key Vault.
3. Ta bort den potentiellt komprometterade nyckeln från Key Vault

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du roterar TDE-skydd för en server för att uppfylla [säkerhetskraven: Rotera transparent datakrypteringsskydd med PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Kom igång med Stöd för Ta med egen nyckel för TDE: [Aktivera TDE med din egen nyckel från Key Vault med PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
