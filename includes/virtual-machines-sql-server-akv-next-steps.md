---
author: rockboyfor
ms.service: virtual-machines-sql
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129627"
---
## <a name="next-steps"></a>Nästa steg

När du har aktiverat Azure Key Vault-integrering kan du aktivera kryptering för SQL Server på din SQL-VM. Först behöver du skapa en asymmetrisk nyckel i ditt nyckelvalv och en symmetrisk nyckel i SQL Server på den virtuella datorn. Sedan kommer du att kunna köra T-SQL-uttryck för att aktivera kryptering för dina databaser och säkerhetskopieringar.

Det finns flera typer av kryptering som du kan dra nytta av:

* [Transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Krypterad säkerhetskopiering](https://msdn.microsoft.com/library/dn449489.aspx)
* [Kolumnen Filnivåkryptering (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Följande Transact-SQL-skript innehåller exempel för var och en av dessa områden.

### <a name="prerequisites-for-examples"></a>Krav för exempel

Varje exempel baseras på två krav: en asymmetrisk nyckel från ditt nyckelvalv kallas **CONTOSO_KEY** och kallas för en autentiseringsuppgift som skapas av funktionen AKV-integreringen **Azure_EKM_TDE_cred**. Följande Transact-SQL-kommandon konfigurera förutsättningarna för att köra exemplen.

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

1. Skapa en SQL Server-inloggning som ska användas av databasmotorn för transparent Datakryptering och sedan lägga till autentiseringsuppgifter i den.

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

1. Skapa databaskrypteringsnyckeln som ska användas för transparent Datakryptering.

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

### <a name="encrypted-backups"></a>Krypterad säkerhetskopiering

1. Skapa en SQL Server-inloggning som ska användas av databasmotorn för kryptering av säkerhetskopieringar och Lägg till autentiseringsuppgifterna.

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

1. Säkerhetskopiera databaskryptering för att ange med en asymmetrisk nyckel som lagras i nyckelvalvet.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Kolumnen Filnivåkryptering (CLE)

Det här skriptet skapar en symmetrisk nyckel som skyddas av en asymmetrisk nyckel i key vault och använder sedan den symmetriska nyckeln för att kryptera data i databasen.

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

Läs mer om hur du använder dessa krypteringsfunktioner, [med hjälp av EKM med SQL Server-krypteringsfunktioner](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Observera att stegen i den här artikeln förutsätter att du redan har SQL Server på virtuella Azure-datorer. Om den inte finns i [etablera en virtuell dator med SQL Server i Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Andra anvisningar om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på Azure virtuella datorer – översikt](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

<!--Update_Description: wording update, update link-->