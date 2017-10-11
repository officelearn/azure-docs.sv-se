---
title: "Vad är Azure Site Recovery? | Microsoft Docs"
description: "Ger en översikt över Azure Site Recovery-tjänsten och sammanfattar distributionsscenarier."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="what-is-site-recovery"></a>Vad är Site Recovery?

Välkommen till Azure Site Recovery-tjänsten! Den här artikeln innehåller en snabb översikt över tjänsten.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Verksamhetskontinuitet och haveriberedskap (BCDR) med Azure Recovery Services

Som organisation behöver ni veta hur ni ska skydda data och appar/arbetsbelastningar som körs när planerade och oplanerade avbrott inträffar.

Azure Recovery Services kan ingå i din BCDR-strategi:

- **Site Recovery-tjänsten**: Site Recovery hjälper till att garantera kontinuitet för företag genom att hålla appar som körs på virtuella datorer och fysiska servrar tillgängliga om en plats kraschar. Site Recovery replikerar arbetsbelastningar som körs på virtuella datorer och fysiska servrar så att de fortfarande är tillgängliga på en sekundär plats om den primära platsen inte är tillgänglig. Den återställer arbetsbelastningar till den primära platsen när den är igång och körs igen.
- **Säkerhetskopiering**: Dessutom håller [Azure Backup](https://docs.microsoft.com/azure/backup/) dina data säkra och dessa kan återställas genom att säkerhetskopiera till Azure.

Site Recovery kan hantera replikering för:

- Replikera virtuella Azure-datorer mellan Azure-regioner.
- Lokala virtuella datorer och fysiska servrar replikerar till Azure eller till en sekundär plats.


## <a name="what-does-site-recovery-provide"></a>Vad tillhandahåller Site Recovery?

**Funktion** | **Detaljer**
--- | ---
**Distribuera en enkel BCDR-lösning** | Med Site Recovery kan du konfigurera och hantera replikering, redundans och återställning från en enda plats i Azure-portalen.
**Replikera virtuella Azure-datorer** | Du kan ställa in din BCDR-strategi så att virtuella Azure-datorer replikeras mellan Azure-regioner.
**Replikera lokala virtuella datorer på annan plats** | Du kan replikera av lokala virtuella datorer och fysiska servrar till Azure eller till en sekundär lokal plats. Replikeringen till Azure besparar dig kostnaden och komplexiteten med att underhålla ett sekundärt datacenter.
**Replikera alla arbetsbelastningar** | Du kan replikera alla typer av arbetsbelastningar som körs på virtuella Azure-datorer, lokala virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows- och Linux-servrar som stöds.
**Skydda data flexibelt och säkert** | Site Recovery dirigerar replikering utan att spärra programdata. Replikerade data lagras flexibelt i Azure Storage. Vid redundans skapas virtuella Azure-datorer utifrån replikerade data.
**Uppfylla återställningstider och återställningspunkter** | Behåll återställningstiden (RTO) och återställningspunktmål (RPO) inom organisationen gränser. Site Recovery ger ständig replikering för virtuella Azure-datorer och virtuella datorer med VMware med replikeringsfrekvenser så låga som 30 sekunder för Hyper-V. Du kan minska återställningstiden (RTO) ytterligare genom att integrera med [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Bevara appar vid redundansväxling** | Du kan konfigurera återställningspunkter med programkonsekventa skuggkopior. Programkonsekventa skuggkopior sparar hårdiskdata, alla data i minnet och alla pågående transaktioner.
**Testa utan avbrott** | Du kan enkelt köra redundanstestning för att stödja tester av haveriberedskap, utan att påverka pågående replikering.
**Kör flexibel redundansväxling** | Du kan köra planerade redundanser för förväntade avbrott med noll dataförlust, eller oplanerade redundanser med minimal dataförlust (beroende på replikeringsfrekvensen) för oväntade haverier. Du kan enkelt återställa till den primära webbplatsen när den är tillgänglig igen.
**Skapa återställningsplaner** | Du kan anpassa och ordna redundansväxling och återställning av multi-nivåapplikationer på flera virtuella datorer med återställningsplaner. Du kan gruppera datorer i planerna samt lägga till skript och manuella åtgärder. Du kan integrera återställningsplaner med runbooks i Azure Automation.
**Integrera med befintlig BCDR-teknik** | Site Recovery kan integreras med annan BCDR-teknik. Du kan exempelvis använda Site Recovery för att skydda serverdelen för SQL Server för företagets arbetsbelastningar, inklusive inbyggt stöd för SQL Server AlwaysOn, för att hantera redundans för tillgänglighetsgrupper.
**Integrera med Automation-bibliotek** | Ett omfattande Azure Automation-bibliotek med produktionsklara, programspecifika skript som kan hämtas och integrerats med Site Recovery.
**Hantera nätverksinställningar** | Site Recovery och Azure förenklar nätverkskraven för program, inklusive reservation av IP-adresser, konfiguration av belastningsutjämnare och integration av Azure Traffic Manager för effektiv nätverksväxling.


## <a name="what-can-i-replicate"></a>Vad kan jag replikera?

**Stöds** | **Detaljer**
--- | ---
**Vad kan jag replikera?** | Migrera virtuella Azure-datorer mellan Azure-regioner (förhandsversion)<br/><br/>  Lokala, virtuella VMware-datorer, virtuella Hyper-V-datorer samt fysiska Windows- och Linux-servrar<br/<br/> Lokala, virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar på sekundär plats. Replikering till en sekundär plats stöds endast för Hyper-V-datorer om Hyper-V-värdar hanteras av System Center VMM.
**Vilka regioner har stöd för Site Recovery?** | [Regioner som stöds](https://azure.microsoft.com/regions/services/) |
**Vilka operativsystem behöver replikerade datorer?** | [Krav för virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)s<br></br>[Krav för virtuella VMware-datorer](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> För virtuella Hyper-V-datorer stöds samtliga [gästoperativsystem](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) som kan användas i Azure samt Hyper-V.<br/><br/> [Krav för fysisk server](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**Vilka servrar/värdar för VMware behöver jag?** | Virtuella VMware-datorer kan finnas på [vSphere värdar/vCenter-servrar som stöds](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Vilka arbetsbelastningar kan jag replikera?** | Du kan replikera alla arbetsbelastningar som körs på en dator det finns replikeringsstöd för. Dessutom har Site Recovery-teamet utfört appspecifika tester för ett [antal appar](site-recovery-workload.md#workload-summary).


## <a name="azure-portal-considerations"></a>Att tänka på om Azure portal

* Site Recovery kan distribueras i [Azure-portalen](https://portal.azure.com).
* På den klassiska Azure-portalen kan du använda Site Recovery med den klassiska tjänsthanteringsmodellen.
- Du bör bara använda den klassiska portalen till att underhålla befintliga Site Recovery-distributioner. Du kan inte skapa nya valv i den klassiska portalen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [stöd för arbetsbelastning](site-recovery-workload.md)
* Kom igång med [Azure VM replikering mellan regioner](site-recovery-azure-to-azure.md), [VMware-replikering till Azure](vmware-walkthrough-overview.md) eller [Hyper-V-replikering till Azure](hyper-v-site-walkthrough-overview.md).
