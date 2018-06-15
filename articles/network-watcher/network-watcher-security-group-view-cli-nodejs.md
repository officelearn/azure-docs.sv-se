---
title: Analysera nätverkssäkerhet med Azure Network Watcher säkerhet gruppvyn - Azure CLI 1.0 | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure CLI 1.0 för att analysera en säkerhet för virtuella datorer med Gruppvy för säkerhet.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
ms.locfileid: "23862833"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>Analysera dina virtuella säkerhet med säkerhet gruppvyn använder Azure CLI 1.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Säkerhet gruppvyn returnerar konfigurerade och effektivt Nätverkssäkerhetsregler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera Nätverkssäkerhetsgrupper och regler som är konfigurerade på en virtuell dator så trafik som tillåts eller nekas på rätt sätt. I den här artikeln hur vi du kan hämta de konfigurerade och effektivt säkerhetsreglerna till en virtuell dator med hjälp av Azure CLI

Den här artikeln använder plattformsoberoende Azure CLI version 1.0, som är tillgänglig för Windows, Mac och Linux. Nätverksbevakaren använder för närvarande Azure CLI 1.0 för CLI-stöd.

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar konfigurerade och effektivt säkerhetsregler för en viss virtuell dator.

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra den `vm list` cmdlet. Följande kommando visar virtuella machinese i en resursgrupp:

```azurecli
azure vm list -g resourceGroupName
```

När du vet att den virtuella datorn kan du använda den `vm show` för att hämta dess resursens Id:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Hämta gruppvy för säkerhet

Nästa steg är att hämta säkerhet grupp visa resultatet. Att lägga till den ”--json” flaggan formaterar resultatet i json.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Visa resultaten

I följande exempel är ett kortare resultaten-svar. Resultaten visar alla säkerhet effektiva och tillämpa regler på den virtuella datorn som är uppdelad i grupper med **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, och **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Besök [granskning Nätverkssäkerhetsgrupp grupper (NSG) med Nätverksbevakaren](network-watcher-nsg-auditing-powershell.md) information om hur du automatiserar validering av Nätverkssäkerhetsgrupper.

Mer information om säkerhetsregler som tillämpas på nätverksresurserna genom att besöka [Säkerhetsöversikt grupp vy](network-watcher-security-group-view-overview.md)
