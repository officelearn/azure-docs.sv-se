---
title: Security attribut för Azure-tjänster
description: En lista med vanliga security attribut för att utvärdera Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 94681351758f34cc53c425f3207660bdb09f6494
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717787"
---
# <a name="common-security-attributes-for-azure-services"></a>Vanliga säkerhetsattribut för Azure-tjänster

Säkerheten är integrerad i alla aspekter av en Azure-tjänst. Den här artikeln samlar in vanliga security-attribut för valda Azure-tjänster. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Med hjälp av kryptering av lagringstjänst för storage-konton. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Nej | Med hjälp av HTTPS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Nej |  |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Nej |  |
| API-anrop som är krypterad| Ja |  |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej |  |
| VNet-stöd för inmatning| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Tvingad tunneltrafik har stöd för säkerhetskopiering av virtuella datorer. Tvingad tunneltrafik finns inte stöd för arbetsbelastningar som körs på virtuella datorer. |
| Tvingad tunneltrafik support| Nej |  |

### <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Log Analytics stöds via diagnostikloggar. Se övervakaren Azure Backup skyddade arbetsbelastningar med hjälp av Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) för mer information. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Kunden har skapat och inbyggda RBAC-roller används. Se Use Role-Based Access Control för att hantera Azure Backup återställningspunkter (/ azure/backup/backup-rbac-rs-valv) för mer information. |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla åtgärder som kund som utlöses från Azure portal loggas aktivitetsloggar. |
| Data plan loggning och granskning| Nej | Azure Backup-dataplanet kan inte nås direkt.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Alla objekt som är krypterade. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | All kommunikation är via krypterade API-anrop |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunden styr alla nycklar i sina Key Vault. När modul (HSM) som backas upp av maskinvara säkerhetsnycklar anges, skyddar en FIPS nivå 2 HSM nyckeln, certifikat eller hemligheten. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Med hjälp av HTTPS. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | Med tjänstslutpunkter i virtuella nätverk (VNet). |
| VNet-stöd för inmatning| Nej |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av brandväggsregler för virtuellt nätverk. |
| Tvingad tunneltrafik support| Nej |  |

### <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Använda Log Analytics. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Med hjälp av Nyckelvalvets åtkomstprincip. |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplaner loggning och granskning| Ja | Använda Log Analytics. |
| Data plan loggning och granskning| Ja | Använda Log Analytics. |

### <a name="access-controls"></a>Åtkomstkontroller

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplanets åtkomstkontroller | Ja | Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Data dataplanets åtkomstkontroller (på varje servicenivå) | Ja | Nyckelvalvets åtkomstprincip |

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja |  |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Service Fabric-API-anrop görs via Azure Resource Manager. Det krävs en giltig JSON webbtoken (JWT). |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja |  |
| VNet-stöd för inmatning| Ja |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG). |
| Tvingad tunneltrafik support| Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

### <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Med hjälp av Azure-övervakning stöd och stöd från tredje part. |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Autentisering är via Azure Active Directory. |
| Auktorisering| Ja | Identitets- och åtkomsthantering (IAM) för anrop via SFRP. Anrop direkt till slutpunkten för klustret har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till någon av rollerna. |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Alla kontrollplanåtgärder kör via processer för att granska och godkännanden. |
| Data plan loggning och granskning| Gäller inte | Kunden äger klustret.  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Tjänstkonfigurationen är versionsnummer och distribuerade använda Azure distribuerar. Koden (program och runtime) skapas med Azure-versionen.
 |

## <a name="azure-storage"></a>Azure Storage

### <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja |  |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | Stöd för standardmetoder HTTPS/TLS.  Användare kan också kryptera data innan de skickas till tjänsten. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Se [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja |  |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja |  |
| VNet-stöd för inmatning| Gäller inte |  |
| Isolering av nätverk och brandväggsfunktioner support| Ja | |
| Tvingad tunneltrafik support| Gäller inte |  |

### <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Azure Monitor Metrics som är tillgängliga nu loggar från förhandsversion |

### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | Azure Active Directory delad nyckel, delad åtkomst-token. |
| Auktorisering| Ja | Stöd för auktorisering via RBAC, POSIX-ACL: er och SAS-token |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning | Ja | Azure Resource Manager-aktivitetslogg |
| Data plan loggning och granskning| Ja | Diagnostikloggar för tjänsten och Azure Monitor-loggning från förhandsversion  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Stöd för versionshantering Resource Provider via Azure Resource Manager API: er |