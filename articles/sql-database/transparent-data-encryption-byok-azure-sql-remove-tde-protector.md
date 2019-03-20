---
title: PowerShell – ta bort ett TDE-skydd – Azure SQL Database | Microsoft Docs
description: Här guiden för att svara på ett potentiellt komprometterade TDE-skydd för en Azure SQL Database eller Data Warehouse med transparent Datakryptering med stöd för ta med din egen nyckel (BYOK).
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
ms.date: 03/12/2019
ms.openlocfilehash: a49c22a0597c34075de3e5fd7b9a324169e1da00
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895190"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Ta bort ett Transparent datakryptering (TDE)-skydd med hjälp av PowerShell

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

- Du måste ha en Azure-prenumeration och vara administratör på den aktuella prenumerationen
- Du måste ha Azure PowerShell installerad och igång. 
- Den här guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skyddet för en Azure SQL Database eller datalagret. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-azure-sql.md) vill veta mer.

## <a name="overview"></a>Översikt

Den här guiden beskriver hur du svarar på ett potentiellt komprometterade TDE-skydd för en Azure SQL-databas eller datalager som använder transparent Datakryptering med Kundhanterade nycklar i Azure Key Vault - stöd för ta med din egen nyckel (BYOK). Läs mer om BYOK-stöd för transparent Datakryptering i den [översiktssidan](transparent-data-encryption-byok-azure-sql.md). 

Följande procedurer bör endast göras i extrema fall eller i miljöer för testning. Granska den här guiden noggrant, som tar bort aktivt används TDE-skydd från Azure Key Vault kan resultera i **dataförlust**. 

Om en nyckel är någonsin misstänkt äventyras, så att en tjänst eller en användare hade obehörig åtkomst till nyckeln, är det bäst att ta bort nyckeln.

Tänk på att om TDE-skyddet tas bort i Key Vault **blockeras alla anslutningar till krypterade databaser under servern och databaserna hamnar i offlineläge och tas bort inom 24 timmar**. Gamla säkerhetskopior som krypterats med den komprometterade nyckeln är inte längre tillgängliga.

Den här guiden går via två sätt beroende på det önskade resultatet efter incident svaret:

- Att hålla Azure SQL-databaser / Data Warehouses **tillgänglig**
- Att göra Azure SQL-databaser / Data Warehouses **otillgängligt**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Att behålla krypterade resurser tillgängliga

1. Skapa en [ny nyckel i Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Kontrollera att den här nya nyckeln skapas i ett separat nyckelvalv från potentiellt komprometterade TDE-skyddet, eftersom åtkomstkontroll etableras på en valvnivå.
2. Lägg till den nya nyckeln till servern med den [Lägg till AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) och [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdletar och uppdatera som serverns nya TDE-skydd.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Kontrollera att servern och alla repliker har uppdaterat till den nya TDE-skydd med hjälp av den [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet. 

   >[!NOTE]
   > Det kan ta några minuter innan det nya TDE-skyddet tillämpas på alla databaser och sekundära databaser under servern.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Ta en [säkerhetskopiering av den nya nyckeln](/powershell/module/az.keyvault/backup-azkeyvaultkey) i Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Ta bort den komprometterade nyckeln från Key Vault med hjälp av den [Remove-AzKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) cmdlet. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Att återställa en nyckel till Key Vault i framtiden med den [återställning AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Att göra krypterade resurserna otillgängligt

1. Ta bort de databaser som krypteras med den potentiellt komprometterade nyckeln.

   Filer för databasen och loggfiler säkerhetskopieras automatiskt, så en point-in-time-återställning av databasen kan göras när som helst (förutsatt att du anger nyckeln). Databaserna måste släppas innan borttagningen av en aktiv TDE-skydd för att förhindra dataförlust på upp till 10 minuter för de senaste transaktionerna. 
2. Säkerhetskopiera nyckelmaterial av TDE-skydd i Key Vault.
3. Ta bort potentiellt komprometterade nyckeln från Key Vault

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att rotera TDE-skydd för en server för att uppfylla krav på säkerhet: [Rotera Transparent datakryptering skydd med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Kom igång med Bring Your Own Key stöd för transparent Datakryptering: [Aktivera transparent Datakryptering med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
