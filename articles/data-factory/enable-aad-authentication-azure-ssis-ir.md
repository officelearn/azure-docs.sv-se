---
title: Aktivera Azure Active Directory-autentisering för Azure-SSIS integration runtime | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar Azure-SSIS integration runtime för att aktivera anslutningar som använder Azure Active Directory-autentisering.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: d2000e626166304e92556e3c965df175a27046ad
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321075"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Aktivera Azure Active Directory-autentisering för Azure-SSIS integration runtime

Den här artikeln visar hur du skapar en Azure-SSIS IR med tjänstidentitet för Azure Data Factory. Du kan använda Azure Active Directory (Azure AD)-autentisering med den hanterade identitet för din Azure Data Factory i stället för SQL-autentisering för att skapa en Azure-SSIS integration runtime.

Mer information om den hanterade identitet för din data factory finns i [tjänstidentitet för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Om du redan har skapat en Azure-SSIS integration runtime med SQL-autentisering kan du konfigurera om IR för att använda Azure AD-autentisering med PowerShell just nu.

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivera Azure AD i Azure SQL-databas

Azure SQL Database kan du skapa en databas med Azure AD-användare. Därför kan du ange en Azure AD-användare som Active Directory-administratör och sedan logga in till SQL Server Management Studio (SSMS) med hjälp av Azure AD-användare. Du kan skapa en innesluten användare för Azure AD-grupp att aktivera din IR att skapa SQL Server Integration Services (SSIS)-katalogen på servern.

### <a name="create-a-group-in-azure-ad-and-make-the-managed-identity-for-your-data-factory-a-member-of-the-group"></a>Skapa en grupp i Azure AD och göra den hanterade identitet för din datafabrik medlem i gruppen

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.

1.  Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulen.

2.  Logga in med `Connect-AzureAD`, och kör följande kommando för att skapa gruppen och spara den i en variabel:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Utdata ser ut som i följande exempel, som också undersöker värdet för variabeln:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Lägg till hanterad identitet för din datafabrik i gruppen. Du kan följa [tjänstidentitet för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) att hämta den huvudsakliga TJÄNSTIDENTITETS-ID (till exempel 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, men inte använder tjänsten IDENTITETSPROGRAM-ID för detta ändamål).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Du kan kontrollera också gruppmedlemskapet efteråt.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Aktivera Azure AD-autentisering för Azure SQL Database

Du kan [konfigurera Azure AD-autentisering för SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

1.  I Azure-portalen väljer du **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfältet.

2.  Välj SQL-databas som ska aktiveras för Azure AD-autentisering.

3.  I den **inställningar** på bladet, väljer **Active Directory-administratör**.

4.  I kommandofältet väljer **konfigurera administratör**.

5.  Välj en Azure AD-användarkonto för att upprätta en administratör för servern och välj sedan **Välj.**

6.  I kommandofältet väljer **spara.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Skapa en innesluten användare i databasen som representerar Azure AD-gruppen

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SQL Server Management Studio.

2.  I den **Anslut till Server** dialogrutan, ange namnet på SQL-servern i den **servernamn** fält.

3.  I den **autentisering** väljer **Active Directory - Universal med stöd för MFA**. (Du kan också använda andra två typer av Active Directory-autentisering. Se [konfigurera och hantera Azure Active Directory-autentisering med SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  I den **användarnamn** fältet, anger du namnet på Azure AD-kontot som du anger som serveradministratör – till exempel testuser@xxxonline.com.

5.  Välj **Connect**. Slutför inloggningsprocessen.

6.  I den **Object Explorer**, expandera den **databaser** -> systemdatabaser mapp.

7.  Höger-Välj på **master** databasen och välj **ny fråga**.

8.  Ange följande rad i frågefönstret och välj **kör** i verktygsfältet:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Kommandot bör slutföras utan problem och skapa den inneslutna användaren för gruppen.

9.  Rensa frågefönstret, ange följande rad och välj **kör** i verktygsfältet:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Kommandot bör slutföras utan problem, beviljar innesluten användare möjlighet att skapa databasen.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Aktivera Azure AD i Azure SQL Database Managed Instance

Azure SQL Database Managed Instance kan du skapa en databas med MSI direkt. Du behöver inte ansluta till data factory MSI i ett AD-grupp eller skapa den inneslutna användaren i MI.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database-managed-instance"></a>Aktivera Azure AD-autentisering för Azure SQL Database Managed Instance

1.   I Azure-portalen väljer du **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfältet.

1.   Välj den SQL servern som ska aktiveras för Azure AD-autentisering.

1.   I den **inställningar** på bladet, väljer **Active Directory-administratör**.

1.   I kommandofältet väljer **konfigurera administratör**.

1.   Välj en Azure AD-användarkonto för att upprätta en administratör för servern och välj sedan **Välj**.

1.   I kommandofältet väljer **spara**.

### <a name="add-data-factory-msi-as-a-user-to-the-azure-sql-database-managed-instance"></a>Lägg till data factory MSI som en användare i Azure SQL Database Managed Instance

1.  Starta SQL Server Management Studio.

2.  Logga in med ett administratörskonto för SQL eller Active Directory-administratörskonto.

3.  I Object Explorer expanderar databaserna -> systemdatabaser mapp.

4.  Högerklicka på master-databasen och välj **ny fråga**.

5.  Du kan följa artikeln [tjänstidentitet för Azure Data Factory](data-factory-service-identity.md) att hämta huvudnamn SERVICE IDENTITY programmets ID. (Använd inte TJÄNSTIDENTITETS-ID för detta ändamål.)

6.  I frågefönstret kör du följande skript för att konvertera SERVICE IDENTITY program-ID till typen binary:

    ```sql
    DECLARE @applicationId uniqueidentifier = {your service identity application id}
    select CAST(@applicationId AS varbinary)
    ```

7.  Du kan hämta värdet från resultatfönstret.

8.  Rensa frågefönstret och kör följande skript:

    ```sql
    CREATE LOGIN [{MSI name}] FROM EXTERNAL PROVIDER with SID ={your service identity application id in binary type}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{MSI name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{MSI name}]
    ```

9.  Kommandot har slutförts.

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Etablera Azure-SSIS IR i portalen

När du etablerar Azure SSIS IR med Azure-portalen på den **SQL-inställningar** markerar den ”Använd AAD-autentisering med den hanterade identitet för din ADF” alternativet. (Följande skärmbild visar inställningarna för IR med Azure SQL Database. För IR med Managed Instance är ”tjänstenivå Catalog Database”-egenskapen inte tillgängliga. andra inställningar är desamma.)

Mer information om hur du skapar en Azure-SSIS integration runtime finns i [skapa en Azure-SSIS integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Inställningar för Azure-SSIS integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Etablera Azure-SSIS IR med PowerShell

För att etablera Azure-SSIS IR med PowerShell, gör du följande:

1.  Installera den [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modulen.

2.  I ditt skript anger inte den *CatalogAdminCredential* parametern. Exempel:

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
