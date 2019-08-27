---
title: ta med fil
description: ta med fil
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020359"
---
Azure uppdaterar regelbundet sin plattform för att förbättra tillförlitligheten, prestanda och säkerheten i värd infrastrukturen för virtuella datorer. Syftet med dessa uppdateringar är att uppdatera program varu komponenter i värd miljön för att uppgradera nätverks komponenter eller inaktivera maskin vara. 

Uppdateringar påverkar sällan de värdar som är värdar för virtuella datorer. När uppdateringar har en effekt väljer Azure den minsta påverkan metoden för uppdateringar:

- Om uppdateringen inte kräver en omstart pausas den virtuella datorn medan värden uppdateras, eller så är den virtuella datorn Live-migrerad till en redan uppdaterad värd.

- Om underhåll kräver en omstart får du ett meddelande om planerat underhåll. Azure tillhandahåller också ett tids fönster där du kan starta underhållet själv, vid en tidpunkt som passar dig. Fönstret för själv underhåll är vanligt vis 35 dagar om inte underhållet är brådskande. Azure investerar i teknikerna för att minska antalet fall där det planerade plattforms underhållet kräver att de virtuella datorerna startas om. 

Den här sidan beskriver hur Azure utför båda typerna av underhåll. Mer information om oplanerade händelser (avbrott) finns i [Hantera tillgängligheten för virtuella datorer för Windows](../articles/virtual-machines/windows/manage-availability.md) eller motsvarande artikel för [Linux](../articles/virtual-machines/linux/manage-availability.md).

I en virtuell dator kan du få aviseringar om kommande underhåll genom [att använda schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md) eller [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Anvisningar om hur du hanterar planerat underhåll finns i [Hantera planerade underhålls meddelanden för Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller motsvarande artikel för [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Underhåll som inte kräver en omstart

Som tidigare nämnts påverkar de flesta plattforms uppdateringar inte kundens virtuella datorer. När en uppdatering utan effekt inte är möjlig väljer Azure den uppdaterings metod som minst påverkar kundens virtuella datorer. 

De flesta underhålls effekter som inte är noll pausar den virtuella datorn i mindre än 10 sekunder. I vissa fall använder Azure minnes konserverings metoder för underhåll. Dessa mekanismer pausar den virtuella datorn i upp till 30 sekunder och bevarar minnet i RAM-minnet. Den virtuella datorn återupptas sedan och klockan synkroniseras automatiskt. 

Underhåll av minnes underhåll fungerar i mer än 90 procent av virtuella Azure-datorer. Den fungerar inte för G-, M-, N-och H-serien. Azure använder i allt större utsträckning teknik för Live-migrering och förbättrar underhålls metoder för att minska paus tiderna.  

De underhålls åtgärder som inte kräver en omstart tillämpas en fel domän i taget. De stoppas om de får varnings hälso signaler. 

De här typerna av uppdateringar kan påverka vissa program. När den virtuella datorn är Live-migrerad till en annan värd kan vissa känsliga arbets belastningar Visa en försämrad prestanda försämring under några minuter, vilket leder till att den virtuella datorn pausas. Om du vill förbereda för underhåll av virtuella datorer och minska påverkan under Azure-underhållet kan du försöka [använda schemalagda händelser för Windows](../articles/virtual-machines/windows/scheduled-events.md) eller [Linux](../articles/virtual-machines/linux/scheduled-events.md) för sådana program. Azure ger också fullständig kontroll över sådana plattforms underhåll som inte är noll på [Azure-dedikerade värdar](../articles/virtual-machines/windows/dedicated-hosts.md) och isolerade [virtuella datorer](../articles/security/fundamentals/isolation-choices.md). Funktionen underhålls kontroll är i för hands version och du kan begära åtkomst genom att skicka [registrerings formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u). Det ger dig möjlighet att hoppa över uppdateringar av plattforms uppdateringar som inte är noll och tillämpa uppdateringarna som en batch vid en tidpunkt som du väljer inom en 35-dagars rullande fönster.

### <a name="live-migration"></a>Direktmigrering

Direktmigrering är en åtgärd som inte kräver en omstart och som bevarar minnet för den virtuella datorn. Det orsakar en paus eller frysning, vanligt vis inte mer än 5 sekunder. Förutom G-, M-, N-och H-serien är alla virtuella IaaS-datorer (Infrastructure as a Service) tillgängliga för direktmigrering. Kvalificerade virtuella datorer representerar över 90 procent av de virtuella IaaS-datorer som distribueras till Azure-flottan. 

Azure-plattformen startar Direktmigrering i följande scenarier:
- Planerat underhåll
- Maskin varu problem
- Fördelnings optimeringar

Vissa planerade underhålls scenarier använder direktmigrering och du kan använda Schemalagda händelser för att känna i förväg när direktmigrering ska starta.

Direktmigrering kan också användas för att flytta virtuella datorer när Azure Machine Learning algoritmer förutsäger ett förestående maskin varu haveri eller när du vill optimera VM-allokeringar. Mer information om förutsägelse modellering som identifierar instanser av försämrad maskin vara finns i [förbättra Azure VM-återhämtning med förutsägande Machine Learning och Direktmigrering](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Meddelanden om Live-migrering visas i Azure Portal i övervakaren och Service Health loggar samt i Schemalagda händelser om du använder dessa tjänster.

## <a name="maintenance-that-requires-a-reboot"></a>Underhåll som kräver en omstart

I sällsynta fall där virtuella datorer måste startas om för planerat underhåll får du ett meddelande i förväg. Planerat underhåll har två faser: självbetjänings fasen och en schemalagd underhålls fas.

Under självbetjänings *fasen*, som vanligt vis varar fyra veckor, startar du underhållet på dina virtuella datorer. Som en del av självbetjäningen kan du fråga varje virtuell dator för att se dess status och resultatet av din senaste underhålls förfrågan.

När du startar självbetjänings underhållet distribueras den virtuella datorn om till en redan uppdaterad nod. Eftersom den virtuella datorn startas om går den temporära disken förlorad och dynamiska IP-adresser som är kopplade till det virtuella nätverks gränssnittet uppdateras.

Om ett fel uppstår under självbetjänings underhållet stoppas åtgärden, den virtuella datorn uppdateras inte och du får möjlighet att försöka utföra självbetjänings underhållet igen. 

När självbetjänings fasen avslutas börjar *fasen* för det schemalagda underhållet. Under den här fasen kan du fortfarande fråga efter underhålls fasen, men du kan inte starta underhållet själv.

Mer information om hur du hanterar underhåll som kräver en omstart finns i [Hantera planerade underhålls aviseringar för Linux](../articles/virtual-machines/linux/maintenance-notifications.md) eller motsvarande artikel för [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Tillgänglighets överväganden under schemalagt underhåll 

Om du väljer att vänta tills den schemalagda underhålls fasen finns det några saker som du bör tänka på när du behåller den högsta tillgängligheten för dina virtuella datorer. 

#### <a name="paired-regions"></a>Länkade regioner

Varje Azure-region är kopplad till en annan region inom samma geografiska närhet. Tillsammans skapar de ett region par. Under den schemalagda underhålls fasen uppdaterar Azure bara de virtuella datorerna i en region i ett region par. Till exempel, när du uppdaterar den virtuella datorn i norra centrala USA, uppdaterar Azure inte någon virtuell dator i södra centrala USA på samma tid. Andra regioner än Europa, norra kan emellertid ges underhåll samtidigt som USA, östra. Att förstå hur regions par fungerar kan hjälpa dig att distribuera dina virtuella datorer i flera regioner bättre. Mer information finns i [regions par för Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Tillgänglighets uppsättningar och skalnings uppsättningar

När du distribuerar en arbets belastning på virtuella Azure-datorer kan du skapa de virtuella datorerna i en *tillgänglighets uppsättning* för att ge hög tillgänglighet till ditt program. Med hjälp av tillgänglighets uppsättningar kan du se till att minst en virtuell dator är tillgänglig under ett avbrott eller underhålls händelser som kräver en omstart.

I en tillgänglighets uppsättning sprids enskilda virtuella datorer över upp till 20 uppdaterings domäner (UDs). Under det schemalagda underhållet uppdateras bara en UD vid en specifik tidpunkt. UDs uppdateras inte nödvändigt vis i tur och ordning. 

Skalnings *uppsättningar* för virtuella datorer är en Azure Compute-resurs som du kan använda för att distribuera och hantera en uppsättning identiska virtuella datorer som en enda resurs. Skalnings uppsättningen distribueras automatiskt över UDs, t. ex. virtuella datorer i en tillgänglighets uppsättning. Precis som med tillgänglighets uppsättningar, när du använder skalnings uppsättningar, uppdateras bara en UD vid en bestämd tidpunkt under det schemalagda underhållet.

Mer information om hur du konfigurerar virtuella datorer för hög tillgänglighet finns i [Hantera tillgängligheten för dina virtuella datorer för Windows](../articles/virtual-machines/windows/manage-availability.md) eller motsvarande artikel för [Linux](../articles/virtual-machines/linux/manage-availability.md).
