---
title: Analysera nätverkssäkerhet med Azure Network Watcher Säkerhetsgruppvy – PowerShell | Microsoft Docs
description: Den här artikeln beskriver hur du använder PowerShell för att analysera en säkerhet för virtuella datorer med Säkerhetsgruppvy.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9cc06e97730ac846e8aa42c2cee77dfe17be99bb
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089654"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analysera din säkerhet för virtuella datorer med Säkerhetsgruppvy med hjälp av PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Säkerhetsgruppvy returnerar konfigurerade och gällande säkerhetsregler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera Nätverkssäkerhetsgrupper och regler som är konfigurerade på en virtuell dator för att se till att trafik som ska tillåtas eller nekas. I den här artikeln har visar vi hur du hämtar de konfigurerade och gällande säkerhetsreglerna till en virtuell dator med hjälp av PowerShell

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot kan du köra den `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet för att hämta säkerhetsinformation för regeln.

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Network Watcher](network-watcher-create.md) att skapa en Network Watcher.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar konfigurerade och gällande säkerhetsregler för en viss virtuell dator.

## <a name="retrieve-network-watcher"></a>Hämta Network Watcher

Det första steget är att hämta Network Watcher-instans. Den här variabeln skickas till den `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra den `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet mot. I följande exempel hämtas ett VM-objekt.

```powershell
$VM = Get-AzurermVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Hämta säkerhetsgruppvy

Nästa steg är att hämta security grupp visa resultatet.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Visa resultaten

I följande exempel är svaret förkortade av resultatet som returneras. Resultaten visar alla effektiva och tillämpade säkerhetsregler på den virtuella datorn som är uppdelade i grupper med **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, och  **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Nästa steg

Besök [granskning Nätverkssäkerhetsgrupper (NSG) med Network Watcher](network-watcher-nsg-auditing-powershell.md) att lära dig hur du automatiserar verifieringen av Nätverkssäkerhetsgrupper.


