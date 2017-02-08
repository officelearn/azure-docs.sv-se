---
title: "Vad är Site Recovery? | Microsoft Docs"
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
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 7455d6f99ed8ceb401224f98105f7b651f55c724
ms.openlocfilehash: 98bf94960c39565243995a1c4dd45787478b8f40


---
# <a name="what-is-site-recovery"></a>Vad är Site Recovery?
Välkommen till Azure Site Recovery-tjänsten! Den här artikeln innehåller en snabb översikt över Site Recovery.

Din organisation behöver ha en strategi för affärskontinuitet och haveriberedskap (BCDR) som ser till att appar och data är säkra och tillgängliga under planerade såväl som oplanerade avbrott och att återställning sker till vanliga driftsförhållanden så snart som möjligt.

Site Recovery bidrar till din BCDR-strategi genom att dirigera replikering av lokala virtuella datorer och fysiska servrar. Du replikerar servrar och virtuella datorer från ditt primära lokala datacenter till molnet (Azure), eller till ett sekundärt datacenter.

Vid avbrott på den primära platsen så växlar du över till den sekundära platsen så att arbetsbelastningar fortsätter att vara åtkomliga och tillgängliga. Du växlar tillbaka till den primära platsen när den har återgått till normal drift.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery på Azure Portal
Azure har två olika [distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md) för att skapa och arbeta med resurser. Azure Resource Manager-modellen och den klassiska tjänstehanteringsmodellen. Azure har även två portaler. Den [klassiska Azure-portalen](https://manage.windowsazure.com/) och [Azure Portal](https://portal.azure.com).

* Site Recovery kan distribueras både i den klassiska portalen och i Azure Portal.
* På den klassiska Azure-portalen kan du använda Site Recovery med den klassiska tjänsthanteringsmodellen.
* I Azure Portal har du stöd för den klassiska modellen eller Resource Manager-distributioner.

Informationen i den här artikeln gäller både klassiska distributioner och distributioner via Azure-portalen. Eventuella skillnader beskrivs i förekommande fall.

## <a name="why-deploy-site-recovery"></a>Varför ska jag distribuera Site Recovery?
Här är exempel på hur Site Recovery kan hjälpa ditt företag:

* **Förenkla BCDR**– Du kan replikera, redundansväxla och återställa flera arbetsbelastningar från en enda plats i Azure Portal. Site Recovery dirigerar replikering och redundansväxling utan att spärra programdata.
* **Tillhandahåll flexibel replikering** – Du kan du replikera alla arbetsbelastningar som körs på virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows- och Linux-servrar som stöds.
* **Ta bort ett sekundärt datacenter**–Du kan replikera arbetsbelastningar till Azure istället för till en sekundär plats. Därmed kan du göra dig av med kostnaden och komplexiteten med att underhålla ett sekundärt datacenter. Replikerade data lagras flexibelt i Azure Storage. Vid redundans skapas virtuella Azure-datorer med replikerade data.
* **Utför enkel replikeringstestning** – Du kan enkelt köra redundanstestning för att stödja tester av haveriberedskap, utan att påverka produktionsmiljöerna.
* **Redundans och återställning**– Du kan köra planerade redundanser för förväntade avbrott med noll dataförlust, eller oplanerade redundanser med minimal dataförlust (beroende på replikeringsfrekvensen) för oväntade haverier. Du kan återställa till den primära webbplatsen när den är tillgänglig igen.
* **Redundans av flera virtuella datorer** – Du kan konfigurera återställningsplaner som omfattar skript och Azure Automation-runbookföden. Med en återställningsplan kan du modellera och anpassa redundans och återställning av program på flera nivåer som finns på olika virtuella datorer.
* **Integrerar med befintlig BCDR-teknik** – Site Recovery integrerar sig med andra BCDR-tekniker. Du kan exempelvis använda Site Recovery för att skydda serverdelen för SQL Server för företagets arbetsbelastningar, inklusive inbyggt stöd för SQL Server AlwaysOn, för att hantera redundans för tillgänglighetsgrupper.

## <a name="what-can-i-replicate"></a>Vad kan jag replikera?
Här är en sammanfattning av vad du kan replikera med Site Recovery.

![Lokal till lokal](./media/site-recovery-overview/asr-overview-graphic.png)

| **Replikera** | **Repliker till** |
| --- | --- |
| Lokala virtuella VMware-datorer |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sekundär plats](site-recovery-vmware-to-vmware.md) |
| Lokala virtuella Hyper-V-datorer som hanteras i VMM-moln |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Sekundär plats](site-recovery-vmm-to-vmm.md) |
| Lokala virtuella Hyper-V-datorer som hanteras i VMM-moln med SAN-lagring |[Sekundär plats](site-recovery-vmm-san.md) |
| Lokala virtuella Hyper-V-datorer, utan VMM |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| Lokala fysiska Windows-/Linux-servrar |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sekundär plats](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>Hur skyddar Site Recovery arbetsbelastningar?
Site Recovery tillhandahåller programmedveten replikering, vilket gör att arbetsbelastningar och appar kan fortsätta att köra på ett konsekvent sätt vid avbrott.

* **Programkonsekventa ögonblicksbilder** – Datorer replikerar sig med programkonsekventa ögonblicksbilder, för appar på en eller flera nivåer. Förutom att samla in diskdata så samlar programkonsekventa ögonblicksbilder in alla data i minnet och alla pågående transaktioner.
* **I princip synkron replikering** – Site Recovery erbjuder replikeringsfrekvenser så låga som 30 sekunder för Hyper-V, samt kontinuerlig replikering för VMware.
* **Flexibla återställningsplaner**– du kan skapa och anpassa återställningsplaner med externa skript och manuella åtgärder. Integrering med Azure Automation-runbooks låter dig återställa en hel programstack med ett enda klick.
* **Integrering med SQL Server AlwaysOn** – Du kan hantera redundans för tillgänglighetsgrupper med återställningsplaner.
* **Automation-bibliotek** – Ett omfattande Azure Automation-bibliotek med produktionsklara, programspecifika skript som kan hämtas och integrerats med Site Recovery.
* **Enkel nätverkshantering** – Avancerad nätverkshantering i Site Recovery och Azure förenklar nätverkskraven för program, inklusive reservation av IP-adresser, konfiguration av belastningsutjämnare och integration av Azure Traffic Manager för effektiv nätverksväxling.

## <a name="next-steps"></a>Nästa steg
* Läs mer i [Vilka arbetsbelastningar kan Site Recovery skydda?](site-recovery-workload.md)
* Mer information om Site Recovery-arkitekturen finns i [Hur fungerar Site Recovery?](site-recovery-components.md)




<!--HONumber=Nov16_HO4-->


