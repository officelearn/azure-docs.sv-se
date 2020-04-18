---
title: Aktivera AAD för Azure SSIS Integration Runtime
description: I den här artikeln beskrivs hur du aktiverar Azure Active Directory-autentisering med den hanterade identiteten för Azure Data Factory för att skapa Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 77b49d8446c7882a155742e8455d77bd1ec110cb
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606196"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Aktivera Azure Active Directory-autentisering för Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln visas hur du aktiverar Azure Active Directory-autentisering (Azure AD) med den hanterade identiteten för din Azure Data Factory (ADF) och använder den i stället för konventionella autentiseringsmetoder (som SQL-autentisering) för att:

- Skapa en Azure-SSIS Integration Runtime (IR) som i sin tur etablerar SSIS-katalogdatabas (SSISDB) i Azure SQL Database server/Managed Instance för din räkning.

- Anslut till olika Azure-resurser när du kör SSIS-paket på Azure-SSIS IR.

Mer information om den hanterade identiteten för din ADF finns i [Hanterad identitet för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  I det här fallet azure AD-autentisering med den hanterade identiteten för din ADF används endast i skapandet och efterföljande startåtgärder för din SSIS IR som i sin tur etablerar och ansluter till SSISDB. För SSIS-paketkörningar ansluter din SSIS IR fortfarande till SSISDB med SQL-autentisering med fullständigt hanterade konton som skapas under SSISDB-etablering.
>-  Om du redan har skapat din SSIS IR med SQL-autentisering kan du inte konfigurera om den så att den kan använda Azure AD-autentisering via PowerShell just nu, men du kan göra det via Azure Portal/ADF-appen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivera Azure AD i Azure SQL Database

Azure SQL Database-servern stöder att skapa en databas med en Azure AD-användare. Först måste du skapa en Azure AD-grupp med den hanterade identiteten för din ADF som medlem. Därefter måste du ange en Azure AD-användare som Active Directory-administratör för din Azure SQL Database-server och sedan ansluta till den på SQL Server Management Studio (SSMS) med den användaren. Slutligen måste du skapa en innehållen användare som representerar Azure AD-gruppen, så att den hanterade identiteten för din ADF kan användas av Azure-SSIS IR för att skapa SSISDB för din räkning.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Skapa en Azure AD-grupp med den hanterade identiteten för din ADF som medlem

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med Azure AD PowerShell.

1.  Installera [Azure AD PowerShell-modulen.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Logga in `Connect-AzureAD`med , kör följande cmdlet för att skapa en grupp och spara den i en variabel:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Resultatet ser ut som följande exempel, som också visar variabelvärdet:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Lägg till den hanterade identiteten för din ADF i gruppen. Du kan följa artikeln [Hanterad identitet för Data Factory för](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) att få huvudnummer för hanterade identitetsobjekt (t.ex. 765ad4ab-XXXX-XXXX-XXXX-XXXX-51ed985819dc, men använd inte Managed Identity Application ID för detta ändamål).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Du kan också kontrollera gruppmedlemskapet efteråt.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurera Azure AD-autentisering för Azure SQL Database-server

Du kan [konfigurera och hantera Azure AD-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) med hjälp av följande steg:

1.  I Azure-portalen väljer du **Alla tjänster** -> **SQL-servrar** från vänsternavigering.

2.  Välj den Azure SQL Database-server som ska konfigureras med Azure AD-autentisering.

3.  I avsnittet **Inställningar** i bladet väljer du **Active Directory-administratör**.

4.  Välj **Ange admin**i kommandofältet .

5.  Välj ett Azure AD-användarkonto som ska göras administratör för servern och välj sedan **Välj.**

6.  Välj Spara i **kommandofältet.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Skapa en innesluten användare i Azure SQL Database-servern som representerar Azure AD-gruppen

För nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Starta SSMS.

2. Ange servernamnet för Azure SQL Database i fältet **Servernamn** i dialogrutan **Anslut till server.**

3. I fältet **Autentisering** väljer du **Active Directory - Universal med MFA-stöd** (du kan också använda de andra två Active Directory-autentiseringstyperna, se Konfigurera och hantera Azure [AD-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. I fältet **Användarnamn** anger du namnet på Azure AD-konto som du anger testuser@xxxonline.comsom serveradministratör, t.ex.

5. välj **Anslut** och slutför inloggningsprocessen.

6. Expandera mappen -> **Databasersystemdatabaser** i **Databases** **Objektutforskaren.**

7. Högerklicka på **huvuddatabasen** och välj **Ny fråga**.

8. I frågefönstret anger du följande T-SQL-kommando och väljer **Kör** i verktygsfältet.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Kommandot ska slutföras och skapa en innesluten användare som representerar gruppen.

9. Avmarkera frågefönstret, ange följande T-SQL-kommando och välj **Kör** i verktygsfältet.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Kommandot bör slutföras och ge den innehållna användaren möjlighet att skapa en databas (SSISDB).

10. Om din SSISDB skapades med SQL-autentisering och du vill växla till att använda Azure AD-autentisering för din Azure-SSIS IR för att komma åt den, högerklicka på **SSISDB-databasen** och välj **Ny fråga**.

11. I frågefönstret anger du följande T-SQL-kommando och väljer **Kör** i verktygsfältet.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Kommandot ska slutföras och skapa en innesluten användare som representerar gruppen.

12. Avmarkera frågefönstret, ange följande T-SQL-kommando och välj **Kör** i verktygsfältet.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Kommandot bör slutföras och ge den innehållna användaren möjlighet att komma åt SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Aktivera Azure AD i hanterad Azure SQL-databas-instans

Azure SQL Database Managed Instance stöder att skapa en databas med den hanterade identiteten för din ADF direkt. Du behöver inte ansluta till den hanterade identiteten för din ADF till en Azure AD-grupp eller skapa en innehållen användare som representerar den gruppen i din hanterade instans.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurera Azure AD-autentisering för Azure SQL Database Managed Instance

Följ stegen i [Etablera en Azure Active Directory-administratör för din hanterade instans](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Lägga till den hanterade identiteten för din ADF som användare i Azure SQL Database Managed Instance

För nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SSMS.

2.  Anslut till din hanterade instans med ett SQL Server-konto som är ett **sysadmin**. Detta är en tillfällig begränsning som tas bort när Azure AD-serverhuvudnamn (inloggningar) för Azure SQL Database Managed Instance blir GA. Du kommer att se följande fel om du försöker använda ett Azure AD-administratörskonto för att skapa inloggningen: Msg 15247, Nivå 16, Tillstånd 1, Linje 1-användare har inte behörighet att utföra den här åtgärden.

3.  Expandera mappen -> **Databasersystemdatabaser** i **Databases** **Objektutforskaren.**

4.  Högerklicka på **huvuddatabasen** och välj **Ny fråga**.

5.  I frågefönstret kör du följande T-SQL-skript för att lägga till den hanterade identiteten för din ADF som användare

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Kommandot bör slutföras och ge den hanterade identiteten för din ADF möjlighet att skapa en databas (SSISDB).

6.  Om din SSISDB skapades med SQL-autentisering och du vill växla till att använda Azure AD-autentisering för din Azure-SSIS IR för att komma åt den, högerklicka på **SSISDB-databasen** och välj **Ny fråga**.

7.  I frågefönstret anger du följande T-SQL-kommando och väljer **Kör** i verktygsfältet.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Kommandot bör slutföras och ge den hanterade identiteten för din ADF möjlighet att komma åt SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Etablera Azure-SSIS IR i Azure portal/ADF-app

När du etablerar din Azure-SSIS IR i Azure portal/ADF-app väljer du **Använd AAD-autentisering med den hanterade identiteten för ditt ADF-alternativ** på **sidan SQL Settings.** Följande skärmbild visar inställningarna för IR med Azure SQL Database server som är värd för SSISDB. För IR med hanterad instans som är värd för SSISDB är **katalogdatabastjänstnivån** och **Tillåt Azure-tjänster för åtkomstinställningar** inte tillämpliga, medan andra inställningar är desamma.

Mer information om hur du skapar en Azure-SSIS IR finns [i Skapa en Azure-SSIS-integreringskörning i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Inställningar för Azure-SSIS-integreringskörningen](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Etablera Azure-SSIS IR med PowerShell

Så här etablerar du din Azure-SSIS IR med PowerShell:

1.  Installera [Azure PowerShell-modul.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 

2.  Ange `CatalogAdminCredential` inte parameter i skriptet. Ett exempel:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Kör SSIS-paket med autentisering av hanterad identitet

När du kör SSIS-paket på Azure-SSIS IR kan du använda autentisering av hanterad identitet för att ansluta till olika Azure-resurser. För närvarande har vi redan stött hanterad identitetsautentisering i följande anslutningshanterare.

- [OLE DB-anslutningshanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Anslutningshanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Anslutningshanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
