---
title: Rotera TDE-skydd – PowerShell
description: Lär dig hur du roterar transparent datakryptering (TDE) skydd för en Azure SQL-Server.
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
ms.openlocfilehash: 553d8535d2fdbd7daa5c93535c7c4bd51f2da1a1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995813"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rotera transparent datakryptering-skyddet (TDE) med hjälp av PowerShell

Den här artikeln beskriver nyckel rotation för en Azure SQL-Server med hjälp av ett TDE-skydd från Azure Key Vault. Att rotera en Azure SQL Servers TDE-skydd innebär att växla till en ny asymmetrisk nyckel som skyddar databaserna på servern. Nyckel rotation är en online-åtgärd som bara tar några sekunder att slutföra, eftersom detta bara dekrypterar och krypterar om databasens data krypterings nyckel, inte hela databasen.

I den här guiden beskrivs två alternativ för att rotera TDE-skyddskomponenten på servern.

> [!NOTE]
> En pausad SQL Data Warehouse måste återupptas innan nyckel rotationer.

> [!IMPORTANT]
> Ta inte bort tidigare versioner av nyckeln efter en omstart. När nycklar överförs är vissa data fortfarande krypterade med de tidigare nycklarna, till exempel äldre databas säkerhets kopior.

## <a name="prerequisites"></a>Krav

- Den här instruktions guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för en Azure SQL Database eller ett informations lager. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md).
- Du måste ha Azure PowerShell installerat och igång.
- [Rekommenderas men valfritt] Skapa nyckel materialet för TDE-skyddet i en HSM-modul (Hardware Security Module) eller lokal nyckel lagring först och importera nyckel materialet till Azure Key Vault. Följ [instruktionerna för att använda en maskin varu säkerhetsmodul (HSM) och Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) om du vill veta mer.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För vissa cmdlets, se [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell-modulen Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Information om installation finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Manuell nyckel rotation

Manuell nyckel rotation använder följande kommandon för att lägga till en helt ny nyckel, som kan vara under ett nytt nyckel namn eller till och med ett annat nyckel valv. Med den här metoden kan du lägga till samma nyckel till olika nyckel valv för att stödja hög tillgänglighet och geo-Dr-scenarier.

> [!NOTE]
> Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Använd cmdletarna [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)och [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [nyckeln AZ Key Vault Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)och [AZ SQL Server TDE-Key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azure-cli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdletar

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att växla TDE-skydd från Microsoft-hanterat till BYOK-läge.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att växla TDE-skyddet från BYOK-läge till Microsoft-hanterad.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

I följande exempel används [AZ SQL Server TDE-Key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- För att växla TDE-skydd från Microsoft-hanterat till BYOK-läge

   ```azure-cli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- För att växla TDE-skyddet från BYOK-läge till Microsoft-hanterat,

   ```azure-cli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Nästa steg

- I händelse av en säkerhets risk lär du dig hur du tar bort ett potentiellt komprometterat TDE-skydd: [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Kom igång med Azure Key Vault integration och Bring Your Own Key stöd för TDE: [Aktivera TDE med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
