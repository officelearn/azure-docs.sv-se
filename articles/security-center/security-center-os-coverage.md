---
title: Plattformar som stöds i Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över Windows och Linux operatings-system som stöds i Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: terrylan
ms.openlocfilehash: 54d173caa0e3eb4bbd8bda7c924e56d546a99662
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297510"
---
# <a name="supported-platforms-in-azure-security-center"></a>Plattformar som stöds i Azure Security Center
Tillstånd säkerhetsövervakning och rekommendationer är tillgängliga för virtuella datorer (VM), som skapats med hjälp av både klassiska och Resource Manager distributionsmodellerna och datorer.

> [!NOTE]
> Läs mer om den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Plattformar som stöds för Windows-datorer och virtuella datorer
Operativsystem som stöds Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Plattformar som stöds för Linux-datorer och virtuella datorer
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

## <a name="next-steps"></a>Nästa steg

- [Azure Security Center planerings- och bruksanvisning](security-center-planning-and-operations-guide.md) – Lär dig hur du planerar och designaspekter att införa Azure Security Center
- [Säkerhetsaviseringar per typ i Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) – Lär dig mer om beteendeanalys av virtuell dator och kraschanalys dump minne i Security Center
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – hittar du blogginlägg om Azure-säkerhet och regelefterlevnad
