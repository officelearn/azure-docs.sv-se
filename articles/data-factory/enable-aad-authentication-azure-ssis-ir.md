---
title: Aktivera Azure Active Directory-autentisering för Azure-SSIS Integration Runtime | Microsoft Docs
description: Den här artikeln beskriver hur du aktiverar Azure Active Directory-autentisering med den hanterade identitet för Azure Data Factory för att skapa Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 2/19/2019
ms.author: douglasl
ms.openlocfilehash: 77ee2a0649d5c815fb68a4a40106455839030695
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671530"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Aktivera Azure Active Directory-autentisering för Azure-SSIS Integration Runtime

Den här artikeln visar hur du aktiverar Azure Active Directory (Azure AD)-autentisering med den hanterade identitet för din Azure Data Factory (ADF) och använda den i stället för SQL-autentisering för att skapa en Azure-SSIS Integration Runtime (IR) som i sin tur skapar SSIS katalogdatabasen (SSISDB) i Azure SQL Database-server/hanterad instans för din räkning.

Mer information om den hanterade identitet för din ADF finns i [hanterade identiy för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Om du redan har skapat en Azure-SSIS IR med SQL-autentisering, du kan inte konfigurera om din IR för att använda Azure AD-autentisering med PowerShell just nu, men du kan göra det i Azure portal/ADF-app. 

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivera Azure AD i Azure SQL-databas

Azure SQL Database-server kan du skapa en databas med Azure AD-användare. Först måste du skapa en Azure AD-grupp med hanterad identitet för din ADF som medlem. Därefter måste du ange en Azure AD-användare som Active Directory-administratör för din Azure SQL Database-server och sedan ansluta till den på SQL Server Management Studio (SSMS) med hjälp av användaren. Slutligen måste du skapa en innesluten användare som representerar Azure AD-grupp så att den hanterade identitet för din ADF kan användas av Azure-SSIS IR för att skapa SSISDB för din räkning.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Skapa en Azure AD-grupp med hanterad identitet för din ADF som en medlem

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.

1.  Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulen.

2.  Logga in med `Connect-AzureAD`, kör du följande cmdlet för att skapa en grupp och spara den i en variabel:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Resultatet ser ut som i följande exempel som visar även variabelns värde:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Lägg till hanterad identitet för din ADF i gruppen. Du kan följa artikeln [hanterade identiy för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) att hämta den huvudsakliga TJÄNSTIDENTITETS-ID (t.ex. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, men Använd inte tjänsten IDENTITETSPROGRAM-ID för detta ändamål).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Du kan också kontrollera gruppmedlemskapet efteråt.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurera Azure AD-autentisering för Azure SQL Database-server

Du kan [konfigurera och hantera Azure AD-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

1.  I Azure-portalen väljer du **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfältet.

2.  Välj din Azure SQL Database-server konfigureras med Azure AD-autentisering.

3.  I den **inställningar** på bladet, väljer **Active Directory-administratör**.

4.  I kommandofältet väljer **konfigurera administratör**.

5.  Välj en Azure AD-användarkonto att göras administratör för servern och välj sedan **Välj.**

6.  I kommandofältet väljer **spara.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Skapa en innesluten användare i Azure SQL Database-server som representerar Azure AD-grupp

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SSMS.

2.  I den **Anslut till Server** dialogrutan, ange namnet på Azure SQL Database-servern i den **servernamn** fält.

3.  I den **autentisering** väljer **Active Directory - Universal med stöd för MFA** (du kan också använda de andra två typer av Active Directory-autentisering finns i [ Konfigurera och hantera Azure AD-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4.  I den **användarnamn** fältet, anger du namnet på Azure AD-konto som du anger som serveradministratör, t.ex. testuser@xxxonline.com.

5.  Välj **Connect** och slutföra inloggning.

6.  I den **Object Explorer**, expandera den **databaser** -> **systemdatabaser** mapp.

7.  Högerklicka på **master** databasen och välj **ny fråga**.

8.  Ange följande T-SQL-kommando i frågefönstret och välj **kör** i verktygsfältet.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Kommandot bör slutföras utan problem, skapa en innesluten användare för att representera gruppen.

9.  Rensa frågefönstret anger du följande T-SQL-kommando och markera **kör** i verktygsfältet.

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Kommandot bör slutföras utan problem, och ge innesluten användare behörighet att skapa en databas (SSISDB).

10.  Om din SSISDB har skapats med SQL-autentisering och vill du istället för att använda Azure AD-autentisering för din Azure-SSIS IR kan komma åt det, högerklickar du på **SSISDB** databasen och välj **ny fråga**.

11.  Ange följande T-SQL-kommando i frågefönstret och välj **kör** i verktygsfältet.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Kommandot bör slutföras utan problem, skapa en innesluten användare för att representera gruppen.

12.  Rensa frågefönstret anger du följande T-SQL-kommando och markera **kör** i verktygsfältet.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Kommandot bör slutföras utan problem, och ge innesluten användare behörighet att få åtkomst till SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Aktivera Azure AD i Azure SQL Database Managed Instance

Azure SQL Database Managed Instance kan du skapa en databas med den hanterade identitet för din ADF direkt. Du behöver inte ansluta till den hanterade identitet för din ADF till en Azure AD-grupp eller skapa en innesluten användare som representerar den gruppen i din hanterade instans.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurera Azure AD-autentisering för Azure SQL Database Managed Instance

1.   I Azure-portalen väljer du **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfältet.

2.   Välj din hanterade instans måste konfigureras med Azure AD-autentisering.

3.   I den **inställningar** på bladet, väljer **Active Directory-administratör**.

4.   I kommandofältet väljer **konfigurera administratör**.

5.   Välj en Azure AD-användarkonto att göras administratör för servern och välj sedan **Välj**.

6.   I kommandofältet väljer **spara**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Lägg till hanterad identitet för din ADF som en användare i Azure SQL Database Managed Instance

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SSMS.

2.  Anslut till din hanterade instans med ditt administratörskonto för SQL/Active Directory.

3.  I den **Object Explorer**, expandera den **databaser** -> **systemdatabaser** mapp.

4.  Högerklicka på **master** databasen och välj **ny fråga**.

5.  Hämta den hanterade identitet för din ADF. Du kan följa artikeln [hanterade identiy för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) att hämta huvudnamn SERVICE IDENTITY program-ID (men inte använder TJÄNSTIDENTITETS-ID för detta ändamål).

6.  I frågefönstret kör du följande T-SQL-skript för att konvertera den hanterade identitet för din ADF till typen binary:

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your SERVICE IDENTITY APPLICATION ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    Kommandot bör slutföras utan problem, visar den hanterade identitet för din ADF som binary.

7.  Rensa frågefönstret och kör följande T-SQL-skript för att lägga till den hanterade identitet för din ADF som en användare

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    Kommandot bör slutföras utan problem, bevilja den hanterade identitet för din ADF möjligheten att skapa en databas (SSISDB).

8.  Om din SSISDB har skapats med SQL-autentisering och vill du istället för att använda Azure AD-autentisering för din Azure-SSIS IR kan komma åt det, högerklickar du på **SSISDB** databasen och välj **ny fråga**.

9.  Ange följande T-SQL-kommando i frågefönstret och välj **kör** i verktygsfältet.

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
    ```

    Kommandot bör slutföras utan problem, bevilja den hanterade identitet för din ADF möjlighet att få åtkomst till SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Etablera Azure-SSIS IR i Azure portal/ADF-app

När du etablerar Azure SSIS IR i Azure portal/ADF-app på **SQL-inställningar** väljer **Använd AAD-autentisering med den hanterade identitet för din ADF** alternativet. Följande skärmbild visar inställningarna för IR med Azure SQL Database-server som är värd för SSISDB. För IR med hanterad instans som är värd för SSISDB, den **Catalog Database tjänstnivå** och **Tillåt Azure-tjänster åtkomst till** inställningarna inte är tillämpligt, medan andra inställningar är desamma.

Mer information om hur du skapar en Azure-SSIS IR finns i [skapa en Azure-SSIS integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Inställningar för Azure-SSIS integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Etablera Azure-SSIS IR med PowerShell

För att etablera Azure-SSIS IR med PowerShell, gör du följande:

1.  Installera [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modulen.

2.  I ditt skript anger inte `CatalogAdminCredential` parametern. Exempel:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
