---
title: Automatisera NSG granskning med Azure Network Watcher säkerhetsgruppvy | Microsoft Docs
description: Den här sidan innehåller anvisningar om hur du konfigurerar granskning av en Nätverkssäkerhetsgrupp
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3d35860452aabb6aecc4e8549c7b5ce4447d7aa4
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047678"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatisera NSG granskning med Azure Network Watcher säkerhetsgruppvy

Kunder har ofta inför utmaningen med att verifiera säkerhetspositionen för sin infrastruktur. Den här utmaningen skiljer sig inte för sina virtuella datorer i Azure. Det är viktigt att du har en liknande säkerhetsprofil baserat på de regler för Nätverkssäkerhetsgrupp (NSG) som tillämpas. Med den Säkerhetsgruppvy kan få du nu listan över regler som tillämpas på en virtuell dator i en NSG. Du kan definiera en gyllene NSG säkerhetsprofil och initiera Säkerhetsgruppvy på en veckovis takt och jämför utdatan till gyllene profilen och skapa en rapport. På så sätt kan du identifiera enkelt alla virtuella datorer som inte överensstämmer med den föreskrivna säkerhetsprofilen.

Om du inte känner till Nätverkssäkerhetsgrupper [översikt över nätverkssäkerhet](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot kan jämföra du en känd bra baslinje till säkerhet grupp visa resultatet som returneras för en virtuell dator.

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Network Watcher](network-watcher-create.md) att skapa en Network Watcher. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator för att användas.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar säkerhetsgruppvy för en virtuell dator.

I det här scenariot kommer du att:

- Hämta en känd bra regeluppsättning
- Hämta en virtuell dator med Rest API
- Hämta säkerhetsgruppvy för virtuell dator
- Utvärdera svar

## <a name="retrieve-rule-set"></a>Hämta regeluppsättning

Det första steget i det här exemplet är att arbeta med en befintlig baslinje. I följande exempel är vissa json som extraheras från en befintlig Nätverkssäkerhetsgrupp med den `Get-AzNetworkSecurityGroup` cmdlet som används som baslinje för det här exemplet.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Konvertera regeluppsättning till PowerShell-objekt

I det här steget ska läser vi en json-fil som du skapade tidigare med de regler som förväntas finnas på den nya Nätverkssäkerhetsgruppen i det här exemplet.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Hämta Network Watcher

Nästa steg är att hämta Network Watcher-instans. Den `$networkWatcher` variabeln skickas till den `AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra den `Get-AzNetworkWatcherSecurityGroupView` cmdlet mot. I följande exempel hämtas ett VM-objekt.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Hämta säkerhetsgruppvy

Nästa steg är att hämta security grupp visa resultatet. Det här resultatet jämförs ”baslinje” json som visades tidigare.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analysera resultaten

Svaret är grupperad efter nätverksgränssnitt. De olika typerna av regler som returneras är effektiva och standardsäkerhetsregler. Resultatet är ytterligare fördelat på hur den används, antingen på ett undernät eller ett virtuellt nätverkskort.

Följande PowerShell-skript jämför resultaten av Säkerhetsgruppvy den befintliga en NSG-utdata. I följande exempel är ett enkelt exempel på hur resultaten kan jämföras med `Compare-Object` cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

I följande exempel är resultatet. Du kan se två av de regler som fanns i den första regeln anger inte fanns i jämförelsen.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Nästa steg

Om inställningarna har ändrats, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de och Nätverkssäkerhetsregler som är i fråga.













