---
title: Funktioner som stöds i Azure Security Center | Microsoft-dokument
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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245244"
---
# <a name="feature-coverage-for-machines"></a>Funktionstäckning för maskiner

Tabellerna nedan visar Azure Security Center-funktioner som är tillgängliga för virtuella datorer och servrar.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funktioner som stöds för virtuella datorer och servrar<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows-maskiner](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funktion**|**Virtuella Azure-datorer**|**Azure Virtual Machine Scale Sets**|**Datorer som inte är Azure**|**Prissättning**
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|✔</br>(på versioner som stöds)|✔</br>(på versioner som stöds)|✔|Standard|
|[Beteendeanalys för virtuella datorer (och säkerhetsvarningar)](threat-protection.md)|✔|✔|✔|Rekommendationer (Gratis) </br></br> Säkerhetsvarningar (standard)|
|[Fillösa säkerhetsvarningar](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Nätverksbaserade säkerhetsvarningar](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Åtkomst till just-in-time-datorer](security-center-just-in-time.md)|✔|-|-|Standard|
|[Inbygg sårbarhetsbedömning](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Övervakning av filintegritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Nätverkskarta](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptiv nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Anpassningsbara nätverkskontroller|✔|✔|-|Standard|
|[Instrumentpanel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Rekommendationer och hotskydd på Docker-värd IaaS behållare|-|-|-|Standard|
|Bedömning av os-patchar saknas|✔|✔|✔|Kostnadsfri|
|Bedömning av felkonfigurationer för säkerhet|✔|✔|✔|Kostnadsfri|
|[Bedömning av skydd av slutpunkt](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Kostnadsfri|
|Bedömning av diskkryptering|✔|✔|-|Kostnadsfri|
|Sårbarhetsbedömning från tredje part|✔|-|-|Kostnadsfri|
|[Bedömning av nätverkssäkerhet](security-center-network-recommendations.md)|✔|✔|-|Kostnadsfri|


### <a name="linux-machines"></a>[Linux-maskiner](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funktion**|**Virtuella Azure-datorer**|**Azure Virtual Machine Scale Sets**|**Datorer som inte är Azure**|**Prissättning**
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|-|-|-|Standard|
|[Beteendeanalys för virtuella datorer (och säkerhetsvarningar)](security-center-alerts-iaas.md)|✔</br>(på versioner som stöds)|✔</br>(på versioner som stöds)|✔|Rekommendationer (Gratis) </br></br> Säkerhetsvarningar (standard)|
|[Fillösa säkerhetsvarningar](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Nätverksbaserade säkerhetsvarningar](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Åtkomst till just-in-time-datorer](security-center-just-in-time.md)|✔|-|-|Standard|
|[Inbygg sårbarhetsbedömning](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Övervakning av filintegritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Nätverkskarta](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Adaptiv nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Anpassningsbara nätverkskontroller|✔|✔|-|Standard|
|[Instrumentpanel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Rekommendationer och hotskydd på Docker-värd IaaS behållare|✔|✔|✔|Standard|
|Bedömning av os-patchar saknas|✔|✔|✔|Kostnadsfri|
|Bedömning av felkonfigurationer för säkerhet|✔|✔|✔|Kostnadsfri|
|[Bedömning av skydd av slutpunkt](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Kostnadsfri|
|Bedömning av diskkryptering|✔|✔|-|Kostnadsfri|
|Sårbarhetsbedömning från tredje part|✔|-|-|Kostnadsfri|
|[Bedömning av nätverkssäkerhet](security-center-network-recommendations.md)|✔|✔|-|Kostnadsfri|

--- 


> [!TIP]
>Om du vill experimentera med funktioner som bara är tillgängliga på standardprisnivån kan användare på den kostnadsfria nivån registrera sig i en 30-dagars utvärderingsversion. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Lösningar för slutpunktsskydd som stöds<a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka slutpunktsskyddslösningar Security Center kan identifiera. Om en slutpunktsskyddslösning från den här listan identifieras rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Bedömning och rekommendationer för slutpunktsskydd](security-center-endpoint-protection.md).

| Slutpunktsskydd| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft-programvara mot skadlig kod)| Windows Server 2016| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (se anmärkning nedan) | Via tillägg | Ja |
| Trend Micro – Alla versioner* | Windows Server-familjen  | Inga | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Inga | Ja |
| McAfee v10 + | Windows Server-familjen  | Inga | Ja |
| McAfee v10 + | Linux Server Familj  | Inga | Ja**\*** |
| Sophos V9+| Linux Server Familj  | Inga | Ja**\***  |

 **\*** Täckningstillståndet och stöddata är för närvarande endast tillgängliga på arbetsytan Log Analytics som är associerad med dina skyddade prenumerationer. Det återspeglas inte i Azure Security Center-portalen.

> [!NOTE]
> - Identifiering av SCEP (System Center Endpoint Protection) på en virtuell Windows Server 2008 R2-dator kräver att SCEP installeras efter PowerShell 3.0 (eller en övre version).
> - Detektion av Trend Micro-skydd stöds för Deep Security-agenter.  OfficeScan-agenter stöds inte.


## <a name="next-steps"></a>Nästa steg

- Läs om hur [Security Center samlar in data och Log Analytics Agent](security-center-enable-data-collection.md).
- Läs om hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig hur du [planerar och förstår designövervägandena om att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Granska [de plattformar som stöder säkerhetscenter](security-center-os-coverage.md).
- Läs mer om [hotskydd för Windows- och Linux-datorer i Azure Security Center](threat-protection.md#windows-machines).
- Hitta [vanliga frågor och svar om Azure Security Center](faq-general.md).