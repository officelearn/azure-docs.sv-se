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
ms.date: 5/02/2019
ms.author: monhaber
ms.openlocfilehash: 4e334af353e1d9556641914056ab0a3071ca361e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233592"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plattformar och funktioner som stöds av Azure Security Center

Tillstånd säkerhetsövervakning och rekommendationer är tillgängliga för virtuella datorer (VM), som skapats med hjälp av både klassiska och Resource Manager distributionsmodellerna och datorer.

> [!NOTE]
> Läs mer om den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
>
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
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plattformar som stöds för Linux-datorer och virtuella datorer
Följande Linux-operativsystem som stöds:

* Version för Ubuntu 12.04 LTS, 14.04 LTS och 16.04 LTS.
* Debian version 6, 7, 8 och 9.
* CentOS 5, 6 och 7.
* Red Hat Enterprise Linux (RHEL) version 5, 6 och 7.
* SUSE Linux Enterprise Server (SLES) version 11 och 12.
* Oracle Linux-versioner 5, 6 och 7.
* Amazon Linux 2012.09 till 2017.
* OpenSSL 1.1.0 stöds bara på 64-bitars x86_64 plattformar.

## <a name="vms-and-cloud-services"></a>Virtuella datorer och molntjänster
Virtuella datorer som körs i en molntjänst stöds också. Endast cloud services webb- och worker-roller som körs i produktionsplatser övervakas. Mer information om molntjänster finns [översikt av Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>IaaS-funktioner som stöds

> [!div class="mx-tableFixed"]
> 

|Server |Windows||Linux||
|----|----|----|----|----|
|Miljö|Azure|Icke-Azure|Azure|Icke-Azure|
|VMBA hotidentifieringsaviseringar|✔|✔|✔ (på versioner som stöds)|✔|
|Nätverksbaserade hotidentifieringsaviseringar|✔|X|✔|X|
|Windows Defender ATP-integrering|✔ (på versioner som stöds)|✔|X|X|
|Saknade uppdateringar|✔|✔|✔|✔|
|Säkerhetskonfigurationer|✔|✔|✔|✔|
|Slutpunktsskydd|✔|✔|X|X|
|JIT VM-åtkomst|✔|X|✔|X|
|Anpassningsbara programkontroller|✔|✔|✔|✔|
|FIM|✔|✔|✔|✔|
|Diskkryptering|✔|X|✔|X|
|Tredjeparts-distribution|✔|X|✔|X|
|Nätverkssäkerhetsgrupper (NSG)|✔|X|✔|X|
|Fileless hotidentifiering|✔|✔|X|X|
|Nätverkskarta|✔|X|✔|X|
|Anpassningsbar nätverkskontroller|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>Stöds slutpunktsskyddslösningar

I följande tabell innehåller en matris med:
 - Om du kan använda Azure Security Center för att installera varje lösning för dig.
 - Som endpoint protection lösningar Security Center kan identifiera. Om någon av följande lösningar för endpoint protection upptäcks rekommenderar Security Center inte installera något.

| Endpoint Protection| Plattformar | Installation av Security Center | Security Center Discovery |
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
>
>

## <a name="supported-paas-features"></a>PaaS-funktioner som stöds 


|Tjänst|Rekommendationer|Hotidentifiering|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob storage-konton *|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNets|✔| Saknas|
|Undernät|✔| Saknas|
|Nätverkskort|✔| ✔|
|Nätverkssäkerhetsgrupper (NSG)|✔| Saknas|
|Prenumeration|✔| ✔|

\* Dessa funktioner stöds för närvarande i offentlig förhandsversion. 



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [planera och vad designöverväganden för att börja använda Azure Security Center](security-center-planning-and-operations-guide.md).
- Läs mer om [beteendeanalys av virtuell dator och krascher dumpa minnesanalyser i Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Hitta [vanliga frågor om hur du använder Azure Security Center](security-center-faq.md).
- Hitta [blogginlägg om Azure-säkerhet och efterlevnad](https://blogs.msdn.com/b/azuresecurity/).
