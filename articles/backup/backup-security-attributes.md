---
title: Vanliga säkerhetsattribut för Azure Backup
description: En lista med vanliga security attribut för att utvärdera Azure Backup
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679695"
---
# <a name="common-security-attributes-for-azure-backup"></a>Vanliga säkerhetsattribut för Azure Backup

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Med hjälp av kryptering av lagringstjänst för storage-konton. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Nej | Med hjälp av HTTPS. |
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Nej |  |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Nej |  |
| API-anrop som är krypterad| Ja |  |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Nej |  |
| vNET-stöd för inmatning| Nej |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | Tvingad tunneltrafik har stöd för säkerhetskopiering av virtuella datorer. Tvingad tunneltrafik finns inte stöd för arbetsbelastningar som körs på virtuella datorer. |
| Stöd för Tvingad tunneltrafik | Nej |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Log Analytics stöds via diagnostikloggar. Se [övervaka Azure Backup skyddade arbetsbelastningar med hjälp av Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) för mer information. |

## <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Autentisering är via Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja | Kunden har skapat och inbyggda RBAC-roller används. Se [Use Role-Based Access Control för att hantera Azure Backup återställningspunkter](/azure/backup/backup-rbac-rs-vault) för mer information. |


## <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning| Ja | Alla åtgärder som kund som utlöses från Azure portal loggas aktivitetsloggar. |
| Data-dataplaner loggning och granskning| Nej | Azure Backup-dataplanet kan inte nås direkt.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja|  |