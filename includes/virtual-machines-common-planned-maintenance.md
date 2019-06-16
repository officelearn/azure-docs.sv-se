---
title: ta med fil
description: ta med fil
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66155914"
---
Azure uppdaterar regelbundet dess plattform för att förbättra tillförlitligheten, prestanda och säkerheten för infrastrukturen för värd för virtuella datorer. Syftet med de här uppdateringarna mellan korrigeringar programvarukomponenter i värdmiljön uppgradering nätverkskomponenter eller inaktivering av maskinvara. 

Uppdateringar av påverkar data som sällan de värdbaserade virtuella datorerna. När uppdateringar har en effekt, väljer Azure metoden minst påverkar efter uppdateringar:

- Om uppdateringen kräver ingen omstart, pausas den virtuella datorn medan värden uppdateras, eller den virtuella datorn är live-migreras till en värd som redan är uppdaterad.

- Om en omstart krävs för underhåll, får du meddelanden planerat underhåll. Azure tillhandahåller även ett tidsfönster som du kan starta underhållet själv, samtidigt som passar dig. Fönstret självunderhåll är vanligtvis 30 dagar såvida inte underhållet är brådskande. Azure investeringar i att minska antalet fall som plattform för planerat underhåll kräver de virtuella datorerna startas om. 

Den här sidan beskriver hur Azure utför båda typerna av underhåll. Läs mer om oplanerade händelser (avbrott), [hantera tillgängligheten för virtuella datorer för Windows](../articles/virtual-machines/windows/manage-availability.md) eller motsvarande artikel för [Linux](../articles/virtual-machines/linux/manage-availability.md).

En virtuell dator kan du få meddelanden om kommande underhåll av [med schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md) eller för [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Anvisningar om hur du hanterar planerat underhåll finns i [hantering av meddelanden för planerat underhåll Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller motsvarande artikel för [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Underhåll som inte kräver en omstart

Som tidigare nämnts påverkar inte de flesta plattformsuppdateringar kundens virtuella datorer. När en uppdatering utan påverkan inte är möjligt väljer Azure uppdateringsmekanism som är minst impactful till kundens virtuella datorer. 

De flesta noll påverkan Underhåll pausar den virtuella datorn för mindre än 10 sekunder. I vissa fall används minnesbevarande mekanismer för underhåll i Azure. Dessa mekanismer pausar den virtuella datorn i upp till 30 sekunder och bevarar minnet i RAM-minne. Virtuellt datorn sedan återupptas och synkroniseras klockan automatiskt. 

Minnesbevarande Underhåll fungerar för mer än 90 procent av virtuella Azure-datorer. Det fungerar inte för G, M, N och H-serien. Azure allt mer använder Direktmigrering tekniker och förbättrar minnesbevarande Underhåll mekanismer för att minska pausa varaktigheter.  

Dessa underhållsåtgärder som inte kräver en omstart är tillämpad en feldomän i taget. De stoppas om de får någon varning hälsotillstånd signaler. 

Dessa typer av uppdateringar kan påverka vissa program. När den virtuella datorn är live-migreras till en annan värd, kan vissa känsliga arbetsbelastningar visas en liten prestandaförsämring i några minuter ledde till VM-pausa. För att förbereda för underhåll av virtuell dator och minska påverkan under Azure-underhåll, försök [med schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md) eller [Linux](../articles/virtual-machines/linux/scheduled-events.md) för sådana program. Azure fungerar på Underhåll-control-funktioner för de här känsliga program. 

### <a name="live-migration"></a>Direktmigrering

Direktmigrering är en åtgärd som kräver ingen omstart och som bevarar minnet för den virtuella datorn. Orsakar en paus eller -låsning, vanligtvis långvarigt mer än 5 sekunder. Förutom G, M, N och H-serien, all infrastruktur som en tjänst (IaaS) virtuella datorer som är berättigade för Direktmigrering. Berättigade virtuella datorer representerar mer än 90 procent av den virtuella IaaS-datorer som distribueras i Azure beståndet. 

Azure-plattformen startar Direktmigrering i följande scenarier:
- Planerat underhåll
- Maskinvarufel
- Allokering optimeringar

Använda Direktmigrering för vissa scenarier för planerat underhåll och du kan använda schemalagda händelser i förväg veta när du live migreringen startar.

Direktmigrering kan också användas för att flytta virtuella datorer när Azure Machine Learning-algoritmer förutsäga ett nära förestående maskinvarufel eller när du vill optimera VM-allokeringar. Läs mer om förutsägande modellering som identifierar instanser av försämrad maskinvara, [förbättra Azure VM-känslighet med förutsägande machine learning och Direktmigrering](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Direktmigrering aviseringar visas i Azure-portalen i övervakaren och Tjänsthälsa loggarna samt som schemalagda händelser om du använder de här tjänsterna.

## <a name="maintenance-that-requires-a-reboot"></a>Underhåll som kräver omstart

I de sällsynta fall där virtuella datorer måste startas om för planerat underhåll, kommer du att meddelas i förväg. Planerat underhåll har två faser: fasen självbetjäning och fasen för schemalagt underhåll.

Under den *självbetjäning fas*, som vanligtvis gäller i fyra veckor, du startar underhållet på dina virtuella datorer. Som en del i självbetjäningen kan fråga du varje virtuell dator om du vill se status och resultatet av din senaste begäran.

När du startar självbetjäningsunderhållet, om den virtuella datorn till en nod som redan är uppdaterad. Eftersom den virtuella datorn om den temporära disken går förlorad och den dynamiska IP-adresser som är associerade med det virtuella nätverksgränssnittet har uppdaterats.

Om ett fel uppstår under självbetjäningsunderhållet, åtgärden stoppar, uppdateras inte den virtuella datorn och du får möjlighet att göra om Underhåll självbetjäning. 

När den självbetjäning fasen avslutas den *fasen för schemalagt underhåll* börjar. Under den här fasen kan du fortfarande kan fråga efter fasen för underhåll, men du kan inte starta underhållet själv.

Läs mer om hur du hanterar underhåll som kräver en omstart, [hantering av meddelanden för planerat underhåll Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller motsvarande artikel för [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Överväganden för tillgänglighet under schemalagt underhåll 

Om du vill vänta tills fasen för schemalagt underhåll, finns det några saker du bör överväga för att upprätthålla högsta möjliga tillgänglighet för dina virtuella datorer. 

#### <a name="paired-regions"></a>Länkade regioner

Varje Azure-region är kopplad till en annan region inom samma geografiska område. De gör tillsammans en regionparet. Under fasen för schemalagt underhåll uppdaterar Azure endast de virtuella datorerna i en enda region för en regionparet. Till exempel vid uppdatering av den virtuella datorn i norra centrala USA, uppdatera Azure inte virtuella datorer i USA, södra centrala på samma gång. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Förstå hur regionpar fungerar kan hjälpa dig att bättre att distribuera dina virtuella datorer mellan regioner. Mer information finns i [Azure regionpar](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Tillgänglighetsuppsättningar och skalningsuppsättningar

När du distribuerar en arbetsbelastning på Azure Virtual Machines kan du skapa virtuella datorer i en *tillgänglighetsuppsättning* att ge hög tillgänglighet för ditt program. Med tillgänglighetsuppsättningar kan kontrollera du att minst en virtuell dator under ett avbrott eller underhåll händelser som kräver en omstart, är tillgängliga.

Enskilda virtuella datorer är fördelade på upp till 20 uppdateringsdomäner (ud) i en tillgänglighetsuppsättning. Under det schemalagda underhållet uppdateras bara en UD vid en given tidpunkt. Ud är inte nödvändigtvis uppdaterade sekventiellt. 

Virtuell dator *skalningsuppsättningar* är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer som en enskild resurs. Skalningsuppsättningen distribueras automatiskt över ud som virtuella datorer i en tillgänglighetsuppsättning. Som med tillgänglighetsuppsättningar uppdateras när du använder skalningsuppsättningar, endast en UD vid en given tidpunkt under schemalagt underhåll.

Mer information om hur du konfigurerar dina virtuella datorer för hög tillgänglighet finns i [hantera tillgängligheten för dina virtuella datorer för Windows](../articles/virtual-machines/windows/manage-availability.md) eller motsvarande artikel för [Linux](../articles/virtual-machines/linux/manage-availability.md).
