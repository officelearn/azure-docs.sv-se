---
title: Migrate TDE certificate - managed instance
description: Migrate certificate protecting Database Encryption Key of a database with transparent Data Encryption to Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 6ef8d49ba7c9ac2c3c60197c11b9bf5936171f9e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420738"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrate certificate of TDE protected database to Azure SQL Database Managed Instance

When migrating a database protected by [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) to Azure SQL Database Managed Instance using native restore option, the corresponding certificate from the on-premises or IaaS SQL Server needs to be migrated before database restore. Den här artikeln vägleder dig genom processen för manuell migrering av certifikatet till Azure SQL Database-hanterad instans:

> [!div class="checklist"]
> * Exportera certifikatet till en Personal Information Exchange-fil (.pfx)
> * Extrahera certifikatet från fil till base-64-sträng
> * Ladda upp den med hjälp av PowerShell-cmdlet

Ett annat alternativ som använder en helt hanterad tjänst för smidig migrering av både TDE-skyddad databas och motsvarande certifikat finns på sidan om [hur du migrerar din lokala databas till hanterad instans med hjälp av Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Migrerade certifikat används endast för återställning av den TDE-skyddade databasen. Soon after restore is done, the migrated certificate gets replaced by a different protector, either service-managed certificate or asymmetric key from the key vault, depending on the type of the transparent data encryption you set on the instance.

## <a name="prerequisites"></a>Krav

Du behöver följande för att slutföra stegen i den här artikeln:

- [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)-kommandoradsverktyget installerat på den lokala servern eller en annan dator med åtkomst till det certifikat som exporterats som en fil. Pvk2Pfx-verktyget är en del av [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), en fristående, självständig kommandoradsmiljö.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) version 5.0 eller senare installerat.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Se till att du har följande:

- Azure PowerShell module [installed and updated](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Az.Sql module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

Run the following commands in PowerShell to install/update the module:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportera TDE-certifikatet till en Personal Information Exchange-fil (.pfx)

Certifikatet kan exporteras direkt från SQL Server-källan eller från certifikatarkivet om det förvaras där.

### <a name="export-certificate-from-the-source-sql-server"></a>Exportera certifikat från SQL Server-källan

Använd följande steg för att exportera certifikat med SQL Server Management Studio och omvandla det till pfx-format. De allmänna namnen *TDE_Cert* och *full_path* används för certifikat- och filnamn samt sökvägar i stegen. De ska ersättas med de faktiska namnen.

1. I SSMS öppnar du ett nytt frågefönster och ansluter till SQL Server-källan.

1. Använd följande skript för att lista TDE-skyddade databaser och hämta namnet på det certifikat som skyddar kryptering på den databas som ska migreras:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![lista över TDE-certifikat](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Kör följande skript för att exportera certifikatet till ett par filer (.cer och .pvk) med informationen för den offentliga nyckeln och den privata nyckeln:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![säkerhetskopiera TDE-certifikat](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. Använd PowerShell-konsolen om du vill kopiera certifikatinformationen från ett par nyligen skapade filer till en Personal Information Exchange-fil (.pfx) med Pvk2Pfx-verktyget:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportera certifikat från certifikatarkiv

Om certifikatet finns i SQL-serverns certifikatarkiv i lokal dator kan det exporteras med följande steg:

1. Öppna PowerShell-konsolen och kör följande kommando för att öppna snapin-modulen för certifikat för Microsoft Management Console:

   ```cmd
   certlm
   ```

2. I MMC-snapin-modulen för certifikat expanderar du sökvägen Personligt -> Certifikat för att se listan över certifikat

3. Högerklicka på certifikatet och klicka på Exportera…

4. Följ guiden för att exportera certifikatet och den privata nyckeln till ett Personal Information Exchange-format

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Upload certificate to Azure SQL Database Managed Instance using Azure PowerShell cmdlet

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Börja med förberedelsestegen i PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. När alla förberedelsesteg är klara kör du följande kommandon för att ladda upp det base-64-kodade certifikatet till den hanterade målinstansen:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

You need to first [setup an Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) with your *.pfx* file.

1. Börja med förberedelsestegen i PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. När alla förberedelsesteg är klara kör du följande kommandon för att ladda upp det base-64-kodade certifikatet till den hanterade målinstansen:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Certifikatet är nu tillgängligt för den angivna hanterade instansen, och en säkerhetskopia av motsvarande TDE-skyddad databas kan återställas.

## <a name="next-steps"></a>Nästa steg

In this article, you learned how to migrate certificate protecting encryption key of database with Transparent Data Encryption, from the on-premises or IaaS SQL Server to Azure SQL Database Managed Instance.

Se [Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans](sql-database-managed-instance-get-started-restore.md) för att lära dig hur du återställer en databassäkerhetskopia till en hanterad Azure SQL Database-instans.
