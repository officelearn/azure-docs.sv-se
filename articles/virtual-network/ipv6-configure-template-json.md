---
title: Distribuera ett IPv6-dual stack-program i Azure-nätverk – Resource Manager-mall (förhandsversion)
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur distribuerar ett IPv6-dual stack-program i Azure-nätverk med Azure Resource Manager-VM-mallar.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131035"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Distribuera ett IPv6-dual stack-program i Azure - mall (förhandsversion)

Den här artikeln innehåller en lista över åtgärder för IPv6-konfigurationen med del av Azure Resource Manager-VM-mallen som gäller för. Med mallen som beskrivs i den här artikeln kan du distribuera ett dual stack (IPv4 + IPv6)-program i Azure som innehåller ett dual stack virtuellt nätverk med IPv4 och IPv6-undernät, en belastningsutjämnare med dubbla (IPv4 + IPv6) frontend konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-adress konfiguration, nätverkssäkerhetsgruppen och offentliga IP-adresser. 

## <a name="required-configurations"></a>Konfigurationer som krävs

Sök efter mall-avsnitt i mallen för att se var de ska ske.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-addressSpace för det virtuella nätverket

Mallavsnittet att lägga till:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-undernät inom addressSpace för IPv6-nätverk

Mallavsnittet att lägga till:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-konfigurationen för nätverkskortet

Mallavsnittet att lägga till:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6-regler för nätverkssäkerhetsgrupper (NSG)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Villkorlig konfiguration

Om du använder en virtuell nätverksinstallation, kan du lägga till IPv6-vägar i routningstabellen. Annars kan är den här konfigurationen valfritt.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Valfri konfiguration

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6-Internet-åtkomst för det virtuella nätverket

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Offentliga IPv6-IP-adresser

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-klientdel för belastningsutjämnare

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6-backend-adresspoolen för belastningsutjämnare

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 load balancer-regler för att associera inkommande och utgående portar

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Exempel på VM-mallens JSON
Klicka på [här](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) du distribuerar ett IPv6-dual stack-program i Azure-nätverk med Azure Resource Manager-mall.

## <a name="next-steps"></a>Nästa steg

Du hittar information om priser för [offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/), [nätverkets bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/), eller [belastningsutjämnaren](https://azure.microsoft.com/pricing/details/load-balancer/).
