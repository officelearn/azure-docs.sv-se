---
title: "Sök efter nästa hopp med Azure-nätverk Watcher nexthop - REST | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan hitta nästa hopptyp är och ip-adressen med nästa hopp med hjälp av Azure REST API"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Ta reda på vilka nästa hopptyp är med nästa hopp-funktionen i Azure Nätverksbevakaren med hjälp av Azure REST API

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST-API](network-watcher-check-next-hop-rest.md)

Nästa hopp är en funktion i Nätverksbevakaren som tillhandahåller möjligheten get nästa hopptyp och IP-adress baserat på en angiven virtuell dator. Den här funktionen är användbart för att fastställa om trafik som lämnar en virtuell dator som passerar en gateway, internet eller virtuella nätverk för att komma till sin destination.

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST-API med hjälp av PowerShell. ARMClient hittas på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln använder nästa hopp, en funktion i Nätverksbevakaren som söker efter nästa hopptyp och IP-adress för en resurs. Läs mer om nästa hopp [nästa hopp översikt](network-watcher-next-hop-overview.md).

I det här scenariot kommer du att:

* Hämta nästa hopp för en virtuell dator.

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

Logga in på armclient med dina autentiseringsuppgifter för Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Hämta en virtuell dator

Kör följande skript för att returnera en virtuell dator. Den här informationen behövs för att köra nästa hopp.

Följande kod måste ha värden för följande variabler:

- **subscriptionId** -prenumerations-Id ska användas.
- **resourceGroupName** -namnet på en resursgrupp som innehåller virtuella datorer.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Id för den virtuella datorn används i följande exempel i följande utdata:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Hämta nästa hopp

Nästa hopp från en virtuell dator kan hämtas när authorization-huvud har skapats. Följande värden måste ersättas för exemplet ska fungera.

> [!Important]
> För nätverket Watcher REST API-anrop som resursgruppens namn i URI-begäran är resursgruppen som innehåller Nätverksbevakaren, inte resurserna du utför de diagnostiska åtgärderna på.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Nästa hopp kräver att den Virtuella datorresursen har allokerats för att köras.

## <a name="results"></a>Resultat

Följande fragment är ett exempel på utdata togs emot. Det resulterar i följande värden:

* **nextHopType** -värdet är ett av följande värden: Internet VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway eller None.
* **nextHopIpAddress** -IP-adressen för nästa hopp.
* **routeTableId** - värdet är antingen en uri för vägtabellen som är associerad med vägen eller om ingen användardefinierad väg är definierad värdet för *Systemväg* returneras.

Följande är resultatet i json-format.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Nästa steg

När du har kunnat ta reda på nästa hopp för en virtuell dator, kan du visa säkerheten för dina nätverksresurser genom att besöka [Säkerhetsvyn översikt](network-watcher-security-group-view-overview.md)














