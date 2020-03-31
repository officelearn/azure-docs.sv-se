---
title: Säkerhetskontroller för virtuella Azure Windows-datorer
description: En checklista med säkerhetskontroller för utvärdering av virtuella Azure Windows-datorer
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: f105eac0f6f21ea3358340a4e2aaec7d1f1a95ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190548"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Säkerhetskontroller för virtuella Windows-datorer

I den här artikeln dokumenteras de säkerhetskontroller som är inbyggda i virtuella datorer i Windows.

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
| Azure övervakningsstöd (Logganalys, App insikter, etc.)| Ja | [Övervaka och uppdatera en virtuell Windows-dator i Azure](tutorial-monitoring.md). |
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
| Kryptering på serversidan i vila: Microsoft-hanterade nycklar | Ja | Se [Kryptera virtuella diskar på en Virtuell Windows.](/azure/virtual-machines/windows/encrypt-disks) |
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
