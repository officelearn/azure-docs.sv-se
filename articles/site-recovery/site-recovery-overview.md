<properties
    pageTitle="Vad är Site Recovery? | Microsoft Azure" 
    description="Innehåller en översikt över Azure Site Recovery-tjänsten och förklarar hur tjänsten kan distribueras." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="02/22/2016" 
    ms.author="raynew"/>

#  Vad är Site Recovery?

Välkommen till Azure Site Recovery! Börja med den här artikeln för att få en snabb överblick över Site Recovery-tjänsten och hur den kan bidra till din BCDR-strategi för affärskontinuitet och haveriberedskap.

## Översikt

Organisationer behöver en BCDR-strategi som beskriver hur appar, arbetsbelastningar och data fungerar och är tillgängliga under planerade och oplanerade driftavbrott och som ser till att systemets normala drifttillstånd återställs så fort som möjligt. Avsikten med en BCDR-strategi är att skydda affärsdata och se till att de kan återställas samt att säkerställa att arbetsbelastningar förblir tillgängliga i händelse av allvarliga fel. 

Site Recovery är en Azure-tjänst som stödjer din BCDR-strategi genom att dirigera replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Vid driftstopp på den primära platsen växlar du över till den sekundära platsen så att program och arbetsbelastningar fortsätter att vara tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift. Läs mer i [Vad är Site Recovery?](site-recovery-overview.md)

## Site Recovery på Azure Portal

Azure har två olika [distributionsmodeller](../resource-manager-deployment-model.md) för att skapa och arbeta med resurser: Azure Resource Manager-modellen och den klassiska tjänsthanteringsmodellen. Azure har också två portaler – den [klassiska Azure-portalen](https://manage.windowsazure.com/) som stöder den klassiska distributionsmodellen och [Azure Portal](https://portal.azure.com) som stöder båda distributionsmodellerna.

Site Recovery är tillgängligt både på den klassiska portalen och på Azure Portal. På den klassiska Azure-portalen kan du använda Site Recovery med den klassiska tjänsthanteringsmodellen. På Azure Portal kan du använda den klassiska modellen eller Resource Manager-distributioner. [Läs mer](site-recovery-overview.md#site-recovery-in-the-azure-portal) om hur du distribuerar med Azure Portal.

Informationen i den här artikeln gäller både klassiska distributioner och distributioner via Azure Portal. Skillnaderna beskrivs i förekommande fall.


## Varför ska jag använda Site Recovery? 

Här är exempel på hur Site Recovery kan hjälpa ditt företag:

- **Förenklar organisationens BCDR-strategi** – Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbetsbelastningar och appar från en enda plats. Site Recovery samordnar replikering och redundans men kan inte komma åt dina programdata och har inte någon information om dem.
- **Tillhandahåller flexibel replikering** – Med Site Recovery kan du replikera arbetsbelastningar som körs på virtuella Hyper-V-datorer, virtuella VMware-datorer och Windows-/Linux-baserade fysiska servrar. 
- **Enkel redundans och återställning** – Site Recovery tillhandahåller redundanstestning så att du kan testa organisationens haveriberedskap utan att påverka produktionsmiljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter en redundansväxling kan du återställa till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer. 
- **Ta bort sekundärt datacenter** – Du kan replikera till en sekundär lokal plats eller till Azure. Med Azure som mål för haveriberedskap slipper du kostnaderna och komplexiteten med att underhålla en sekundär plats och replikerade data lagras i Azure Storage, med all den flexibilitet som det innebär.
- **Integrera med befintlig BCDR-teknik** – Site Recovery fungerar med andra BCDR-programfunktioner. Du kan till exempel använda Site Recovery för att skydda SQL Server-servrar i företagets arbetsbelastningar, med inbyggt stöd för SQL Server AlwaysOn för att hantera redundans för tillgänglighetsgrupper. 

## Vad kan jag replikera?

Här är en sammanfattning av vad du kan replikera med Site Recovery.

![Lokal till lokal](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLIKERA** | **REPLIKERA FRÅN (LOKAL)** | **REPLIKERA TILL** | **ARTIKEL**
---|---|---|---
VMwares virtuella datorer | VMware-server | Azure | [Läs mer](site-recovery-vmware-to-azure-classic.md)
VMwares virtuella datorer | VMware-server | Sekundär VMware-plats | [Läs mer](site-recovery-vmware-to-vmware.md) 
Hyper-V:s virtuella datorer | Hyper-V-värd i VMM-moln | Azure | [Läs mer](site-recovery-vmm-to-azure.md) 
Hyper-V:s virtuella datorer | Hyper-V-värd i VMM-moln | Sekundär VMM-plats | [Läs mer](site-recovery-vmm-to-vmm.md)
Hyper-V:s virtuella datorer | Hyper-V-värds i VMM-moln med SAN-lagring| Sekundär VMM-plats med SAN-lagring | [Läs mer](site-recovery-vmm-san.md)
Hyper-V:s virtuella datorer | Hyper-V-värd (ingen VMM) | Azure | [Läs mer](site-recovery-hyper-v-site-to-azure.md)
Fysiska Windows-/Linux-servrar | Fysisk server | Azure | [Läs mer](site-recovery-vmware-to-azure-classic.md)
Arbetsbelastningar som körs på fysiska Windows-/Linux-servrar | Fysisk server | Sekundärt datacenter | [Läs mer](site-recovery-vmware-to-vmware.md) 


## Vilka arbetsbelastningar kan jag skydda?

Site Recovery kan hjälpa dig med programmedveten affärskontinuitet och haveriberedskap så att arbetsbelastningar och appar fortsätter att köra på ett konsekvent sätt när avbrott uppstår. Site Recovery tillhandahåller: 

- **Programkonsekventa ögonblicksbilder** – Replikering med programkonsekventa ögonblicksbilder för appar med en eller flera nivåer.
- **Nästintill synkron replikering** – En replikeringsfrekvens så låg som 30 sekunder för Hyper-V och kontinuerlig replikering för VMware.
- **Integrering med SQL Server AlwaysOn** – Du kan hantera redundans för tillgänglighetsgrupper i Site Recovery-återställningsplaner. 
- **Flexibla återställningsplaner** – Du kan skapa och anpassa återställningsplaner med externa skript, manuella åtgärder och Azure Automation Runbook-rutiner som gör att du kan återställa en hel programstack med en enda klickning.
- **Automation-bibliotek** – Ett omfattande Azure Automation-bibliotek med produktionsklara, programspecifika skript som kan hämtas och integrerats med Site Recovery.
- **Enkel nätverkshantering** – Avancerad nätverkshantering i Site Recovery och Azure förenklar nätverkskraven för program, inklusive reservation av IP-adresser, konfiguration av belastningsutjämnare och integration av Azure Traffic Manager för effektiv nätverksväxling.


## Nästa steg

- Läs mer i [Vilka arbetsbelastningar kan Site Recovery skydda?](site-recovery-workload.md)
- Mer information om Site Recovery-arkitekturen finns i [Hur fungerar Site Recovery?](site-recovery-components.md)
 



<!--HONumber=sep16_HO1-->


