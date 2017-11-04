---
title: "Visa Azure Nätverksbevakaren topologi - Azure CLI | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder Azure CLI för att fråga nätverkets topologi."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 654c23e567d81bff1cb0c3091ba3d8f96f0a3eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="view-network-watcher-topology-with-azure-cli"></a>Visa Nätverksbevakaren topologi med Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

Funktionen topologi i Nätverksbevakaren ger en bild av nätverksresurser i en prenumeration. I portalen visas den här visualiseringen för dig automatiskt. Informationen bakom vyn topologi i portalen kan hämtas via PowerShell.
Den här funktionen gör topologiinformationen mer flexibla data kan användas av andra verktyg för att bygga ut visualiseringen.

Den här artikeln använder plattformsoberoende Azure CLI version 1.0, som är tillgänglig för Windows, Mac och Linux. Nätverksbevakaren använder för närvarande Azure CLI 1.0 för CLI-stöd.

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

I det här scenariot kan du använda den `network watcher topology` för att hämta topologiinformationen. Det finns också en artikel om hur du [hämta nätverkets topologi med REST API](network-watcher-topology-rest.md).

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar topologi-svar för en viss resursgrupp.

## <a name="retrieve-topology"></a>Hämta topologi

Den `network watcher topology` cmdlet hämtar topologin för en viss resursgrupp. Lägg till argumentet ”--json” att visa oput i json-format

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Resultat

Resultaten har egenskapen name ”resurser”, som innehåller text för json-svar för den `network watcher topology` cmdlet.  Svaret innehåller resurser för Nätverkssäkerhetsgruppen och deras associationer (det vill säga innehåller, associerade).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om säkerhetsregler som tillämpas på nätverksresurserna genom att besöka [Säkerhetsöversikt grupp vy](network-watcher-security-group-view-overview.md)
