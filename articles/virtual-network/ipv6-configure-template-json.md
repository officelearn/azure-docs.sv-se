---
title: Distribuera ett IPv6-program med dubbla stackar med grundläggande belastningsutjämning i azure virtuellt nätverk - Resource Manger-mall
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar ett IPv6-program med dubbla stackar i Azure virtuellt nätverk med hjälp av Azure Resource Manager VM-mallar.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 7c2f66d92597801aa7c6b0b32f86f594d3effb05
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420606"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Distribuera ett IPv6-program med dubbla stackar med grundläggande belastningsutjämning i Azure – mall

Den här artikeln innehåller en lista över IPv6-konfigurationsuppgifter med den del av Azure Resource Manager VM-mallen som gäller för. Använd mallen som beskrivs i den här artikeln för att distribuera ett IPv4 + IPv6-program med enkel belastningsutjämning som innehåller ett virtuellt nätverk med dubbla staplar med IPv4- och IPv6-undernät, en grundläggande belastningsutjämnare med dubbla frontend-konfigurationer (IPv4 + IPv6), virtuella datorer med nätverkskort med dubbla IP-datorer som har en dubbel IP-konfiguration, nätverkssäkerhetsgrupp och offentliga IP-adresser.

Information om hur du distribuerar ett IPV4 + IPv6-program med standardbelastningsutjämning finns i [Distribuera ett IPv6-program med dubbla staplar med standardbelastningsutjämning - mall](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Nödvändiga konfigurationer

Sök efter mallavsnitten i mallen för att se var de ska inträffa.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6-adressSpace för det virtuella nätverket

Mallavsnitt för att lägga till:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-undernät i IPv6-adressen för virtuellt nätverk

Mallavsnitt för att lägga till:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>IPv6-konfiguration för nätverkskortet

Mallavsnitt för att lägga till:
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

### <a name="ipv6-network-security-group-nsg-rules"></a>NSG-regler (IPv6 network security group)

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

Om du använder en virtuell nätverksinstallation lägger du till IPv6-vägar i vägtabellen. Annars är den här konfigurationen valfri.

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6 Internet-åtkomst för det virtuella nätverket

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Offentliga IP-adresser för IPv6

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

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-frontend för belastningsutjämnare

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6-slutadresspool för belastningsutjämnare

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6-belastningsutjämnare regler för att associera inkommande och utgående portar

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

## <a name="sample-vm-template-json"></a>Exempel på VM-mall JSON
Om du vill distribuera ett IPv6-program med dubbla stackar med Basic Load Balancer i azure virtuellt nätverk med Azure Resource Manager-mallen visar du exempelmall [här](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Nästa steg

Du hittar information om priser för [offentliga IP-adresser,](https://azure.microsoft.com/pricing/details/ip-addresses/) [nätverksbandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)eller [belastningsutjämnare](https://azure.microsoft.com/pricing/details/load-balancer/).
