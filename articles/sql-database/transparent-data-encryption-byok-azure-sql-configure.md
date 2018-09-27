---
title: 'PowerShell och CLI: Aktivera SQL TDE - din nyckel – Azure SQL Database | Microsoft Docs'
description: Lär dig hur du konfigurerar en Azure SQL Database och Data Warehouse för att börja använda Transparent datakryptering (TDE) för kryptering i vila med PowerShell eller CLI.
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
ms.date: 09/20/2018
ms.openlocfilehash: 0fad0cd32e8df38c5a9c06ecf01a14340f1bc9ef
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165083"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell och CLI: aktivera Transparent datakryptering med hjälp av en egen nyckel från Azure Key Vault

Den här artikeln beskriver hur du använder en nyckel från Azure Key Vault för Transparent datakryptering (TDE) på en SQL-databas eller datalager. Läs mer om transparent Datakryptering med stöd för ta med din egen nyckel (BYOK) [TDE Bring Your Own Key till Azure SQL](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

- Du måste ha en Azure-prenumeration och vara administratör på den aktuella prenumerationen.
- (Rekommenderas men valfria) Ha en maskinvarusäkerhetsmodul (HSM) eller lokal nyckel lagra för att skapa en lokal kopia av nyckelmaterial TDE-skydd.
- Du måste ha Azure PowerShell-version 4.2.0 eller senare installerat och körs. 
- Skapa en Azure Key Vault och en nyckel som ska användas för TDE.
   - [PowerShell-instruktioner från Key Vault](../key-vault/key-vault-get-started.md)
   - [Anvisningar för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Nyckelvalvet måste ha följande egenskap som ska användas för transparent Datakryptering:
   - [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)
   - [Så här använder du Key Vault mjuk borttagning med PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Nyckeln måste ha följande attribut som ska användas för transparent Datakryptering:
   - Inga upphör att gälla
   - Inte inaktiverat
   - Kan utföra *hämta*, *omsluta nyckel*, *nyckelomslutning* åtgärder

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Steg 1. Tilldela en Azure AD-identitet till din server 

Om du har en befintlig server kan du använda följande för att lägga till en Azure AD-identitet till din server:

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Om du skapar en server, använder du den [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) cmdlet med taggen-identitet för att lägga till en Azure AD-identitet när servern skapas:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Steg 2. Bevilja behörigheter för Key Vault till servern

Använd den [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet för att ge din serveråtkomst till nyckeln valvet innan du använder en nyckel från den för transparent Datakryptering.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Steg 3. Lägg till Key Vault-nyckeln till servern och ange TDE-skydd

- Använd den [Lägg till AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) cmdlet för att lägga till nyckeln från Key Vault på servern.
- Använd den [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet för att ange nyckeln som TDE-skydd för alla serverresurser.
- Använd den [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) cmdlet för att bekräfta att TDE-skyddet har konfigurerats rätt.

> [!Note]
> Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.
> 

>[!Tip]
>Ett exempel KeyId från Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Steg 4. Aktivera transparent Datakryptering 

Använd den [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) cmdlet för att aktivera TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Databasen eller datalagret har nu TDE aktiverat med en krypteringsnyckel i Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Steg 5. Kontrollera krypteringsstatus och kryptering aktivitet

Använd den [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) att hämta krypteringsstatus och [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) att kontrollera förloppet för kryptering för en databasen eller datalagret.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Andra användbara PowerShell-cmdletar

- Använd den [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) cmdlet för att inaktivera TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Använd den [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) cmdleten returnerade listan med Key Vault-nycklar som läggs till på servern.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Använd den [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) att ta bort en Key Vault-nyckeln från servern.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Felsökning

Kontrollera följande om ett problem inträffar:
- Om det inte går att hitta nyckelvalvet, se till att du befinner dig i rätt prenumeration med hjälp av den [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) cmdlet.

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Om den nya nyckeln inte kan läggas till servern eller den nya nyckeln kan inte uppdateras som TDE-skydd kan du kontrollera följande:
   - Nyckeln ska inte ha ett förfallodatum
   - Nyckeln måste ha den *hämta*, *omsluta nyckel*, och *nyckelomslutning* operations aktiverat.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du rotera TDE-skydd för en server för att uppfylla krav på säkerhet: [rotera Transparent datakryptering skydd med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Om en säkerhetsrisk, lär du dig hur du tar bort ett potentiellt komprometterade TDE-skydd: [tar bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Krav för CLI

- Du måste ha en Azure-prenumeration och vara administratör på den aktuella prenumerationen.
- (Rekommenderas men valfria) Ha en maskinvarusäkerhetsmodul (HSM) eller lokal nyckel lagra för att skapa en lokal kopia av nyckelmaterial TDE-skydd.
- Kommandoradsgränssnitt 2.0 eller senare. Om du vill installera den senaste versionen och ansluter till din Azure-prenumeration, se [installerar och konfigurerar Azure plattformsoberoende kommandoradsgränssnitt 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Skapa en Azure Key Vault och en nyckel som ska användas för TDE.
   - [Hantera Nyckelvalv med hjälp av CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
   - [Anvisningar för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Nyckelvalvet måste ha följande egenskap som ska användas för transparent Datakryptering:
   - [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)
   - [Så här använder du Key Vault mjuk borttagning med CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Nyckeln måste ha följande attribut som ska användas för transparent Datakryptering:
   - Inga upphör att gälla
   - Inte inaktiverat
   - Kan utföra *hämta*, *omsluta nyckel*, *nyckelomslutning* åtgärder
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Steg 1. Skapa en server och tilldela en Azure AD-identitet till din server
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Steg 2. Bevilja behörigheter för Key Vault till servern
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Steg 3. Lägg till Key Vault-nyckeln till servern och ange TDE-skydd
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.
> 

>[!Tip]
>Ett exempel KeyId från Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Steg 4. Aktivera transparent Datakryptering 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

Databasen eller datalagret har nu TDE aktiverat med en krypteringsnyckel i Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Steg 5. Kontrollera krypteringsstatus och kryptering aktivitet

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>SQL-CLI-referenser

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

