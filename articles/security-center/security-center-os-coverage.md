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
ms.author: monhaber
ms.openlocfilehash: c5b5b88ee1334ac6d7b39b8ad53bd020e6042454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480534"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plattformar och funktioner som stöds av Azure Security Center

Tillstånd säkerhetsövervakning och rekommendationer är tillgängliga för virtuella datorer (VM), som skapats med hjälp av både klassiska och Resource Manager distributionsmodellerna och datorer.

> [!NOTE]
> Läs mer om den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plattformar som stöder datainsamlingsagenterna 

Det här avsnittet ser du vilka plattformar som Azure Security Center-agenten kan köra och varifrån den kan samla in data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plattformar som stöds för Windows-datorer och virtuella datorer
Följande Windows-operativsystem som stöds:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integrering med Windows Defender ATP stöder endast Windows Server 2012 R2 och Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plattformar som stöds för Linux-datorer och virtuella datorer

Följande Linux-operativsystem som stöds:

> [!NOTE]
> Eftersom listan med Linux-operativsystem som stöds ständigt, om du föredrar, klickar du på [här](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) att visa den senaste listan över versioner som stöds, om det har gjorts ändringar sedan den här artikeln senast publicerades.

64-bitars
* CentOS 6 och 7
* Amazon Linux 2017.09
* Oracle Linux 6 och 7
* Red Hat Enterprise Linux Server 6 och 7
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS och 18.04 LTS
* SUSE Linux Enterprise Server 12

32-bitars
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 och 9
* Ubuntu Linux 14.04 LTS och 16.04 LTS

## <a name="vms-and-cloud-services"></a>Virtuella datorer och molntjänster
Virtuella datorer som körs i en molntjänst stöds också. Endast cloud services webb- och worker-roller som körs i produktionsplatser övervakas. Mer information om molntjänster finns [översikt av Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>IaaS-funktioner som stöds

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||||Prissättning|
|----|----|----|----|----|----|----|----|
|**Miljö**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Virtuell dator**|**Virtual Machine Scale Sets**||**Virtuell dator**|**Virtual Machine Scale Sets**|
|VMBA hotidentifieringsaviseringar|✔|✔|✔|✔ (på versioner som stöds)|✔ (på versioner som stöds)|✔|Hotidentifiering för rekommendationer (kostnadsfri) (Standard)|
|Nätverksbaserade hotidentifieringsaviseringar|✔|✔|X|✔|✔|X|Standard|
|Windows Defender ATP-integrering|✔ (på versioner som stöds)|✔ (på versioner som stöds)|✔|X|X|X|Standard|
|Saknade uppdateringar|✔|✔|✔|✔|✔|✔|Lediga|
|Säkerhetskonfigurationer|✔|✔|✔|✔|✔|✔|Lediga|
|Endpoint protection-utvärdering|✔|✔|✔|X|X|X|Lediga|
|JIT VM-åtkomst|✔|X|X|✔|X|X|Standard|
|Anpassningsbara programkontroller|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Utvärdering av kryptering av disk|✔|✔|X|✔|✔|X|Lediga|
|Tredjeparts-distribution|✔|X|X|✔|X|X|Lediga|
|NSG-utvärdering|✔|✔|X|✔|✔|X|Lediga|
|Fileless hotidentifiering|✔|✔|✔|X|X|X|Standard|
|Nätverkskarta|✔|✔|X|✔|✔|X|Standard|
|Anpassningsbar nätverkskontroller|✔|✔|X|✔|✔|X|Standard|
|Instrumentpanel för efterlevnad av föreskrifter och rapporter|✔|✔|✔|✔|✔|✔|Standard|
|Rekommendationer och hotidentifiering på Docker-värd IaaS-behållare|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Stöds slutpunktsskyddslösningar

I följande tabell innehåller en matris med:
 - Om du kan använda Azure Security Center för att installera varje lösning för dig.
 - Som endpoint protection lösningar Security Center kan identifiera. Om någon av följande lösningar för endpoint protection upptäcks rekommenderar Security Center inte installera något.

Information om när rekommendationerna har genererats för var och en av dessa skydd finns i [Endpoint Protection-utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Slutpunktsskydd| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft-programvara mot skadlig kod)| Windows Server 2016| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (Se kommentaren nedan) | Via tillägg | Ja |
| Trend Micro – Alla versioner | Windows Server-familjen  | Nej | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Windows Server-familjen  | Nej | Ja |
| Kaspersky| Windows Server-familjen  | Nej | Nej  |
| Sophos| Windows Server-familjen  | Nej | Nej  |

> [!NOTE]
> - Identifiering av System Center Endpoint Protection (SCEP) på en Windows Server 2008 R2-dator kräver SCEP installeras efter PowerShell 3.0 (eller en övre version).

## <a name="supported-paas-features"></a>PaaS-funktioner som stöds


|Tjänst|Rekommendationer (kostnadsfri)|Hotidentifiering (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob storage-konton|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNets|✔| Saknas|
|Undernät|✔| Saknas|
|Nätverkskort|✔| Saknas|
|NSG:er|✔| Saknas|
|Prenumeration|✔ **| ✔|
|Batch|✔| Saknas|
|Service fabric|✔| Saknas|
|Automation-konto|✔| Saknas|
|Lastbalanserare|✔| Saknas|
|Search|✔| Saknas|
|Service Bus|✔| Saknas|
|Stream analytics|✔| Saknas|
|Händelsehubb|✔| Saknas|
|Logikappar|✔| Saknas|
|Lagringskonto|✔| Saknas|
|Redis|✔| Saknas|
|Data lake analytics|✔| Saknas|
|Nyckelvalv|✔| Saknas|




\* Dessa funktioner stöds för närvarande i offentlig förhandsversion.

\*\* AAD-rekommendationer är bara tillgängliga för standardprenumerationer



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [planera och vad designöverväganden för att börja använda Azure Security Center](security-center-planning-and-operations-guide.md).
- Läs mer om [beteendeanalys av virtuell dator och krascher dumpa minnesanalyser i Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Hitta [vanliga frågor om hur du använder Azure Security Center](security-center-faq.md).
- Hitta [blogginlägg om Azure-säkerhet och efterlevnad](https://blogs.msdn.com/b/azuresecurity/).
