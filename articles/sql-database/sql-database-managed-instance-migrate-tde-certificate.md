---
title: Migrera TDE-certifikat – Azure SQL Database-hanterad instans | Microsoft Docs
description: Migrera certifikat skyddar Databaskrypteringsnyckel för en databas med transparent datakryptering till Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: f27a5b0deb0dd446d4f05b0a6d6e96d67d24d9e9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316004"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrera certifikat för TDE skyddad databas till Azure SQL Database Managed Instance

När du migrerar en databas som skyddas av [Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) till Azure SQL Database Managed Instance med hjälp av inbyggda återställningsalternativet, motsvarande certifikat från en lokal eller IaaS SQL Server måste migreras före återställning av databasen. Den här artikeln vägleder dig genom processen för manuell migrering av certifikatet till Azure SQL Database-hanterad instans:

> [!div class="checklist"]
> * Exportera certifikatet till en Personal Information Exchange-fil (.pfx)
> * Extrahera certifikatet från fil till base-64-sträng
> * Ladda upp den med hjälp av PowerShell-cmdlet

Ett annat alternativ som använder en helt hanterad tjänst för smidig migrering av både TDE-skyddad databas och motsvarande certifikat finns på sidan om [hur du migrerar din lokala databas till hanterad instans med hjälp av Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Transparent datakryptering för Azure SQL Database Managed Instance fungerar i tjänsthanterade läge. Migrerade certifikat används endast för återställning av den TDE-skyddade databasen. Kort efter att återställningen är klar ersätts det migrerade certifikatet med ett annat, systemhanterat certifikat.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du behöver följande för att slutföra stegen i den här artikeln:

- [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx)-kommandoradsverktyget installerat på den lokala servern eller en annan dator med åtkomst till det certifikat som exporterats som en fil. Pvk2Pfx-verktyget är en del av [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), en fristående, självständig kommandoradsmiljö.
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) version 5.0 eller senare installerat.
- Azure PowerShell-modul [installerad och uppdaterad](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Az.Sql modulen](https://www.powershellgallery.com/packages/Az.Sql).
  Kör följande kommandon i PowerShell för att installera/uppdatera PowerShell-modulen:

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Exportera TDE-certifikatet till en Personal Information Exchange-fil (.pfx)

Certifikatet kan exporteras direkt från SQL Server-källan eller från certifikatarkivet om det förvaras där.

### <a name="export-certificate-from-the-source-sql-server"></a>Exportera certifikat från SQL Server-källan

Använd följande steg för att exportera certifikat med SQL Server Management Studio och omvandla det till pfx-format. De allmänna namnen *TDE_Cert* och *full_path* används för certifikat- och filnamn samt sökvägar i stegen. De ska ersättas med de faktiska namnen.

1. I SSMS öppnar du ett nytt frågefönster och ansluter till SQL Server-källan.
2. Använd följande skript för att lista TDE-skyddade databaser och hämta namnet på det certifikat som skyddar kryptering på den databas som ska migreras:

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

3. Kör följande skript för att exportera certifikatet till ett par filer (.cer och .pvk) med informationen för den offentliga nyckeln och den privata nyckeln:

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

4. Använd PowerShell-konsolen om du vill kopiera certifikatinformationen från ett par nyligen skapade filer till en Personal Information Exchange-fil (.pfx) med Pvk2Pfx-verktyget:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Exportera certifikat från certifikatarkiv

Om certifikatet finns i SQL-serverns certifikatarkiv i lokal dator kan det exporteras med följande steg:

1. Öppna PowerShell-konsolen och kör följande kommando för att öppna snapin-modulen för certifikat för Microsoft Management Console:

   ```powershell
   certlm
   ```

2. I MMC-snapin-modulen för certifikat expanderar du sökvägen Personligt -> Certifikat för att se listan över certifikat

3. Högerklicka på certifikatet och klicka på Exportera…

4. Följ guiden för att exportera certifikatet och den privata nyckeln till ett Personal Information Exchange-format

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Överför certifikatet till Azure SQL Database Managed Instance med Azure PowerShell-cmdlet

1. Börja med förberedelsestegen i PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. När alla förberedelsesteg är klara kör du följande kommandon för att ladda upp det base-64-kodade certifikatet till den hanterade målinstansen:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Certifikatet är nu tillgängligt för den angivna hanterade instansen, och en säkerhetskopia av motsvarande TDE-skyddad databas kan återställas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att migrera certifikat skydda krypteringsnyckeln för databasen med Transparent datakryptering, från lokala eller IaaS SQL Server till Azure SQL Database Managed Instance.

Se [Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans](sql-database-managed-instance-get-started-restore.md) för att lära dig hur du återställer en databassäkerhetskopia till en hanterad Azure SQL Database-instans.
