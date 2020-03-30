---
title: Underhåll och uppdateringar
description: Översikt över underhåll och uppdateringar för virtuella datorer som körs i Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250236"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Underhåll för virtuella datorer i Azure

Azure uppdaterar regelbundet sin plattform för att förbättra tillförlitligheten, prestandan och säkerheten för värdinfrastrukturen för virtuella datorer. Syftet med dessa uppdateringar är att uppdatera programvarukomponenter i värdmiljön för att uppgradera nätverkskomponenter eller inaktivera maskinvara. 

Uppdateringar påverkar sällan de värdbaserade virtuella datorerna. När uppdateringar har en effekt väljer Azure den minst effektkänsliga metoden för uppdateringar:

- Om uppdateringen inte kräver en omstart pausas den virtuella datorn medan värden uppdateras eller så migreras den virtuella datorn till en redan uppdaterad värd. 
- Om underhåll kräver en omstart får du ett meddelande om det planerade underhållet. Azure tillhandahåller också ett tidsfönster där du kan starta underhållet själv, vid en tidpunkt som fungerar för dig. Självunderhållsfönstret är vanligtvis 30 dagar om inte underhållet är brådskande. Azure investerar i tekniker för att minska antalet fall där planerat plattformsunderhåll kräver att de virtuella datorerna startas om. Instruktioner om hur du hanterar planerat underhåll finns i Hantera planerade underhållsmeddelanden med Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md).

På den här sidan beskrivs hur Azure utför båda typerna av underhåll. Mer information om oplanerade händelser (avbrott finns i [Hantera tillgängligheten för virtuella datorer för Windows](./windows/manage-availability.md) eller motsvarande artikel för [Linux](./linux/manage-availability.md).

Inom en virtuell dator kan du få meddelanden om kommande underhåll [genom att använda schemalagda händelser för Windows](./windows/scheduled-events.md) eller För [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Underhåll som inte kräver en omstart

De flesta plattformsuppdateringar påverkar inte kundens virtuella datorer. När en uppdatering utan påverkan inte är möjlig väljer Azure den uppdateringsmekanism som är minst effektkänslig för kundens virtuella datorer. 

De flesta underhåll som inte påverkar den virtuella datorn pausar den virtuella datorn i mindre än 10 sekunder. I vissa fall använder Azure minnesbevarande underhållsmekanismer. Dessa mekanismer pausar den virtuella datorn i upp till 30 sekunder och bevarar minnet i RAM.These mechanisms pause the VM for up to 30 seconds and preserve the memory in RAM. Den virtuella datorn återupptas sedan och klockan synkroniseras automatiskt. 

Minnesbevarande underhåll fungerar för mer än 90 procent av virtuella Azure-datorer. Det fungerar inte för G, M, N och H-serien. Azure använder i allt högre grad direktmigreringsteknik och förbättrar underhållsmekanismer för minnesbevarande för att minska pausvaraktigheterna.  

Dessa underhållsåtgärder som inte kräver en omstart tillämpas en feldomän i taget. De slutar om de får några varningssignaler. 

Dessa typer av uppdateringar kan påverka vissa program. När den virtuella datorn är live-migrerad till en annan värd, kan vissa känsliga arbetsbelastningar visa en liten prestandaförsämring under de få minuter som leder fram till den virtuella datorn paus. Om du vill förbereda för vm-underhåll och minska påverkan under Azure-underhåll kan du prova [att använda schemalagda händelser för Windows](./windows/scheduled-events.md) eller [Linux](./linux/scheduled-events.md) för sådana program. 

Det finns också en funktion, underhållskontroll, i offentlig förhandsversion som kan hjälpa till att hantera underhåll som inte kräver en omstart. Du måste använda antingen [Azure Dedicated Hosts](./linux/dedicated-hosts.md) eller en [isolerad virtuell dator](../security/fundamentals/isolation-choices.md). Underhållskontroll ger dig möjlighet att hoppa över plattformsuppdateringar och tillämpa uppdateringarna vid ditt val av tid inom ett rullande 35-dagars fönster. Mer information finns i [Kontrollera uppdateringar med underhållskontroll och Azure CLI](maintenance-control-cli.md).


### <a name="live-migration"></a>Direktmigrering

Live migrering är en åtgärd som inte kräver en omstart och som bevarar minnet för den virtuella datorn. Det orsakar en paus eller frysning, vanligtvis varar högst 5 sekunder. Med undantag för virtuella datorer i G-, M-, N- och H-serien är all infrastruktur som en tjänst (IaaS) virtuella datorer berättigade till direktmigrering. Kvalificerade virtuella datorer representerar mer än 90 procent av de virtuella IaaS-datorer som distribueras till Azure-flottan. 

Azure-plattformen startar direktmigrering i följande scenarier:
- Planerat underhåll
- Maskinvarufel
- Allokeringsoptimeringar

Vissa scenarier för planerat underhåll använder livemigrering och du kan använda schemalagda händelser för att veta i förväg när direktmigreringsåtgärder startar.

Live migrering kan också användas för att flytta virtuella datorer när Azure Machine Learning-algoritmer förutsäga ett förestående maskinvarufel eller när du vill optimera VM-allokeringar. Mer information om förutsägande modellering som identifierar instanser av försämrad maskinvara finns i [Förbättra Azure VM-återhämtning med prediktiv maskininlärning och livemigrering](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Aviseringar om livemigrering visas i Azure-portalen i monitor- och tjänsthälsologgarna samt i schemalagda händelser om du använder dessa tjänster.

## <a name="maintenance-that-requires-a-reboot"></a>Underhåll som kräver en omstart

I de sällsynta fall där virtuella datorer måste startas om för planerat underhåll, kommer du att meddelas i förväg. Planerat underhåll har två faser: självbetjäningsfasen och en schemalagd underhållsfas.

Under *självbetjäningsfasen*, som vanligtvis varar i fyra veckor, startar du underhållet på dina virtuella datorer. Som en del av självbetjäning kan du fråga varje virtuell dator för att se dess status och resultatet av din senaste underhållsbegäran.

När du startar självbetjäningsunderhåll distribueras den virtuella datorn till en redan uppdaterad nod. Eftersom den virtuella datorn startas om går den tillfälliga disken förlorad och dynamiska IP-adresser som är associerade med det virtuella nätverksgränssnittet uppdateras.

Om ett fel uppstår under självbetjäningsunderhåll stoppas inte åtgärden, den virtuella datorn uppdateras inte och du får möjlighet att försöka med självbetjäningsunderhållet igen. 

När självbetjäningsfasen är börjar den *schemalagda underhållsfasen.* Under den här fasen kan du fortfarande fråga efter underhållsfasen, men du kan inte starta underhållet själv.

Mer information om hur du hanterar underhåll som kräver en omstart finns i **Hantera planerade underhållsmeddelanden** med Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Tillgänglighetsöverväganden under planerat underhåll 

Om du bestämmer dig för att vänta tills den schemalagda underhållsfasen finns det några saker du bör tänka på för att behålla den högsta tillgängligheten för dina virtuella datorer. 

#### <a name="paired-regions"></a>Länkade regioner

Varje Azure-region är parkopplad med en annan region inom samma geografiska närhet. Tillsammans gör de ett regionpar. Under den schemalagda underhållsfasen uppdaterar Azure endast de virtuella datorerna i en enda region i ett regionpar. När du till exempel uppdaterar den virtuella datorn i norra centrala USA uppdaterar Azure inte någon virtuell dator i södra centrala USA samtidigt. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Om du förstår hur regionpar fungerar kan du bättre distribuera dina virtuella datorer mellan regioner. Mer information finns i [Azure-regionpar](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Tillgänglighetsuppsättningar och skalningsuppsättningar

När du distribuerar en arbetsbelastning på virtuella Azure-datorer kan du skapa de virtuella datorerna i en *tillgänglighetsuppsättning* för att ge hög tillgänglighet till ditt program. Med hjälp av tillgänglighetsuppsättningar kan du se till att minst en virtuell dator är tillgänglig under antingen ett avbrott eller underhållshändelser som kräver en omstart.

Inom en tillgänglighetsuppsättning är enskilda virtuella datorer spridda över upp till 20 uppdateringsdomäner. Under schemalagt underhåll uppdateras endast en uppdateringsdomän vid en given tidpunkt. Uppdateringsdomäner uppdateras inte nödvändigtvis sekventiellt. 

*Skalningsuppsättningar* för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer som en enda resurs. Skalningsuppsättningen distribueras automatiskt över UD:er, till exempel virtuella datorer i en tillgänglighetsuppsättning. Precis som med tillgänglighetsuppsättningar uppdateras endast en UD vid en viss tidpunkt under schemalagt underhåll när du använder skalningsuppsättningar.

Mer information om hur du konfigurerar virtuella datorer för hög tillgänglighet finns i [Hantera tillgängligheten för dina virtuella datorer för Windows](./windows/manage-availability.md) eller motsvarande artikel för [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Tillgänglighetszoner

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. 

En tillgänglighetszon är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller fler virtuella datorer i tre zoner i en Azure-region distribueras dina virtuella datorer effektivt mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Varje infrastrukturuppdatering distribuerar zon för zon, inom en enda region. Men du kan ha distributionen pågår i zon 1 och olika distribution som går i zon 2, samtidigt. Distributioner är inte alla serialiserade. Men en enda distribution distribuerar bara ut en zon i taget för att minska risken.

## <a name="next-steps"></a>Nästa steg 

Du kan använda [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md) för att hantera planerat underhåll. 