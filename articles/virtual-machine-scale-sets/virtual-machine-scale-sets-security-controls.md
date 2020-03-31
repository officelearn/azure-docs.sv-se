---
title: Säkerhetskontroller för Azure-skalningsuppsättningar för virtuella datorer
description: En checklista med säkerhetskontroller för utvärdering av Azure Virtual Machine Scale Sets
ms.service: virtual-machine-scale-sets
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 4007f4adeee065fe32492d3bd16f3a06d24e7d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190613"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Säkerhetskontroller för Azure-skalningsuppsättningar för virtuella datorer

Den här artikeln dokumenterar säkerhetskontrollerna som är inbyggda i Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Support för tjänstens slutpunkt| Ja | |
| Stöd för VNet-injektion| Ja | |
| Stöd för nätverksisolering och brandväggar| Ja |  |
| Stöd för påtvingad tunnelning| Ja | Se [Konfigurera tvingande tunnel med hjälp av distributionsmodellen för Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Övervakning & loggning

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Se [Övervaka och uppdatera en virtuell Linux-dator i Azure](/azure/virtual-machines/linux/tutorial-monitoring) och Övervaka och uppdatera en virtuell [Windows-dator i Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Kontroll- och hanteringsplan loggning och revision| Ja |  |
| Loggning och granskning av dataplan | Inga |  |

## <a name="identity"></a>Identitet

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja |  |
| Auktorisering| Ja |  |

## <a name="data-protection"></a>Dataskydd

| Säkerhetskontroll | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Se [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | Virtuella Azure-datorer stöder [ExpressRoute-](/azure/expressroute) och VNet-kryptering. Se [Transitkryptering i virtuella datorer](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja | Kundhanterade nycklar är ett Azure-krypteringsscenario som stöds. Se Se [Azure Disk Encryption för skalningsuppsättningar för virtuella datorer](disk-encryption-overview.md)|
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |
| API-anrop krypterade| Ja | Via HTTPS och TLS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja |  | 

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../security/fundamentals/security-controls.md).
