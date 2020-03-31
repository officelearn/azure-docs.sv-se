---
title: Automatisera NSG-granskning - Säkerhetsgruppsvy
titleSuffix: Azure Network Watcher
description: Den här sidan innehåller instruktioner om hur du konfigurerar granskning av en nätverkssäkerhetsgrupp
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
ms.openlocfilehash: 59c1b6e6c281a736a79d110bd7d943344bcd5130
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840986"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatisera NSG-granskning med azure network watcher-säkerhetsgruppvyn

Kunderna står ofta inför utmaningen att verifiera säkerhetshållningen i sin infrastruktur. Den här utmaningen är inte annorlunda för deras virtuella datorer i Azure. Det är viktigt att ha en liknande säkerhetsprofil baserad på reglerna för NSG (Network Security Group). Med hjälp av säkerhetsgruppvyn kan du nu hämta listan över regler som tillämpas på en virtuell dator inom en NSG. Du kan definiera en gyllene NSG-säkerhetsprofil och initiera säkerhetsgruppvy på en veckokadens och jämföra utdata med den gyllene profilen och skapa en rapport. På så sätt kan du enkelt identifiera alla virtuella datorer som inte överensstämmer med den föreskrivna säkerhetsprofilen.

Om du inte känner till nätverkssäkerhetsgrupper läser du [Översikt över nätverkssäkerhet](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Innan du börjar

I det här fallet kan du jämföra en fungerande baslinje med säkerhetsgruppsvyns resultat som returneras för en virtuell dator.

Det här scenariot förutsätter att du redan har följt stegen i [Skapa en nätverksbevakare](network-watcher-create.md) för att skapa en Network Watcher. Scenariot förutsätter också att en resursgrupp med en giltig virtuell dator finns för att användas.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar säkerhetsgruppvyn för en virtuell dator.

I det här fallet kommer du att:

- Hämta en fungerande regeluppsättning
- Hämta en virtuell dator med Rest API
- Hämta säkerhetsgruppsvy för virtuell dator
- Utvärdera svar

## <a name="retrieve-rule-set"></a>Hämta regeluppsättning

Det första steget i det här exemplet är att arbeta med en befintlig originalplan. Följande exempel är några json som extraherats `Get-AzNetworkSecurityGroup` från en befintlig nätverkssäkerhetsgrupp med hjälp av den cmdlet som används som baslinje för det här exemplet.

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

I det här steget läser vi en json-fil som skapades tidigare med de regler som förväntas finnas i nätverkssäkerhetsgruppen i det här exemplet.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Hämta nätverksbevakare

Nästa steg är att hämta Network Watcher-instansen. Variabeln `$networkWatcher` skickas till `AzNetworkWatcherSecurityGroupView` cmdleten.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Skaffa en virtuell dator

En virtuell dator krävs `Get-AzNetworkWatcherSecurityGroupView` för att köra cmdlet mot. Följande exempel hämtar ett VM-objekt.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Hämta säkerhetsgruppsvyn

Nästa steg är att hämta resultatet för säkerhetsgruppvyn. Detta resultat jämförs med "baslinje" json som visades tidigare.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analysera resultaten

Svaret grupperas efter nätverksgränssnitt. De olika typerna av regler som returneras är effektiva och standardsäkerhetsregler. Resultatet delas upp ytterligare efter hur det tillämpas, antingen på ett undernät eller ett virtuellt nätverkskort.

I följande PowerShell-skript jämförs resultatet av säkerhetsgruppvyn med ett befintligt utdata från en NSG. Följande exempel är ett enkelt exempel på hur `Compare-Object` resultaten kan jämföras med cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Följande exempel är resultatet. Du kan se två av de regler som fanns i den första regeluppsättningen fanns inte i jämförelsen.

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

Om inställningarna har ändrats läser du [Hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) för att spåra den nätverkssäkerhetsgrupp och säkerhetsregler som är i fråga.













