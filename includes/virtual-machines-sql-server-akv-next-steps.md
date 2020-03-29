---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187293"
---
## <a name="next-steps"></a>Nästa steg

När du har aktiverat Integrering av Azure Key Vault kan du aktivera SQL Server-kryptering på din SQL VM. Först måste du skapa en asymmetrisk nyckel i nyckelvalvet och en symmetrisk nyckel i SQL Server på den virtuella datorn. Sedan kan du köra T-SQL-satser för att aktivera kryptering för dina databaser och säkerhetskopior.

Det finns flera olika former av kryptering som du kan dra nytta av:

* [Transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Krypterade säkerhetskopior](https://msdn.microsoft.com/library/dn449489.aspx)
* [Kryptering på kolumnnivå (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Följande Transact-SQL-skript ger exempel för vart och ett av dessa områden.

### <a name="prerequisites-for-examples"></a>Förutsättningar för exempel

Varje exempel baseras på de två förutsättningarna: en asymmetrisk nyckel från nyckelvalvet som kallas **CONTOSO_KEY** och en autentiseringsfil som skapats av funktionen AKV-integrering som kallas **Azure_EKM_TDE_cred**. Följande Transact-SQL-kommandon ställer in dessa förutsättningar för att köra exemplen.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent datakryptering (TDE)

1. Skapa en SQL Server-inloggning som ska användas av databasmotorn för TDE och lägg sedan till autentiseringsuppgifterna i den.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Skapa databaskrypteringsnyckeln som ska användas för TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Krypterade säkerhetskopior

1. Skapa en SQL Server-inloggning som ska användas av databasmotorn för att kryptera säkerhetskopior och lägg till autentiseringsuppgifterna i den.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Säkerhetskopiera databasen som anger kryptering med den asymmetriska nyckeln som lagras i nyckelvalvet.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Kryptering på kolumnnivå (CLE)

Det här skriptet skapar en symmetrisk nyckel som skyddas av den asymmetriska nyckeln i nyckelvalvet och använder sedan den symmetriska nyckeln för att kryptera data i databasen.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Ytterligare resurser

Mer information om hur du använder dessa krypteringsfunktioner finns i [Använda EKM med SQL Server-krypteringsfunktioner](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Observera att stegen i den här artikeln förutsätter att du redan har SQL Server som körs på en virtuell Azure-dator. Om inte, se [Etablera en virtuell SQL Server-dator i Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Mer information om hur du kör SQL Server på virtuella Azure-datorer finns i [översikten SQL Server på Virtuella Azure-datorer](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
