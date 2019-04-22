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
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59007621"
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
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Nej |  |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Nej |  |
| API-anrop som är krypterad| Ja |  |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Nej |  |
| vNET-stöd för inmatning| Nej |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | Tvingad tunneltrafik har stöd för säkerhetskopiering av virtuella datorer. Tvingad tunneltrafik finns inte stöd för arbetsbelastningar som körs på virtuella datorer. |
| Stöd för Tvingad tunneltrafik | Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Log Analytics stöds via diagnostikloggar. Se övervakaren Azure Backup skyddade arbetsbelastningar med hjälp av Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) för mer information. |

### <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Autentisering är via Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja | Kunden har skapat och inbyggda RBAC-roller används. Se Use Role-Based Access Control för att hantera Azure Backup återställningspunkter (/ azure/backup/backup-rbac-rs-valv) för mer information. |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning| Ja | Alla åtgärder som kund som utlöses från Azure portal loggas aktivitetsloggar. |
| Data-dataplaner loggning och granskning| Nej | Azure Backup-dataplanet kan inte nås direkt.  |

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
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Kunden styr alla nycklar i sina Key Vault. När modul (HSM) som backas upp av maskinvara säkerhetsnycklar anges, skyddar en FIPS nivå 2 HSM nyckeln, certifikat eller hemligheten. |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Med hjälp av HTTPS. |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja | Med tjänstslutpunkter i virtuella nätverk (Vnet). |
| vNET-stöd för inmatning| Nej |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | Med hjälp av brandväggsregler för virtuellt nätverk. |
| Stöd för Tvingad tunneltrafik | Nej |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Använda Log Analytics. |

### <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Autentisering är via Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja | Med hjälp av Nyckelvalvets åtkomstprincip. |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering dataplaner loggning och granskning| Ja | Använda Log Analytics. |
| Data-dataplaner loggning och granskning| Ja | Använda Log Analytics. |

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
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Kunden äger klustret och skalningsuppsättningen för virtuell dator (VM) som klustret bygger på. Azure-diskkryptering kan aktiveras på virtuella datorns skalningsuppsättning. |
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja | Service Fabric-API-anrop görs via Azure Resource Manager. Det krävs en giltig JSON webbtoken (JWT). |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja |  |
| vNET-stöd för inmatning| Ja |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | Med hjälp av nätverk nätverkssäkerhetsgrupper (NSG). |
| Stöd för Tvingad tunneltrafik | Ja | Azure-nätverket innehåller Tvingad tunneltrafik. |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Med hjälp av Azure-övervakning stöd och stöd från tredje part. |

### <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Autentisering är via Azure Active Directory. |
| Åtkomsthantering - auktorisering| Ja | Identitets- och åtkomsthantering (IAM) för anrop via SFRP. Anrop direkt till slutpunkten för klustret har stöd för två roller: Användare och administratör. Kunden kan mappa API: erna till någon av rollerna. |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning| Ja | Alla kontrollplanåtgärder kör via processer för att granska och godkännanden. |
| Data-dataplaner loggning och granskning| Gäller inte | Kunden äger klustret.  |

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
| Hantering av kryptering nyckel (CMK, BYOK osv.)| Ja | Se [kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Kolumnen Filnivåkryptering (Azure-datatjänster)| Gäller inte |  |
| API-anrop som är krypterad| Ja |  |

### <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för tjänstslutpunkt| Ja |  |
| vNET-stöd för inmatning| Gäller inte |  |
| Nätverksisolering / brandväggsfunktioner support| Ja | |
| Stöd för Tvingad tunneltrafik | Gäller inte |  |

### <a name="detection"></a>Identifiering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Azure Monitor Metrics som är tillgängliga nu loggar från förhandsversion |

### <a name="iam-support"></a>IAM-stöd

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Åtkomsthantering - autentisering| Ja | Azure Active Directory delad nyckel, delad åtkomst-token. |
| Åtkomsthantering - auktorisering| Ja | Stöd för auktorisering via RBAC, POSIX-ACL: er och SAS-token |


### <a name="audit-trail"></a>Spårning

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering planera loggning och granskning | Ja | Azure Resource Manager-aktivitetslogg |
| Data-dataplaner loggning och granskning| Ja | Diagnostikloggar för tjänsten och Azure Monitor-loggning från förhandsversion  |

### <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja | Stöd för versionshantering Resource Provider via Azure Resource Manager API: er |