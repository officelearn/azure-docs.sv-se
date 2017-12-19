---
title: "Visa Azure Nätverksbevakaren topologi - PowerShell | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder PowerShell för att fråga nätverkets topologi."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>Visa Nätverksbevakaren topologi med PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Funktionen topologi i Nätverksbevakaren ger en bild av nätverksresurser i en prenumeration. I portalen visas den här visualiseringen för dig automatiskt. Informationen bakom vyn topologi i portalen kan hämtas via PowerShell.
Den här funktionen gör topologiinformationen mer flexibla data kan användas av andra verktyg för att bygga ut visualiseringen.

Sambandet modelleras under två relationer.

- **Inneslutning** -exempel: innehåller ett undernät för virtuellt nätverk innehåller ett nätverkskort
- **Associerade** -exempel: NIC som är kopplad till en virtuell dator

I följande lista finns egenskaper som returneras när du frågar topologi REST API.

* **namnet** -namnet på resursen
* **ID** -uri för resursen.
* **plats** -plats där resurserna finns.
* **kopplingarna** -en lista över kopplingar till det refererade objektet.
    * **namnet** -namnet på den refererade resursen.
    * **resourceId** -resourceId är URI: n för den resurs som refereras i kopplingen.
    * **associationType** -relationen mellan det underordnade objektet och överordnat refererar till det här värdet. Giltiga värden är **innehåller** eller **associerade**.

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot kan du använda den `Get-AzureRmNetworkWatcherTopology` för att hämta topologiinformationen. Det finns också en artikel om hur du [hämta nätverkets topologi med REST API](network-watcher-topology-rest.md).

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar topologi-svar för en viss resursgrupp.

## <a name="retrieve-network-watcher"></a>Hämta Nätverksbevakaren

Det första steget är att hämta Nätverksbevakaren-instans. Den `$networkWatcher` variabel har skickats till den `Get-AzureRmNetworkWatcherTopology` cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Hämta topologi

Den `Get-AzureRmNetworkWatcherTopology` cmdlet hämtar topologin för en viss resursgrupp.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Resultat

Resultaten har egenskapen name ”resurser”, som innehåller text för json-svar för den `Get-AzureRmNetworkWatcherTopology` cmdlet.  Svaret innehåller resurser för Nätverkssäkerhetsgruppen och deras associationer (det vill säga innehåller, associerade).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualisera dina NSG flödet loggar med Power BI genom att besöka [visualisera NSG flödar loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


