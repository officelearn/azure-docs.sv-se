---
title: Red Hat Enterprise Linux ta med-din-egen-prenumeration Azure avbildningar | Microsoft-dokument
description: Lär dig mer om ta med egna prenumerationsavbildningar för Red Hat Enterprise Linux på Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264601"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux bring-your-own-prenumeration Gold Images i Azure

Red Hat Enterprise Linux (RHEL) avbildningar är tillgängliga i Azure via en pay-as-you-go eller bring-your-own-subscription (BYOS) (Red Hat Gold Image) modell. Den här artikeln innehåller en översikt över Red Hat Gold-avbildningarna i Azure.

>[!NOTE]
> RHEL BYOS Gold Images är tillgängliga i Azure Public (kommersiella) och Azure Government moln. De är inte tillgängliga i Azure Kina eller Azure Blackforest-moln.

## <a name="important-points-to-consider"></a>Viktiga punkter att ta hänsyn till

- Red Hat Gold-avbildningarna i det här programmet är produktionsklara RHEL-avbildningar som liknar RHEL-avbildningar som du använder i Azure Marketplace.
- Avbildningarna följer de aktuella principerna som beskrivs i [Red Hat Enterprise Linux-avbildningar på Azure](./redhat-images.md).
- Standardstödprinciper gäller för virtuella datorer som skapats från dessa avbildningar.
- De virtuella datorer som etablerats från Red Hat Gold Images har inte RHEL-avgifter som är kopplade till RHEL-pay-as-you-go-bilder.
- Bilderna är oentiterade. Du måste använda Red Hat Subscription-Manager för att registrera och prenumerera på de virtuella datorerna för att få uppdateringar från Red Hat direkt.
- Det är för närvarande inte möjligt att dynamiskt växla mellan BYOS och pay-as-you-go faktureringsmodeller för Linux-avbildningar. Om du vill växla faktureringsmodellen måste du distribuera om den virtuella datorn från respektive avbildning.

>[!NOTE]
> Generation 2 RHEL BYOS-avbildningar är för närvarande inte tillgängliga via marketplace-erbjudandet. Om du behöver en Generation 2 RHEL BYOS-avbildning besöker du cloud access-instrumentpanelen i Red Hat-prenumerationshantering. Mer information finns i [Red Hat-dokumentationen](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Krav och villkor för åtkomst till Red Hat Gold Images

1. Bekanta dig med villkoren för [Programmet Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Aktivera dina Red Hat-prenumerationer för Cloud Access på [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Du måste ha azure-prenumerationer som ska registreras för Cloud Access.

1. Om Red Hat-prenumerationerna som du har aktiverat för Cloud Access uppfyller kvalificeringskraven aktiveras dina Azure-prenumerationer automatiskt för åtkomst till Guldavbildning.

### <a name="expected-time-for-image-access"></a>Förväntad tid för bildåtkomst

När du är klar med aktiveringsstegen för Molnåtkomst validerar Red Hat din behörighet för Red Hat Gold Images. Om valideringen lyckas får du åtkomst till Guldbilderna inom tre timmar.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Använda Red Hat Gold-avbildningar från Azure-portalen

1. När din Azure-prenumeration har fått åtkomst till Red Hat Gold Images kan du hitta dem i [Azure-portalen](https://portal.azure.com). Gå till **Skapa en resurs** > **Se alla**.

1. Högst upp på sidan ser du att du har privata erbjudanden.

    ![Marknadsplats privata erbjudanden](./media/rhel-byos-privateoffers.png)

1. Markera den lila länken eller rulla ned till sidans nederkant för att se dina privata erbjudanden.

1. Resten av etableringen i användargränssnittet skiljer sig inte från någon annan befintlig Red Hat-avbildning. Välj din RHEL-version och följ anvisningarna för att etablera den virtuella datorn. Med den här processen kan du också acceptera villkoren för bilden i det sista steget.

>[!NOTE]
>De här stegen hittills aktiverar inte red hat-guldavbildningen för programmatisk distribution. Ytterligare ett steg krävs enligt beskrivningen i avsnittet "Ytterligare information".

Resten av det här dokumentet fokuserar på CLI-metoden för att etablera och acceptera villkor på bilden. Användargränssnittet och CLI är helt utbytbara när det gäller slutresultatet (en etablerad RHEL Gold Image VM) berörs.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Använda Red Hat Gold-avbildningar från Azure CLI

Följande instruktioner går igenom den första distributionsprocessen för en virtuell RHEL-dator med hjälp av Azure CLI. Dessa instruktioner förutsätter att du har [Azure CLI installerat](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Se till att du använder alla gemener i utgivar-, erbjudande-, abonnemangs- och bildreferenserna för alla följande kommandon.

1. Kontrollera att du finns med i önskad prenumeration.

    ```azurecli
    az account show -o=json
    ```

1. Skapa en resursgrupp för din virtuella röda hattguldavbildning.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Acceptera bildvillkoren.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Dessa villkor måste accepteras *en gång per Azure-prenumeration, per avbildning SKU*.

1. (Valfritt) Validera vm-distributionen med följande kommando:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Etablera den virtuella datorn genom att köra samma `--validate` kommando som visas i föregående exempel utan argumentet.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH i din virtuella dator och kontrollera att du har en oenigen avbildning. Kör om du `sudo yum repolist`vill göra det här steget . För RHEL 8 `sudo dnf repolist`använder du . Utdata ber dig att använda Prenumerationshanteraren för att registrera den virtuella datorn med Red Hat.

>[!NOTE]
>På RHEL 8, `dnf` och `yum` är utbytbara. Mer information finns i [administrationsguiden för RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Använda Red Hat Gold-bilder från PowerShell

Följande skript är ett exempel. Ersätt resursgruppen, platsen, VM-namnet, inloggningsinformationen och andra variabler med valfri konfiguration. Information om utgivare och abonnemang måste vara gemener.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Kryptera Red Hat Enterprise Linux bring-your-own-prenumeration Gold Images

Red Hat Enterprise Linux BYOS Gold Images kan skyddas med hjälp av [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Prenumerationen *måste* registreras innan du kan aktivera kryptering. Mer information om hur du registrerar en RHEL BYOS Gold Image finns i [Så här registrerar du dig och prenumererar på ett system på Red Hat Kundportal med Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Om du har en aktiv Red Hat-prenumeration kan du även läsa [Skapa Red Hat Customer Portal Activation Keys](https://access.redhat.com/articles/1378093).

Azure Disk Encryption stöds inte på [anpassade avbildningar](../../linux/redhat-create-upload-vhd.md)i Red Hat . Ytterligare krav och förutsättningar för Azure Disk-kryptering dokumenteras i [Azure Disk Encryption för virtuella Linux-datorer](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Steg för att tillämpa Azure Disk Encryption finns i [Azure Disk Encryption-scenarier på virtuella Linux-datorer](../../linux/disk-encryption-linux.md) och relaterade artiklar.

## <a name="additional-information"></a>Ytterligare information

- Om du försöker etablera en virtuell dator på en prenumeration som inte är aktiverad för det här erbjudandet visas följande meddelande:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    I så fall kontaktar du Microsoft eller Red Hat för att aktivera din prenumeration.

- Om du ändrar en ögonblicksbild från en RHEL BYOS-avbildning och försöker publicera den anpassade avbildningen i [det delade bildgalleriet](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)måste du ange planinformation som matchar den ursprungliga källan till ögonblicksbilden. Kommandot kan till exempel se ut så här:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Notera planparametrarna på den sista raden.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) stöds inte på anpassade avbildningar.

- Om du använder automatisering för att etablera virtuella datorer från RHEL BYOS-avbildningarna måste du ange planparametrar som liknar de som visades i exempelkommandona. Om du till exempel använder Terraform anger du planinformationen i ett [planblock](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Nästa steg

- Stegvisa guider och programinformation för Cloud Access finns i [Red Hat Cloud Access-dokumentationen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Mer information om Red Hat Update Infrastructure finns i [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Mer information om alla Red Hat-avbildningar i Azure finns på [dokumentationssidan](./redhat-images.md).
- Information om Supportpolicyer för Red Hat för alla versioner av RHEL finns på life [cycle-sidan för Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Mer information om RHEL Gold-bilder finns i [Red Hat-dokumentationen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
