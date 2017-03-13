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
ms.date: 02/26/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e4bb13a73f6338d2d844a0561edc65063c685d59
ms.openlocfilehash: e554a0ba87efb0272e092a121ba96edc9d9eb011
ms.lasthandoff: 03/02/2017


---
# <a name="what-is-site-recovery"></a>Vad är Site Recovery?

Välkommen till Azure Site Recovery-tjänsten! Den här artikeln innehåller en översikt över tjänsten och länkar till mer information.

Avbrott i verksamheten kan både orsakas av naturliga händelser och av operativa fel. Din organisation behöver ha en strategi för affärskontinuitet och haveriberedskap (BCDR) som ser till att data är säkra och appar tillgängliga under planerade såväl som oplanerade avbrott och att återställning sker till vanliga driftsförhållanden så snart som möjligt.

Azure Recovery Services kan ingå i BCDR-strategin. Med [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) kan du skydda och återställa data. Med Site Recovery kan du replikera, redundansväxla och återställa arbetsbelastningar så att de fortfarande är tillgängliga om det inträffar ett fel.

## <a name="what-does-site-recovery-provide"></a>Vad tillhandahåller Site Recovery?

- **Haveriberedskap i molnet** – du kan replikera arbetsbelastningar som körs på virtuella datorer och fysiska servrar till Azure i stället för en sekundär plats. Därmed kan du göra dig av med kostnaden och komplexiteten med att underhålla ett sekundärt datacenter.
- **Flexibel replikering i hybridmiljöer** – du kan du replikera alla typer av arbetsbelastningar som körs på de lokala, virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows- och Linux-servrar som stöds.
- **Migrering** – du kan använda Site Recovery till att migrera lokala, virtuella AWS-instanser till virtuella Azure-datorer och till att migrera virtuella Azure-datorer mellan olika Azure-regioner.
- **Enklare BCDR** – du kan distribuera all replikering från en enda plats i Azure Portal.  Du kan köra enkel redundansväxling och återställning efter fel för en eller flera datorer.
- **Flexibilitet** – Site Recovery dirigerar replikering och redundansväxling utan att spärra programdata.
Replikerade data lagras flexibelt i Azure Storage. Vid redundans skapas virtuella Azure-datorer utifrån replikerade data.
- **Replikeringsprestanda** – Site Recovery har replikeringsfrekvenser så låga som 30 sekunder för Hyper-V och kontinuerlig replikering för VMware. Du kan ange tröskelvärden för RPO-mål om du vill styra hur ofta återställningspunkter för data skapas, och du kan minska RTO-målen tack vare den automatiserade återställningen i Site Recovery och integreringen med [Azure Traffic Manager](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
- **Programkonsekvens** – datorer replikeras med hjälp av programkonsekventa ögonblicksbilder. Förutom att samla in diskdata så samlar programkonsekventa ögonblicksbilder in alla data i minnet och alla pågående transaktioner.
- **Avbrottsfria tester** – du kan enkelt köra redundanstestning i samband med övningar för haveriberedskap utan att påverka produktionsmiljön.
- **Flexibel redundansväxling och återställning** – du kan köra planerade redundansväxlingar för förväntade avbrott helt utan dataförlust, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvensen) vid oväntade haverier. Du kan enkelt återställa till den primära webbplatsen när den är tillgänglig igen.
- **Anpassade återställningsplaner** – med återställningsplaner kan du modellera och anpassa redundansväxling och återställning av program på flera nivåer som finns på olika virtuella datorer. Du kan ordna grupper i planerna samt lägga till skript och manuella åtgärder. Du kan integrera återställningsplaner med runbooks i Azure Automation.
- **Program på flera nivåer** – du kan skapa återställningsplaner med sekvenser av redundansväxlingar och återställningar för program på flera nivåer. Du kan gruppera datorer på olika nivåer (till exempel databas, webbprogram eller app) i en återställningsplan och anpassa hur varje grupp växlas över och startas.
* **Integrering med befintlig BCDR-teknik** – Site Recovery kan integreras med andra BCDR-tekniker. Du kan exempelvis använda Site Recovery för att skydda serverdelen för SQL Server för företagets arbetsbelastningar, inklusive inbyggt stöd för SQL Server AlwaysOn, för att hantera redundans för tillgänglighetsgrupper.
* **Integrering med Automation-biblioteket** – det omfattande Azure Automation-biblioteket innehåller produktionsklara, programspecifika skript som kan hämtas och integrerats med Site Recovery.
* **Enkel nätverkshantering** – avancerad nätverkshantering i Site Recovery och Azure förenklar nätverkskraven för program, inklusive reservation av IP-adresser, konfiguration av belastningsutjämnare och integration av Azure Traffic Manager för effektiv nätverksväxling.

## <a name="which-azure-portal"></a>Vilken Azure Portal?

* Site Recovery kan distribueras både i nya [Azure Portal](https://portal.azure.com) och i [den klassiska Azure-portalen](https://manage.windowsazure.com/).
* På den klassiska Azure-portalen kan du använda Site Recovery med den klassiska tjänsthanteringsmodellen.
* Azure Portal har stöd för den klassiska modellen och den nyare modellen med [Resource Manager-distribution](../azure-resource-manager/resource-manager-deployment-model.md).
- Du bör bara använda den klassiska portalen till att underhålla befintliga Site Recovery-distributioner. Du kan inte skapa nya valv i den klassiska portalen.

## <a name="whats-supported"></a>Vad stöds?

**Stöds** | **Detaljer**
--- | ---
**Vilka regioner har stöd för Site Recovery?** | [Regioner som stöds](https://azure.microsoft.com/en-us/regions/services/) |
**Vad kan jag replikera?** | Lokala, virtuella VMware-datorer, virtuella Hyper-V-datorer samt fysiska Windows- och Linux-servrar.
**Vilka operativsystem behöver replikerade datorer?** | [Operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) för virtuella VMware-datorer<br/><br/> För virtuella Hyper-V-datorer stöds samtliga [gästoperativsystem](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) som kan användas i Azure samt Hyper-V.<br/><br/> [Operativsystem](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) för fysiska servrar
**Vad kan jag använda för replikeringsmål?** | Till Azure Storage eller till ett sekundärt datacenter<br/><br/> För Hyper-V går det bara att replikera virtuella datorer på Hyper-V-värdar som hanteras i System Center VMM-moln till ett sekundärt datacenter.
**Vilka servrar/värdar för VMware behöver jag?** | De virtuella VMware-datorer du vill replikera kan hanteras av [de vSphere-värdar/vCenter-servrar som stöds](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Vilka arbetsbelastningar kan jag replikera?** | Du kan replikera alla arbetsbelastningar som körs på en dator det finns replikeringsstöd för. Dessutom har Site Recovery-teamet utfört appspecifika tester för ett [antal appar](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [stöd för arbetsbelastning](site-recovery-workload.md)
* Läs mer om [Site Recovery-arkitektur och -komponenter](site-recovery-components.md)

