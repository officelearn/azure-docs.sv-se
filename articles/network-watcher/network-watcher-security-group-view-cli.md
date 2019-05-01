---
title: Analysera nätverkssäkerhet med Azure Network Watcher Säkerhetsgruppvy – Azure CLI | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure CLI för att analysera en säkerhet för virtuella datorer med Säkerhetsgruppvy.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 68222a90eb60ab4f84a34b5e46833128ea081ec1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724433"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analysera din säkerhet för virtuella datorer med Säkerhetsgruppvy med Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

Säkerhetsgruppvy returnerar konfigurerade och gällande säkerhetsregler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera Nätverkssäkerhetsgrupper och regler som är konfigurerade på en virtuell dator för att se till att trafik som ska tillåtas eller nekas. I den här artikeln har visar vi hur du hämtar de konfigurerade och gällande säkerhetsreglerna som en virtuell dator med Azure CLI

Om du vill utföra stegen i den här artikeln, måste du [installera Azures kommandoradsgränssnitt för Mac, Linux och Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Network Watcher](network-watcher-create.md) att skapa en Network Watcher.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar konfigurerade och gällande säkerhetsregler för en viss virtuell dator.

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra den `vm list` cmdlet. Följande kommando visar de virtuella datorerna i en resursgrupp:

```azurecli
az vm list -resource-group resourceGroupName
```

När du vet att den virtuella datorn kan du använda den `vm show` cmdlet för att hämta dess resurs-Id:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Hämta säkerhetsgruppvy

Nästa steg är att hämta security grupp visa resultatet.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Visa resultaten

I följande exempel är svaret förkortade av resultatet som returneras. Resultaten visar alla effektiva och tillämpade säkerhetsregler på den virtuella datorn som är uppdelade i grupper med **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, och  **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Besök [granskning Nätverkssäkerhetsgrupper (NSG) med Network Watcher](network-watcher-nsg-auditing-powershell.md) att lära dig hur du automatiserar verifieringen av Nätverkssäkerhetsgrupper.

Mer information om säkerhetsregler som tillämpas på nätverksresurserna genom att besöka [Säkerhetsöversikt grupp vy](network-watcher-security-group-view-overview.md)
