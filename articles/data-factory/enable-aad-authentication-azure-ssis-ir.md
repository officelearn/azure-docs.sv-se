---
title: Aktivera Azure Active Directory-autentisering för Azure-SSIS-integrering runtime | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar Azure SSIS-integrering runtime för att aktivera anslutningar som använder Azure Active Directory-autentisering.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: douglasl
ms.openlocfilehash: 5fce1a3b8370ce49a522f41749795362e1bf1f9b
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757285"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Aktivera Azure Active Directory-autentisering för Azure-SSIS-integrering runtime

Den här artikeln visar hur du skapar en Azure-SSIS-IR med Azure Data Factory-tjänstidentitet. Azure Active Directory (Azure AD)-autentisering med den hanterade tjänsten identitet (MSI) för Azure-SSIS-integrering runtime kan du använda Data Factory MSI i stället för SQL-autentisering för att skapa en Azure-SSIS-integrering körning.

Mer information om Data Factory MSI-filerna finns [Azure Data Factory tjänstidentiteten](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Om du redan har skapat en körning för Azure SSIS-integrering med SQL-autentisering kan du konfigurera om IR att använda Azure AD-autentisering med PowerShell just nu.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Skapa en grupp i Azure AD och göra Data Factory MSI medlem i gruppen

Du kan använda en befintlig Azure AD-grupp eller skapa en ny med hjälp av Azure AD PowerShell.

1.  Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul.

2.  Logga in med `Connect-AzureAD`, och kör följande kommando för att skapa gruppen och spara det i en variabel:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Det ser ut som följande exempel, som också undersöker värdet för variabeln utdata:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Lägg till Data Factory MSI i gruppen. Du kan följa [Azure Data Factory tjänstidentiteten](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity) att hämta service identity-ID (till exempel 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Du också undersöka gruppmedlemskap efteråt.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivera Azure AD på Azure SQL-databas

Azure SQL Database har stöd för att skapa en databas med en Azure AD-användare. Därför kan du ange en Azure AD-användare som Active Directory-administratör och logga sedan in SSMS med Azure AD-användare. Du kan skapa en innesluten användare för Azure AD-grupp att aktivera IR att skapa katalogen för SQL Server Integration Services (SSIS) på servern.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Aktivera Azure AD-autentisering för Azure SQL Database

Du kan [konfigurera Azure AD-autentisering för SQL-databasen](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

1.  Välj i Azure-portalen **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfönstret.

2.  Välj SQL-databas som ska aktiveras för Azure AD-autentisering.

3.  I den **inställningar** avsnitt av bladet välj **Active Directory-administratör**.

4.  Välj i kommandofältet **ange admin**.

5.  Markera ett användarkonto i Azure AD att göras en administratör på servern och välj sedan **Välj.**

6.  Välj i kommandofältet **spara.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Skapa en innesluten användare i databasen som representerar Azure AD-grupp

För den här nästa steg behöver du [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starta SQL Server Management Studio.

2.  I den **Anslut till Server** dialogrutan, ange namnet på SQL-servern i den **servernamn** fältet.

3.  I den **autentisering** väljer **Active Directory - Universal med stöd för MFA**. (Du kan också använda andra två typer av Active Directory-autentisering. Se [konfigurera och hantera Azure Active Directory-autentisering med SQL Database, hanteras instans](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure).)

4.  I den **användarnamn** , ange namnet på Azure AD-kontot som du anger som serveradministratören – till exempel testuser@xxxonline.com.

5.  Välj **ansluta**. Slutföra inloggningen.

6.  I den **Object Explorer**, expandera den **databaser** -> systemdatabaser mapp.

7.  Höger – Välj på **master** databasen och välj **ny fråga**.

8.  Ange följande rad i frågefönstret och välj **Execute** i verktygsfältet:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Kommandot ska slutföras, skapa innesluten användare för gruppen.

9.  Rensa frågefönstret, ange följande rad och välj **Execute** i verktygsfältet:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Kommandot ska slutföras, bevilja innesluten användare möjlighet att skapa databasen.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Aktivera Azure AD på Azure SQL-hanterade databasinstans

Azure SQL-hanterade databasinstans stöder inte att skapa en databas med alla Azure AD-användare än AD-administratör. Därför kan behöva du ange Azure AD-grupp som Active Directory-administratör. Du behöver inte skapa den inneslutna användaren.

Du kan [konfigurera Azure AD-autentisering för SQL-hanterade databasinstans servern](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) med följande steg:

7.  Välj i Azure-portalen **alla tjänster** -> **SQL-servrar** från det vänstra navigeringsfönstret.

8.  Välj SQLServer måste vara aktiverat för Azure AD-autentisering.

9.  I den **inställningar** avsnitt av bladet välj **Active Directory-administratör**.

10. Välj i kommandofältet **ange admin**.

11. Söka efter och välj Azure AD-grupp (till exempel SSISIrGroup) och välj **Välj.**

12. Välj i kommandofältet **spara.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Etablera Azure SSIS-IR i portalen

När du konfigurerar din Azure-SSIS-IR med Azure-portalen på den **SQL-inställningar** sida, kontrollera på ”Använd AAD autentisering med ADF-MSI” alternativet. (Följande skärmbild visar inställningarna för IR med Azure SQL Database. IR med hanterade instans är egenskapen ”Catalog Service databasnivå” inte tillgängligt; andra inställningar är desamma.)

Mer information om hur du skapar en Azure-SSIS-integrering körning finns [skapa en Azure-SSIS-integrering körning i Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

![Inställningar för Azure-SSIS-integrering runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Etablera Azure SSIS-IR med PowerShell

För att etablera din Azure-SSIS-IR med PowerShell kan du göra följande:

1.  Installera den [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modul.

2.  I ditt skript inte anger den *CatalogAdminCredential* parameter. Exempel:

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
