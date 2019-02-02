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
ms.date: 12/06/2018
ms.openlocfilehash: 14a39d283d9ec4f8d5267e6a6628609ac79879ee
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567510"
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

## <a name="prerequisites"></a>Förutsättningar

- Den här guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skyddet för en Azure SQL Database eller datalagret. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md).
- Du måste ha Azure PowerShell-version 3.7.0 eller senare installerat och körs. 
- (Rekommenderas men valfria) Skapa nyckelmaterial för TDE-skydd i en maskinvarusäkerhetsmodul (HSM) eller lokal nyckel lagra först och importera nyckelmaterial till Azure Key Vault. Följ den [anvisningar för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) vill veta mer.

## <a name="option-1-auto-rotation"></a>Alternativ 1: Automatisk rotation

Generera en ny version av den befintliga nyckeln för TDE-skydd i Key Vault under samma nyckelnamn och nyckelvalvet. Azure SQL-tjänsten startar med den här nya versionen inom 24 timmar. 

Att skapa en ny version av TDE-skydd med hjälp av den [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) cmdlet:

   ```powershell
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>
   ```

## <a name="option-2-manual-rotation"></a>Alternativ 2: Manuell rotation

Alternativet använder den [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [Lägg till AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey), och [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) -cmdletar för att lägga till en helt ny nyckel, vilket kan vara under ett nytt namn eller en annan nyckelvalv. 

>[!NOTE]
>Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Andra användbara PowerShell-cmdletar

- Om du vill växla TDE-skydd från Microsoft-hanterade till BYOK-läge, Använd den [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Om du vill växla TDE-skydd från BYOK-läge som hanteras av Microsoft använder den [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du tar bort ett potentiellt komprometterade TDE-skydd i händelse av en säkerhetsrisk: [Tar bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Kom igång med Bring Your Own Key stöd för transparent Datakryptering: [Aktivera transparent Datakryptering med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
