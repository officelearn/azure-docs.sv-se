---
title: "Automatisera NSG granskning med Watcher nätverkssäkerhet för Azure gruppvyn | Microsoft Docs"
description: "Den här sidan innehåller instruktioner om hur du konfigurerar granskning av en Nätverkssäkerhetsgrupp"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 57f2200e541eeb629f72d60ffa0acb2d8233c018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatisera NSG granskning med vyn för Watcher nätverkssäkerhet för Azure-grupp

Kunder är ofta inför utmaningen i att verifiera säkerhetstillståndet av infrastrukturen. Denna utmaning skiljer sig inte för sina virtuella datorer i Azure. Det är viktigt att ha en liknande säkerhetsprofil baserat på Nätverkssäkerhetsgrupp (NSG)-regler tillämpas. I vyn säkerhet grupp kan nu du få listan över regler som tillämpas på en virtuell dator inom en NSG. Du kan definiera en gyllene NSG säkerhetsprofil och initiera Gruppvy för säkerhet i varje vecka takt och jämför utdata till gyllene profilen och skapa en rapport. Det här sättet kan du identifiera med alla de virtuella datorer som inte överensstämmer med den föreskrivna säkerhetsprofilen.

Om du är bekant med Nätverkssäkerhetsgrupper [översikt över säkerheten i nätverket](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Innan du börjar

I det här scenariot kan du jämföra kända fungerande baslinje till säkerhet grupp visa resultaten för en virtuell dator.

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator som ska användas.

## <a name="scenario"></a>Scenario

Det scenario som beskrivs i den här artikeln hämtar gruppvyn säkerhet för en virtuell dator.

I det här scenariot kommer du att:

- Hämta en känd bra regeluppsättning
- Hämta en virtuell dator med Rest API
- Hämta gruppvy för säkerhet för den virtuella datorn
- Utvärdera svar

## <a name="retrieve-rule-set"></a>Hämta regeluppsättning

Det första steget i det här exemplet är att arbeta med en befintlig baslinjen. Följande exempel är vissa json som extraherats från en befintlig säkerhetsgrupp för nätverk med den `Get-AzureRmNetworkSecurityGroup` cmdlet som används som baslinje för det här exemplet.

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

I det här steget ska läser vi en json-fil som du skapade tidigare med regler som förväntas finnas på Nätverkssäkerhetsgruppen för det här exemplet.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Hämta Nätverksbevakaren

Nästa steg är att hämta Nätverksbevakaren-instans. Den `$networkWatcher` variabel har skickats till den `AzureRmNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Hämta en virtuell dator

En virtuell dator krävs för att köra den `Get-AzureRmNetworkWatcherSecurityGroupView` cmdlet mot. I följande exempel hämtas ett VM-objekt.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Hämta gruppvy för säkerhet

Nästa steg är att hämta säkerhet grupp visa resultatet. Det här resultatet jämförs med ”baseline” json som visades tidigare.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analysera resultaten

Svaret är grupperad efter nätverksgränssnitt. Olika typer av regler som returnerades är giltiga och standard säkerhetsregler. Resultatet är ytterligare fördelade på hur den används, antingen på ett undernät eller ett virtuellt nätverkskort.

Följande PowerShell-skript jämför resultaten för en befintlig utdata från en NSG säkerhet gruppvyn. Följande exempel är ett enkelt exempel på hur resultaten kan jämföras med `Compare-Object` cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

I följande exempel är resultatet. Du kan se två regler i den första regeln inställda inte fanns i jämförelse.

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

Om inställningarna har ändrats, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/virtual-network-manage-nsg-arm-portal.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som är i fråga.













