---
title: Aktivera Azure Active Directory autentisering för Azure-SSIS Integration Runtime | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar Azure Active Directory-autentisering med den hanterade identiteten för Azure Data Factory för att skapa Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: 51f67667caa9e0e564709de40c145b107c619b59
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016013"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Aktivera Azure Active Directory autentisering för Azure-SSIS Integration Runtime

Den här artikeln visar hur du aktiverar Azure Active Directory (Azure AD)-autentisering med den hanterade identiteten för din Azure Data Factory (ADF) och använder den i stället för konventionella autentiseringsmetoder (t. ex. SQL-autentisering) för att:

- Skapa en Azure-SSIS Integration Runtime (IR) som i sin tur tillhandahåller SSIS Catalog-databasen (SSISDB) i Azure SQL Database Server/hanterad instans för din räkning.

- Anslut till olika Azure-resurser när du kör SSIS-paket på Azure-SSIS IR.

Mer information om den hanterade identiteten för din ADF finns i [hanterade Identiy för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  I det här scenariot används Azure AD-autentisering med den hanterade identiteten för din ADF bara vid skapande och efterföljande start åtgärder för din SSIS IR som i sin tur etablerar och ansluter till SSISDB. För att köra SSIS-paket kommer din SSIS IR fortfarande att ansluta till SSISDB med hjälp av SQL-autentisering med fullständigt hanterade konton som skapas under SSISDB-etableringen.
>-  Om du redan har skapat din SSIS IR med SQL-autentisering kan du inte konfigurera om den för att använda Azure AD-autentisering via PowerShell just nu, men du kan göra det via Azure Portal/ADF-appen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivera Azure AD på Azure SQL Database

Azure SQL Database servern har stöd för att skapa en databas med en Azure AD-användare. Först måste du skapa en Azure AD-grupp med den hanterade identiteten för din ADF som medlem. Sedan måste du ställa in en Azure AD-användare som Active Directory administratör för din Azure SQL Database-Server och sedan ansluta till den på SQL Server Management Studio (SSMS) med den användaren. Slutligen måste du skapa en innesluten användare som representerar Azure AD-gruppen, så den hanterade identiteten för din ADF kan användas av Azure-SSIS IR för att skapa SSISDB för din räkning.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Skapa en Azure AD-grupp med den hanterade identiteten för din ADF som medlem

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.

1.  Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulen.

2.  Logga in med `Connect-AzureAD`, kör följande cmdlet för att skapa en grupp och spara den i en variabel:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Resultatet ser ut som i följande exempel, som också visar variabeln Value:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Lägg till den hanterade identiteten för din ADF i gruppen. Du kan följa artikeln [Managed Identiy för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) för att hämta huvudhanterad identitets objekt-ID (t. ex. 765AD4AB-XXXX-XXXX-XXXX-51ed985819dc, men inte använda hanterat identitets program-ID för det här ändamålet).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Du kan också kontrol lera grupp medlemskapet efteråt.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurera Azure AD-autentisering för Azure SQL Database Server

Du kan [Konfigurera och hantera Azure AD-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

1.  I Azure Portal väljer du **alla tjänster** -> **SQL-servrar** i den vänstra navigeringen.

2.  Välj din Azure SQL Database-Server som ska konfigureras med Azure AD-autentisering.

3.  I avsnittet **Inställningar** på bladet väljer du **Active Directory admin**.

4.  I kommando fältet väljer du **Ange administratör**.

5.  Välj ett Azure AD-användarkonto som ska bli administratör för servern och välj sedan **Välj.**

6.  I kommando fältet väljer du **Spara.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Skapa en innesluten användare i Azure SQL Database Server som representerar Azure AD-gruppen

I nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Starta SSMS.

2. I dialog rutan **Anslut till Server** anger du Azure SQL Database serverns namn i fältet **Server namn** .

3. I fältet **autentisering** väljer du **Active Directory-Universal med MFA-stöd** (du kan också använda de andra två Active Directory autentiseringstyper, se [Konfigurera och hantera Azure AD-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. I fältet **användar namn** anger du namnet på det Azure AD-konto som du anger som Server administratör, t. ex testuser@xxxonline.com.

5. Välj **Anslut** och slutför inloggnings processen.

6. Expandera mappen **databaser** -> **system databaser** i **Object Explorer**.

7. Högerklicka på **huvud** databasen och välj **ny fråga**.

8. I frågefönstret anger du följande T-SQL-kommando och väljer **Kör** i verktygsfältet.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Kommandot bör slutföras, vilket skapar en innesluten användare som representerar gruppen.

9. Rensa frågefönstret, ange följande T-SQL-kommando och välj **Kör** i verktygsfältet.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Kommandot ska slutföras, vilket ger den inneslutna användaren möjlighet att skapa en databas (SSISDB).

10. Om din SSISDB har skapats med SQL-autentisering och du vill växla till att använda Azure AD-autentisering för Azure-SSIS IR för att få åtkomst till den, högerklickar du på **SSISDB** Database och väljer **ny fråga**.

11. I frågefönstret anger du följande T-SQL-kommando och väljer **Kör** i verktygsfältet.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Kommandot bör slutföras, vilket skapar en innesluten användare som representerar gruppen.

12. Rensa frågefönstret, ange följande T-SQL-kommando och välj **Kör** i verktygsfältet.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Kommandot ska slutföras, vilket ger den inneslutna användaren möjlighet att komma åt SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Aktivera Azure AD på Azure SQL Database hanterade instansen

Azure SQL Database hanterade instansen har stöd för att skapa en databas med den hanterade identiteten för din ADF direkt. Du behöver inte ansluta till den hanterade identiteten för din ADF till en Azure AD-grupp eller skapa en innesluten användare som representerar gruppen i din hanterade instans.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurera Azure AD-autentisering för Azure SQL Database Hanterad instans

Följ stegen i [etablera en Azure Active Directory administratör för din hanterade instans](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Lägg till den hanterade identiteten för din ADF som en användare i Azure SQL Database Hanterad instans

I nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SSMS.

2.  Anslut till din hanterade instans med ett SQL Server konto som är en **sysadmin**. Detta är en tillfällig begränsning som tas bort när Azure AD server-Huvudkonton (inloggningar) för Azure SQL Database Hanterad instans blir GA. Följande fel meddelande visas om du försöker använda ett administratörs konto för Azure AD för att skapa inloggningen: MSG 15247, nivå 16, tillstånd 1, rad 1 användaren har inte behörighet att utföra den här åtgärden.

3.  Expandera mappen **databaser** -> **system databaser** i **Object Explorer**.

4.  Högerklicka på **huvud** databasen och välj **ny fråga**.

5.  I frågefönstret kör du följande T-SQL-skript för att lägga till den hanterade identiteten för din ADF som en användare

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Kommandot bör slutföras, vilket ger den hanterade identiteten för din ADF möjlighet att skapa en databas (SSISDB).

6.  Om din SSISDB har skapats med SQL-autentisering och du vill växla till att använda Azure AD-autentisering för Azure-SSIS IR för att få åtkomst till den, högerklickar du på **SSISDB** Database och väljer **ny fråga**.

7.  I frågefönstret anger du följande T-SQL-kommando och väljer **Kör** i verktygsfältet.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Kommandot ska slutföras, vilket ger den hanterade identiteten för din ADF möjlighet att komma åt SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Etablera Azure-SSIS IR i Azure Portal/ADF-appen

När du etablerar din Azure-SSIS IR i Azure Portal/ADF-appen väljer du **Använd AAD-autentisering med den hanterade identiteten för ditt ADF** -alternativ på sidan **SQL-inställningar** . Följande skärm bild visar inställningarna för IR med Azure SQL Database-Server som är värd för SSISDB. För IR med hanterad instans som är värd för SSISDB är **katalog databasens tjänst nivå** och **Tillåt Azure-tjänster åtkomst till** inställningarna inte tillämpliga, medan andra inställningar är desamma.

Mer information om hur du skapar en Azure-SSIS IR finns i [skapa en Azure-SSIS integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Inställningar för Azure-SSIS integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Etablera Azure-SSIS IR med PowerShell

Gör så här för att etablera Azure-SSIS IR med PowerShell:

1.  Installera [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) -modul.

2.  Ange `CatalogAdminCredential` inte parameter i skriptet. Exempel:

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

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Köra SSIS-paket med hanterad identitets autentisering

När du kör SSIS-paket på Azure-SSIS IR kan du använda hanterad identitets autentisering för att ansluta till olika Azure-resurser. För närvarande har vi redan stöd för hanterad identitets autentisering i följande anslutnings hanterare.

- [OLE DB anslutnings hanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET anslutnings hanterare](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage anslutnings hanteraren](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
