---
title: "Skapa nätverkssäkerhetsgrupper - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar och distribuerar nätverkssäkerhetsgrupper med hjälp av Azure CLI."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9ea82c09-f4a6-4268-88bc-fc439db40c48
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4827fabf1d8cde366dda8b3a782a2fefe01db8d5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="create-network-security-groups-using-the-azure-cli"></a>Skapa nätverk med hjälp av Azure CLI-säkerhetsgrupper

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Följande exempelkommandon Azure CLI förväntar sig en befintlig enkel miljö baserat på scenariot ovan. 

## <a name="create-the-nsg-for-the-frontend-subnet"></a>Skapa NSG för den `FrontEnd` undernät

Så här skapar du en NSG som heter *NSG-klientdel* baserat på scenariot ovan, Följ stegen nedan.

1. Om du inte har gjort det ännu, installerar och konfigurerar senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). 

2. Skapa en NSG med hjälp av den [az nätverket nsg skapa](/cli/azure/network/nsg#az_network_nsg_create) kommando. 

    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-FrontEnd \
    --location centralus 
    ```

    Parametrar:
   
   * `--resource-group`: Namnet på resursgruppen där NSG: N har skapats. I vårt exempel, *TestRG*.
   * `--location`: Azure-region där den nya NSG skapas. I vårt scenario, *westus*.
   * `--name`: Namn på ny NSG: N. I vårt scenario, *NSG-klientdel*.

    Förväntad utdata är ganska lite information, inklusive en lista över alla standardregler. I följande exempel visas de standardregler som använder en JMESPATH frågefilter med den `table` utdataformat:

    ```azurecli
    az network nsg show \
    -g testrg \
    -n nsg-frontend \
    --query 'defaultSecurityRules[].{Access:access,Desc:description,DestPortRange:destinationPortRange,Direction:direction,Priority:priority}' \
    -o table
    ```
   
   Resultat:

        Access    Desc                                                    DestPortRange    Direction      Priority
        
        Allow     Allow inbound traffic from all VMs in VNET              *                Inbound           65000
        Allow     Allow inbound traffic from azure load balancer          *                Inbound           65001
        Deny      Deny all inbound traffic                                *                Inbound           65500
        Allow     Allow outbound traffic from all VMs to all VMs in VNET  *                Outbound          65000
        Allow     Allow outbound traffic from all VMs to Internet         *                Outbound          65001
        Deny      Deny all outbound traffic                               *                Outbound          65500



3. Skapa en regel som tillåter åtkomst till port 3389 (RDP) från Internet med de [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) kommando.

    > [!NOTE]
    > Beroende på gränssnittet som du använder kan du behöva ändra den `*` tecken i argumenten efter för att expandera argument före körning.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name rdp-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 3389
    ```
   
    Förväntad utdata:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "3389",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
        "name": "rdp-rule",
        "priority": 100,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

    Parametrar:

    * `--resource-group testrg`: Resursgruppen som ska användas. Observera att den är skiftlägeskänsliga.
    * `--nsg-name NSG-FrontEnd`: Namnet på NSG: N som regeln skapades.
    * `--name rdp-rule`: Namnet på den nya regeln.
    * `--access Allow`: Åtkomstnivå för regeln (Tillåt eller neka).
    * `--protocol Tcp`: Protokoll (Tcp, Udp eller *).
    * `--direction Inbound`: Riktning anslutningen (inkommande eller utgående).
    * `--priority 100`: Prioritet för regeln.
    * `--source-address-prefix Internet`: Källadress-prefix i CIDR- eller använda standardtaggar.
    * `--source-port-range "*"`: Datakällan port eller ett intervall. Porten som öppnade anslutningen.
    * `--destination-address-prefix "*"`: Måladress-prefix i CIDR- eller använda standardtaggar.
    * `--destination-port-range 3389`: Mål port eller ett intervall. Porten som tar emot anslutningsbegäran.



4. Skapa en regel som tillåter åtkomst till port 80 (HTTP) från Internet **az nätverket nsg regeln skapa** kommando.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name web-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 200 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 80
    ```
   
    Förväntade putput:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "80",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
        "name": "web-rule",
        "priority": 200,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

5. Binda NSG till den **klientdel** undernät med den [az network vnet undernät uppdatering](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) kommando.
        
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name FrontEnd \
    --resource-group testrg \
    --network-security-group NSG-FrontEnd
    ```
   
    Förväntad utdata:
   
    ```json
    {
        "addressPrefix": "192.168.1.0/24",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
        "ipConfigurations": [
            {
            "etag": null,
            "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
            "name": null,
            "privateIpAddress": null,
            "privateIpAllocationMethod": null,
            "provisioningState": null,
            "publicIpAddress": null,
            "resourceGroup": "TestRG",
            "subnet": null
            }
        ],
        "name": "FrontEnd",
        "networkSecurityGroup": {
            "defaultSecurityRules": null,
            "etag": null,
            "id": "/subscriptions/<guid>f/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
            "location": null,
            "name": null,
            "networkInterfaces": null,
            "provisioningState": null,
            "resourceGroup": "testrg",
            "resourceGuid": null,
            "securityRules": null,
            "subnets": null,
            "tags": null,
            "type": null
        },
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "resourceNavigationLinks": null,
        "routeTable": null
    }
    ```

## <a name="create-the-nsg-for-the-backend-subnet"></a>Skapa NSG för den `BackEnd` undernät
Så här skapar du en NSG som heter *NSG BackEnd* baserat på scenariot ovan, Följ stegen nedan.

1. Skapa den `NSG-BackEnd` NSG med **az nätverket nsg skapa**.
   
    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-BackEnd \
    --location centralus
    ```
   
    Som i steg 2, föregående, är förväntade utdata väldigt stora, inklusive standardregler.
   
2. Skapa en regel som tillåter åtkomst till port 1433 (SQL) från den `FrontEnd` undernät med den **az nätverket nsg regeln skapa** kommando.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name sql-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix 192.168.1.0/24 \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 1433
    ```
   
    Förväntad utdata:

    ```json  
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1433",
    "direction": "Inbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule",
    "name": "sql-rule",
    "priority": 100,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "192.168.1.0/24",
    "sourcePortRange": "*"
    }
    ```

3. Skapa en regel som nekar åtkomst till Internet med de **az nätverket nsg regeln skapa** kommando.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name web-rule \
    --access Deny \
    --protocol Tcp  \
    --direction Outbound  \
    --priority 200 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range "*"
    ```
   
    Förväntade putput:
   
    ```json
    {
    "access": "Deny",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "*",
    "direction": "Outbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
    "name": "web-rule",
    "priority": 200,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

4. Binda NSG till den `BackEnd` undernätet med hjälp av den **az network vnet subnet set** kommando.
   
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name BackEnd \
    --resource-group testrg \
    --network-security-group NSG-BackEnd
    ```
   
    Förväntad utdata:
   
    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": {
        "defaultSecurityRules": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "location": null,
        "name": null,
        "networkInterfaces": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "resourceGuid": null,
        "securityRules": null,
        "subnets": null,
        "tags": null,
        "type": null
    },
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```
