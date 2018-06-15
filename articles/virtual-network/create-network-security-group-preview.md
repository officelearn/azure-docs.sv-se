---
title: Filtrera nätverkstrafik med Azure nätverks- och säkerhetsgrupper (förhandsversion) | Microsoft Docs
description: Lär dig hur du skapar nätverks- och säkerhetsgrupper (förhandsgranskning) om du vill begränsa vilka typer av nätverkstrafik till och från virtuella datorer.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2018
ms.locfileid: "30294284"
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrera nätverkstrafik med nätverks- och säkerhetsgrupper (förhandsgranskning)

I den här kursen lär du dig hur du skapar nätverkssäkerhetsgrupper för att filtrera nätverkstrafik till och från grupper med virtuella datorer med säkerhetsgrupper för programmet. Läs mer om nätverkssäkerhetsgrupper och säkerhetsgrupper för programmet i [Säkerhetsöversikt](security-overview.md). 

De följande avsnitten innehåller steg som du kan vidta för att skapa nätverk säkerhetsgrupper med hjälp av Azure-kommandoradsgränssnittet ([Azure CLI](#azure-cli)) och [Azure PowerShell](#powershell). Resultatet är samma, oavsett vilket verktyg du använder för att skapa säkerhetsgrupper för nätverket. Klicka för att gå till avsnittet av kursen. 

Den här artikeln innehåller steg för att skapa säkerhetsgrupper via distributionsmodell hanteraren för filserverresurser, som är den distributionsmodell som vi rekommenderar att du använder när du skapar nätverket säkerhetsgrupper för nätverket. Om du behöver skapa en nätverkssäkerhetsgrupp (klassisk) [skapar en nätverkssäkerhetsgrupp (klassisk)](virtual-networks-create-nsg-classic-ps.md). Om du inte är bekant med Azures distributionsmodeller [förstå Azure distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> Den här kursen använder gruppfunktioner för nätverkssäkerhet som för närvarande i förhandsversionen. Funktioner i förhandsversionen har inte samma tillgänglighet och tillförlitlighet som funktioner i allmänhet versionen. Om du vill implementera nätverkssäkerhet släpper grupper med hjälp av endast funktioner i allmänhet kan du gå [skapar en nätverkssäkerhetsgrupp](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Azure CLI

Azure CLI-kommandona är desamma, om du kör kommandon från Windows, Linux eller macOS. Det finns dock scripting skillnader mellan gränssnitt för operativsystemet. Skript i följande steg körs i ett Bash-gränssnitt. 

1. [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Se till att du använder version 2.0.18 eller senare av Azure CLI genom att ange den `az --version` kommando. Om du inte installera den senaste versionen.
3. Logga in på Azure med den `az login` kommando.
4. Registrera dig för förhandsversionen genom att ange följande kommandon:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Bekräfta att du är registrerad för förhandsversionen av genom att ange följande kommando:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > Registrering kan ta upp till en timme att slutföra. Fortsätt inte med stegen tills *registrerade* visas för **tillstånd** i utdatan från det föregående kommandot. Om du fortsätter innan registreras du stegen att misslyckas.

6. Kör följande bash-skript för att skapa en resursgrupp:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Skapa tre program säkerhetsgrupper, ett för varje servertyp:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Skapa en säkerhetsgrupp för nätverk:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Skapa säkerhetsregler inom NSG, ställa in programmet säkerhetsgrupper som mål:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Skapa ett virtuellt nätverk: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Koppla nätverkssäkerhetsgruppen för undernätet i det virtuella nätverket:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Skapa tre nätverksgränssnitt, ett för varje servertyp: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Endast motsvarande säkerhetsregeln du skapade i steg 9 tillämpas på nätverksgränssnitt, baserat på säkerhetsgruppen programmet nätverksgränssnittet är medlem i. Om du exempelvis bara den *AppRule* regeln ska gälla för *myNic2*eftersom nätverksgränssnittet är medlem i den *AppServers* programmet säkerhetsgrupp och regeln Anger den *AppServers* programmet säkerhetsgrupp som mål. Den *WebRule* och *DatabaseRule* regler tillämpas inte på *myNic2*eftersom nätverksgränssnittet inte är medlem i den *webbservrar*och *DatabaseServers* säkerhetsgrupper för programmet. Både den *WebRule* och *AppRule* reglerna är giltiga för *myNic1* dock eftersom den *myNic1* nätverksgränssnittet är medlem i både den *webbservrar* och *AppServers* säkerhetsgrupper för programmet och reglerna som anger den *webbservrar* och *AppServers* programmet säkerhetsgrupper som sina mål. 

13. Skapa en virtuell dator för varje servertyp som kopplar nätverksgränssnittet motsvarande till varje virtuell dator. Det här exemplet skapar Windows-datorer, men du kan ändra *win2016datacenter* till *UbuntuLTS* att skapa virtuella Linux-datorer i stället.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Valfria**: ta bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i [bort resurser](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Installera och konfigurera [PowerShell](/powershell/azure/install-azurerm-ps).
2. Se till att du har version 4.4.0 eller högre för modulen AzureRm installerad. Du kan kontrollera dina installerade versionen genom att ange den `Get-Module -ListAvailable AzureRM` kommando. Om du behöver installera eller uppgradera, installera den senaste versionen av AzureRM-modul från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureRM).
3. I PowerShell-sessionen, logga in på Azure med din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) med hjälp av den `login-azurermaccount` kommando.
4. Registrera dig för förhandsversionen genom att ange följande kommandon:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Bekräfta att du är registrerad för förhandsversionen av genom att ange följande kommando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > Registrering kan ta upp till en timme att slutföra. Fortsätt inte med stegen tills *registrerade* visas för **RegistrationState** i utdatan från det föregående kommandot. Om du fortsätter innan registreras du stegen att misslyckas.
        
6. Skapa en resursgrupp:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Skapa tre program säkerhetsgrupper, ett för varje servertyp:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Skapa säkerhetsregler för varje server.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Skapa en säkerhetsgrupp för nätverk:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Skapa en konfiguration av undernät och koppla till den säkerhetsgrupp för nätverk:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Skapa ett virtuellt nätverk: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Skapa tre nätverksgränssnitt, ett för varje server. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Endast motsvarande säkerhetsregeln du skapade i steg 8 tillämpas på nätverksgränssnitt, baserat på säkerhetsgruppen programmet nätverksgränssnittet är medlem i. Om du exempelvis bara den *AppRule* regeln ska gälla för *myNic2*eftersom nätverksgränssnittet är medlem i den *AppServers* programmet säkerhetsgrupp och regeln Anger den *AppServers* programmet säkerhetsgrupp som mål. Den *WebRule* och *DatabaseRule* regler tillämpas inte på *myNic2*eftersom nätverksgränssnittet inte är medlem i den *webbservrar*och *DatabaseServers* säkerhetsgrupper för programmet. Både den *WebRule* och *AppRule* reglerna är giltiga för *myNic1* dock eftersom den *myNic1* nätverksgränssnittet är medlem i både den *webbservrar* och *AppServers* säkerhetsgrupper för programmet och reglerna som anger den *webbservrar* och *AppServers* programmet säkerhetsgrupper som sina mål. 

13. Skapa en virtuell dator för varje servertyp som kopplar nätverksgränssnittet motsvarande till varje virtuell dator. Det här exemplet skapar Windows-datorer, men innan du kör skriptet kan du ändra *-Windows* till *- Linux*, *Microsoft Windows Server* till *Kanoniska*, *WindowsServer* till *UbuntuServer* och *2016 Datacenter* till *14.04.2-LTS*att skapa virtuella Linux-datorer i stället.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Valfria**: ta bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i [bort resurser](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Ta bort ett nätverkskort från en ASG
När du tar bort ett nätverksgränssnitt från en säkerhetsgrupp för programmet tillämpas ingen av de regler som anger säkerhetsgruppen programmet till nätverksgränssnittet som du tar bort.

### <a name="azure-cli"></a>Azure CLI

Ta bort *myNic3* från alla säkerhetsgrupper för programmet, anger du följande kommando:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Ta bort *myNic3* från alla säkerhetsgrupper för programmet, ange följande kommandon:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Ta bort resurser

När du är klar med den här kursen kan du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen.

### <a name="delete-portal"></a>Azure-portalen

1. Skriv i sökrutan portal **myResourceGroup**. I sökresultaten klickar du på **myResourceGroup**.
2. På den **myResourceGroup** bladet, klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroup**, och klicka sedan på **ta bort**.

### <a name="delete-cli"></a>Azure CLI

Ange följande kommando i en CLI-session:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Ange följande kommando i en PowerShell-session:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

