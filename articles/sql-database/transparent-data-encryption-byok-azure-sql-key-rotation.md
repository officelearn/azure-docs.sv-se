---
title: PowerShell - rotera TDE-skydd – Azure SQL Database | Microsoft Docs
description: Lär dig mer om att rotera Transparent datakryptering (TDE)-skydd för en Azure SQL server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330864"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rotera Transparent datakryptering (TDE)-skydd med hjälp av PowerShell

Den här artikeln beskriver nyckelrotation för en Azure SQL-server med hjälp av ett TDE-skydd från Azure Key Vault. Rotera en Azure SQL-server TDE-skydd innebär byter till en ny asymmetrisk nyckel som skyddar databaser på servern. Rotation av är en online-åtgärd och tar bara några sekunder att slutföra eftersom det endast dekrypterar återkrypterar databasens datakrypteringsnyckeln, inte hela databasen.

Den här guiden beskrivs två alternativ för att rotera TDE-skydd på servern.

> [!NOTE]
> Ett pausat SQL Data Warehouse måste det köras innan nyckelrotationer.
>

> [!IMPORTANT]
> **Gör inte att ta bort** tidigare versioner av nyckeln när du har en förnyelse.  När nycklar har flyttats över krypteras vissa data fortfarande med tidigare nycklar, till exempel äldre säkerhetskopior av databasen. 
>

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

- Den här guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skyddet för en Azure SQL Database eller datalagret. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md).
- Du måste ha Azure PowerShell installerad och igång.
- (Rekommenderas men valfria) Skapa nyckelmaterial för TDE-skydd i en maskinvarusäkerhetsmodul (HSM) eller lokal nyckel lagra först och importera nyckelmaterial till Azure Key Vault. Följ den [anvisningar för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) vill veta mer.

## <a name="manual-key-rotation"></a>Manuell nyckelrotation

Rotation av manuell använder den [Lägg till AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Lägg till AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), och [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) -cmdletar för att lägga till en helt ny nyckel, vilket kan vara under ett nytt namn eller en annan nyckelvalv. Med den här metoden har stöd för att lägga till samma nyckel i olika nyckelvalv för att ge stöd för hög tillgänglighet och geo-dr-scenarier.

>[!NOTE]
>Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.

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

## <a name="option-2-manual-rotation"></a>Alternativ 2: Manuell rotation

Alternativet använder den [Lägg till AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), [Lägg till AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), och [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) -cmdletar för att lägga till en helt ny nyckel, vilket kan vara under ett nytt namn eller en annan nyckelvalv. 

>[!NOTE]
>Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.
>

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
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Andra användbara PowerShell-cmdletar

- Om du vill växla TDE-skydd från Microsoft-hanterade till BYOK-läge, Använd den [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Om du vill växla TDE-skydd från BYOK-läge som hanteras av Microsoft använder den [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du tar bort ett potentiellt komprometterade TDE-skydd i händelse av en säkerhetsrisk: [Tar bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Kom igång med Azure Key Vault-integrering och stöd för Bring Your Own Key för transparent Datakryptering: [Aktivera transparent Datakryptering med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
