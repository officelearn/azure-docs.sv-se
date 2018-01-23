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
ms.openlocfilehash: f40f0551ed65a42bcacf2307cbec462fd5c3ac25
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Ansluta till en Azure-SSIS-integrering körning till ett virtuellt nätverk
Du måste ansluta Azure SSIS-integrering runtime (IR) till ett Azure virtual network (VNet) om någon av följande villkor är uppfyllda: 

- Du är värd för SSIS-katalogdatabasen på en SQL Server-hanterad instans (privat förhandsversion) som är en del av ett VNet.
- Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime.

 Azure Data Factory version 2 (förhandsversion) kan du ansluta din Azure-SSIS-integrering runtime till en klassisk eller ett Azure Resource Manager VNet. 

 > [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).

## <a name="access-on-premises-data-stores"></a>Åtkomst till lokala datalager
Om SSIS-paket åtkomst till endast de offentliga moln datalager, behöver du inte ansluta till Azure-SSIS IR till ett virtuellt nätverk. Om SSIS-paket åtkomst till lokala datalager, måste du ansluta Azure SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket. Om SSIS-katalogen finns i Azure SQL-databas som inte är i VNet, måste du öppna lämpliga portar. Om SSIS-katalogen finns i Azure SQL hanteras-instans som är i ett Azure Resource Manager VNet eller ett klassiskt virtuellt nätverk, kan du delta i Azure-SSIS IR till samma virtuella nätverk (eller) ett annat virtuellt nätverk som har en VNet-till-VNet-anslutning med det som har Azure SQL hanteras-instans. Följande avsnitt innehåller mer information.

Här följer några viktiga saker att Observera: 

- Om det finns inga befintliga virtuella nätverk som är anslutna till ditt lokala nätverk måste du först skapa en [Azure Resource Manager VNet](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vnet) eller en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera sedan en plats-till-plats [VPN-gatewayanslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) anslutning från det virtuella nätverket till ditt lokala nätverk.
- Om det finns en befintlig Azure Resource Manager VNet eller ett klassiskt virtuellt nätverk som är anslutna till ditt lokala nätverk på samma plats som din Azure-SSIS-integrering runtime, kan du delta i din Azure-SSIS-integrering runtime till den.
- Om det finns en befintlig klassiska virtuella nätverk som är ansluten till nätverket lokalt på en annan plats från din Azure-SSIS-integrering Runtime måste du först skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering Runtime att ansluta till. Konfigurera sedan en [klassisk till klassiska VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) anslutning. Du kan också skapa ett [Azure Resource Manager VNet](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vnet) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera en [klassisk till Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning.
- Om det finns en befintlig Azure Resource Manager VNet anslutna till ditt lokala nätverk på en annan plats från din Azure-SSIS-integrering Runtime måste du först skapa en [Azure Resource Manager VNet](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vnet) för din Azure-SSIS Integration runtime att ansluta till. Konfigurera sedan en Azure Resource Manager till Azure Resource Manager VNet-anslutning. Du kan skapa en [klassiska VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för din Azure-SSIS-integrering runtime att ansluta till. Konfigurera sedan en [klassisk till Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) anslutning.

## <a name="domain-name-services-server"></a>Domain Name Services server 
Om du behöver använda din egen Services DNS (Domain Name)-server i ett VNet kopplas med din Azure-SSIS-integrering runtime följer vägledning för att [säkerställa att noderna i Azure-SSIS-integrering-körningsmiljön i VNet kan matcha Azure-slutpunkter](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="network-security-group"></a>Nätverkssäkerhetsgrupp
Om du behöver implementera Nätverkssäkerhetsgrupp (NSG) i ett VNet kopplas med din Azure-SSIS-integrering Runtime tillåta inkommande/utgående traffics via följande portar:

| Portar | Riktning | Transportprotokoll | Syfte | Inkommande källa för utgående mål |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | Inkommande | TCP | Azure-tjänster använder dessa portar för att kommunicera med noderna i Azure-SSIS-integrering-körningsmiljön i VNet. | Internet | 
| 443 | Utgående | TCP | Noderna i din Azure-SSIS-integrering körning i virtuella nätverk använda den här porten för att komma åt Azure-tjänster, till exempel Azure Storage, Event Hub osv. | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | Utgående | TCP | Noderna i Azure-SSIS-integrering-körningsmiljön i VNet använda dessa portar för att komma åt SSISDB hos dina Azure SQL Database-server (gäller inte för SSISDB Azure SQL-instans som hanteras som värd). | Internet | 

## <a name="configure-vnet"></a>Konfigurera virtuella nätverk
Först måste du konfigurera VNet med något av följande sätt (jämfört med skript. Azure portal) innan du kan ansluta till en Azure-SSIS-IR till VNet. 

### <a name="script-to-configure-vnet"></a>Skript för att konfigurera virtuella nätverk 
Lägg till följande skript för att automatiskt konfigurera VNet/behörighetsinställningar för din Azure-SSIS-integrering runtime att ansluta till det virtuella nätverket.

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

### <a name="use-portal-to-configure-a-classic-vnet"></a>Använda portalen för att konfigurera ett klassiskt virtuellt nätverk
Kör skriptet är det enklaste sättet att konfigurera virtuella nätverk. Om du inte har åtkomst till att konfigurera virtuella nätverk för den automatiska konfigurationen misslyckas, ägaren av det virtuella nätverket / du kan försöka att konfigurera dem manuellt i följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **fler tjänster**. Filtrera efter och välj **virtuella nätverk (klassiskt)**.
3. Filtrera efter och välj din **virtuellt nätverk** i listan. 
4. På sidan virtuella nätverk (klassiska) Välj **egenskaper**. 

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
Kör skriptet är det enklaste sättet att konfigurera virtuella nätverk. Om du inte har åtkomst till att konfigurera virtuella nätverk för den automatiska konfigurationen misslyckas, ägaren av det virtuella nätverket / du kan försöka att konfigurera dem manuellt i följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **fler tjänster**. Filtrera efter och välj **virtuella nätverken**.
3. Filtrera efter och välj din **virtuellt nätverk** i listan. 
4. Virtuella nätverk på sidan Välj **egenskaper**. 
5. Klicka på kopieringsknappen för den **resurs-ID** Kopiera resurs-ID för det virtuella nätverket till Urklipp. Spara ID från Urklipp i OneNote eller en fil.
6. Klicka på **undernät** på den vänstra menyn och kontrollera att antalet **tillgängliga adresser** är större än noderna i din Azure-SSIS-integrering runtime.
5. Kontrollera att Azure Batch-provider är registrerad i Azure-prenumeration som har VNet eller registrera Azure Batch-providern. Om du redan har ett Azure Batch-konto i din prenumeration är prenumerationen registrerad för Azure Batch.
    1. I Azure-portalen klickar du på **prenumerationer** på den vänstra menyn. 
    2. Välj din **prenumeration**. 
    3. Klicka på **resursproviders** till vänster och kontrollera att `Microsoft.Batch` är en registrerad provider. 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    Om du inte ser `Microsoft.Batch` i listan för att registrera den, [skapa ett tomt Azure Batch-konto](../batch/batch-account-create-portal.md) i din prenumeration. Du kan ta bort den senare.

## <a name="create-an-azure-ssis-ir-and-join-it-to-a-vnet"></a>Skapa en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk
Du kan skapa en Azure-SSIS-IR och ansluta den till virtuella nätverk på samma gång. Fullständig skript och instruktioner för att skapa en Azure-SSIS-IR och ansluta den till ett virtuellt nätverk på samma gång finns [skapa Azure-SSIS-IR](create-azure-ssis-integration-runtime.md).

## <a name="join-an-existing-azure-ssis-ir-to-a-vnet"></a>Anslut en befintlig Azure-SSIS-IR till ett virtuellt nätverk
Skriptet i den [skapa Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) artikeln visar hur du skapar en Azure-SSIS-IR och ansluta den till ett VNet i samma skript. Om du har en befintlig Azure-SSIS, utför följande steg för att ansluta till det virtuella nätverket. 

1. Stoppa Azure SSIS-IR.
2. Konfigurera Azure SSIS-IR för att ansluta det virtuella nätverket. 
3. Starta Azure-SSIS-IR. 

## <a name="define-the-variables"></a>Definiera variabler

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
# OPTIONAL: specify your VNet ID and the subnet name. 
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

## <a name="start-the-azure-ssis-ir"></a>Starta Azure-SSIS-IR
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
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med självstudien och innehåller instruktioner om att använda Azure SQL-hanterade instanser (privat förhandsgranskning) och ansluta IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
