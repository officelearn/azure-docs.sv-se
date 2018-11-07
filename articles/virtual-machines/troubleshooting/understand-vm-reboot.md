---
title: Förstå en omstart av systemet för en Azure-dator | Microsoft Docs
description: Visar en lista över de händelser som kan orsaka en virtuell dator ska startas om
services: virtual-machines
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c1f48e3273ac0a237c72565acf25049b763dba8a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246675"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Förstå en omstart av systemet för Azure VM

Azure-datorer (VM) kan ibland att starta om ingen uppenbar anledning, utan bevis på din har initierat åtgärden för omstart. Den här artikeln visar en lista över de åtgärder och händelser som kan orsaka virtuella datorer att starta om och ger information om hur du undviker du omstartsproblem med oväntad eller minska inverkan på sådana problem.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurera virtuella datorer för hög tillgänglighet
Det bästa sättet att skydda ett program som körs på Azure mot virtuell dator startar om och avbrottstid är att konfigurera virtuella datorer för hög tillgänglighet.

För att tillhandahålla den här nivån av ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse är tillgänglig och uppfyller serviceavtal på 99,95% [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Mer information om tillgänglighetsuppsättningar finns i följande artiklar:

- [Hantera tillgängligheten för virtuella datorer](../windows/manage-availability.md)
- [Konfigurera tillgängligheten för virtuella datorer](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Hälsoinformation 
Azure Resource Health är en tjänst som visar hälsotillståndet hos enskilda Azure-resurser och ger praktisk vägledning vid felsökning av problem. I en molnmiljö där det inte går att direkt åtkomst till servrar eller infrastruktur, är målet med Resource Health att minska den tid som du lägger på felsökning. I synnerhet är syftet att minska den tid du ägnar fastställa om roten till problemet ligger i programmet eller i en händelse i Azure-plattformen. Mer information finns i [förstå och använda Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Åtgärder och händelser som kan göra att den virtuella datorn ska startas om

### <a name="planned-maintenance"></a>Planerat underhåll
Microsoft Azure utför regelbundet uppdateringar över hela världen att förbättra tillförlitligheten, prestanda och säkerheten för den underliggande virtuella datorer värd-infrastrukturen. Många av dessa uppdateringar, inklusive minnesbevarande uppdateringar genomförs utan att någon inverkan på dina virtuella datorer eller molntjänster.

Vissa uppdateringar kräver en omstart. I sådana fall kan stänga de virtuella datorerna av medan vi uppdaterar infrastrukturen och sedan de virtuella datorerna startas om.

Information om vilka Azure planerat underhåll är och hur de kan påverka tillgängligheten för din virtuella Linux-datorer finns i artiklarna som visas här. Artiklarna innehåller information om Azure planerat underhållsprocessen och hur du schemalägger planerat underhåll att ytterligare minska påverkan.

- [Planerat underhåll för virtuella datorer i Azure](../windows/planned-maintenance.md)
- [Så här schemalägger du planerat underhåll på virtuella Azure-datorer](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Minnesbevarande uppdateringar   
Användare får ingen påverkan på sina virtuella datorer som körs för den här klassen för uppdateringar i Microsoft Azure. Många av de här uppdateringarna är till komponenter eller tjänster som kan uppdateras utan att störa den instans som körs. Vissa är plattformsinfrastrukturuppdateringar i värdoperativsystemet som kan användas utan en omstart av de virtuella datorerna.

De här minnesbevarande uppdateringarna möjliggörs med teknik som aktiverar livemigrering på plats. När de uppdateras den virtuella datorn placeras i en *pausats* tillstånd. Det här tillståndet bevarar minnet i RAM medan det underliggande värdoperativsystemet får nödvändiga uppdateringar och korrigeringar. Den virtuella datorn återupptas inom 30 sekunder efter att ha pausats. När det virtuella datorn har återupptagits synkroniseras klockan automatiskt.

På grund av den korta perioden minskar distribuerar uppdateringar via den här mekanismen avsevärt påverkan på de virtuella datorerna. Inte alla uppdateringar kan dock distribueras på det här sättet. 

Uppdateringar med flera instanser (för virtuella datorer i en tillgänglighetsuppsättning) tillämpas med en uppdateringsdomän i taget.

> [!NOTE]
> Linux-datorer som har gamla kernel-versioner som påverkas av ett kernel panic-meddelande under den här uppdateringsmetoden. Undvik problemet genom att uppdatera till kernel-version 3.10.0-327.10.1 eller senare. Mer information finns i [en virtuell Linux-dator på en 3.10-baserade kernel panic efter en uppgradering för värd-nod](https://support.microsoft.com/help/3212236).     
    
### <a name="user-initiated-reboot-or-shutdown-actions"></a>Användarinitierade åtgärder för omstart eller avstängning
 
Om du utför en omstart från Azure portal, Azure PowerShell, kommandoradsgränssnittet eller Reset API, hittar du händelsen i den [Azure-aktivitetsloggen](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Om du utför åtgärden från den Virtuella datorns operativsystem hittar händelsen i systemloggar.

Andra scenarier som vanligtvis orsakar den virtuella datorn ska startas om innehålla flera konfigurationsändring åtgärder. Normalt visas ett varningsmeddelande som visar att köra en viss åtgärd resulterar i en omstart av den virtuella datorn. Exempel innefattar VM resize åtgärder, ändra lösenordet för det administrativa kontot och ange en statisk IP-adress.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center och Windows Update
Azure Security Center övervakar dagliga Windows och Linux-datorer efter saknade uppdateringar av operativsystemet. Security Center hämtar en lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services (WSUS), beroende på vilken tjänst som har konfigurerats på en virtuell Windows-dator. Security Center söker också efter de senaste uppdateringarna för Linux-system. Om den virtuella datorn saknar en systemuppdatering, rekommenderar Security Center att du installerar uppdateringar. Tillämpningen av dessa systemuppdateringar styrs via de Säkerhetscenter i Azure-portalen. När du installerar vissa uppdateringar kan det krävas omstarter av Virtuella datorer. Mer information finns i [tillämpa systemuppdateringar i Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Som lokala servrar, Azure inte hämta uppdateringar från Windows Update till Windows virtuella datorer, eftersom dessa datorer är avsedda att hanteras av sina användare. Du är, men uppmuntras att lämna inställningen för automatisk uppdatering för Windows. Automatisk installation av uppdateringar från Windows Update kan även orsaka omstarter när uppdateringarna tillämpas. Mer information finns i [Windows Update vanliga frågor och svar](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andra situationer som påverkar tillgängligheten för din virtuella dator
Det finns andra fall där Azure aktivt kan inaktivera användningen av en virtuell dator. Du får e-postmeddelanden innan den här åtgärden tas, så har du chansen att lösa de underliggande problem. Exempel på problem som påverkar VM-tillgänglighet är säkerhetsöverträdelser och förfallodatum för betalningsmetoder.

### <a name="host-server-faults"></a>Värd-serverfel 
Den virtuella datorn finns på en fysisk server som körs i ett Azure-datacenter. Den fysiska servern kör en agent kallas Värdagenten förutom några andra Azure-komponenter. När dessa komponenter för Azure-programvara på den fysiska servern svarar, utlöser övervakningssystemet en omstart av värdservern att återställa. Den virtuella datorn är vanligtvis tillgängligt igen inom fem minuter och fortsätter att live på samma värddator som tidigare.

Serverfel orsakas vanligtvis av maskinvarufel, till exempel fel på en hårddisk eller Solid State-hårddisk. Azure kontinuerligt övervakar dessa förekomster, identifierar de underliggande buggarna och sprider uppdateringar när minskningen har implementerat och testas.

Eftersom vissa värden serverfel kan vara specifika för den här servern, kan en upprepad VM omstart situation förbättras genom att manuellt distribuera om den virtuella datorn till en annan värdserver. Den här åtgärden kan aktiveras med hjälp av den **omdistribuera** alternativ på sidan för den virtuella datorn eller genom att stoppa och starta om den virtuella datorn i Azure-portalen.

### <a name="auto-recovery"></a>Automatisk återställning
Om värdservern inte kan en omstart av någon anledning, initierar en åtgärd för automatisk återställning om du vill ta bort från roteringen för vidare studier felaktiga värdservern i Azure-plattformen. 

Alla virtuella datorer på värden flyttas automatiskt till en annan, felfri värdserver. Den här processen är vanligtvis klar inom 15 minuter. Läs mer om den automatiska återställningsprocessen i [automatisk återställning av virtuella datorer](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Oplanerat Underhåll
I sällsynta fall kanske Azure driftsteamet behöver utföra underhållsaktiviteter för att säkerställa den övergripande hälsan för Azure-plattformen. Det här beteendet kan påverka tillgängligheten för virtuella datorer och det resulterar vanligtvis i samma åtgärd för automatisk återställning enligt beskrivningen ovan.  

Oplanerad maintenances inkluderar följande:

- Brådskande noden defragmentering
- Brådskande nätverk växel uppdateringar

### <a name="vm-crashes"></a>VM-krascher
Virtuella datorer kanske startar om på grund av problem i Virtuellt datorn. Arbetsbelastning eller roll som körs på den virtuella datorn kan utlösa en felkontroll i gästoperativsystemet. Hjälp med att fastställa orsaken till kraschen, visa i systemet och programloggarna för virtuella Windows-datorer och seriell loggar för virtuella Linux-datorer.

### <a name="storage-related-forced-shutdowns"></a>Storage-relaterade Tvingad avstängningar
Virtuella datorer i Azure är beroende av virtuella diskar för lagring av data som finns på Azure Storage-infrastrukturen och operativsystemet. När mer än 120 sekunder påverkas tillgänglighet eller anslutning mellan den virtuella datorn och de associera virtuella diskarna, utför tvångsavstängning av de virtuella datorerna att undvika att data skadas i Azure-plattformen. De virtuella datorerna sätts automatiskt på igen när lagringsanslutningen har återställts. 

Varaktigheten för avstängningen kan vara så kort som fem minuter, men kan vara betydligt längre. Följande är en av de särskilda fall som är associerad med lagringsrelaterade Tvingad avstängningar: 

**Överstiger IO begränsar**

Virtuella datorer kan tillfälligt stänga av när i/o-begäranden är konsekvent begränsas eftersom volymen för i/o-åtgärder per sekund (IOPS) överskrider den i/o-gränserna för disken. (Standard-disklagring är begränsad till 500 IOPS.) För att lösa det här problemet, Använd disk striping eller konfigurera lagringsutrymmet på gästen för virtuell dator, beroende på arbetsbelastningen. Mer information finns i [Konfigurera virtuella Azure-datorer för optimala lagringsprestanda](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

Högre IOPS-gränser är tillgängliga via Azure Premium Storage med upp till 80 000 IOPS. Mer information finns i [högpresterande Premium Storage](../windows/premium-storage.md).

### <a name="other-incidents"></a>Andra incidenter
I sällsynta fall kan kan ett omfattande problem påverka flera servrar i ett Azure-datacenter. Om det här problemet uppstår, skickar Azure-teamet e-postmeddelanden till de berörda prenumerationerna. Du kan kontrollera den [Azure Service Health-instrumentpanelen](https://azure.microsoft.com/status/) och Azure portal så att statusen för pågående avbrott och tidigare incidenter.
