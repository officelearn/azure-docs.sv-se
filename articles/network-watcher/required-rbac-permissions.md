---
title: Behörigheter som krävs för att använda funktionerna för Azure Network Watcher | Microsoft Docs
description: Lär dig vilka Azure rollbaserad behörighet krävs för att arbeta med Network Watcher-funktioner.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 8c8fe6125d9c638fedadc3d299ff0ac0d601fd61
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685700"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Rollbaserad åtkomstbehörighet som krävs för att använda funktionerna för Network Watcher

Azure rollbaserad åtkomstkontroll (RBAC) kan du tilldela bara specifika åtgärder för medlemmar i din organisation som de behöver för att slutföra sina tilldelade ansvarsområden. För att använda Network Watcher-funktioner, måste det konto som du loggar in på Azure med, tilldelas till den [ägare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), eller [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) inbyggda roller eller tilldelad till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som är tilldelade åtgärderna för varje funktion för Network Watcher i avsnitten som följer. Mer information om funktionerna i Network Watcher finns [vad är Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Hämta en network watcher                                          |
| Microsoft.Network/networkWatchers/write                             | Skapa eller uppdatera en nätverksbevakaren                             |
| Microsoft.Network/networkWatchers/delete                            | Ta bort en nätverksbevakaren                                       |

## <a name="nsg-flow-logs"></a>NSG flödesloggar

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurera ett flöde Log                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Fråga status för en flow-logg                                    |

## <a name="connection-troubleshoot"></a>Felsökning av anslutning

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Initiera en anslutning felsöka test
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Resultatet av frågan för en anslutning felsöka test                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Kör en anslutning felsöka test                             |

## <a name="connection-monitor"></a>Anslutningsövervakare

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Starta en anslutningsövervakaren                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Stoppa en anslutningsövervakare                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Fråga en anslutningsövervakare                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Hämta en anslutningsövervakare                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Skapa en anslutningsövervakare                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Ta bort en anslutningsövervakare                                    |

## <a name="packet-capture"></a>Paketinsamling

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Fråga status för ett infångat paket                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Stoppa ett infångat paket                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Hämta ett infångat paket                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Skapa ett infångat paket                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Ta bort ett infångat paket                                        |

## <a name="ip-flow-verify"></a>Kontrollera IP-flöde

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verifiera ett IP-flöde                                              |

## <a name="next-hop"></a>Nästa hopp

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Hämta nästa hopp från en virtuell dator                                     |

## <a name="network-security-group-view"></a>Vy för nätverkssäkerhetsgrupp

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Visa säkerhetsgrupper                                           |

## <a name="topology"></a>Topologi

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Hämta topologin                                                   |

## <a name="reachability-report"></a>Nätverksåtkomst rapport

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Hämta en Azure nätverksåtkomst-rapport                               |

## <a name="additional-actions"></a>Ytterligare åtgärder

Funktioner för Network Watcher kräver även följande åtgärder:

- Microsoft.Authorization/ \* /läsa
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
