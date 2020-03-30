---
title: Meddelanden om underhåll
description: Översikt över underhållsmeddelanden för virtuella datorer som körs i Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 68159577cb31145be5063bb19af6db71ca1727bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115693"
---
# <a name="handling-planned-maintenance-notifications"></a>Hantera planerade underhållsmeddelanden

Azure utför med jämna mellanrum uppdateringar för att förbättra värdinfrastrukturens tillförlitlighet, prestanda och säkerhet för virtuella datorer. Uppdateringar är ändringar som att korrigera värdmiljön eller uppgradera och inaktivera maskinvara. En majoritet av dessa uppdateringar slutförs utan någon inverkan på de värdbaserade virtuella datorerna. Det finns dock fall där uppdateringar har en inverkan:

- Om underhållet inte kräver en omstart använder Azure migrering på plats för att pausa den virtuella datorn medan värden uppdateras. Dessa typer underhållsåtgärder tillämpas feldomän av feldomän. Förloppet stoppas om några varningshälsosignaler tas emot.

- Om underhåll kräver en omstart får du ett meddelande om när underhållet är planerat. Du får ett tidsfönster på ca 35 dagar där du kan starta underhållet själv, när det fungerar för dig.


Planerat underhåll som kräver en omstart schemaläggs i vågor. Varje våg har olika omfattning (regioner).

- En våg börjar med en avisering till kunder. Som standard skickas meddelandet till tjänstadministratören och medadministratörerna. Du kan lägga till fler mottagare och meddelandealternativ som e-post, SMS och webhooks med hjälp av [aktivitetsloggvarningar](../service-health/alerts-activity-log-service-notifications.md).  
- När ett meddelande slocknar görs ett *fönster med självbetjäning* tillgängligt. Under det här fönstret kan du fråga vilka av dina virtuella datorer som påverkas och starta underhåll baserat på dina egna schemaläggningsbehov. Självbetjäningsfönstret är vanligtvis cirka 35 dagar.
- Efter självbetjäningsfönstret börjar ett *schemalagt underhållsfönster.* Vid något tillfälle under det här fönstret schemalägger och tillämpar Azure det underhåll som krävs på din virtuella dator. 

Målet med att ha två fönster är att ge dig tillräckligt med tid för att starta underhåll och starta om din virtuella dator samtidigt som du vet när Azure automatiskt startar underhåll.


Du kan använda Azure-portalen, PowerShell, REST API och CLI för att fråga efter underhållsfönster för dina virtuella datorer och starta underhåll med självbetjäning.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Ska du börja underhålla under självbetjäningsfönstret?  

Följande riktlinjer bör hjälpa dig att avgöra om du vill använda den här funktionen och starta underhållet vid din egen tidpunkt. 

> [!NOTE] 
> Självbetjäningsunderhåll kanske inte är tillgängligt för alla dina virtuella datorer. Om du vill ta reda på om proaktiv omdistribution är tillgänglig för den virtuella datorn letar du efter **Start nu** i underhållsstatusen. Självbetjäningsunderhåll är för närvarande inte tillgängligt för molntjänster (webb-/arbetarroll) och serviceinfrastruktur.


Självbetjäningsunderhåll rekommenderas inte för distributioner med hjälp av **tillgänglighetsuppsättningar**. Tillgänglighetsuppsättningar uppdateras redan bara en uppdateringsdomän åt gången. 

- Låt Azure utlösa underhållet. För underhåll som kräver omstart, underhåll kommer att göras uppdatera domänen efter uppdatering domän. Uppdateringsdomänerna får inte nödvändigtvis underhållet sekventiellt och att det finns en 30-minuters paus mellan uppdateringsdomäner. 
- Om en tillfällig förlust av viss kapacitet (1 uppdateringsdomän) är ett problem kan du lägga till instanser under underhållsperioden. 
- För underhåll som inte kräver omstart tillämpas uppdateringar på feldomännivå. 

**Använd inte** självbetjäningsunderhåll i följande scenarier: 
- Om du stänger av dina virtuella datorer ofta, antingen manuellt, med DevTest Labs, använder automatisk avstängning eller följer ett schema, kan det återställa underhållsstatusen och därmed orsaka ytterligare driftstopp.
- På kortlivade virtuella datorer som du vet kommer att tas bort före slutet av underhållsvågen. 
- För arbetsbelastningar med ett stort tillstånd som lagras i den lokala (tillfälliga) disken som önskas underhållas vid uppdatering. 
- För fall där du ofta ändrar storlek på den virtuella datorn, eftersom den kan återställa underhållsstatusen. 
- Om du har antagit schemalagda händelser som möjliggör proaktiv redundans eller smidig avstängning av din arbetsbelastning, 15 minuter före underhållsstopp

**Använd** självbetjäningsunderhåll om du planerar att köra den virtuella datorn utan avbrott under den schemalagda underhållsfasen och ingen av de motindikationer som nämns ovan är tillämpliga. 

Det är bäst att använda underhåll av självbetjäning i följande fall:
- Du måste kommunicera ett exakt underhållsfönster till din ledning eller slutkund. 
- Du måste slutföra underhållet vid ett visst datum. 
- Du måste styra sekvensen av underhåll, till exempel program med flera nivåer för att garantera säker återställning.
- Mer än 30 minuter av vm-återställningstid behövs mellan två uppdateringsdomäner (UDs). Om du vill styra tiden mellan uppdateringsdomäner måste du utlösa underhåll på dina virtuella datorer en uppdateringsdomän (UD) åt gången.


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR


**F: Varför behöver du starta om mina virtuella datorer nu?**

**A.** De flesta uppdateringar och uppgraderingar till Azure-plattformen påverkar inte den virtuella datorns tillgänglighet, men det finns fall där vi inte kan undvika att starta om virtuella datorer som finns i Azure. Vi har samlat flera ändringar som kräver att vi startar om våra servrar som kommer att resultera i virtuella datorer omstart.

**F: Om jag följer dina rekommendationer för hög tillgänglighet med hjälp av ett tillgänglighetsuppsättning, är jag säker?**

**A.** Virtuella datorer som distribueras i en tillgänglighetsuppsättning eller skalningsuppsättningar för virtuella datorer har begreppet Uppdatera domäner (UD). När du utför underhåll respekterar Azure UD-begränsningen och startar inte om virtuella datorer från olika UD (inom samma tillgänglighetsuppsättning).  Azure väntar också i minst 30 minuter innan du flyttar till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [Tillgänglighet för virtuella datorer i Azure](./linux/availability.md).

**F: Hur får jag ett meddelande om planerat underhåll?**

**A.** En planerad underhållsvåg börjar med att ange ett schema till en eller flera Azure-regioner. Strax efter skickas ett e-postmeddelande till prenumerationen Administratörer (ett e-postmeddelande per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med hjälp av aktivitetsloggaviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan är schemalagt, får du inte meddelandet utan behöver snarare kontrollera underhållstillståndet för den virtuella datorn.

**F: Jag ser ingen indikation på planerat underhåll i portalen, Powershell eller CLI. Vad är fel?**

**A.** Information om planerat underhåll är tillgänglig under en planerad underhållsvåg endast för de virtuella datorer som kommer att påverkas av den. Med andra ord, om du inte ser data, kan det vara så att underhållsvågen redan har slutförts (eller inte startats) eller att den virtuella datorn redan finns i en uppdaterad server.

**F: Finns det något sätt att veta exakt när min virtuella dator kommer att påverkas?**

**A.** När du ställer in schemat definierar vi ett tidsfönster på flera dagar. Den exakta sekvenseringen av servrar (och virtuella datorer) i det här fönstret är dock okänd. Kunder som vill veta den exakta tiden för sina virtuella datorer kan använda [schemalagda händelser](./linux/scheduled-events.md) och frågor inifrån den virtuella datorn och få ett 15-minutersmeddelande innan en omstart av den virtuella datorn.

**F: Hur lång tid tar det för dig att starta om min virtuella dator?**

**A.**  Beroende på storleken på den virtuella datorn kan omstarten ta upp till flera minuter under underhållsfönstret för självbetjäning. Under azure-initierade omstarter i det schemalagda underhållsfönstret tar omstarten vanligtvis cirka 25 minuter. Observera att om du använder Molntjänster (webb-/arbetarroll), skalningsuppsättningar för virtuella datorer eller tillgänglighetsuppsättningar, får du 30 minuter mellan varje grupp av virtuella datorer (UD) under det schemalagda underhållsfönstret.

**F: Vad är upplevelsen när det gäller skaluppsättningar för virtuella datorer?**

**A.** Planerat underhåll är nu tillgängligt för skalningsuppsättningar för virtuella datorer. Instruktioner om hur du initierar underhåll med självbetjäning finns [planerat underhåll för skalningsuppsättningar för virtuella datorer.](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)

**F: Vad är upplevelsen när det gäller molntjänster (webb-/arbetsroll) och serviceinfrastruktur?**

**A.** Även om dessa plattformar påverkas av planerat underhåll, anses kunder som använder dessa plattformar vara säkra med tanke på att endast virtuella datorer i en enda uppgraderingsdomän (UD) kommer att påverkas vid en viss tidpunkt. Självbetjäningsunderhåll är för närvarande inte tillgängligt för molntjänster (webb-/arbetarroll) och serviceinfrastruktur.

**F: Jag ser ingen underhållsinformation på mina virtuella datorer. Vad gick fel?**

**A.** Det finns flera anledningar till att du inte ser någon underhållsinformation på dina virtuella datorer:
1.  Du använder en prenumeration som är markerad som Microsofts interna.
2.  Dina virtuella datorer är inte schemalagda för underhåll. Det kan vara så att underhållsvågen har avslutats, avbrutits eller ändrats så att dina virtuella datorer inte längre påverkas av den.
3.  Du har inte kolumnen **Underhåll** tillagt i listvyn för den virtuella datorn. Vi har lagt till den här kolumnen i standardvyn, men kunder som har konfigurerats för att se kolumner som inte är standard måste manuellt lägga till kolumnen **Underhåll** i sin vm-listvy.

**F: Min virtuella dator är schemalagd för underhåll för andra gången. Varför?**

**A.** Det finns flera användningsfall där du ser din virtuella dator schemalagd för underhåll när du redan har slutfört din omdistribution av underhåll:
1.  Vi har avbrutit underhållsvågen och startat om den med en annan nyttolast. Det kan vara så att vi har upptäckt fel nyttolast och vi behöver bara distribuera en extra nyttolast.
2.  Den virtuella datorn *har läkts* till en annan nod på grund av ett maskinvarufel.
3.  Du har valt att stoppa (deallocate) och starta om den virtuella datorn.
4.  Du har **aktiverat automatisk avstängning** för den virtuella datorn.



## <a name="next-steps"></a>Nästa steg

Du kan hantera planerat underhåll med [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) eller [portal](maintenance-notifications-portal.md).

