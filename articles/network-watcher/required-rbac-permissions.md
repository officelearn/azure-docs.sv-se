---
title: Behörigheter som krävs för att använda Azure Nätverksbevakaren funktioner | Microsoft Docs
description: Lär dig vilka Azure rollbaserad behörighet krävs för att arbeta med Nätverksbevakaren funktioner.
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jdial
ms.openlocfilehash: 09f3a1e1d9c6796cb55ae8f0ab18bf8e1b3fa198
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077880"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Rollbaserad behörighet krävs för att använda funktionerna för Nätverksbevakaren

Azure rollbaserad åtkomstkontroll (RBAC) kan du tilldela medlemmar i din organisation som de behöver för att slutföra sina tilldelade ansvarsområden endast specifika åtgärder. Om du vill använda Nätverksbevakaren funktioner kontot du loggar in på Azure med, måste tilldelas den [ägare](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [deltagare](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor), eller [Network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) inbyggda roller eller tilldelad till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som tilldelas de åtgärder som anges för varje Nätverksbevakaren funktion i avsnitten som följer. Mer information om funktionerna i nätverket Watcher finns [vad är Nätverksbevakaren?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Hämta en nätverksbevakaren                                          |
| Microsoft.Network/networkWatchers/write                             | Skapa eller uppdatera en nätverksbevakaren                             |
| Microsoft.Network/networkWatchers/delete                            | Ta bort en nätverksbevakaren                                       |

## <a name="nsg-flow-logs"></a>NSG-loggar flöde

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Konfigurera ett flöde logg                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Fråga status för en flödet logg                                    |

## <a name="connection-troubleshoot"></a>Felsökning av anslutning

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Resultatet av frågan för en anslutning felsöka test                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Kör en anslutning felsöka test                             |

## <a name="connection-monitor"></a>Övervakaren anslutning

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Starta en anslutning Övervakare                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Stoppa en anslutning Övervakare                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Fråga en anslutning Övervakare                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Få en skärm för anslutning                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Skapa en anslutning                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Ta bort en anslutning Övervakare                                    |

## <a name="packet-capture"></a>Paketinsamling

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Fråga status för en paketinsamling                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Stoppa en paketinsamling                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Hämta en paketinsamling                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Skapa en paketinsamling                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Ta bort en paketinsamling                                        |

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
| Microsoft.Network/networkWatchers/topology/action                   | Hämta topologi                                                   |

## <a name="reachability-report"></a>Reachability rapport

| Åtgärd                                                              | Namn                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Hämta en Azure reachability-rapport                               |

## <a name="additional-actions"></a>Ytterligare åtgärder

Watcher nätverksfunktioner kräver också följande åtgärder:

- Microsoft.Storage/Read
- Microsoft.Authorization/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*