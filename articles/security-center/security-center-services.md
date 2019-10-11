---
title: Tillgängliga funktioner som stöds i Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över tjänster som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: ed231bc05d58a40c93cea74081c027e5b49f5306
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254501"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funktioner som stöds i Azure Security Center

> [!NOTE]
>Vissa funktioner är bara tillgängliga med standard nivån. Om du inte redan har registrerat dig för Security Center standard nivån finns en kostnads fri utvärderings period. Mer information finns på sidan med [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

I följande avsnitt visas Security Center funktioner som är tillgängliga för [plattformar som stöds](security-center-os-coverage.md).

* [Virtuella datorer/servrar](#vm-server-features)
* [PaaS-tjänster](#paas-services)


## Funktioner som stöds av virtuella datorer/servrar<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Prissättning|
|----|----|----|----|----|----|----|----|
|**Miljö**|**Azure**||**Icke-Azure**|**Azure**||**Icke-Azure**||
||**Virtuell dator**|**Skalnings uppsättning för virtuell dator**||**Virtuell dator**|**Skalnings uppsättning för virtuell dator**|
|[Microsoft Defender ATP-integrering](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (i versioner som stöds)|✔ (i versioner som stöds)|✔|X|X|X|Standard|
|[Aviseringar om hot identifiering för virtuella datorer med beteende analys](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (i versioner som stöds)|✔ (i versioner som stöds)|✔|Rekommendationer (kostnads fri) hot identifiering (standard)|
|[Aviseringar om filbaserad hot identifiering](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standard|
|[Nätverksbaserade hot identifierings aviseringar](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standard|
|[Just-in-Time VM-åtkomst](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standard|
|[Övervakning av fil integritet](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standard|
|[Nätverks karta](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standard|
|[Anpassad nätverks härdning](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standard|
|Anpassningsbara nätverks kontroller|✔|✔|X|✔|✔|X|Standard|
|[Instrument panel för regelefterlevnad & rapporter](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standard|
|Rekommendationer och hot identifiering på Docker-värdbaserade IaaS behållare|X|X|X|✔|✔|✔|Standard|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|✔|✔|✔|Kostnadsfri|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|✔|✔|✔|Kostnadsfri|
|[Endpoint Protection-utvärdering](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Kostnadsfri|
|Disk krypterings bedömning|✔|✔|X|✔|✔|X|Kostnadsfri|
|Sårbarhets bedömning från tredje part|✔|X|X|✔|X|X|Kostnadsfri|
|[Nätverks säkerhets utvärdering]()|✔|✔|X|✔|✔|X|Kostnadsfri|

### Slut punkts skydds lösningar som stöds<a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om en Endpoint Protection-lösning från den här listan identifieras rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Endpoint Protection utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Endpoint Protection| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft-programvara mot skadlig kod)| Windows Server 2016| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (se anmärkning nedan) | Via tillägg | Ja |
| Trend Micro – alla versioner * | Windows Server-familjen  | Nej | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Linux Server-serien  | Nej | Ja **\*** |
| Sophos v9 +| Linux Server-serien  | Nej | Ja **\***  |

 **\*** Täcknings tillstånd och kompletterande data är för närvarande bara tillgängligt i Log Analytics arbets ytan som är kopplad till dina skyddade prenumerationer. Den visas inte i Azure Security Center portalen.

> [!NOTE]
>
> - Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell 3,0 (eller en övre version).
> - Identifiering av Trend Micro Protection stöds för djup säkerhets agenter.  OfficeScan-agenter stöds inte.


## Funktioner <a name="paas-services"></a> som stöds av PaaS Services

Följande PaaS-resurser stöds av Azure Security Center:

|Tjänst|Rekommendationer (kostnads fri)|Hot identifiering (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Blobb-lagring|✔| ✔|
|Lagringskonto|✔| Ej tillämpligt|
|App Service|✔| ✔|
|Funktion|✔| X|
|Molntjänst|✔| X|
|VNet|✔| Ej tillämpligt|
|Undernät|✔| Ej tillämpligt|
|NIC|✔| Ej tillämpligt|
|NSG|✔| Ej tillämpligt|
|Prenumeration|✔ * *| ✔|
|Batch-konto|✔| X|
|Service Fabric-konto|✔| X|
|Automation-konto|✔| X|
|Load Balancer|✔| X|
|Sök|✔| X|
|Service Bus-namnrymd|✔| X|
|Stream analytics|✔| X|
|Namnområde för händelsehubb|✔| X|
|Logikappar|✔| X|
|Redis|✔| Ej tillämpligt|
|Data Lake Analytics|✔| X|
|Datasjölagring|✔| X|
|Nyckelvalv|✔| X|

\* dessa funktioner stöds för närvarande i offentlig för hands version.

\* @ no__t-1 Azure Active Directory (Azure AD) rekommendationer är bara tillgängliga för standard prenumerationer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data och Log Analytics agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig att [planera och förstå design överväganden för att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Granska de [plattformar som har stöd för Security Center](security-center-os-coverage.md).
- Lär dig mer om [hot identifiering för virtuella datorer & servrar i Azure Security Center](security-center-alerts-iaas.md).
- Hitta [vanliga frågor om hur du använder Azure Security Center](security-center-faq.md).
- Hitta [blogg inlägg om säkerhet och efterlevnad i Azure](https://blogs.msdn.com/b/azuresecurity/).
