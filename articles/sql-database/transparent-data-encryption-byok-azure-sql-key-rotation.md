---
title: Rotera TDE-skydd – PowerShell
description: Lär dig hur du roterar transparent datakryptering (TDE) skydd för en Azure SQL-Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824686"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rotera transparent datakryptering-skyddet (TDE) med hjälp av PowerShell

Den här artikeln beskriver nyckel rotation för en Azure SQL-Server med hjälp av ett TDE-skydd från Azure Key Vault. Att rotera en Azure SQL Servers TDE-skydd innebär att växla till en ny asymmetrisk nyckel som skyddar databaserna på servern. Nyckel rotation är en online-åtgärd som bara tar några sekunder att slutföra, eftersom detta bara dekrypterar och krypterar om databasens data krypterings nyckel, inte hela databasen.

I den här guiden beskrivs två alternativ för att rotera TDE-skyddskomponenten på servern.

> [!NOTE]
> En pausad SQL Data Warehouse måste återupptas innan nyckel rotationer.
>

> [!IMPORTANT]
> **Ta inte bort** tidigare versioner av nyckeln efter en omstart.  När nycklar överförs är vissa data fortfarande krypterade med de tidigare nycklarna, till exempel äldre databas säkerhets kopior. 
>

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

- Den här instruktions guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för en Azure SQL Database eller ett informations lager. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md).
- Du måste ha Azure PowerShell installerat och igång.
- [Rekommenderas men valfritt] Skapa nyckel materialet för TDE-skyddet i en HSM-modul (Hardware Security Module) eller lokal nyckel lagring först och importera nyckel materialet till Azure Key Vault. Följ [instruktionerna för att använda en maskin varu säkerhetsmodul (HSM) och Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) om du vill veta mer.

## <a name="manual-key-rotation"></a>Manuell nyckel rotation

Manuell nyckel rotation använder cmdletarna [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)och [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att lägga till en helt ny nyckel, som kan vara under ett nytt nyckel namn eller till och med en annan nyckel Vault. Med den här metoden kan du lägga till samma nyckel till olika nyckel valv för att stödja hög tillgänglighet och geo-Dr-scenarier.

>[!NOTE]
>Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Andra användbara PowerShell-cmdletar

- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att växla TDE-skydd från Microsoft-hanterat till BYOK-läge.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att växla TDE-skyddet från BYOK-läge till Microsoft-hanterad.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Nästa steg

- I händelse av en säkerhets risk lär du dig hur du tar bort ett potentiellt komprometterat TDE-skydd: [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Kom igång med Azure Key Vault integration och Bring Your Own Key stöd för TDE: [Aktivera TDE med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
