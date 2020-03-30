---
title: Om Återställning av Azure-webbplatser
description: Ger en översikt över Azure Site Recovery-tjänsten och sammanfattar distributionsscenarier för haveriberedskap och migrering.
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: ee387682e935522b37a5b4f9f32e53712bfb9150
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067545"
---
# <a name="about-site-recovery"></a>Om Site Recovery

Välkommen till Azure Site Recovery-tjänsten! Den här artikeln innehåller en snabb tjänstöversikt.

Som organisation måste du anta en strategi för affärskontinuitet och haveriberedskap (BCDR) som håller dina data säkra och dina appar och arbetsbelastningar online när planerade och oplanerade avbrott inträffar.

Azure Recovery Services bidrar till din BCDR-strategi:

- **Site Recovery-tjänsten**: Site Recovery bidrar till att säkerställa affärskontinuitet genom att se till att appar och arbetsbelastningar körs under driftstopp. Site Recovery replikerar arbetsbelastningar som körs på fysiska och virtuella datorer (VM) från en primär plats till en sekundär plats. När ett driftstopp uppstår på den primära platsen sker redundansväxling till den sekundära platsen, och det går att komma åt apparna därifrån. När den primära platsen körs igen kan du växla tillbaka till den.
- **Säkerhetskopieringstjänst**: [Azure Backup-tjänsten](/azure/backup/) håller dina data säkra och återställningsbara.

Site Recovery kan hantera replikering för:

- Replikera virtuella Azure-datorer mellan Azure-regioner.
- Lokala virtuella datorer, virtuella Azure Stack-datorer och fysiska servrar.

## <a name="what-does-site-recovery-provide"></a>Vad tillhandahåller Site Recovery?

**Funktion** | **Detaljer**
--- | ---
**Enkel BCDR-lösning** | Med Site Recovery kan du konfigurera och hantera replikering, redundans och återställning från en enda plats i Azure-portalen.
**Replikering av virtuella Azure-datorer** | Du kan konfigurera återställning av virtuella Azure-datorer från en primär region till en sekundär region.
**Lokal replikering av virtuella datorer** | Du kan replikera lokala virtuella datorer och fysiska servrar till Azure eller till ett sekundärt lokalt datacenter. Replikeringen till Azure besparar dig kostnaden och komplexiteten med att underhålla ett sekundärt datacenter.
**Replikering av arbetsbelastning** | Du kan replikera alla typer av arbetsbelastningar som körs på virtuella Azure-datorer, lokala virtuella Hyper-V- och VMware-datorer och fysiska Windows- och Linux-servrar som stöds.
**Dataåterhämtning** | Site Recovery dirigerar replikering utan att avlyssna programdata. När du replikerar till Azure lagras data flexibelt i Azure Storage. Vid redundans skapas virtuella Azure-datorer utifrån replikerade data.
**Mål för återställningstid och återställningspunkter (RTO och RPO)** | Behåll mål för återställningstid (RTO) och återställningspunkt (RPO) inom organisationens gränser. Site Recovery ger ständig replikering för virtuella Azure-datorer och virtuella datorer med VMware med replikeringsfrekvenser så låga som 30 sekunder för Hyper-V. Du kan minska målet för återställningstiden (RTO) ytterligare genom att integrera med [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Bevara appar vid redundansväxling** | Du kan replikera med hjälp av återställningspunkter med programkonsekventa skuggkopior. Dessa skuggkopior sparar hårddiskdata, alla data i minnet och alla pågående transaktioner.
**Testa utan avbrott** | Du kan enkelt köra tester av haveriberedskap, utan att påverka pågående replikering.
**Flexibel redundansväxling** | Du kan köra planerade redundans för förväntade avbrott med nolldataförlust. Eller oplanerade redundans med minimal dataförlust, beroende på replikeringsfrekvens, för oväntade katastrofer. Du kan enkelt återställa till den primära webbplatsen när den är tillgänglig igen.
**Anpassningsbara återställningsplaner** | Med hjälp av återställningsplaner kan du anpassa och ordna redundans och återställning av program på flera nivåer som körs på flera virtuella datorer. Du kan gruppera datorer i en återställningsplan och välja att lägga till skript och manuella åtgärder. Du kan integrera återställningsplaner med runbooks i Azure Automation.
**BCDR-integrering** | Site Recovery kan integreras med annan BCDR-teknik. Du kan exempelvis använda Site Recovery för att skydda serverdelen för SQL Server för företagets arbetsbelastningar, med inbyggt stöd för SQL Server AlwaysOn, för att hantera redundans för tillgänglighetsgrupper.
**Integrering av Azure Automation** | Ett omfattande Azure Automation-bibliotek med produktionsklara, programspecifika skript som kan hämtas och integrerats med Site Recovery.
**Nätverksintegrering** | Site Recovery integreras med Azure för hantering av programnätverk. Om du till exempel vill reservera IP-adresser konfigurerar du belastningsutjämning och använder Azure Traffic Manager för effektiva nätverksövergångar.

## <a name="what-can-i-replicate"></a>Vad kan jag replikera?

**Stöds** | **Detaljer**
--- | ---
**Replikeringsscenarier** | Replikera virtuella Azure-datorer från en Azure-region till en annan.<br/><br/>  Replikera lokala, virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar (Windows och Linux) samt virtuella Azure Stack-datorer till Azure.<br/><br/> Replikera AWS Windows-instanser till Azure.<br/><br/> Replikera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer som hanteras av System Center VMM och fysiska servrar till en sekundär plats.
**Regioner** | Kontrollera [regioner som stöds](https://azure.microsoft.com/regions/services/) för Site Recovery. |
**Replikerade datorer** | Granska replikeringskraven för [virtuella Azure-datorer](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [lokala virtuella datorer med VMware och fysiska servrar](vmware-physical-azure-support-matrix.md#replicated-machines) samt [lokala virtuella datorer med Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms).
**Arbetsbelastningar** | Du kan replikera alla arbetsbelastningar som körs på en dator det finns replikeringsstöd för. Och site recovery-teamet gjorde appspecifika tester för ett [antal appar](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [arbetsbelastningsstöd](site-recovery-workload.md).
- Kom igång med replikering av [virtuella Azure-datorer mellan regioner](azure-to-azure-quickstart.md).
