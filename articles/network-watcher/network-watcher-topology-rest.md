---
title: "Visa Azure Nätverksbevakaren topologi - REST API | Microsoft Docs"
description: "Den här artikeln beskrivs hur du använder REST API för att fråga nätverkets topologi."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Visa Nätverksbevakaren topologi med REST API

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

I det här scenariot kan du hämta topologiinformationen. ARMclient används för att anropa REST-API med hjälp av PowerShell. ARMClient hittas på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar topologi-svar för en viss resursgrupp.

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

Logga in på armclient med dina autentiseringsuppgifter för Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Hämta topologi

I följande exempel begär topologin från REST API.  Exemplet parametriserade för att möjliggöra flexibilitet för att skapa ett exempel.  Ersätter alla värden med \< \> omgivande.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

Följande svar är ett exempel på ett kortare svar som returneras när hämta topologin för en resursgrupp:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualisera dina NSG flödet loggar med Power BI genom att besöka [visualisera NSG flödar loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

