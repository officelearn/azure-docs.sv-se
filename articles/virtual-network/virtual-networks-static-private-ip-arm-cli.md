---
title: Konfigurera privata IP-adresser för virtuella datorer – Azure CLI | Microsoft Docs
description: Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer med hjälp av Azure-kommandoradsgränssnittet (CLI).
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4f6a40fde23ee70391c5057762f17ce1eb44123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423549"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurera privat IP-adresser för en virtuell dator med hjälp av Azure CLI

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också [hantera statisk privat IP-adress i den klassiska distributionsmodellen](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Följande exempelkommandon Azure CLI förväntar sig en befintlig enkel miljö. Om du vill köra kommandon som de visas i det här dokumentet, först skapa testmiljön som beskrivs i [skapa ett vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Ange en statisk privat IP-adress när du skapar en virtuell dator

Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernätet i ett VNet med namnet *TestVNet* med en statisk privat IP-adress för *192.168.1.101*, fullständig följande steg:

1. Om du inte har gjort det ännu, installerar och konfigurerar senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). 

2. Skapa en offentlig IP-adress för den virtuella datorn med den [az nätverket offentliga IP-skapa](/cli/azure/network/public-ip#az_network_public_ip_create) kommando. Listan som visas efter utdatan beskriver de parametrar som används.

    > [!NOTE]
    > Om du vill eller måste du använda olika värden för att argumenten i den här och efterföljande steg beroende på din miljö.
   
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

   * `--resource-group`: Namnet på resursgruppen där du vill skapa den offentliga IP-Adressen.
   * `--name`: Namnet på den offentliga IP-Adressen.
   * `--location`: Azure-region där du vill skapa den offentliga IP-Adressen.

3. Kör den [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create) kommando för att skapa ett nätverkskort med en statisk privat IP. Listan som visas efter utdatan beskriver de parametrar som används. 
   
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
    * `--vnet-name`: Namnet på VNet där du vill skapa nätverkskortet.
    * `--subnet`: Namnet på undernätet att skapa det nätverkskortet.

4. Kör den [azure vm skapa](/cli/azure/vm/nic#az_vm_nic_create) kommando för att skapa den virtuella datorn med hjälp av offentliga IP-adresser och NIC som skapats tidigare. Listan som visas efter utdatan beskriver de parametrar som används.
   
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
   
   Andra parametrar än grundläggande [az vm skapa](/cli/azure/vm#az_vm_create) parametrar.

   * `--nics`: Namnet på nätverkskortet som den virtuella datorn är ansluten.
   
Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt, t.ex när [tilldela flera IP-adresser till en Windows-VM](virtual-network-multiple-ip-addresses-cli.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats i Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Lär dig mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar.

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Hämta statisk privat IP-adressinformation för en virtuell dator

Kör följande Azure CLI-kommando för att se värdena för *privat IP allokeringsenhets-metoden* och *privata IP-adressen*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Förväntad utdata:

```json
"192.168.1.101"
```

Fråga NIC specifikt för att visa specifika IP-information för nätverkskortet för den virtuella datorn:

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

Du kan inte ta bort en statisk privat IP-adress från ett nätverkskort i Azure CLI för Azure Resource Manager distributioner. Du måste:
- Skapa ett nytt nätverkskort som använder en dynamisk IP-adress
- Ange nätverkskortet på VM gör det nyligen skapade nätverkskortet. 

Om du vill ändra nätverkskortet för den virtuella datorn som används i de föregående kommandona, gör du följande:

1. Kör den **azure-nätverk nic skapa** kommando för att skapa ett nytt nätverkskort med hjälp av dynamisk IP-adressallokering med en ny IP-adress. Eftersom ingen IP-adress har angetts allokeringsmetoden är **dynamiska**.

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

2. Kör den **azure vm set** kommando för att ändra det nätverkskort som används av den virtuella datorn.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
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
    > Om den virtuella datorn är tillräckligt stor för att ha flera nätverkskort, kör den **ta bort azure-nätverk nic** kommando för att ta bort gamla nätverkskortet.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du hanterar [IP-adressinställningarna](virtual-network-network-interface-addresses.md).