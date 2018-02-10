---
title: "Anslut Azure SSIS-integrering runtime till ett virtuellt nätverk | Microsoft Docs"
description: "Lär dig hur du ansluter till Azure-SSIS-integrering runtime till ett Azure virtual network."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 72b0965e1fda733651baa04997da1242a73320f1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta till en Azure-SSIS-integrering körning till ett virtuellt nätverk
Ansluta till din Azure-SSIS-integrering runtime (IR) till ett Azure virtual network (VNet) i följande scenarier: 

- Du är värd för SSIS-katalogdatabasen på en SQL Server-hanterad instans (privat förhandsversion) som är en del av ett VNet.
- Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime.

 Azure Data Factory version 2 (förhandsversion) kan du ansluta din Azure-SSIS-integrering runtime till en klassisk eller ett Azure Resource Manager VNet. 

 > [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Åtkomst till lokala datalager
Om SSIS-paket åtkomst till endast de offentliga moln datalager, behöver du inte ansluta till Azure-SSIS IR till ett virtuellt nätverk. Om SSIS-paket åtkomst till lokala datalager, måste du ansluta Azure SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. 

Om SSIS-katalogen finns i Azure SQL-databas som inte är i VNet, måste du öppna lämpliga portar. 

Om SSIS-katalogen finns i Azure SQL hanteras instansen (MI) som är i ett VNet, kan du delta i Azure-SSIS IR till samma virtuella nätverk (eller) ett annat virtuellt nätverk som har en VNet-till-VNet-anslutning med det som har Azure SQL hanteras-instans. VNet kan vara ett klassiskt virtuellt nätverk eller ett Azure VNet för hantering av resursen. Om du planerar att ansluta till Azure-SSIS-IR den **samma virtuella nätverk** som innehåller SQL-MI, kontrollera att Azure-SSIS-IR är i ett **annat undernät** än det som har SQL-MI.   

Följande avsnitt innehåller mer information.

Här följer några viktiga saker att Observera: 

- Om det finns inga befintliga virtuella nätverk som är anslutna till ditt lokala nätverk måste du först skapa en [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) eller en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera sedan en plats-till-plats [VPN-gatewayanslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) anslutning från det virtuella nätverket till ditt lokala nätverk.
- Om det finns en befintlig Azure Resource Manager eller klassiska virtuella nätverk som är anslutna till ditt lokala nätverk på samma plats som din Azure-SSIS-IR, kan du ansluta IR till det virtuella nätverket.
- Om det finns en befintlig klassiska virtuella nätverk som är anslutna till ditt lokala nätverk på en annan plats från Azure-SSIS-IR måste du först skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-IR att ansluta till. Konfigurera sedan en [klassisk till klassiska VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) anslutning. Du kan också skapa ett [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md#create-a-virtual-network) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera en [klassisk till Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning.
- Om det finns en befintlig Azure Resource Manager VNet anslutna till ditt lokala nätverk på en annan plats från Azure-SSIS-IR måste du först skapa en [Azure Resource Manager VNet](../virtual-network/quick-create-portal.md##create-a-virtual-network) för din Azure-SSIS-IR att ansluta till. Konfigurera sedan en Azure Resource Manager till Azure Resource Manager VNet-anslutning. Du kan skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-IR att ansluta till. Konfigurera sedan en [klassisk till Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning.

## <a name="domain-name-services-server"></a>Domain Name Services server 
Om du behöver använda din egen Services DNS (Domain Name)-server i ett VNet kopplas med din Azure-SSIS-integrering runtime följer vägledning för att [säkerställa att noderna i Azure-SSIS-integrering-körningsmiljön i VNet kan matcha Azure-slutpunkter](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Nätverkssäkerhetsgrupp
Om du behöver implementera Nätverkssäkerhetsgrupp (NSG) i ett VNet kopplas med din Azure-SSIS-integrering Runtime tillåta inkommande/utgående traffics via följande portar:

| Portar | Riktning | Transportprotokoll | Syfte | Inkommande källa för utgående mål |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (om du vill ansluta till IR till klassiska virtuella nätverk)<br/><br/>29876, 29877 (om du vill ansluta till IR i Azure Resource Manager VNet) | Inkommande | TCP | Azure-tjänster använder dessa portar för att kommunicera med noderna i Azure-SSIS-integrering-körningsmiljön i VNet. | Internet | 
| 443 | Utgående | TCP | Noderna i din Azure-SSIS-integrering körning i virtuella nätverk använda den här porten för att komma åt Azure-tjänster, till exempel Azure Storage, Event Hub osv. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Utgående | TCP | Noderna i Azure-SSIS-integrering-körningsmiljön i VNet använda dessa portar för att komma åt SSISDB hos dina Azure SQL Database-server (gäller inte för SSISDB Azure SQL-instans som hanteras som värd). | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Azure-portalen (Data Factory UI)
Det här avsnittet visar hur du kopplar en befintlig Azure SSIS-körning till ett virtuellt nätverk (klassiskt eller Azure Resource Manager) med hjälp av Azure-portalen och Data Factory-Användargränssnittet. Först måste du konfigurera VNet korrekt innan du ansluter din Azure SSIS-IR till VNet. Gå igenom följande två avsnitt baserat på vilken typ av ditt virtuella nätverk (klassiskt eller Azure Resource Manager). Fortsätt sedan med det tredje avsnittet för att ansluta till din Azure SSIS-IR VNet. 

### <a name="use-portal-to-configure-a-classic-vnet"></a>Använda portalen för att konfigurera ett klassiskt virtuellt nätverk
Du måste först konfigurera VNet innan du kan ansluta till en Azure-SSIS-IR till VNet.

1. Starta **Microsoft Edge** eller **Google Chrome** webbläsare. Data Factory UI stöds för närvarande bara i Microsoft Edge och Google Chrome webbläsare.
2. Logga in på [Azure-portalen](https://portal.azure.com).
3. Klicka på **fler tjänster**. Filtrera efter och välj **virtuella nätverk (klassiskt)**.
4. Filtrera efter och välj din **virtuellt nätverk** i listan. 
5. På sidan virtuella nätverk (klassiska) Välj **egenskaper**. 

    ![klassiska VNet-resurs-ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Klicka på kopieringsknappen för den **resurs-ID** Kopiera resurs-ID för det klassiska nätverket till Urklipp. Spara ID från Urklipp i OneNote eller en fil.
6. Klicka på **undernät** på den vänstra menyn och kontrollera att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS-integrering runtime.

    ![Antal tillgängliga adresser i virtuella nätverk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Anslut **MicrosoftAzureBatch** till **klassiska Virtual Machine-deltagare** roll för VNet. 
    1. Åtkomstkontroll (IAM) på den vänstra menyn och klicka på **Lägg till** i verktygsfältet.
    
        ![Åtkomstkontroll -> Lägg till](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. I **lägga till behörigheter** väljer **klassiska Virtual Machine-deltagare** för **rollen**. Kopiera och klistra in **ddbf3205-c6bd-46ae-8127-60eb93363864** i den **Välj** textruta och välj sedan **Microsoft Azure Batch** från listan över sökresultat. 
    
        ![Lägg till behörigheter – Sök](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. Klicka på Spara för att spara inställningarna och stänga sidan.
    
        ![Spara inställningar](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. Bekräfta att du ser **MicrosoftAzureBatch** i listan över deltagare.
    
        ![Bekräfta AzureBatch åtkomst](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. Kontrollera att Azure Batch-provider är registrerad i Azure-prenumeration som har VNet eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration är prenumerationen registrerad för Azure Batch.
    1. I Azure-portalen klickar du på **prenumerationer** på den vänstra menyn. 
    2. Välj din **prenumeration**. 
    3. Klicka på **resursproviders** till vänster och kontrollera att `Microsoft.Batch` är en registrerad provider. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Om du inte ser `Microsoft.Batch` i listan för att registrera den, [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare. 

### <a name="use-portal-to-configure-an-azure-resource-manager-vnet"></a>Använda portalen för att konfigurera en Azure Resource Manager-VNet
Du måste först konfigurera VNet innan du kan ansluta till en Azure-SSIS-IR till VNet.

1. Starta **Microsoft Edge** eller **Google Chrome** webbläsare. Data Factory UI stöds för närvarande bara i Microsoft Edge och Google Chrome webbläsare.
2. Logga in på [Azure-portalen](https://portal.azure.com).
3. Klicka på **fler tjänster**. Filtrera efter och välj **virtuella nätverken**.
4. Filtrera efter och välj din **virtuellt nätverk** i listan. 
5. Virtuella nätverk på sidan Välj **egenskaper**. 
6. Klicka på kopieringsknappen för den **resurs-ID** Kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID från Urklipp i OneNote eller en fil.
7. Klicka på **undernät** på den vänstra menyn och kontrollera att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS-integrering runtime.
8. Kontrollera att Azure Batch-provider är registrerad i Azure-prenumeration som har VNet eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration är prenumerationen registrerad för Azure Batch.
    1. I Azure-portalen klickar du på **prenumerationer** på den vänstra menyn. 
    2. Välj din **prenumeration**. 
    3. Klicka på **resursproviders** till vänster och kontrollera att `Microsoft.Batch` är en registrerad provider. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Om du inte ser `Microsoft.Batch` i listan för att registrera den, [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare.

### <a name="join-the-azure-ssis-ir-to-a-vnet"></a>Anslut Azure SSIS-IR till ett virtuellt nätverk


1. Starta **Microsoft Edge** eller **Google Chrome** webbläsare. Data Factory UI stöds för närvarande bara i Microsoft Edge och Google Chrome webbläsare.
2. I den [Azure-portalen](https://portal.azure.com)väljer **datafabriker** på den vänstra menyn. Om du inte ser **datafabriker** på menyn, Välj **fler tjänster**väljer **datafabriker** i den **INTELLIGENCE + analys** avsnittet. 
    
    ![Data fabriker lista](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Välj din data factory med Azure SSIS-integrering körning i listan. Du kan se startsidan för din data factory. Välj **författare & distribuera** panelen. Du kan se Data Factory-användargränssnittet (UI) i en separat flik. 

    ![Datafabrikens startsida](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. I Användargränssnittet för Data Factory växla till den **redigera** väljer **anslutningar**, och växla till den **integrering körningar** fliken. 

    ![På fliken Integration körningar](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Om din Azure SSIS-IR körs i listan integration runtime väljer **stoppa** knappen i den **åtgärder** för din Azure SSIS IR. Du kan inte redigera en IR tills du avbryter den. 

    ![Stoppa IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Välj i listan integration runtime **redigera** knappen i den **åtgärder** för din Azure SSIS IR.

    ![Redigera integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. På den **allmänna inställningar** sida av den **integrering Runtime installationsprogrammet** väljer **nästa**. 

    ![IR-installation - allmänna inställningar](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. På den **SQL-inställningar** anger administratören **lösenord**, och välj **nästa**.

    ![IR-installation - SQL-inställningar](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. På den **avancerade inställningar** gör du följande åtgärder: 

    1. Markera kryssrutan för den **väljer ett virtuellt nätverk för din Azure-SSIS-integrering Runtime att ansluta och ge Azure-tjänster så här konfigurerar du VNet behörighetsinställningar**. 
    2. För **typen**, ange om VNet är ett klassiskt virtuellt nätverk eller ett Azure Resource Manager VNet. 
    3. För **VNet namn**, Välj ditt VNet.
    4. För **Undernätsnamn**, Välj ditt undernät i VNet. 
    5. Välj **uppdatering**. 

        ![IR-installation - avancerade inställningar](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Nu, kan du börja IR med hjälp av den **starta** knappen i den **åtgärder** för din Azure SSIS IR. Det tar cirka 20 minuter för att starta en Azure SSIS IR. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-vnet"></a>Konfigurera virtuella nätverk
Du måste först konfigurera VNet innan du kan ansluta till en Azure-SSIS-IR till VNet. Lägg till följande skript för att automatiskt konfigurera VNet/behörighetsinställningar för din Azure-SSIS-integrering runtime att ansluta till det virtuella nätverket.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Skapa en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk
Du kan skapa en Azure-SSIS-IR och ansluta den till virtuella nätverk på samma gång. Fullständig skript och instruktioner för att skapa en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk på samma gång finns [skapa Azure-SSIS-IR](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Anslut en befintlig Azure-SSIS-IR till ett virtuellt nätverk
Skriptet i den [skapa Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) artikeln visar hur du skapar en Azure-SSIS-IR och ansluta den till ett VNet i samma skript. Om du har en befintlig Azure-SSIS, utför följande steg för att ansluta till det virtuella nätverket. 

1. Stoppa Azure SSIS-IR.
2. Konfigurera Azure SSIS-IR för att ansluta det virtuella nätverket. 
3. Starta Azure-SSIS-IR. 

### <a name="define-the-variables"></a>Definiera variabler

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet).
$VnetId = "<Name of your Azure virtual netowrk>"
$SubnetName = "<Name of the subnet in VNet>"
```

### <a name="stop-the-azure-ssis-ir"></a>Stoppa Azure SSIS-IR
Stoppa Azure SSIS-integrering runtime innan du kan ansluta till ett virtuellt nätverk. Det här kommandot släpper alla dess noder och stoppar fakturering.

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-vnet-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurera VNet-inställningarna att ansluta till Azure-SSIS IR
Registrera Azure Batch-resursprovidern:

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurera Azure SSIS-IR
Kör kommandot Set-AzureRmDataFactoryV2IntegrationRuntime att konfigurera Azure SSIS-integrering runtime för att ansluta det virtuella nätverket: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS-IR
Kör följande kommando för att starta Azure-SSIS Integration Runtime: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Det här kommandot tar mellan **20 till 30 minuter** att slutföra.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med självstudien och innehåller instruktioner om att använda Azure SQL-hanterade instanser (privat förhandsgranskning) och ansluta IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
