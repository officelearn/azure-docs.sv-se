---
title: Red Hat Enterprise Linux Azure-avbildningar med egen prenumeration | Microsoft Docs
description: Lär dig mer om att överföra prenumerations avbildningar för Red Hat Enterprise Linux på Azure
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
ms.openlocfilehash: a54e1c9cbc9a80139d71a89f86ac51ecf5ce9902
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190503"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux guld-avbildningar för prenumerationer i Azure

Red Hat Enterprise Linux-avbildningar (RHEL) är tillgängliga i Azure via en PAYG (pay-on-go) eller med en egen prenumerations modell (Red Hat Gold image). Det här dokumentet ger en översikt över de Red Hat Gold-avbildningarna i Azure.

## <a name="important-points-to-consider"></a>Viktiga saker att tänka på

- De guld-avbildningar i Red Hat som tillhandahålls i det här programmet är produktions färdiga RHEL-avbildningar som liknar RHEL PAYG-avbildningarna i Azure-galleriet/Marketplace.

- Bilderna följer våra aktuella principer som beskrivs i [Red Hat Enterprise Linux avbildningar på Azure](./redhat-images.md)

- Standard Support principer gäller för virtuella datorer som skapats från de här avbildningarna

- De virtuella datorer som tillhandahålls från Red Hat Gold-avbildningar har inte RHEL-avgifter kopplade till RHEL PAYG-avbildningar

- Bilderna är inte berättigade, så du måste använda prenumerations hanteraren för att registrera och prenumerera på de virtuella datorerna för att få uppdateringar från Red Hat direkt

- Det går för närvarande inte att växla mellan BYOS-och PAYG-fakturerings modeller för Linux-avbildningar dynamiskt. Omdistribution av den virtuella datorn från respektive avbildning krävs för att byta fakturerings modell

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Krav och villkor för åtkomst till Red Hat Gold-avbildningar

1. Bekanta dig med program villkoren för [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) och aktivera dina Red Hat-prenumerationer för moln åtkomst i [Red Hat-prenumerations hanteraren](https://access.redhat.com/management/cloud). Du måste ha till gång till den eller de Azure-prenumerationer som ska registreras för moln åtkomst.

1. Om du har aktiverat Red Hat-prenumerationer för moln åtkomst som uppfyller rätt berättigande krav, aktive ras din Azure-prenumeration (er) automatiskt för åtkomst till Gold-avbildning.

### <a name="expected-time-for-image-access"></a>Förväntad tid för bild åtkomst

När du har slutfört stegen för att aktivera moln åtkomst verifierar Red Hat din behörighet för de röda hat-avbildningarna. Om verifieringen lyckas får du åtkomst till guld avbildningarna inom tre timmar.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Använd Red Hat Gold-avbildningarna från Azure Portal

1. När din Azure-prenumeration får åtkomst till Red Hat Gold-avbildningar kan du hitta dem i [Azure Portal](https://portal.azure.com) genom att gå till **skapa en resurs** och **Se alla**.

1. Längst upp på sidan visas att du har privata erbjudanden.

    ![Privata erbjudanden för Marketplace](./media/rhel-byos-privateoffers.png)

1. Du kan klicka på den lila länken eller rulla längst ned på sidan för att se dina privata erbjudanden.

1. Resten av etableringen i användar gränssnittet är ingen annan befintlig Red Hat-bild. Välj din RHEL-version och följ anvisningarna för att etablera den virtuella datorn. Med den här processen kan du också acceptera villkoren i avbildningen i det sista steget.

>[!NOTE]
>De här stegen gör det inte möjligt att använda Red Hat Gold-bildavbildningen för program distribution – ytterligare ett steg krävs enligt beskrivningen i avsnittet "Ytterligare information" nedan.

Resten av det här dokumentet fokuserar på CLI-metoden för att etablera och godkänna villkoren på avbildningen. Användar gränssnittet och CLI är helt utbytbara så långt som det slutliga resultatet (en etablerad virtuell RHEL Gold image VM) berörs.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Använd Red Hat Gold-avbildningar från Azure CLI
I följande uppsättning instruktioner går vi igenom den första distributions processen för en virtuell RHEL-dator med hjälp av Azure CLI. Dessa instruktioner förutsätter att du har [installerat Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Se till att du använder alla små bokstäver i referenserna utgivare, erbjudande, plan och bild för alla följande kommandon

1. Kontrol lera att du har den önskade prenumerationen:
    ```azurecli
    az account show -o=json
    ```

1. Skapa en resurs grupp för din Red Hat Gold-avbildning av virtuella datorer:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Acceptera avbildnings villkoren:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Dessa villkor måste godkännas *en gång per Azure-prenumeration, per avbildnings-SKU*.

1. Valfritt Verifiera distributionen av virtuella datorer med följande kommando:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Etablera den virtuella datorn genom att köra samma kommando som ovan utan argumentet `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH till den virtuella datorn och kontrol lera att du har en berättigad avbildning. Det gör du genom att köra `sudo yum repolist` (för RHEL 8 använder `sudo dnf repolist`). I utmatningen uppmanas du att använda prenumerations hanteraren för att registrera den virtuella datorn med Red Hat.

>[!NOTE]
>RHEL 8 `dnf` och `yum` är utbytbara, mer om detta finns i [Administratörs hand boken för RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Använd Red Hat Gold-avbildningar från PowerShell
Följande är ett exempel skript. Du bör ersätta resurs gruppen, platsen, namnet på den virtuella datorn, inloggnings informationen och andra variabler med den konfiguration du väljer. Information om utgivare och plan måste vara gemener.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Kryptera Red Hat Enterprise Linux Hämta guld bilder i din egen prenumeration

Red Hat Enterprise Linux Gold-avbildningar med egen prenumeration kan säkras genom användning av [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Men prenumerationen **måste** registreras innan kryptering aktive ras.  Information om hur du registrerar en RHEL BYOS guld-avbildning finns på Red Hat-webbplatsen. Se [hur du registrerar och prenumererar på ett system till Red Hat-kundportalen med Red Hat-prenumeration-Manager](https://access.redhat.com/solutions/253273); Om du har en aktiv Red Hat-prenumeration kan du också läsa [skapa Red Hat kund Portal aktiverings nycklar](https://access.redhat.com/articles/1378093).

Azure Disk Encryption stöds inte för [anpassade Red Hat-bilder](../../linux/redhat-create-upload-vhd.md). Ytterligare ADE-krav och nödvändiga komponenter finns dokumenterade i [Azure Disk Encryption för virtuella Linux-datorer](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Steg för att använda Azure Disk Encryption finns i [Azure Disk Encryption scenarier för virtuella Linux-datorer](../../linux/disk-encryption-linux.md) och relaterade artiklar.

## <a name="additional-information"></a>Ytterligare information

- Om du försöker etablera en virtuell dator för en prenumeration som inte är aktive rad för det här erbjudandet får du följande fel meddelande:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    I det här fallet kontaktar du Microsoft eller Red Hat för att aktivera din prenumeration.

- Om du ändrar en ögonblicks bild från en RHEL BYOS-avbildning och försöker publicera den anpassade avbildningen i det [delade bild galleriet](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)måste du ange plan information som matchar den ursprungliga källan till ögonblicks bilden. Kommandot kan till exempel se ut så här:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    Observera plan parametrarna på den sista raden ovan.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) stöds inte för anpassade avbildningar.

- Om du använder Automation för att etablera virtuella datorer från RHEL BYOS-avbildningar måste du ange plan parametrar som liknar vad som visades ovan. Om du till exempel använder terraform kan du ange plan informationen i ett [plan block](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Nästa steg
- Steg-för-steg-guider och programinformation för Cloud Access finns i [dokumentationen för moln åtkomst i Red Hat.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Läs mer om [Azure Red Hats uppdaterings infrastruktur](./redhat-rhui.md).
- Om du vill veta mer om alla Red Hat-bilder i Azure går du till [dokumentations sidan](./redhat-images.md).
- Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
- Ytterligare dokumentation om RHEL Gold-avbildningar finns i [Red Hat-dokumentationen](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
