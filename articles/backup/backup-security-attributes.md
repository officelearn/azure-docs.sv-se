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
ms.openlocfilehash: 7bc4fd42f64bba09fd20fedf5e06dc30c3b2cc49
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64680801"
---
# <a name="common-security-attributes-for-azure-backup"></a>Vanliga säkerhetsattribut för Azure Backup

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln beskrivs vanliga security attribut som är inbyggda i Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Med hjälp av kryptering av lagringstjänst för storage-konton. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Nej | Med hjälp av HTTPS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej |  |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Nej |  |
| API-anrop som är krypterad| Ja |  |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej |  |
| VNet-stöd för inmatning| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Tvingad tunneltrafik har stöd för säkerhetskopiering av virtuella datorer. Tvingad tunneltrafik finns inte stöd för arbetsbelastningar som körs på virtuella datorer. |
| Tvingad tunneltrafik support| Nej |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Log Analytics stöds via diagnostikloggar. Se [övervaka Azure Backup skyddade arbetsbelastningar med hjälp av Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) för mer information. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Kunden har skapat och inbyggda RBAC-roller används. Se [Use Role-Based Access Control för att hantera Azure Backup återställningspunkter](/azure/backup/backup-rbac-rs-vault) för mer information. |


## <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla åtgärder som kund som utlöses från Azure portal loggas aktivitetsloggar. |
| Data plan loggning och granskning| Nej | Azure Backup-dataplanet kan inte nås direkt.  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja|  |