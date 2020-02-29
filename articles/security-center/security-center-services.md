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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 14c54028dacb545532b540ae8ff79fc3dc5356bc
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921274"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funktioner som stöds i Azure Security Center

> [!NOTE]
>Vissa funktioner är bara tillgängliga med standard nivån. Om du inte redan har registrerat dig för Security Center standard nivån finns en kostnads fri utvärderings period. Mer information finns på sidan med [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

I följande avsnitt visas Security Center funktioner som är tillgängliga för [plattformar som stöds](security-center-os-coverage.md).

* [Virtuella datorer/servrar](#vm-server-features)
* [PaaS-tjänster](#paas-services)


## Funktioner som stöds av virtuella datorer/servrar<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure-Virtual Machine Scale Sets**|**Datorer som inte är Azure-datorer**|**Prissättning**
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Standard|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](threat-protection.md)|✔|✔|✔|Rekommendationer (kostnads fri) </br></br> Säkerhets aviseringar (standard)|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Nätverksbaserade säkerhets aviseringar](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-Time VM-åtkomst](security-center-just-in-time.md)|✔|-|-|Standard|
|[Ursprunglig sårbarhets bedömning](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Övervakning av fil integritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Anpassad nätverks härdning](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Anpassningsbara nätverks kontroller|✔|✔|-|Standard|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|-|-|-|Standard|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Kostnadsfri|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Kostnadsfri|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Kostnadsfri|
|Disk krypterings bedömning|✔|✔|-|Kostnadsfri|
|Sårbarhets bedömning från tredje part|✔|-|-|Kostnadsfri|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Kostnadsfri|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Azure Virtual Machines**|**Azure-Virtual Machine Scale Sets**|**Datorer som inte är Azure-datorer**|**Prissättning**
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|-|-|-|Standard|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](security-center-alerts-iaas.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Rekommendationer (kostnads fri) </br></br> Säkerhets aviseringar (standard)|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Nätverksbaserade säkerhets aviseringar](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-Time VM-åtkomst](security-center-just-in-time.md)|✔|-|-|Standard|
|[Ursprunglig sårbarhets bedömning](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Övervakning av fil integritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Anpassad nätverks härdning](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Anpassningsbara nätverks kontroller|✔|✔|-|Standard|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|✔|✔|✔|Standard|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Kostnadsfri|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Kostnadsfri|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Kostnadsfri|
|Disk krypterings bedömning|✔|✔|-|Kostnadsfri|
|Sårbarhets bedömning från tredje part|✔|-|-|Kostnadsfri|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Kostnadsfri|

--- 

## Slut punkts skydds lösningar som stöds<a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om en Endpoint Protection-lösning från den här listan identifieras rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Endpoint Protection utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Slutpunktsskydd| Plattformar | Installation av Security Center | Security Center Discovery |
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
> - Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell 3,0 (eller en övre version).
> - Identifiering av Trend Micro Protection stöds för djup säkerhets agenter.  OfficeScan-agenter stöds inte.


## Funktioner <a name="paas-services"></a> som stöds av PaaS Services

Följande PaaS-resurser stöds av Azure Security Center:

|Tjänst|Rekommendationer (kostnads fri)|Säkerhets aviseringar (standard)|Sårbarhets bedömning (standard)|
|----|:----:|:----:|:----:|
|SQL Databases|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure-CosmosDB *|-|✔|-|
|Lagringskonton|✔|-|-|
|Blob Storage|✔|✔|-|
|App Service|✔|✔|-|
|Funktionsapp|✔|-|-|
|Cloud Services|✔|-|-|
|Virtual Network|✔|-|-|
|Undernät|✔|-|-|
|NIC|✔|-|-|
|Nätverkssäkerhetsgrupper|✔|-|-|
|Prenumeration|✔ * *|✔|-|
|Batch-konto|✔|-|-|
|Service Fabric konto|✔|-|-|
|Automation-konto|✔|-|-|
|Lastbalanserare|✔|-|-|
|Kognitiv sökning|✔|-|-|
|Service Bus-namnområde|✔|-|-|
|Stream analytics|✔|-|-|
|Namnområde för händelsehubb|✔|-|-|
|Logic Apps|✔|-|-|
|Cache för Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Key Vault|✔|✔ *|-|

\* dessa funktioner stöds för närvarande i för hands versionen.

rekommendationer för \*\* Azure Active Directory (Azure AD) är endast tillgängliga för standard prenumerationer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data och Log Analytics agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig att [planera och förstå design överväganden för att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Granska de [plattformar som har stöd för Security Center](security-center-os-coverage.md).
- Läs mer om [skydd mot hot för Windows-och Linux-datorer i Azure Security Center](threat-protection.md#windows-machines).
- Hitta [vanliga frågor om Azure Security Center](faq-general.md).