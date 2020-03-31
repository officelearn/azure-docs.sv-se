---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471724"
---
I förhandsgranskningen omfattas hanterade diskar som har delade diskar av följande begränsningar:

- För närvarande endast tillgänglig med premium SSD.
- För närvarande endast stöds i västra centrala USA regionen.
- Alla virtuella datorer som delar en disk måste distribueras i samma [närhetsplaceringsgrupper](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Kan bara aktiveras på datadiskar, inte OS-diskar.
- Endast grundläggande diskar kan användas med vissa versioner av Windows Server Failover Cluster, mer information se [Maskinvarukrav för redundanskluster och lagringsalternativ](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadOnly värdcache är inte tillgänglig för premium `maxShares>1`SSD med .
- Tillgänglighetsuppsättningar och skalningsuppsättningar `FaultDomainCount` för virtuella datorer kan endast användas med inställd 1.
- Azure Backup och Azure Site Recovery support är ännu inte tillgänglig.

Om du är intresserad av att prova delade diskar sedan [registrera dig för vår förhandsvisning](https://aka.ms/AzureSharedDiskPreviewSignUp).
