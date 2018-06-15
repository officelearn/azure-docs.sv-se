---
title: Hantera nätverkssäkerhetsgrupper - Azure PowerShell | Microsoft Docs
description: Lär dig hur du hanterar nätverkssäkerhetsgrupper med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca7f4926ca4edf9d20612aca74f6ae5f0ed847b3
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2018
ms.locfileid: "30960383"
---
# <a name="manage-network-security-groups-using-powershell"></a>Hantera säkerhetsgrupper i nätverket med hjälp av PowerShell

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln täcker distributionsmodell hanteraren för filserverresurser, som Microsoft rekommenderar för de flesta nya distributioner i stället för den klassiska distributionsmodellen.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Hämta Information
Du kan visa dina befintliga NSG: er, hämta regler för en befintlig NSG och ta reda på vilka resurser en NSG är kopplad till.

### <a name="view-existing-nsgs"></a>Visa befintliga NSG: er
Om du vill visa alla befintliga NSG: er i en prenumeration kör den `Get-AzureRmNetworkSecurityGroup` cmdlet.

Förväntat resultat:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Om du vill visa listan över NSG: er i en viss resursgrupp, kör den `Get-AzureRmNetworkSecurityGroup` cmdlet.

Förväntad utdata:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>Visa en lista med alla regler för en NSG
Visa regler för en NSG som heter **NSG-klientdel**, anger du följande kommando:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

Förväntad utdata:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> Du kan också använda `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` att lista standardregler från den **NSG-klientdel** NSG.
> 

### <a name="view-nsgs-associations"></a>Visa NSG: er associationer
Visa vilka resurser de **NSG-klientdel** NSG är associerat med, kör du följande kommando:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

Leta efter den **NetworkInterfaces** och **undernät** egenskaper som visas nedan:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

I det förra exemplet är NSG: N inte kopplad till någon nätverksgränssnitt (NIC) den är kopplad till ett undernät med namnet **klientdel**.

## <a name="manage-rules"></a>Hantera regler
Du kan lägga till regler i en befintlig NSG, redigera befintliga regler och ta bort regler.

### <a name="add-a-rule"></a>Lägg till en regel
Att lägga till en regel som tillåter **inkommande** trafik till port **443** från en dator till den **NSG-klientdel** NSG, gör du följande:

1. Kör följande kommando för att hämta befintliga NSG: N och lagra den i en variabel:

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Kör följande kommando för att lägga till en regel till NSG: N:

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Om du vill spara ändringarna i NSG: N, kör du följande kommando:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    Förväntad utdata visar endast säkerhetsregler:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Ändra en regel
Så här ändrar du regeln som skapade ovan för att tillåta inkommande trafik från den **Internet** , Följ stegen nedan.

1. Kör följande kommando för att hämta befintliga NSG: N och lagra den i en variabel:

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Kör följande kommando med de nya inställningarna för regeln:

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Om du vill spara ändringarna i NSG: N, kör du följande kommando:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Förväntad utdata visar endast säkerhetsregler:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Ta bort en regel
1. Kör följande kommando för att hämta befintliga NSG: N och lagra den i en variabel:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Kör följande kommando för att ta bort regeln från NSG: N:

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. Spara ändringarna i NSG: N, genom att köra följande kommando:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Förväntad utdata visar endast säkerhetsregler, meddelande i **https-regel** inte längre visas:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Hantera kopplingar
Du kan koppla en NSG till undernät och nätverkskort. Du kan också koppla bort en NSG från alla resurser som den är kopplad till.

### <a name="associate-an-nsg-to-a-nic"></a>Koppla en NSG till ett nätverkskort
Associera den **NSG-klientdel** NSG till den **TestNICWeb1** NIC, gör du följande:

1. Kör följande kommando för att hämta befintliga NSG: N och lagra den i en variabel:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Kör följande kommando för att hämta befintlig NIC och lagra den i en variabel:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. Ange den **NetworkSecurityGroup** -egenskapen för den **NIC** variabeln till värdet för den **NSG** variabel, genom att ange följande kommando:

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. Om du vill spara ändringarna i nätverkskortet, kör du följande kommando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Förväntad utdata visar endast den **NetworkSecurityGroup** egenskapen:
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Koppla bort en NSG från ett nätverkskort
Koppla bort den **NSG-klientdel** NSG från den **TestNICWeb1** NIC, gör du följande:

1. Kör följande kommando för att hämta befintlig NIC och lagra den i en variabel:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. Ange den **NetworkSecurityGroup** -egenskapen för den **NIC** variabeln **$null** genom att köra följande kommando:

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. Om du vill spara ändringarna i nätverkskortet, kör du följande kommando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Förväntad utdata visar endast den **NetworkSecurityGroup** egenskapen:
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Koppla bort en NSG från ett undernät
Koppla bort den **NSG-klientdel** NSG från den **klientdel** undernät, gör du följande:

1. Kör följande kommando för att hämta befintliga VNet och lagra den i en variabel:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Kör följande kommando för att hämta den **klientdel** undernät och lagra den i en variabel:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Ange den **NetworkSecurityGroup** -egenskapen för den **undernät** variabeln **$null** genom att ange följande kommando:

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. Om du vill spara ändringarna i undernätet, kör du följande kommando:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Förväntad utdata visar egenskaperna för den **klientdel** undernät. Observera att det finns inte en egenskap för **NetworkSecurityGroup**:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Koppla en NSG till ett undernät
Associera den **NSG-klientdel** NSG till den **FronEnd** undernät igen, gör du följande:

1. Kör följande kommando för att hämta befintliga VNet och lagra den i en variabel:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Kör följande kommando för att hämta den **klientdel** undernät och lagra den i en variabel:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Kör följande kommando för att hämta befintliga NSG: N och lagra den i en variabel:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. Ange den **NetworkSecurityGroup** -egenskapen för den **undernät** variabeln **$null** genom att köra följande kommando:

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. Om du vill spara ändringarna i undernätet, kör du följande kommando:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Förväntad utdata visar endast den **NetworkSecurityGroup** -egenskapen för den **klientdel** undernät:
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Ta bort en NSG
Du kan bara ta bort en NSG om den inte är kopplad till en resurs. Följ stegen nedan om du vill ta bort en NSG.

1. Om du vill kontrollera resurser som är associerade med en NSG kör den `azure network nsg show` enligt [visa NSG: er kopplingarna](#View-NSGs-associations).
2. Om NSG: N är kopplad till varje nätverkskort, kör du den `azure network nic set` enligt [koppla bort en NSG från ett nätverkskort](#Dissociate-an-NSG-from-a-NIC) för varje nätverkskort. 
3. Om NSG: N är kopplat till alla undernät måste köra den `azure network vnet subnet set` enligt [koppla bort en NSG från ett undernät](#Dissociate-an-NSG-from-a-subnet) för varje undernät.
4. Ta bort NSG: N genom att köra följande kommando:

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > Den `-Force` parametern säkerställer du inte behöver bekräfta borttagningen.
   > 

## <a name="next-steps"></a>Nästa steg
* [Aktivera loggning](virtual-network-nsg-manage-log.md) för NSG: er.

