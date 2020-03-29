---
title: Analysera nätverkssäkerhet - Säkerhetsgruppvy - Azure PowerShell
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du använder PowerShell för att analysera en säkerhet för virtuella datorer med säkerhetsgruppvyn.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 4cba2c7e25b5f76b0638da1c551514f102247ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840799"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analysera säkerheten för den virtuella datorn med säkerhetsgruppvyn med PowerShell

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Säkerhetsgruppvyn returnerar konfigurerade och effektiva nätverkssäkerhetsregler som tillämpas på en virtuell dator. Den här funktionen är användbar för att granska och diagnostisera nätverkssäkerhetsgrupper och regler som är konfigurerade på en virtuell dator för att säkerställa att trafik tillåts eller nekas på rätt sätt. I den här artikeln visar vi hur du hämtar de konfigurerade och effektiva säkerhetsreglerna till en virtuell dator med PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här fallet `Get-AzNetworkWatcherSecurityGroupView` kör du cmdleten för att hämta säkerhetsregelinformationen.

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher.

## <a name="scenario"></a>Scenario

Scenariot som beskrivs i den här artikeln hämtar de konfigurerade och effektiva säkerhetsreglerna för en viss virtuell dator.

## <a name="retrieve-network-watcher"></a>Hämta nätverksbevakare

Det första steget är att hämta Network Watcher-instansen. Denna variabel skickas `Get-AzNetworkWatcherSecurityGroupView` till cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Skaffa en virtuell dator

En virtuell dator krävs `Get-AzNetworkWatcherSecurityGroupView` för att köra cmdlet mot. Följande exempel hämtar ett VM-objekt.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Hämta säkerhetsgruppsvyn

Nästa steg är att hämta resultatet för säkerhetsgruppvyn.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Visa resultaten

Följande exempel är ett förkortat svar på de resultat som returneras. Resultaten visar alla effektiva och tillämpade säkerhetsregler på den virtuella datorn uppdelad i grupper av **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**och **EffectiveSecurityRules**.

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

Besök [Auditing Network Security Groups (NSG) med Network Watcher](network-watcher-nsg-auditing-powershell.md) om du vill lära dig hur du automatiserar valideringen av nätverkssäkerhetsgrupper.


