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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471724"
---
I för hands versionen är Managed disks som har delade diskar aktiverade enligt följande begränsningar:

- För närvarande endast tillgängligt med Premium-SSD.
- Stöds för närvarande endast i regionen västra centrala USA.
- Alla virtuella datorer som delar en disk måste distribueras i samma [närhets placerings grupper](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Kan bara aktive ras på data diskar, inte på OS-diskar.
- Det går bara att använda standard diskar med vissa versioner av Windows Server-redundanskluster, mer information finns i [maskin varu krav och lagrings alternativ för redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- ReadOnly-cachelagring av värden är inte tillgängligt för Premium-SSD med `maxShares>1`.
- Tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer kan bara användas med `FaultDomainCount` inställd på 1.
- Azure Backup och Azure Site Recovery support är ännu inte tillgängligt.

Om du är intresse rad av att prova delade diskar kan du [Registrera dig för vår för hands version](https://aka.ms/AzureSharedDiskPreviewSignUp).
