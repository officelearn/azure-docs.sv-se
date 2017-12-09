---
title: "Plattformar som stöds i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet innehåller en lista över Windows- och Linux operatings system som stöds i Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: terrylan
ms.openlocfilehash: c80e78b39b3fba4dcd9523e1e679822758822805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="supported-platforms-in-azure-security-center"></a>Plattformar som stöds i Azure Security Center
Tillstånd säkerhetsövervakning och rekommendationer är tillgängliga för virtuella datorer (VM) med hjälp av både klassiskt och Resource Manager distributionsmodellerna.

> [!NOTE]
> Lär dig mer om den [klassisk och Resource Manager distributionsmodellerna](../azure-classic-rm.md) för Azure-resurser.
>
>

## <a name="supported-platforms-for-windows-vms"></a>Plattformar som stöds för virtuella Windows-datorer
Windows operativsystem:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-vms"></a>Plattformar som stöds för virtuella Linux-datorer
Operativsystem som stöds Linux:

* Versioner av Ubuntu 12.04, 14.04, 16.04, 16.10
* Debian versioner 7, 8
* CentOS version 6. \*, 7.*
* Red Hat Enterprise Linux (RHEL) version 6. \*, 7.*
* SUSE Linux Enterprise Server (SLES) versioner 11 SP4 +, 12.*
* Oracle Linux-version 6. \*, 7.*

> [!NOTE]
> Virtuella beteendeanalys är ännu inte tillgängliga för Linux-operativsystem.
>
>

## <a name="vms-and-cloud-services"></a>Virtuella datorer och molntjänster
Virtuella datorer som körs i en molntjänst stöds också. Endast cloud services webb- och arbetsroller roller som körs i produktion fack övervakas. Mer information om Molntjänsten finns [översikt över molntjänster](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Nästa steg

- [Planera för Azure Security Center och handboken](security-center-planning-and-operations-guide.md) – Lär dig att planera och designöverväganden för att införa Azure Security Center
- [Säkerhetsaviseringar efter typ i Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis) – Lär dig mer om virtuella beteendeanalys och krascher dump minnesanalys i Security Center
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – hittar du blogginlägg om säkerhet och Azure kompatibilitet
