---
title: Aktivera SQL TDE med Azure Key Vault
description: Lär dig hur du konfigurerar en Azure SQL Database och Data Warehouse för att börja använda Transparent Data Encryption (TDE) för kryptering i vila med PowerShell eller CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452765"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell och CLI: Aktivera transparent datakryptering med kundhanterad nyckel från Azure Key Vault

Den här artikeln går igenom hur du använder en nyckel från Azure Key Vault for Transparent Data Encryption (TDE) på en SQL-databas eller datalager. Om du vill veta mer om TDE med Azure Key Vault-integrering - Ta med din egen nyckel (BYOK) Support, besök [TDE med kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Förutsättningar för PowerShell

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
- [Rekommenderas men valfritt] Har en maskinvarusäkerhetsmodul (HSM) eller ett lokalt nyckellager för att skapa en lokal kopia av TDE Protector-nyckelmaterialet.
- Du måste ha Azure PowerShell installerat och körs.
- Skapa ett Azure Key Vault och -nyckel som ska användas för TDE.
  - [Instruktioner för hur du använder en maskinvarusäkerhetsmodul (HSM) och Key Vault](../key-vault/keys/hsm-protected-keys.md)
    - Nyckelvalvet måste ha följande egenskap som ska användas för TDE:
  - [mjuk-ta bort](../key-vault/general/overview-soft-delete.md) och rensa skydd
- Nyckeln måste ha följande attribut som ska användas för TDE:
   - Inget utgångsdatum
   - Inte inaktiverad
   - Kunna utföra *få,* *radbryta nyckel,* *packa upp nyckelåtgärder*

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För specifika cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Mer information om Key Vault finns i [PowerShell-instruktioner från Key Vault](../key-vault/secrets/quick-create-powershell.md) och [så här använder du Soft delete för Key Vault med PowerShell](../key-vault/general/soft-delete-powershell.md).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Tilldela en Azure AD-identitet till servern

Om du har en befintlig server använder du följande för att lägga till en Azure AD-identitet på servern:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Om du skapar en server använder du cmdleten [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) med taggen -Identity för att lägga till en Azure AD-identitet när servern skapas:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Bevilja behörigheter för nyckelvalv till servern

Använd cmdleten [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att ge servern åtkomst till nyckelvalvet innan du använder en nyckel från den för TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Lägg till Key Vault-nyckeln på servern och ange TDE-skydd

- Använd [cmdleten Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) för att hämta nyckel-ID:et från nyckelvalvet
- Använd cmdleten [Add-AzSqlServerKeyVaultKey för](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) att lägga till nyckeln från Nyckelvalvet till servern.
- Använd [cmdleten Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att ställa in nyckeln som TDE-skydd för alla serverresurser.
- Använd [cmdleten Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) för att bekräfta att TDE-skyddet har konfigurerats som avsett.

> [!NOTE]
> Den sammanlagda längden för nyckelvalvets namn och nyckelnamn får inte överstiga 94 tecken.

> [!TIP]
> Ett exempel keyid från Key Vault:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Aktivera TDE

Använd [cmdleten Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) för att slå på TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Nu har databasen eller datalagret TDE aktiverat med en krypteringsnyckel i Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Kontrollera krypteringstillstånd och krypteringsaktivitet

Använd [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) för att hämta krypteringstillståndet och [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) för att kontrollera krypteringsförloppet för en databas eller ett informationslager.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Information om hur du installerar den obligatoriska Kommandoradsgränssnittsversionen 2.0 eller senare och ansluter till din Azure-prenumeration finns i [Installera och konfigurera Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Mer information om Key Vault finns i [Hantera nyckelvalv med CLI 2.0](../key-vault/general/manage-with-cli2.md) och [Så här använder du Soft Delete för Key Vault med CLI](../key-vault/general/soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Tilldela en Azure AD-identitet till servern

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Behåll "principalID" från att skapa servern, det är det objekt-ID som används för att tilldela nyckelvalvbehörigheter i nästa steg

## <a name="grant-key-vault-permissions-to-your-server"></a>Bevilja behörigheter för nyckelvalv till servern

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Behåll nyckeln URI eller keyID för den nya nyckeln för nästa steg, till exempel:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Lägg till Key Vault-nyckeln på servern och ange TDE-skydd

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Den sammanlagda längden för nyckelvalvets namn och nyckelnamn får inte överstiga 94 tecken.

## <a name="turn-on-tde"></a>Aktivera TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Nu har databasen eller datalagret TDE aktiverat med en kundhanterad krypteringsnyckel i Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Kontrollera krypteringstillstånd och krypteringsaktivitet

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdlets

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Använd [cmdleten Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) för att stänga av TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Använd cmdleten [Get-AzSqlServerKeyVaultKeyKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för att returnera listan över nycklar till nyckelvalv som lagts till på servern.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Använd [Delete-AzSqlServerKeyVaultKey för](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) att ta bort en Key Vault-nyckel från servern.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Allmänna databasinställningar finns i [az sql](/cli/azure/sql).

- För inställningar för valvnyckel finns i [az sql-servernyckel](/cli/azure/sql/server/key).

- För TDE-inställningar finns i [az sql server tde-key](/cli/azure/sql/server/tde-key) och [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Felsökning

Kontrollera följande om ett problem uppstår:

- Om nyckelvalvet inte kan hittas kontrollerar du att du har rätt prenumeration.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Om den nya nyckeln inte kan läggas till på servern eller om den nya nyckeln inte kan uppdateras som TDE Protector kontrollerar du följande:
   - Nyckeln bör inte ha ett utgångsdatum
   - Nyckeln måste ha *aktiverade åtgärder för att hämta,* *radbryta*och *packa upp nyckelåtgärder.*

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du roterar TDE-skydd för en server för att uppfylla [säkerhetskraven: Rotera transparent datakrypteringsskydd med PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Vid en säkerhetsrisk kan du läsa om hur du tar bort en potentiellt komprometterade TDE-skydd: [Ta bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
