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
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: aa06110a6f6fe668388c6aecd98c1ddeeae37edd
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576637"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Aktivera Azure Active Directory-autentisering för Azure-SSIS integration runtime

Den här artikeln visar hur du skapar en Azure-SSIS IR med tjänstidentitet för Azure Data Factory. Azure Active Directory (Azure AD)-autentisering med den hanterad tjänstidentitet (MSI) för Azure-SSIS integration runtime kan du använda Data Factory MSI i stället för SQL-autentisering för att skapa en Azure-SSIS integration runtime.

Mer information om Data Factory MSI, se [tjänstidentitet för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Om du redan har skapat en Azure-SSIS integration runtime med SQL-autentisering kan du konfigurera om IR för att använda Azure AD-autentisering med PowerShell just nu.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Skapa en grupp i Azure AD och göra Data Factory MSI medlem i gruppen

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.

1.  Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modulen.

2.  Logga in med `Connect-AzureAD`, och kör följande kommando för att skapa gruppen och spara den i en variabel:

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

3.  Lägg till Data Factory MSI i gruppen. Du kan följa [tjänstidentitet för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) att hämta den huvudsakliga TJÄNSTIDENTITETS-ID (till exempel 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, men inte använder tjänsten IDENTITETSPROGRAM-ID för detta ändamål).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Du kan kontrollera också gruppmedlemskapet efteråt.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivera Azure AD i Azure SQL-databas

Azure SQL Database kan du skapa en databas med Azure AD-användare. Därför kan du ange en Azure AD-användare som Active Directory-administratör och sedan logga in på SSMS med Azure AD-användare. Du kan skapa en innesluten användare för Azure AD-grupp att aktivera IR att skapa SQL Server Integration Services (SSIS)-katalogen på servern.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Aktivera Azure AD-autentisering för Azure SQL Database

Du kan [konfigurera Azure AD-autentisering för SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

1.  I Azure-portalen väljer du **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfältet.

2.  Välj SQL-databas som ska aktiveras för Azure AD-autentisering.

3.  I den **inställningar** på bladet, väljer **Active Directory-administratör**.

4.  I kommandofältet väljer **konfigurera administratör**.

5.  Välj en Azure AD-användarkonto för att upprätta en administratör för servern och välj sedan **Välj.**

6.  I kommandofältet väljer **spara.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Skapa en innesluten användare i databasen som representerar Azure AD-gruppen

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SQL Server Management Studio.

2.  I den **Anslut till Server** dialogrutan, ange namnet på SQL-servern i den **servernamn** fält.

3.  I fältet **Autentisering** väljer du **Active Directory – Universell med stöd för MFA**. (Du kan också använda andra två typer av Active Directory-autentisering. Se [konfigurera och hantera Azure Active Directory-autentisering med SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  I den **användarnamn** fältet, anger du namnet på Azure AD-kontot som du anger som serveradministratör – till exempel testuser@xxxonline.com.

5.  Välj **ansluta**. Slutför inloggningsprocessen.

6.  I den **Object Explorer**, expandera den **databaser** -> systemdatabaser mapp.

7.  Höger-Välj på **master** databasen och välj **ny fråga**.

8.  Ange följande rad i frågefönstret och välj **kör** i verktygsfältet:

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

Azure SQL Database Managed Instance stöder inte skapa en databas med alla Azure AD-användare än AD-administratör. Därför kan behöva du konfigurera Azure AD-grupp som Active Directory-administratör. Du behöver inte skapa den inneslutna användaren.

Du kan [konfigurera Azure AD-autentisering för SQL Database Managed Instance servern](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

7.  I Azure-portalen väljer du **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfältet.

8.  Välj den SQL servern som ska aktiveras för Azure AD-autentisering.

9.  I den **inställningar** på bladet, väljer **Active Directory-administratör**.

10. I kommandofältet väljer **konfigurera administratör**.

11. Sök och välj den Azure AD-grupp (till exempel SSISIrGroup) och välj **Välj.**

12. I kommandofältet väljer **spara.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Etablera Azure-SSIS IR i portalen

När du etablerar Azure SSIS IR med Azure-portalen på den **SQL-inställningar** markerar den ”Använd AAD autentisering med ADF-MSI” alternativet. (Följande skärmbild visar inställningarna för IR med Azure SQL Database. För IR med Managed Instance är ”tjänstenivå Catalog Database”-egenskapen inte tillgängliga. andra inställningar är desamma.)

Mer information om hur du skapar en Azure-SSIS integration runtime finns i [skapa en Azure-SSIS integration runtime i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Inställningar för Azure-SSIS integration runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Etablera Azure-SSIS IR med PowerShell

För att etablera Azure-SSIS IR med PowerShell, gör du följande:

1.  Installera den [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modulen.

2.  I ditt skript anger inte den *CatalogAdminCredential* parametern. Exempel:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
