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
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: a311439e8efc5481fbfd7431c1514ba6be576e39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858021"
---
# <a name="feature-coverage-for-machines"></a>Funktions täckning för datorer

De två flikarna nedan visar funktionerna i Azure Security Center som är tillgängliga för virtuella Windows-och Linux-datorer och-servrar.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funktioner som stöds för virtuella datorer och servrar <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-datorer**](#tab/features-windows)

|**Funktion**|**Azure Virtual Machines**|**Skalningsuppsättningar för virtuella Azure-datorer**|**Datorer som inte är Azure-datorer**|**Azure Defender krävs**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Ja|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](alerts-reference.md)|✔|✔|✔|Ja|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Nätverksbaserade säkerhets aviseringar](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-åtkomst till virtuella datorer](security-center-just-in-time.md)|✔|-|-|Ja|
|[Ursprunglig sårbarhets bedömning](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Ja|
|[Övervakning av filintegritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|-|-|-|Ja|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Azure: Nej<br><br>Icke-Azure: Ja|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Azure: Nej<br><br>Icke-Azure: Ja|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nej<br><br>Icke-Azure: Ja|
|Disk krypterings bedömning|✔</br>(för [scenarier som stöds](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Inga|
|Sårbarhets bedömning från tredje part|✔|-|-|Inga|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Inga|


### <a name="linux-machines"></a>[**Linux-datorer**](#tab/features-linux)

|**Funktion**|**Azure Virtual Machines**|**Skalningsuppsättningar för virtuella Azure-datorer**|**Datorer som inte är Azure-datorer**|**Azure Defender krävs**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|-|-|-|Ja|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](security-center-alerts-iaas.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Ja|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|-|-|-|Ja|
|[Nätverksbaserade säkerhets aviseringar](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-åtkomst till virtuella datorer](security-center-just-in-time.md)|✔|-|-|Ja|
|[Ursprunglig sårbarhets bedömning](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Ja|
|[Övervakning av filintegritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|✔|✔|✔|Ja|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Azure: Nej<br><br>Icke-Azure: Ja|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Azure: Nej<br><br>Icke-Azure: Ja|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Inga|
|Disk krypterings bedömning|✔</br>(för [scenarier som stöds](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Inga|
|Sårbarhets bedömning från tredje part|✔|-|-|Inga|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Inga|

--- 


> [!TIP]
>Om du vill experimentera med funktioner som bara är tillgängliga med Azure Defender kan du registrera dig för en 30-dagars utvärderings version. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Slut punkts skydds lösningar som stöds <a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om en Endpoint Protection-lösning från den här listan identifieras rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Endpoint Protection utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Slutpunktsskydd| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 eller senare| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (se anmärkning nedan) | Via tillägg | Ja |
| Trend Micro – djup säkerhet | Windows Server-familjen  | Inga | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Inga | Ja |
| McAfee v10 + | Windows Server-familjen  | Inga | Ja |
| McAfee v10 + | Linux Server-serien  | Inga | Ja **\*** |
| Sophos v9 +| Linux Server-serien  | Inga | Ja  **\***  |

 **\*** Täcknings tillstånd och kompletterande data är för närvarande bara tillgängligt i Log Analytics arbets ytan som är kopplad till dina skyddade prenumerationer. Den visas inte i Azure Security Center portalen.

> [!NOTE]
> Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell (v 3.0 eller senare).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data med hjälp av Log Analytics-agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Granska de [plattformar som har stöd för Security Center](security-center-os-coverage.md).
