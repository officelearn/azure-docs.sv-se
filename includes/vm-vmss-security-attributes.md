---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800281"
---
## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Kryptering i vila (t.ex kryptering på serversidan, kryptering på serversidan med Kundhanterade nycklar och andra krypteringsfunktioner) | Ja | Se [hur du krypterar en Linux-dator i Azure](/azure/virtual-machines/linux/encrypt-disks.md) och [kryptera virtuella diskar på en virtuell Windows-dator](/azure/virtual-machines/windows/encrypt-disks.md). |
| Kryptering under överföring (till exempel ExpressRoute-kryptering, i virtuella nätverk, och kryptering för VNet-VNet)| Ja | Azure virtuella datorer stöder [ExpressRoute](/azure/expressroute) och VNET-kryptering. Se [-kryptering för virtuella datorer under överföring](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Ja | Kundhanterade nycklar är ett scenario som stöds Azure kryptering; Se [översikt över Azure kryptering](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Kolumnen filnivåkryptering (Azure-datatjänster)| Gäller inte | |
| API-anrop som är krypterad| Ja | Via HTTPS och SSL. |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Ja | |
| VNet-stöd för inmatning| Ja | . |
| Stöd för isolering av nätverk och Firewalling| Ja |  |
| Tvingad tunneltrafik support| Ja | Se [konfigurera Tvingad tunneltrafik med hjälp av Azure Resource Manager-distributionsmodellen](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Ja | Se [övervaka och uppdatera en Linux-dator i Azure](/azure/virtual-machines/linux/tutorial-monitoring.md) och [övervaka och uppdatera en Windows-dator i Azure](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Authentication| Ja |  |
| Authorization| Ja |  |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja |  |
| Data plan loggning och granskning | Nej |  |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja |  | 
