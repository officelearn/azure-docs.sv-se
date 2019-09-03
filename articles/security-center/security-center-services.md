---
title: Tillgängliga funktioner som stöds i Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över tjänster som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0d2b417dd01e26f0f93722be66d82972b52aca83
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234587"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funktioner som stöds i Azure Security Center

> [!NOTE]
>Vissa funktioner är bara tillgängliga med standard nivån. Om du inte redan har registrerat dig för Security Center standard nivån finns en kostnads fri utvärderings period. På [prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/) finns det mer information.

I följande avsnitt visas Security Center funktioner som är tillgängliga för [plattformar som stöds](security-center-os-coverage.md).

* [Virtuella datorer/servrar](#vm-server-features)
* [PaaS-tjänster](#paas-services)


## Funktioner som stöds av virtuella datorer/servrar<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Server|Windows|||Linux|||Prissättning|
|----|----|----|----|----|----|----|----|
|**Miljö**|**Azure**||**Icke-Azure**|**Azure**||**Icke-Azure**||
||**Virtuell dator**|**Skalnings uppsättning för virtuell dator**||**Virtuell dator**|**Skalnings uppsättning för virtuell dator**|
|Aviseringar om VMBA hot identifiering|✔|✔|✔|✔ (i versioner som stöds)|✔ (i versioner som stöds)|✔|Rekommendationer (kostnads fri) hot identifiering (standard)|
|Nätverksbaserade hot identifierings aviseringar|✔|✔|X|✔|✔|X|Standard|
|Windows Defender ATP-integrering|✔ (i versioner som stöds)|✔ (i versioner som stöds)|✔|X|X|X|Standard|
|Korrigeringar som saknas|✔|✔|✔|✔|✔|✔|Lediga|
|Säkerhetskonfigurationer|✔|✔|✔|✔|✔|✔|Lediga|
|Endpoint Protection-utvärdering|✔|✔|✔|X|X|X|Lediga|
|JIT VM-åtkomst|✔|X|X|✔|X|X|Standard|
|Anpassningsbara programkontroller|✔|X|✔|✔|X|✔|Standard|
|TJÄNSTDATABASEN|✔|✔|✔|✔|✔|✔|Standard|
|Disk krypterings bedömning|✔|✔|X|✔|✔|X|Lediga|
|Distribution från tredje part|✔|X|X|✔|X|X|Lediga|
|NSG-utvärdering|✔|✔|X|✔|✔|X|Lediga|
|Filbaserad hot identifiering|✔|✔|✔|X|X|X|Standard|
|Nätverks karta|✔|✔|X|✔|✔|X|Standard|
|Anpassningsbara nätverks kontroller|✔|✔|X|✔|✔|X|Standard|
|Instrument panel för regelefterlevnad & rapporter|✔|✔|✔|✔|✔|✔|Standard|
|Rekommendationer och hot identifiering på Docker-värdbaserade IaaS behållare|X|X|X|✔|✔|✔|Standard|

### Slut punkts skydds lösningar som stöds<a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om någon av dessa slut punkts skydds lösningar upptäcks rekommenderar Security Center inte att installera en.

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

 **\*** Täcknings tillstånd och kompletterande data är för närvarande endast tillgängligt i Log Analytics arbets ytan som är kopplad till dina skyddade prenumerationer och återspeglas inte i Azure Security Center Portal.

> [!NOTE]
>
> - Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell 3,0 (eller en övre version).
> - Identifiering av Trend Micro Protection stöds för djup säkerhets agenter.  OfficeScan-agenter stöds inte.


## Funktioner <a name="paas-services"></a> som stöds av PaaS Services

Följande PaaS-resurser stöds av Azure Security Center:

|Tjänsten|Rekommendationer (kostnads fri)|Hot identifiering (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Blob Storage|✔| ✔|
|Lagringskonto|✔| Ej tillämpligt|
|Apptjänst|✔| ✔|
|Funktion|✔| X|
|Molntjänst|✔| X|
|Virtuellt nätverk|✔| Ej tillämpligt|
|Subnet|✔| Ej tillämpligt|
|Nätverkskort|✔| Ej tillämpligt|
|NSG|✔| Ej tillämpligt|
|Subscription|✔ * *| ✔|
|Batch-konto|✔| X|
|Service Fabric-konto|✔| X|
|Automation-konto|✔| X|
|Lastbalanserare|✔| X|
|Search|✔| X|
|service bus namnrymd|✔| X|
|Stream Analytics|✔| X|
|Namnrymd för händelshubb|✔| X|
|Logikappar|✔| X|
|Redis|✔| Ej tillämpligt|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Nyckelvalv|✔| X|

\*Dessa funktioner stöds för närvarande i offentlig för hands version.

\*\*Azure Active Directory (Azure AD) rekommendationer är bara tillgängliga för standard prenumerationer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data och Log Analytics agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig att [planera och förstå design överväganden för att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Granska de [plattformar som har stöd för Security Center](security-center-os-coverage.md).
- Lär dig mer om [hot identifiering för virtuella datorer & servrar i Azure Security Center](security-center-alerts-iaas.md).
- Hitta [vanliga frågor om hur du använder Azure Security Center](security-center-faq.md).
- Hitta [blogg inlägg om säkerhet och efterlevnad i Azure](https://blogs.msdn.com/b/azuresecurity/).
