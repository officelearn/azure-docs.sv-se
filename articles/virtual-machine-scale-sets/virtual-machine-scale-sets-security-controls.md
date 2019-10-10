---
title: Säkerhets kontroller för Azure Virtual Machine Scale Sets
description: En check lista över säkerhets kontroller för utvärdering av Azure-Virtual Machine Scale Sets
services: virtual-machine-scale-sets
ms.service: virtual-machine-scale-sets
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d7e69e8ad0c5b14ac7ed8b941a7949f4f675812
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176771"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Säkerhets kontroller för Azure Virtual Machine Scale Sets

I den här artikeln dokumenteras de säkerhets kontroller som är inbyggda i Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Ja | |
| Stöd för VNet-injektering| Ja | |
| Stöd för nätverks isolering och brand vägg| Ja |  |
| Stöd för Tvingad tunnel trafik| Ja | Se [Konfigurera Tvingad tunnel trafik med Azure Resource Manager distributions modell](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Övervaka & loggning

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Se [övervaka och uppdatera en virtuell Linux-dator i Azure](/azure/virtual-machines/linux/tutorial-monitoring) och [övervaka och uppdatera en virtuell Windows-dator i Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Loggning och granskning av kontroll-och hanterings plan| Ja |  |
| Loggning och granskning av data planet | Nej |  |

## <a name="identity"></a>Identitet

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja |  |
| Autentisering| Ja |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhets kontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Ja | Se [så här krypterar du en virtuell Linux-dator i Azure](/azure/virtual-machines/linux/disk-encryption-linux) och [krypterar virtuella diskar på en virtuell Windows-dator](/azure/virtual-machines/windows/encrypt-disks). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Ja | Azure Virtual Machines stöder [ExpressRoute](/azure/expressroute) och VNET-kryptering. Se [kryptering under överföring i virtuella datorer](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Kryptering på Server sidan på rest: Kundhanterade nycklar (BYOK) | Ja | Kundhanterade nycklar är ett Azure-krypterings scenario som stöds. Se [Översikt över Azure-kryptering](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Kryptering på kolumn nivå (Azure Data Services)| Gäller inte | |
| Krypterade API-anrop| Ja | Via HTTPS och SSL. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhets kontroll | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja |  | 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om de [inbyggda säkerhets kontrollerna i Azure-tjänster](../security/fundamentals/security-controls.md).