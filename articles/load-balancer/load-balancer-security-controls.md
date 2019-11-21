---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214905"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Nätverk

| Security control | Ja/nej | Anteckningar |
|---|---|--|
| Service endpoint support| Gäller inte | |
| VNet injection support| Gäller inte | |
| Network Isolation and Firewalling support| Gäller inte |  |
| Forced tunneling support| Gäller inte | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | Ja/nej | Anteckningar|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Ja | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| Ja | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | Gäller inte |  |

## <a name="identity"></a>Identitet

| Security control | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Gäller inte |  |
| Autentisering| Gäller inte |  |

## <a name="data-protection"></a>Dataskydd

| Security control | Ja/nej | Anteckningar |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | Gäller inte | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| Gäller inte | |
| Server-side encryption at rest: customer-managed keys (BYOK) | Gäller inte | |
| Column level encryption (Azure Data Services)| Gäller inte | |
| API calls encrypted| Ja | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Konfigurationshantering

| Security control | Ja/nej | Anteckningar|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| Gäller inte |  | 

## <a name="next-steps"></a>Nästa steg

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).