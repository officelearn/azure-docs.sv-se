---
title: Använd PowerShell för att registrera och skanna Power BI (för hands version)
description: Lär dig hur du använder PowerShell för att registrera och skanna en Power BI-klient i Azure avdelningens kontroll.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553504"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Använda PowerShell för att registrera och skanna Power BI i Azure avdelningens kontroll (för hands version) 

Den här artikeln visar hur du använder PowerShell för att ställa in en sökning av en Power BI-klient i en Azure avdelningens kontroll-katalog.

## <a name="power-bi-authentication-background"></a>Power BI autentiserings-bakgrund

Avdelningens kontroll-katalogen måste ansluta till Power BI Admin API för att kunna söka efter artefakter i en Power BI klient. Power BI Admin API stöder för närvarande två typer av autentisering:

- Hanterad identitet (MSI).
- Delegerad användarautentisering.

> [!Note]
> MSI rekommenderas, om inte delegerad användarautentisering krävs.

## <a name="create-a-security-group"></a>Skapa en säkerhets grupp

Varje avdelningens kontroll-katalog har sin egen systemtilldelade hanterade identitet som måste ges åtkomst till den Power BI klienten för att aktivera genomsökning. Katalog namnet kan användas för att hitta identiteten på Azure Portal.

1. Sök efter Azure Active Directory i [Azure Portal](https://portal.azure.com).
1. Skapa en ny säkerhets grupp i Azure Active Directory genom att följa [skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Du kan hoppa över det här steget om du redan har en säkerhets grupp som ska användas.

1. Se till att välja säkerhet som **grupptyp**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ av säkerhets grupp":::

1. Lägg till din katalogs hanterade identitet i den här säkerhets gruppen genom att välja medlemmar och sedan **+ Lägg till medlemmar**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Lägg till katalogens hanterade instans i gruppen":::

1. Sök efter din katalog och markera den.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Lägg till katalog genom att söka efter den":::

1. Du bör se ett meddelande som visar att det har lagts till.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Tillägget av katalog-MSI lyckades":::

## <a name="associate-the-security-group-with-power-bi"></a>Associera säkerhets gruppen med Power BI

1. Logga in på [Power BI admin-portalen](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Lägg till den här funktions flaggan:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Den här flaggan aktiverar funktionen som gör att du kan associera din säkerhets grupp. Exempel:

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Du måste vara Power BI administratör för att se sidan klient inställningar.

1. Välj **Inställningar**  >  **för utvecklare Tillåt att tjänstens huvud namn använder skrivskyddade Power BI-API: er (för hands version)**.
1. Välj **vissa säkerhets grupper**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Bild som visar hur du tillåter tjänstens huvud namn för att få skrivskyddade Power BI administrations-API-behörigheter ":::

    > [!Caution]
    > När du tillåter säkerhets gruppen som du skapade (som har din hanterade identitet för data katalogen som medlem) att använda skrivskyddade Power BI administrations-API: er kan du också få åtkomst till metadata (t. ex. instrument panel och rapport namn, ägare, beskrivningar osv.) för alla dina Power BI artefakter i den här klienten. När metadata har hämtats till Azure-avdelningens kontroll, avdelningens kontroll-behörigheter, inte Power BI behörigheter, avgör vem som kan se dessa metadata.

    > [!Note]
    > Du kan ta bort säkerhets gruppen från dina inställningar för utvecklare, men de metadata som tidigare extraheras tas inte bort från avdelningens kontroll-kontot. Du kan ta bort den separat.

## <a name="register-power-bi-and-set-up-a-scan"></a>Registrera Power BI och konfigurera en sökning

Nu när du har gett katalog behörighet att ansluta till administrations-API: t för din Power BI-klient måste du konfigurera din genomsökning i katalogen. Det gör du genom att konfigurera och köra ett PowerShell-skript.

1. Ladda ned och extrahera ADC PowerShell-cmdletar.
1. Konfigurera skriptet genom att ange värden för tilldelningarna överst i skriptet.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > Du måste vara deltagare eller ägare för den prenumeration som du kör kommandona för.

1. Starta din genomsökning genom att köra följande skript:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Registrera och skanna Power BI

Den rekommenderade autentiseringsmetoden är MSI. Men om du vill skanna en Power BI klient som finns i en annan Azure-klient än din katalog använder du delegerad autentisering.

För att du ska kunna delegera autentisering måste du ha autentiseringsuppgifter för administratörer, samt Power BI admin-autentiseringsuppgifter. Du måste också skapa en Azure-App och bevilja den Power BI Tenant. ReadAll-behörigheter.

1. Navigera till [Azure Portal](https://portal.azure.com) och Sök efter **registrerings program**.

1. Välj **+ ny registrering** från **Appregistreringar**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Bild som visar hur du skapar en ny Azure App-registrering":::

1. Ange ett namn för din app.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="Registrera ny app":::

1. När din app har skapats väljer du **API-behörigheter** och sedan **+ Lägg till en behörighet**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Bild som visar hur du lägger till behörighet till appen":::

1. Välj **Power BI tjänst** på **begäran API-behörighet**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Bild som visar hur du väljer PBI-tjänsten":::

1. Välj **delegerade behörigheter** och **klient. Läs. alla**. Välj sedan **Lägg till behörigheter**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Bild som visar hur du begär API-behörigheter":::

1. Välj **bevilja administratörs medgivande**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Bild som visar hur du beviljar administratörs medgivande":::

1. Kopiera **program-ID: t (klient) ID** och **katalogens ID-** värden.  Du kommer att använda dessa värden när du konfigurerar din sökning.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Bild som visar kopiering av klienten och klient-ID: n":::

1. Konfigurera din genomsökning i PowerShell. Skriptet kommer att fråga efter autentiseringsuppgifter. Du måste ha minst deltagar rollen och avdelningens kontroll-rollen som administratör för data källa på den Azure-prenumeration som du tänker använda.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Kör din sökning.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure avdelningens kontroll finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
