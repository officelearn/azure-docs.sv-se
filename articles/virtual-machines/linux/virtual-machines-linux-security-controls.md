---
title: Säkerhetskontroller för Virtuella Azure Linux-datorer - Linux
description: En checklista över säkerhetskontroller för utvärdering av virtuella Azure Linux-datorer
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190576"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Säkerhetskontroller för virtuella Linux-datorer

Den här artikeln dokumenterar säkerhetskontrollerna inbyggda i Virtuella Linux-datorer.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

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
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | Se [Övervaka och uppdatera en virtuell Linux-dator i Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
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
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Se [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, vnet-kryptering och VNet-VNet-kryptering)| Ja | Virtuella Azure-datorer stöder [ExpressRoute-](/azure/expressroute) och VNet-kryptering. Se [Transitkryptering i virtuella datorer](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Kryptering på serversidan i vila: kundhanterade nycklar (BYOK) | Ja | Kundhanterade nycklar är ett Azure-krypteringsscenario som stöds. se [Översikt över Azure-kryptering](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Kryptering på kolumnnivå (Azure Data Services)| Ej tillämpligt | |
| API-anrop krypterade| Ja | Via HTTPS och TLS. |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetskontroll | Ja/nej | Anteckningar|
|---|---|--|
| Stöd för konfigurationshantering (versionshantering av konfiguration osv.)| Ja |  | 

## <a name="next-steps"></a>Nästa steg

- Läs mer om de [inbyggda säkerhetskontrollerna för Azure-tjänster](../../security/fundamentals/security-controls.md).
