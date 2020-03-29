---
title: RBAC-behörigheter som krävs för att använda funktioner
titleSuffix: Azure Network Watcher
description: Lär dig vilka Azure-rollbaserade åtkomstkontrollbehörigheter som krävs för att arbeta med Network Watcher-funktioner.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: damendo
ms.openlocfilehash: f8743f19d6cd262ad140659be55a4fc57e842564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840561"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Rollbaserade behörigheter för åtkomstkontroll som krävs för att använda Nätverksbevakningsfunktioner

Med Azure-rollbaserad åtkomstkontroll (RBAC) kan du bara tilldela specifika åtgärder till medlemmar i organisationen som de behöver för att slutföra sina tilldelade ansvarsområden. Om du vill använda network watcher-funktioner måste kontot du loggar in på Azure med tilldelas de inbyggda rollerna [ägare,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner) [deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)eller [Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) eller tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som tilldelas de åtgärder som anges för varje Network Watcher-funktion i de avsnitt som följer. Mer information om Network Watchers funktioner finns i [Vad är Network Watcher?](network-watcher-monitoring-overview.md)

## <a name="network-watcher"></a>Network Watcher

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Skaffa en nätverksbevakare                                          |
| Microsoft.Network/networkWatchers/write                             | Skapa eller uppdatera en nätverksbevakare                             |
| Microsoft.Network/networkWatchers/delete                            | Ta bort en nätverksbevakare                                       |

## <a name="nsg-flow-logs"></a>NSG flödesloggar

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/åtgärd           | Konfigurera en flödeslogg                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/åtgärd         | Frågestatus för en flödeslogg                                    |

## <a name="connection-troubleshoot"></a>Felsökning av anslutning

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Initiera ett felsökningstest för anslutning
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Frågeresultat för ett felsökningstest för anslutningar                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Kör ett felsökningstest för anslutningar                             |

## <a name="connection-monitor"></a>Anslutningsmonitor

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Starta en anslutningsmonitor                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Stoppa en anslutningsövervakare                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Fråga en anslutningsövervakare                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Skaffa en anslutningsövervakare                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Skapa en anslutningsövervakare                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Ta bort en anslutningsövervakare                                    |

## <a name="packet-capture"></a>Paketinsamling

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/åtgärd | Fråga status för en paketfångst                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Stoppa en paketfångst                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Hämta en paketfångst                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Skapa en paketfångst                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Ta bort en paketfångst                                        |

## <a name="ip-flow-verify"></a>Kontrollera IP-flöde

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verifiera ett IP-flöde                                              |

## <a name="next-hop"></a>Nästa hopp

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Hämta nästa hopp från en virtuell dator                                     |

## <a name="network-security-group-view"></a>Vy för nätverkssäkerhetsgrupp

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Visa säkerhetsgrupper                                           |

## <a name="topology"></a>Topologi

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topologi/åtgärd                   | Få topologi                                                   |

## <a name="reachability-report"></a>Nåbarhetsrapport

| Åtgärd                                                              | Beskrivning                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Få en azure-nåbarhetsrapport                               |


## <a name="additional-actions"></a>Ytterligare åtgärder

Nätverksbevakningsfunktionerna kräver också följande åtgärder:

| Åtgärder                                                           | Beskrivning                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Auktorisering/\*/Läs                                     | Används för att hämta RBAC-rolltilldelningar och principdefinitioner          |
| Microsoft.Resurser/prenumerationer/resourceGroups/Read               | Används för att räkna upp alla resursgrupper i en prenumeration    |
| Microsoft.Storage/storageAccounts/Read                              | Används för att hämta egenskaperna för det angivna lagringskontot   |
| Microsoft.Storage/storageAccounts/listServiceSas/Åtgärd, </br> Microsoft.StorageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| Används för att hämta SIGNATURER för delad åtkomst (SAS) som möjliggör [säker åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) och skriver till lagringskontot |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| Används för att logga in på den virtuella datorn, göra ett paket fånga och ladda upp den till lagringskonto|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Används för att kontrollera om Network Watcher-tillägget finns och installera vid behov |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| Används för att komma åt skalningsuppsättningar för virtuella datorer, göra paketinsamlingar och ladda upp dem till lagringskonto|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/write Microsoft.Compute/virtualMachineScaleSets/extensions/Write Microsoft.Compute/virtualMachineScaleSets/extensions/Write Microsoft.| Används för att kontrollera om Network Watcher-tillägget finns och installera vid behov |
| Microsoft.Insights/alertRules/*                                     | Används för att ställa in måttaviseringar                                     |
| Microsoft.Support/*                                                 | Används för att skapa och uppdatera supportärenden från Network Watcher |
