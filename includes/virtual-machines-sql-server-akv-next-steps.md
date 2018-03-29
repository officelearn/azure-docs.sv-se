## <a name="next-steps"></a>Nästa steg

När du har aktiverat Azure Key Vault-integrering kan du aktivera SQL Server-kryptering på din SQL-VM. Du måste först skapa en asymmetrisk nyckel i nyckelvalvet och en symmetrisk nyckel i SQL Server på den virtuella datorn. Sedan kommer du att kunna köra T-SQL-uttryck för att aktivera kryptering för dina databaser och säkerhetskopieringar.

Det finns flera typer av kryptering som du kan dra nytta av:

* [Transparent datakryptering (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Krypterad säkerhetskopiering](https://msdn.microsoft.com/library/dn449489.aspx)
* [Kryptering på kolumnen (radera)](https://msdn.microsoft.com/library/ms173744.aspx)

Följande Transact-SQL-skript innehåller exempel för dessa olika områden.

### <a name="prerequisites-for-examples"></a>Krav för exempel

Varje exempel är baserad på två krav: kallas för en asymmetrisk nyckel från nyckelvalvet **CONTOSO_KEY** och kallas för en autentiseringsuppgift som skapas av funktionen AKV-integreringen **Azure_EKM_TDE_cred**. Följande Transact-SQL-kommandon installationsprogrammet förutsättningarna för att köra exemplen.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that have sysadmin permissions, this will allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent datakryptering (TDE)

1. Skapa en SQL Server-inloggning som ska användas av databasmotorn för TDE och sedan lägga till autentiseringsuppgifter.

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

1. Skapa krypteringsnyckeln för databasen som ska användas för TDE.

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

1. Säkerhetskopiera databasen att ange kryptering med den asymmetriska nyckeln lagras i nyckelvalvet.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Kryptering på kolumnen (radera)

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

Mer information om hur du använder dessa krypteringsfunktioner finns [med hjälp av EKM med SQL Server-krypteringsfunktioner](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Observera att anvisningarna i den här artikeln förutsätter att du redan har SQL Server körs på en virtuell Azure-dator. Om inte, se [etablera en virtuell dator med SQL Server i Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Andra anvisningar om hur du kör SQL Server på Azure Virtual Machines finns [SQL Server på Azure virtuella datorer – översikt](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
