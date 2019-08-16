---
title: Funktioner och plattformar som stöds av Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över funktioner och plattformar som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 9a11af7e2875c9af5cf4b08d459bc67b55dbdcf3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515546"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plattformar och funktioner som stöds av Azure Security Center

Övervakning och rekommendationer för säkerhets tillstånd är tillgängliga för virtuella datorer (VM) som skapats med hjälp av både klassiska distributions modeller för och Resource Manager och datorer.

> [!NOTE]
> Läs mer om de [klassiska distributions modellerna för och Resource Manager](../azure-classic-rm.md) för Azure-resurser.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plattformar som stöder data insamlings agenten 

I det här avsnittet visas vilka plattformar som Azure Security Center agenten kan köra och från vilken den kan samla in data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plattformar som stöds för Windows-datorer och virtuella datorer
Följande Windows-operativ system stöds:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integrering med Windows Defender ATP stöder endast Windows Server 2012 R2 och Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plattformar som stöds för Linux-datorer och virtuella datorer

Följande Linux-operativ system stöds:

> [!NOTE]
> Eftersom listan över Linux-operativsystem som stöds ständigt ändras kan du klicka [här](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) om du vill visa den senaste listan över versioner som stöds, om det har gjorts ändringar sedan det här avsnittet publicerades senast.

64-bitars
* CentOS 6 och 7
* Amazon Linux 2017,09
* Oracle Linux 6 och 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS och 18,04 LTS
* SUSE Linux Enterprise Server 12

32-bitars
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14,04 LTS och 16,04 LTS

## <a name="vms-and-cloud-services"></a>Virtuella datorer och Cloud Services
Virtuella datorer som körs i en moln tjänst stöds också. Endast webb-och arbets roller för moln tjänster som körs i produktions platser övervakas. Mer information om moln tjänster finns i [Översikt över Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>IaaS funktioner som stöds

> [!div class="mx-tableFixed"]
> 

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

### <a name="supported-endpoint-protection-solutions"></a>Slut punkts skydds lösningar som stöds

Följande tabell innehåller en matris med:
 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om någon av dessa slut punkts skydds lösningar upptäcks rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Endpoint Protection utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Endpoint Protection| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft-programvara mot skadlig kod)| Windows Server 2016| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (se anmärkning nedan) | Via tillägg | Ja |
| Trend Micro – Alla versioner | Windows Server-familjen  | Nej | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Linux Server-serien  | Nej | Ja **\*** |
| Sophos v9 +| Linux Server-serien  | Nej | Ja **\***  |

 **\*** Täcknings tillstånd och kompletterande data är för närvarande endast tillgängligt i Log Analytics arbets ytan som är kopplad till dina skyddade prenumerationer och återspeglas inte i Azure Security Center Portal.

> [!NOTE]
> - Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell 3,0 (eller en övre version).

## <a name="supported-paas-features"></a>PaaS funktioner som stöds


|Tjänsten|Rekommendationer (kostnads fri)|Hot identifiering (standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob Storage-konton|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNets|✔| Ej tillämpligt|
|Undernät|✔| Ej tillämpligt|
|Nätverkskort|✔| Ej tillämpligt|
|Nätverkssäkerhetsgrupper (NSG)|✔| Ej tillämpligt|
|Subscription|✔ * *| ✔|
|Batch|✔| Ej tillämpligt|
|Service fabric|✔| Ej tillämpligt|
|Automation-konto|✔| Ej tillämpligt|
|Lastbalanserare|✔| Ej tillämpligt|
|Search|✔| Ej tillämpligt|
|Service Bus|✔| Ej tillämpligt|
|Stream Analytics|✔| Ej tillämpligt|
|Händelsehub|✔| Ej tillämpligt|
|Logikappar|✔| Ej tillämpligt|
|Lagringskonto|✔| Ej tillämpligt|
|Redis|✔| Ej tillämpligt|
|Data Lake Analytics|✔| Ej tillämpligt|
|Nyckelvalv|✔| Ej tillämpligt|




\*Dessa funktioner stöds för närvarande i offentlig för hands version.

\*\*AAD-rekommendationer är bara tillgängliga för standard prenumerationer



## <a name="next-steps"></a>Nästa steg

- Lär dig att [planera och förstå design överväganden för att anta Azure Security Center](security-center-planning-and-operations-guide.md).
- Lär dig mer om [hot identifiering för virtuella datorer & servrar i Azure Security Center](security-center-alerts-iaas.md).
- Hitta [vanliga frågor om hur du använder Azure Security Center](security-center-faq.md).
- Hitta [blogg inlägg om säkerhet och efterlevnad i Azure](https://blogs.msdn.com/b/azuresecurity/).
