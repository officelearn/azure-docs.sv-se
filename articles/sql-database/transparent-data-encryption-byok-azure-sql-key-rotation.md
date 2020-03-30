---
title: Rotera TDE-skydd - PowerShell
description: Lär dig hur du roterar TDE-skydd (Transparent Data Encryption) för en Azure SQL-server.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067176"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rotera TDE-skydd (Transparent Data Encryption) med PowerShell

I den här artikeln beskrivs nyckelrotation för en Azure SQL-server med hjälp av ett TDE-skydd från Azure Key Vault. Att rotera en Azure SQL-servers TDE-skydd innebär att du byter till en ny asymmetrisk nyckel som skyddar databaserna på servern. Nyckelrotation är en onlineåtgärd och bör bara ta några sekunder att slutföra, eftersom detta bara dekrypterar och krypterar om databasens datakrypteringsnyckel, inte hela databasen.

I den här guiden beskrivs två alternativ för att rotera TDE-skydd på servern.

> [!NOTE]
> Ett pausat SQL Data Warehouse måste återupptas före tangentrotationer.

> [!IMPORTANT]
> Ta inte bort tidigare versioner av nyckeln efter en överrullning. När nycklar rullas över krypteras vissa data fortfarande med de tidigare nycklarna, till exempel äldre säkerhetskopieringar av databaser.

## <a name="prerequisites"></a>Krav

- Den här hjälpguiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för en Azure SQL-databas eller datalager. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md).
- Du måste ha Azure PowerShell installerat och körs.
- [Rekommenderas men valfritt] Skapa nyckelmaterialet för TDE-skydd i en maskinvarusäkerhetsmodul (HSM) eller lokal nyckellagring först och importera nyckelmaterialet till Azure Key Vault. Läs [instruktionerna för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) för att lära dig mer.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För specifika cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Installation finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Manuell nyckelrotation

Manuell nyckelrotation använder följande kommandon för att lägga till en helt ny nyckel, som kan vara under ett nytt nyckelnamn eller till och med ett annat nyckelvalv. Med den här metoden stöder du att lägga till samma nyckel till olika nyckelvalv för att stödja scenarier med hög tillgänglighet och geo-dr.

> [!NOTE]
> Den sammanlagda längden för nyckelvalvets namn och nyckelnamn får inte överstiga 94 tecken.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Använd [cmdleterna Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)och [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [az keyvault-tangenten skapa](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server nyckel skapa](/cli/azure/sql/server/key#az-sql-server-key-create)och az sql server [tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) kommandon.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdlets

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Om du vill växla TDE-skyddet från Microsoft-hanterat till BYOK-läge använder du cmdleten [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Om du vill växla TDE-skyddet från BYOK-läget till Microsoft-hanterat använder du cmdleten [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

I följande exempel används [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Om du vill växla TDE-skydd från Microsoft-hanterat till BYOK-läge

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Om du vill växla TDE-skydd från BYOK-läget till Microsoft-hanterat

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Nästa steg

- Vid en säkerhetsrisk kan du läsa om hur du tar bort ett potentiellt komprometterat TDE-skydd: [Ta bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Kom igång med Integrering av Azure Key Vault och få stöd för din egen nyckel för TDE: [Aktivera TDE med din egen nyckel från Key Vault med PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
