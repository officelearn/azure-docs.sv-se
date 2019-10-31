---
title: 'PowerShell och CLI: Aktivera SQL-TDE – med Azure Key Vault – ta med din egen nyckel Azure SQL Database | Microsoft Docs'
description: Lär dig hur du konfigurerar en Azure SQL Database och ett informations lager för att börja använda transparent datakryptering (TDE) för kryptering vid vila med hjälp av PowerShell eller CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: beeb5fa4f979ac457db8a779dd8f8f2e94ef87f5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163849"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell och CLI: Aktivera transparent datakryptering med kundhanterad nyckel från Azure Key Vault

Den här artikeln beskriver hur du använder en nyckel från Azure Key Vault för transparent datakryptering (TDE) på en SQL Database eller ett informations lager. Om du vill veta mer om TDE med stöd för Azure Key Vault integration-Bring Your Own Key (BYOK) går du till [TDE med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
- [Rekommenderas men valfritt] Ha en maskinvaru-säkerhetsmodul (HSM) eller lokal nyckel lagring för att skapa en lokal kopia av TDE skydds nyckel materialet.
- Du måste ha Azure PowerShell installerat och igång. 
- Skapa en Azure Key Vault och nyckel som ska användas för TDE.
  - [PowerShell-instruktioner från Key Vault](../key-vault/quick-create-powershell.md)
  - [Instruktioner för att använda en maskinvaru-säkerhetsmodul (HSM) och Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Nyckel valvet måste ha följande egenskap för att kunna användas för TDE:
  - [mjukt borttagnings-](../key-vault/key-vault-ovw-soft-delete.md) och rensnings skydd
  - [Så här använder du Key Vault mjuk borttagning med PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Nyckeln måste ha följande attribut för att kunna användas för TDE:
   - Inget förfallo datum
   - Inte inaktiverat
   - Kan utföra *Get*-, *wrap*-och *unwrap Key* -åtgärder

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Steg 1. Tilldela en Azure AD-identitet till servern 

Om du har en befintlig server kan du använda följande för att lägga till en Azure AD-identitet på servern:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Om du skapar en server använder du cmdleten [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) med taggen tag identitet för att lägga till en Azure AD-identitet när servern skapas:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Steg 2. Bevilja Key Vault behörigheter till servern

Använd cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att ge servern åtkomst till nyckel valvet innan du använder en nyckel från den för TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Steg 3. Lägg till Key Vault-nyckeln till servern och ange TDE-skyddet


- Använd cmdleten [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) för att hämta nyckel-ID: t från Key Vault
- Använd cmdleten [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) för att lägga till nyckeln från Key Vault på servern.
- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att ange nyckeln som TDE-skydd för alla server resurser.
- Använd cmdleten [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) för att bekräfta att TDE-skyddet har kon figurer ATS som avsett.

> [!Note]
> Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.
> 

>[!Tip]
>Ett exempel på KeyId från Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Steg 4. Aktivera TDE 

Använd cmdleten [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) för att aktivera TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Databasen eller informations lagret har nu TDE Aktiver ATS med en krypterings nyckel i Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Steg 5. Kontrol lera krypterings tillstånd och krypterings aktivitet

Använd [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) för att hämta krypterings tillstånd och [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) för att kontrol lera krypterings förloppet för en databas eller ett informations lager.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Andra användbara PowerShell-cmdletar

- Använd cmdleten [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) för att inaktivera TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled"
   ```
 
- Använd cmdleten [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för att returnera listan över Key Vault nycklar som har lagts till på servern.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Använd [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) om du vill ta bort en Key Vault nyckel från servern.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Felsöka

Kontrol lera följande om ett problem inträffar:
- Om nyckel valvet inte hittas kontrollerar du att du har rätt prenumeration med hjälp av cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) .

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Om den nya nyckeln inte kan läggas till på servern, eller om den nya nyckeln inte kan uppdateras som TDE-skydd, kontrollerar du följande:
   - Nyckeln ska inte ha ett utgångs datum
   - Nyckeln måste ha de *Get*-, *wrap*-och *unwrap* -nycklar som är aktiverade.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du roterar TDE-skyddet för en server för att uppfylla säkerhets kraven: [rotera Transparent datakryptering-skyddet med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- I händelse av en säkerhets risk lär du dig hur du tar bort ett potentiellt komprometterat TDE-skydd: [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Krav för CLI

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
- [Rekommenderas men valfritt] Ha en maskinvaru-säkerhetsmodul (HSM) eller lokal nyckel lagring för att skapa en lokal kopia av TDE skydds nyckel materialet.
- Kommando rads gränssnitt version 2,0 eller senare. Om du vill installera den senaste versionen och ansluta till din Azure-prenumeration läser du [Installera och konfigurera Azures plattforms oberoende kommando rads gränssnitt 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Skapa en Azure Key Vault och nyckel som ska användas för TDE.
  - [Hantera Key Vault med CLI 2,0](../key-vault/key-vault-manage-with-cli2.md)
  - [Instruktioner för att använda en maskinvaru-säkerhetsmodul (HSM) och Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Nyckel valvet måste ha följande egenskap för att kunna användas för TDE:
  - [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)
  - [Så här använder du Key Vault mjuk borttagning med CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Nyckeln måste ha följande attribut för att kunna användas för TDE:
   - Inget förfallo datum
   - Inte inaktiverat
   - Kan utföra *Get*-, *wrap*-och *unwrap Key* -åtgärder
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Steg 1. Skapa en server med en Azure AD-identitet
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Behåll "principalID" från att skapa servern, det är det objekt-ID som används för att tilldela Key Vault-behörigheter i nästa steg
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Steg 2. Bevilja Key Vault behörigheter till den logiska SQL-servern
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Behåll nyckel-URI: n eller keyID för den nya nyckeln för nästa steg, till exempel: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Steg 3. Lägg till Key Vault-nyckeln till servern och ange TDE-skyddet
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.
> 

  
## <a name="step-4-turn-on-tde"></a>Steg 4. Aktivera TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Databasen eller informations lagret har nu TDE Aktiver ATS med en kundhanterad krypterings nyckel i Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Steg 5. Kontrol lera krypterings tillstånd och krypterings aktivitet

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL CLI-referenser

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

