---
title: Kontrollera trafik med Azure Network Watcher IP verifiera - REST | Microsoft Docs
description: Den här artikeln beskrivs hur du kontrollerar om trafik till eller från en virtuell dator tillåts eller nekas
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c3d38f776db63e777174b7dca8b09a0d19c387e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Kontrollera om trafik tillåts eller nekas med IP-flöde verifiera en komponent i Azure Nätverksbevakaren

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST-API](network-watcher-check-ip-flow-verify-rest.md)


IP-flöde verifiera är en funktion i Nätverksbevakaren som hjälper dig att kontrollera om tillåts trafik till eller från en virtuell dator. Verifieringen kan köras för inkommande eller utgående trafik. Det här scenariot är användbart för att hämta aktuella tillstånd om en virtuell dator kan kommunicera med en extern resurs eller backend. IP-flöde Kontrollera kan användas för att kontrollera om Nätverkssäkerhetsgrupp (NSG)-regler är korrekt konfigurerade och felsöka flöden som blockeras av NSG-regler. En annan orsak till att använda IP flödet Kontrollera är att kontrollera att trafik som du vill blockerade blockeras korrekt av NSG: N.

## <a name="before-you-begin"></a>Innan du börjar

ARMclient används för att anropa REST-API med hjälp av PowerShell. ARMClient hittas på chocolatey på [ARMClient på Chocolatey](https://chocolatey.org/packages/ARMClient)

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren.

## <a name="scenario"></a>Scenario

Det här scenariot använder IP-flöde Kontrollera för att verifiera om en virtuell dator kan du kontakta en annan dator via port 443. Om trafiken nekas returnerar säkerhetsregeln som nekar trafiken. Läs mer om IP-flöde Kontrollera [IP-flöde Kontrollera översikt](network-watcher-ip-flow-verify-overview.md)

I det här scenariot kan du:

* Hämta en virtuell dator
* Verifiera anropet IP-flöde
* Verifiera resultaten

## <a name="log-in-with-armclient"></a>Logga in med ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Hämta en virtuell dator

Kör följande skript för att returnera en virtuell dator. Följande kod måste värdena för variabler:

* **subscriptionId** -prenumerations-Id ska användas.
* **resourceGroupName** -namnet på en resursgrupp som innehåller virtuella datorer.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Den information som behövs är id under typen `Microsoft.Compute/virtualMachines`. Resultatet bör likna följande kodexempel:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Anropa IP-flöde Kontrollera

I följande exempel skapas en begäran om att kontrollera trafik för en angiven virtuell dator. Svaret returnerar om trafiken tillåts eller nekas trafiken. Om trafik nekas den returnerar även vilken regel som blockerar trafiken.

> [!NOTE]
> IP-flöde Kontrollera kräver att den Virtuella datorresursen allokeras.

Skriptet kräver resursens Id för en virtuell dator och ett nätverkskort på den virtuella datorn. Dessa värden tillhandahålls av föregående utdata.

> [!Important]
> För alla nätverk Watcher REST-anrop är det som innehåller Nätverksbevakaren-instans, inte de resurser som du utför de diagnostiska åtgärderna på resursgruppens namn i URI-begäran.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Förstå resultaten

Svaret åter anger om trafik tillåts eller nekas. Svaret ser ut som följande exempel:

**Tillåtna**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Nekad**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Nästa steg

Om trafik blockeras och får inte vara, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) lära dig mer om Nätverkssäkerhetsgrupper.












