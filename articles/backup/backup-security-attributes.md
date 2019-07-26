---
title: Säkerhetsattribut för Azure Backup
description: En check lista över säkerhetsattribut för utvärdering av Azure Backup
author: utraghuv
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: c2500c6c9ff6882e521f4edce02426a92a0bd39f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464932"
---
# <a name="security-attributes-for-azure-backup"></a>Säkerhetsattribut för Azure Backup

I den här artikeln dokumenteras säkerhetsattributen som är inbyggda i Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t. ex. kryptering på Server sidan, kryptering på Server sidan med Kundhanterade nycklar och andra krypterings funktioner)| Ja | Använda kryptering av lagrings tjänst för lagrings konton. |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i VNet-kryptering och VNet-VNet-kryptering)| Nej | Använda HTTPS. |
| Hantering av krypterings nyckel (CMK, BYOK osv.)| Nej |  |
| Kryptering på kolumn nivå (Azure Data Services)| Nej |  |
| Krypterade API-anrop| Ja |  |

## <a name="network-segmentation"></a>Nätverks segmentering

| Säkerhetsattribut | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänst slut punkt| Nej |  |
| Stöd för VNet-injektering| Nej |  |
| Stöd för nätverks isolering och brand vägg| Ja | Tvingad tunnel trafik stöds för VM-säkerhetskopiering. Tvingad tunnel trafik stöds inte för arbets belastningar som körs i virtuella datorer. |
| Stöd för Tvingad tunnel trafik| Nej |  |

## <a name="detection"></a>Identifiering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Azure Monitoring support (Log Analytics, App Insights osv.)| Ja | Log Analytics stöds via diagnostikloggar. Mer information finns i [övervaka Azure Backup skyddade arbets belastningar med hjälp av Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja | Autentisering är via Azure Active Directory. |
| Authorization| Ja | Kunden skapade och inbyggda RBAC-roller används. Mer information finns i [använda rollbaserad Access Control för att hantera Azure Backup återställnings punkter](/azure/backup/backup-rbac-rs-vault) . |


## <a name="audit-trail"></a>Gransknings logg

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Loggning och granskning av kontroll-och hanterings plan| Ja | Alla kunder som har utlöst åtgärder från Azure Portal loggas till aktivitets loggar. |
| Loggning och granskning av data planet| Nej | Det går inte att nå Azure Backup data planet direkt.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattribut | Ja/nej | Anteckningar|
|---|---|--|
| Konfigurations hanterings stöd (konfigurations version osv.)| Ja|  |