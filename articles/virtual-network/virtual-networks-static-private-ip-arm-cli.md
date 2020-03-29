---
title: Konfigurera privata IP-adresser för virtuella datorer - Azure CLI
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer med hjälp av Azure command-line interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: f4643aae0b28861f4ddb99d8dace749e62f930b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199486"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurera privata IP-adresser för en virtuell dator med Hjälp av Azure CLI


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Följande exempel på Azure CLI-kommandon förväntar sig en befintlig enkel miljö. Om du vill köra kommandona när de visas i det här dokumentet skapar du först testmiljön som beskrivs i [skapa ett vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Ange en statisk privat IP-adress när du skapar en virtuell dator

Så här skapar du en virtuell dator med namnet *DNS01* i *FrontEnd-undernätet* i ett virtuellt nätverk med namnet *TestVNet* med en statisk privat IP på *192.168.1.101:*

1. Om du inte har gjort det ännu installerar och konfigurerar du den senaste [Azure CLI](/cli/azure/install-azure-cli) och loggar in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

2. Skapa en offentlig IP för den virtuella datorn med kommandot [az network public-ip create.](/cli/azure/network/public-ip) Listan som visas efter utdatan beskriver de parametrar som används.

    > [!NOTE]
    > Du kanske vill eller behöver använda olika värden för dina argument i det här och efterföljande steget, beroende på din miljö.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Förväntad utdata:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: Namn på den resursgrupp där den offentliga IP-adressen ska skapas.
   * `--name`: Namnet på den offentliga IP.
   * `--location`: Azure-region där du kan skapa den offentliga IP-adressen.

3. Kör kommandot [az network nic create](/cli/azure/network/nic) för att skapa ett nätverkskort med en statisk privat IP. Listan som visas efter utdatan beskriver de parametrar som används. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Förväntad utdata:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametrar:

    * `--private-ip-address`: Statisk privat IP-adress för nätverkskortet.
    * `--vnet-name`: Namn på det virtuella nätverk som nätverkskortet ska skapas på.
    * `--subnet`: Namnet på det undernät där nätverkskortet ska skapas.

4. Kör kommandot [azure vm create](/cli/azure/vm/nic) för att skapa den virtuella datorn med den offentliga IP- och nätverkskortet som skapats tidigare. Listan som visas efter utdatan beskriver de parametrar som används.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Förväntad utdata:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Andra parametrar än den grundläggande [az vm skapa](/cli/azure/vm) parametrar.

   * `--nics`: Namn på det nätverkskort som den virtuella datorn är kopplad till.
   
Vi rekommenderar att du inte statiskt tilldelar den privata IP som tilldelats den virtuella Azure-datorn i operativsystemet för en virtuell dator, om det inte behövs, till exempel när [du tilldelar flera IP-adresser till en Windows VM](virtual-network-multiple-ip-addresses-cli.md). Om du manuellt anger den privata IP-adressen i operativsystemet, se till att det är samma adress som den privata IP-adressen som tilldelats [Azure-nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings)eller så kan du förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressinställningar.](virtual-network-network-interface-addresses.md#private)

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Hämta statisk privat IP-adressinformation för en virtuell dator

Kör följande Azure CLI-kommando för att observera värdena för *private IP alloc-metod* och *Private IP-adress:*

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Förväntad utdata:

```json
"192.168.1.101"
```

Om du vill visa den specifika IP-informationen för nätverkskortet för den virtuella datorn frågar du nätverkskortet specifikt:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Resultatet ser ut ungefär så här:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator

Du kan inte ta bort en statisk privat IP-adress från ett nätverkskort i Azure CLI för Azure Resource Manager-distributioner. Du måste:
- Skapa ett nytt nätverkskort som använder en dynamisk IP
- Ange nätverkskortet på den virtuella datorn gör det nyskapade nätverkskortet. 

Så här ändrar du nätverkskortet för den virtuella datorn som används i föregående kommandon:

1. Kör kommandot **Azure Network nic create** för att skapa ett nytt nätverkskort med dynamisk IP-allokering med en ny IP-adress. Eftersom ingen IP-adress har angetts är allokeringsmetoden **Dynamisk**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Förväntad utdata:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Kör kommandot **azure vm set** för att ändra nätverkskortet som används av den virtuella datorn.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Förväntad utdata:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Om den virtuella datorn är tillräckligt stor för att ha mer än ett nätverkskort kör du kommandot **azure network nic delete** för att ta bort det gamla nätverkskortet.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar [IP-adressinställningar](virtual-network-network-interface-addresses.md).
