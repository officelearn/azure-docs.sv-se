---
title: Funktioner och plattformar som stöds av Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över funktioner och plattformar som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319990"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plattformar och funktioner som stöds av Azure Security Center

Tillstånd säkerhetsövervakning och rekommendationer är tillgängliga för virtuella datorer (VM), som skapats med hjälp av både klassiska och Resource Manager distributionsmodellerna och datorer.

> [!NOTE]
> Läs mer om den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
>
>

## <a name="supported-platforms"></a>Plattformar som stöds 

Det här avsnittet ser du vilka plattformar som Azure Security Center-agenten kan köra och varifrån den kan samla in data.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plattformar som stöds för Windows-datorer och virtuella datorer
Operativsystem som stöds Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plattformar som stöds för Linux-datorer och virtuella datorer
Operativsystem som stöds Linux:

* Versioner för Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS
* Debian versioner 6, 7, 8, 9
* CentOS version 5, 6, 7
* Red Hat Enterprise Linux (RHEL) version 5, 6, 7
* SUSE Linux Enterprise Server (SLES) versioner 11, 12
* Oracle Linux-versioner 5, 6, 7
* Amazon Linux 2012.09 till 2017
* Openssl 1.1.0 stöds bara på x86_64 plattformar (64-bitars)

> [!NOTE]
> Beteendeanalys för virtuell dator är ännu inte tillgängliga för Linux-operativsystem.
>
>

## <a name="vms-and-cloud-services"></a>Virtuella datorer och molntjänster
Virtuella datorer som körs i en molntjänst stöds också. Endast molntjänster web och worker-roller som körs i produktionsmiljön fack övervakas. Läs mer om Molntjänsten i [översikt över Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>IaaS-funktioner som stöds

> [!div class="mx-tableFixed"]
> 

|Server|Windows||Linux||
|----|----|----|----|----|
|Miljö|Azure|Icke-Azure|Azure|Icke-Azure|
|VMBA hotidentifieringsaviseringar|✔|✔|✔ (på versioner som stöds)|✔|
|Nätverksbaserade hotidentifieringsaviseringar|✔|X|✔|X|
|Windows Defender ATP-integrering *|✔ (på versioner som stöds)|✔|X|X|
|Saknade uppdateringar|✔|✔|✔|✔|
|Säkerhetskonfigurationer|✔|✔|✔|✔|
|Mot skadlig kod|✔|✔|X|X|
|JIT VM-åtkomst|✔|X|✔|X|
|Anpassningsbara programkontroller|✔ (endast Azure)|X|X|X|
|FIM|✔|✔|✔|✔|
|Diskkryptering|✔|X|✔|X|
|Tredjeparts-distribution|✔|X|✔|X|
|NSG:er|✔|X|✔|X|
|Filess V1|✔|✔|X|X|
|Nätverkskarta|✔|X|✔|X|
|Anpassningsbar nätverk appstyrning|✔|X|✔|X|

* Dessa funktioner stöds för närvarande i offentlig förhandsversion.


## <a name="supported-paas-features"></a>PaaS-funktioner som stöds


|Tjänst|Rekommendationer|Hotidentifiering|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL *|✔| ✔|
|MySQL *|✔| ✔|
|BLOB storage-konton *|✔| ✔|
|App Services|✔| ✔|
|Molntjänster|✔| X|
|Redis Cache|✔| X|
|Service fabric|✔| X|
|Azure-automatisering|✔| X|
|Data Lake |✔| X|
|Nyckelvalv|✔| X|
|Service bus|✔| X|
|Stream analytics|✔| X|
|Batch|✔| X|
|Logikappar|✔| X|
|Virtuella nätverk|✔| Ej tillämpligt|
|Undernät|✔| Ej tillämpligt|
|Nätverkskort|✔| ✔|
|NSG:er|✔| Ej tillämpligt|
|Prenumeration|✔| ✔|

* Dessa funktioner stöds för närvarande i offentlig förhandsversion.

## <a name="next-steps"></a>Nästa steg

- [Azure Security Center planerings- och bruksanvisning](security-center-planning-and-operations-guide.md) – Lär dig hur du planerar och designaspekter att införa Azure Security Center
- [Säkerhetsaviseringar per typ i Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – Lär dig mer om beteendeanalys av virtuell dator och kraschanalys dump minne i Security Center
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – hittar du blogginlägg om Azure-säkerhet och regelefterlevnad
