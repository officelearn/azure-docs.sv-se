---
title: Förstå en omstart av systemet för en virtuell Azure-dator | Microsoft-dokument
description: Visar en lista över de händelser som kan orsaka att en virtuell dator startas om
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e94ffb3d34082745c3d7ca86cfda2b93c0ed08da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919421"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Förstå en omstart av systemet för Azure VM

Virtuella Azure-datorer kan ibland startas om utan någon uppenbar anledning, utan bevis på att du har initierat omstarten. I den här artikeln visas de åtgärder och händelser som kan orsaka att virtuella datorer startas om och ger insikt i hur du undviker oväntade omstartsproblem eller minskar effekten av sådana problem.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurera de virtuella datorerna för hög tillgänglighet

Det bästa sättet att skydda ett program som körs på Azure mot omstarter av virtuella datorer och driftstopp är att konfigurera de virtuella datorerna för hög tillgänglighet.

Om du vill tillhandahålla den här redundansnivån till ditt program rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen säkerställer att under antingen en planerad eller oplanerad underhållshändelse är minst en virtuell dator tillgänglig och uppfyller 99,95 procent [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Mer information om tillgänglighetsuppsättningar finns [i Hantera tillgängligheten för virtuella datorer](../windows/manage-availability.md)

## <a name="resource-health-information"></a>Information om resurshälsa

Azure Resource Health är en tjänst som exponerar hälsotillståndet för enskilda Azure-resurser och ger användbar vägledning för felsökning av problem. I en molnmiljö där det inte är möjligt att direkt komma åt servrar eller infrastrukturelement är målet med Resurshälsa att minska den tid du spenderar på felsökning. Syftet är särskilt att minska den tid som du bestämmer om roten till problemet ligger i programmet eller i en händelse inuti Azure-plattformen. Mer information finns i [Förstå och använda Resurshälsa](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Åtgärder och händelser som kan orsaka att den virtuella datorn startas om

### <a name="planned-maintenance"></a>Planerat underhåll

Microsoft Azure utför regelbundet uppdateringar över hela världen för att förbättra tillförlitligheten, prestandan och säkerheten för värdinfrastrukturen som ligger till grund för virtuella datorer. Många av dessa uppdateringar, inklusive minnesbevarande uppdateringar, utförs utan någon inverkan på dina virtuella datorer eller molntjänster.

Vissa uppdateringar kräver dock en omstart. I sådana fall stängs de virtuella datorerna av medan vi korrigerar infrastrukturen och sedan startas de virtuella datorerna om.

För att förstå vad Azure planerat underhåll är och hur det kan påverka tillgängligheten för dina virtuella Linux-datorer, se artiklarna som anges här. Artiklarna innehåller information om processen för planerat underhåll av Azure och hur du schemalägger planerat underhåll för att minska påverkan ytterligare.

- [Planerat underhåll av virtuella datorer i Azure](../windows/planned-maintenance.md)
- [Så här schemalägger du planerat underhåll av virtuella Azure-datorer](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Minnesbevarande uppdateringar

För den här klassen av uppdateringar i Microsoft Azure får användarna ingen inverkan på deras virtuella datorer som körs. Många av de här uppdateringarna är till komponenter eller tjänster som kan uppdateras utan att störa den instans som körs. Vissa är uppdateringar av plattformsinfrastruktur på värdoperativsystemet som kan tillämpas utan omstart av de virtuella datorerna.

De här minnesbevarande uppdateringarna möjliggörs med teknik som aktiverar livemigrering på plats. När den uppdateras placeras den virtuella datorn i *pausat* tillstånd. Det här tillståndet bevarar minnet i RAM medan det underliggande värdoperativsystemet får nödvändiga uppdateringar och korrigeringar. Den virtuella datorn återupptas inom 30 sekunder efter att ha pausats. När det virtuella datorn har återupptagits synkroniseras klockan automatiskt.

På grund av den korta pausperioden minskar distributionen av uppdateringar via den här mekanismen avsevärt påverkan på de virtuella datorerna. Alla uppdateringar kan dock inte distribueras på det här sättet. 

Uppdateringar med flera instanser (för virtuella datorer i en tillgänglighetsuppsättning) tillämpas med en uppdateringsdomän i taget.

> [!NOTE]
> Linux-datorer som har gamla kärnversioner påverkas av en kernel-panik under den här uppdateringsmetoden. Undvik det här problemet genom att uppdatera till kernel version 3.10.0-327.10.1 eller senare. Mer information finns i [En virtuell Azure Linux-dator på en 3.10-baserad kernel-panik efter en uppgradering av värdnoden](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Användarinitierade åtgärder för omstart eller avstängning

Om du gör en omstart från Azure-portalen, Azure PowerShell, kommandoradsgränssnitt eller REST API kan du hitta händelsen i [Azure Activity Log](../../azure-monitor/platform/platform-logs-overview.md).

Om du utför åtgärden från den virtuella datorns operativsystem kan du hitta händelsen i systemloggarna.

Andra scenarier som vanligtvis orsakar den virtuella datorn att starta om inkluderar flera konfigurationsändringsåtgärder. Du ser vanligtvis ett varningsmeddelande som anger att körning av en viss åtgärd kommer att resultera i en omstart av den virtuella datorn. Exempel på detta är alla åtgärder för att ändra storlek på datorn, ändra lösenordet för det administrativa kontot och ange en statisk IP-adress.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center och Windows Update

Azure Security Center övervakar dagliga virtuella Datorer i Windows och Linux för saknade operativsystemuppdateringar. Security Center retrieves a list of available security and critical updates from Windows Update or Windows Server Update Services (WSUS), depending on which service is configured on a Windows VM. Security Center söker också efter de senaste uppdateringarna för Linux-system. Om den virtuella datorn saknar en systemuppdatering rekommenderar Security Center att du installerar systemuppdateringar. Tillämpningen av dessa systemuppdateringar styrs via Security Center i Azure-portalen. När du har tillämpat vissa uppdateringar kan omstarter av virtuella datorer krävas. Mer information finns [i Tillämpa systemuppdateringar i Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Precis som lokala servrar skickar Azure inte uppdateringar från Windows Update till virtuella datorer i Windows, eftersom dessa datorer är avsedda att hanteras av sina användare. Du uppmanas dock att lämna den automatiska Windows Update-inställningen aktiverad. Automatisk installation av uppdateringar från Windows Update kan också orsaka omstarter när uppdateringarna har tillämpats. Mer information finns i [Vanliga frågor och svar om Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andra situationer som påverkar tillgängligheten för din virtuella dator

Det finns andra fall där Azure aktivt kan avbryta användningen av en virtuell dator. Du får e-postmeddelanden innan den här åtgärden vidtas, så att du har en chans att lösa de underliggande problemen. Exempel på problem som påverkar den virtuella datorns tillgänglighet är säkerhetsöverträdelser och förfallodatum för betalningsmetoder.

### <a name="host-server-faults"></a>Värdserverfel

Den virtuella datorn finns på en fysisk server som körs i ett Azure-datacenter. Den fysiska servern kör en agent som kallas värdagenten utöver några andra Azure-komponenter. När dessa Azure-programvarukomponenter på den fysiska servern inte svarar utlöser övervakningssystemet en omstart av värdservern för att försöka återställa. Den virtuella datorn är vanligtvis tillgänglig igen inom fem minuter och fortsätter att leva på samma värd som tidigare.

Serverfel orsakas vanligtvis av maskinvarufel, till exempel fel på en hårddisk eller en solid state-enhet. Azure övervakar kontinuerligt dessa förekomster, identifierar underliggande fel och distribuerar uppdateringar efter att begränsningen har implementerats och testats.

Eftersom vissa värdserverfel kan vara specifika för den servern kan en upprepad omstart av den virtuella datorn förbättras genom att den virtuella datorn distribueras manuellt till en annan värdserver. Den här åtgärden kan utlösas med hjälp av **alternativet omdistribution** på informationssidan för den virtuella datorn, eller genom att stoppa och starta om den virtuella datorn i Azure-portalen.

### <a name="auto-recovery"></a>Automatisk återställning

Om värdservern inte kan startas om av någon anledning initierar Azure-plattformen en automatisk återställningsåtgärd för att ta den felaktiga värdservern ur rotation för vidare undersökning. 

Alla virtuella datorer på den värden flyttas automatiskt till en annan, felfri värdserver. Denna process är vanligtvis klar inom 15 minuter. Mer information om processen för automatisk återställning finns i [Automatisk återställning av virtuella datorer](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Oplanerat underhåll

I sällsynta fall kan Azure-driftteamet behöva utföra underhållsaktiviteter för att säkerställa den övergripande hälsan för Azure-plattformen. Detta kan påverka den virtuella datorns tillgänglighet och resulterar vanligtvis i samma automatiska återställningsåtgärd som beskrivs tidigare.  

Oplanerat underhåll omfattar följande:

- Brådskande noddefragmentering
- Brådskande uppdateringar av nätverksväxlar

### <a name="vm-crashes"></a>VM kraschar

Virtuella datorer kan startas om på grund av problem inom själva den virtuella datorn. Arbetsbelastningen eller rollen som körs på den virtuella datorn kan utlösa en felkontroll i gästoperativsystemet. Om du vill ha hjälp med att fastställa orsaken till kraschen kan du visa system- och programloggarna för virtuella datorer med Windows och seriella loggar för virtuella Linux-datorer.

### <a name="storage-related-forced-shutdowns"></a>Storage-relaterade tvingade avstängningar

Virtuella datorer i Azure är beroende av virtuella diskar för operativsystem och datalagring som finns på Azure Storage-infrastrukturen. När tillgängligheten eller anslutningen mellan den virtuella datorn och de associerade virtuella diskarna påverkas i mer än 120 sekunder utför Azure-plattformen en påtvingad avstängning av de virtuella datorerna för att undvika dataskador. De virtuella datorerna slås automatiskt på igen efter att lagringsanslutningen har återställts. 

Avstängningens varaktighet kan vara så kort som fem minuter men kan vara betydligt längre. Följande är ett av de specifika fall som är associerade med lagringsrelaterade tvångsavstängningar: 

**Överskrider IO-gränsvärden**

Virtuella datorer kan tillfälligt stängas av när I/O-begäranden konsekvent begränsas eftersom volymen I/O-åtgärder per sekund (IOPS) överskrider I/O-gränserna för disken. (Standarddisklagring är begränsad till 500 IOPS.) Om du vill minska problemet använder du diskremsning eller konfigurerar lagringsutrymmet i gästdatorn, beroende på arbetsbelastningen. Mer information finns i [Konfigurera virtuella Azure-datorer för optimal lagringsprestanda](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

### <a name="other-incidents"></a>Andra incidenter

I sällsynta fall kan ett utbrett problem påverka flera servrar i ett Azure-datacenter. Om det här problemet uppstår skickar Azure-teamet e-postmeddelanden till de berörda prenumerationerna. Du kan kontrollera [instrumentpanelen](https://azure.microsoft.com/status/) för Azure Service Health och Azure-portalen för status för pågående avbrott och tidigare incidenter.
