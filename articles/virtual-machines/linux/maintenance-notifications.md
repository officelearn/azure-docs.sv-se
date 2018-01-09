---
title: "Hantera Underhåll meddelanden för Linux virtuella datorer i Azure | Microsoft Docs"
description: "Visa aviseringar för underhåll för Linux virtuella datorer som körs i Azure och starta självbetjäning underhåll."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zivr
ms.openlocfilehash: d551a62a59e0a7f63f5fd4862680a271de659a19
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Hantering av planerat underhåll meddelanden för Linux virtuella datorer

Azure utför regelbundet uppdateringar för att förbättra tillförlitligheten, prestanda och säkerhet för infrastrukturen för värd för virtuella datorer. Uppdateringar är ändringar som korrigering värdmiljön eller uppgradera och inaktivering av maskinvara. En majoritet av uppdateringarna utförs utan någon inverkan på de virtuella datorerna. Det finns dock fall där uppdateringar har konsekvenser:

- Om underhåll som inte kräver en omstart, använder Azure migrering på plats för att pausa den virtuella datorn när värden har uppdaterats.

- Om en omstart krävs för underhåll, får du ett meddelande om vid underhåll är planerade. I dessa fall måste ges ett tidsfönster där du kan starta underhållet själv när det passar dig.


Planerat underhåll som kräver omstart schemaläggs i waves. Varje wave har ett annat område (regioner).

- En våg börjar med ett meddelande till kunder. Som standard skickas meddelandet till prenumerationen ägare och Medägare. Du kan lägga till fler meddelanden alternativ som e-post, SMS och webhooks, och mottagare till meddelanden med Azure [aktivitet loggen aviseringar](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- Vid tidpunkten för meddelandet, en *självbetjäning fönstret* görs tillgänglig. Under det här fönstret kan du söka efter vilka av dina virtuella datorer ingår i den här wave och proaktivt starta Underhåll efter behov schemaläggning.
- När fönstret självbetjäning en *schemalagda underhållsperiod* börjar. Någon gång under det här fönstret Azure schemalägger och gäller det nödvändiga underhållet för den virtuella datorn. 

Målet med att ha två windows är att ge tillräckligt med tid för att starta underhåll och starta om den virtuella datorn samtidigt som du vet när Azure startar automatiskt underhåll.


Du kan använda Azure-portalen, PowerShell, REST-API och CLI för att fråga efter underhållsperioderna för dina virtuella datorer och starta självbetjäning underhåll.

 > [!NOTE]
 > Om du försöker starta underhåll och begäran misslyckas, Azure markerar den virtuella datorn som **hoppas över**. Du kommer inte längre att kunna använda alternativet kunden initierade underhåll. Den virtuella datorn måste startas av Azure under fasen schemalagt underhåll.


 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Bör du börja Underhåll använda under fönstret självbetjäning?  

Följande riktlinjer hjälper dig att avgöra om du ska använda den här funktionen och starta Underhåll på din egen tid.

> [!NOTE] 
> Självbetjäning Underhåll kanske inte är tillgängliga för alla dina virtuella datorer. För att avgöra om proaktiv Omdistributionen är tillgänglig för den virtuella datorn, leta efter den **starta nu** statusen underhåll. Självbetjäning Underhåll är inte tillgängligt för molntjänster (Web/Worker-rollen), Service Fabric och virtuella datorer.


Självbetjäning Underhåll rekommenderas inte för distributioner med hjälp av **tillgänglighetsuppsättningar** eftersom dessa inställningar för hög tillgänglighet, där endast en uppdateringsdomän påverkas vid en given tidpunkt. 
    - Låta Azure utlösaren underhållet, men tänk på att ordningen för update-domäner som påverkas inte nödvändigtvis sker sekventiellt och att det finns en paus på 30 minuter mellan domäner för uppdateringen.
    - Om en temporär förlust av vissa av dina kapacitet (1/uppdateringsdomänantalet) är ett problem, kan den enkelt kompenseras genom att allokera ytterligare instanser under underhållsperioden. 

**Inte** använder självbetjäning Underhåll i följande scenarier: 
    - Om du stänger av din virtuella dator ofta, antingen manuellt med hjälp av DevTest labs, med hjälp av automatisk avstängning eller enligt ett schema den kunde Återställ status för underhåll och därför orsaka ytterligare driftstopp.
    - På tillfällig virtuella datorer som du vet att tas bort före utgången av Underhåll wave. 
    - För arbetsbelastningar med en stor tillstånd lagras i den lokala (tillfälliga) disken som önskas bevaras vid uppdatering. 
    - I de fall där du ändrar storlek på den virtuella datorn ofta, eftersom den kan återställa Underhåll status. 
    - Om du har valt schemalagda händelser som aktiverar proaktiv växling vid fel eller korrekt avslutning av din arbetsbelastning 15 minuter innan Underhåll avstängning

**Använd** självbetjäning underhåll, om du planerar att köra den virtuella datorn utan avbrott under fasen schemalagt underhåll och ingen plats uppgifter som nämns ovan är tillämpliga. 

Det är bäst att använda självbetjäning Underhåll i följande fall:
    - Du behöver kommunicera en exakt underhållsperiod till hanterings- eller end-kund. 
    - Du behöver utföra underhåll av ett visst datum. 
    - Du behöver styra sekvensen av underhåll, t.ex. flernivåapp att säkerställa säker återställning.
    - Du behöver mer än 30 minuter tiden för återställning av virtuell dator mellan två update domäner (UDs). Om du vill styra tiden mellan uppdatering domäner utlösa du Underhåll på din virtuella datorer en uppdateringsdomän (UD) i taget.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Virtuella datorer med hjälp av CLI Underhåll schemalagt för att hitta

Planerat underhållsinformation kan visas med hjälp av [get till azure vm-instansvyn](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Underhållsinformation returneras bara om det är planerat underhåll. Om det finns inget Underhåll schemalagda som påverkar den virtuella datorn, kommer kommandot inte att returnerar underhållsinformation om. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

Följande värden returneras under MaintenanceRedeployStatus: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta Underhåll på den virtuella datorn just nu ||
| PreMaintenanceWindowStartTime         | I början av självbetjäning underhållsfönstret när du kan initiera Underhåll på den virtuella datorn ||
| PreMaintenanceWindowEndTime           | Slutet av självbetjäning underhållsfönstret när du kan initiera Underhåll på den virtuella datorn ||
| MaintenanceWindowStartTime            | I början av den schemalagda underhållsperiod som initierar Azure Underhåll på den virtuella datorn ||
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhållsperiod som initierar Azure Underhåll på den virtuella datorn ||
| LastOperationResultCode               | Resultatet av det senaste försöket att starta Underhåll på den virtuella datorn ||




## <a name="start-maintenance-on-your-vm-using-cli"></a>Starta Underhåll på den virtuella datorn med hjälp av CLI

Följande anrop initierar Underhåll på en virtuell dator om `IsCustomerInitiatedMaintenanceAllowed` har angetts till true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Klassiska distributioner

Om du fortfarande har äldre virtuella datorer som har kan distribuerats med den klassiska distributionsmodellen du använda CLI 1.0 frågan för virtuella datorer och initiera underhåll.

Kontrollera att du är i rätt läge för att arbeta med klassiska Virtuella genom att skriva:

```
azure config mode asm
```

Att hämta status för underhåll av en virtuell dator med namnet *myVM*, typ:

```
azure vm show myVM 
``` 

Starta Underhåll på den klassiska virtuella datorn med namnet *myVM* i den *myService* service och *myDeployment* distribution, typ:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR


**F: Varför behöver du starta om min virtuella datorer nu?**

**S:** Även merparten av uppdateringar och uppgraderingar till Windows Azure-plattformen inte påverkar tillgängligheten för virtuell dator, så finns det fall där vi inte kan undvika startas om virtuella datorer som finns i Azure. Vi har ackumulerade flera ändringar som kräver att starta om våra servrar vilket innebär att virtuella datorer startas om.

**F: om jag följer rekommendationer för hög tillgänglighet med hjälp av en Tillgänglighetsuppsättning kan jag säker?**

**S:** virtuella datorer som distribueras i en tillgänglighetsgrupp eller skalningsuppsättningar i virtuella datorer har begreppet av uppdateringen domäner (UD). När du utför underhåll, Azure godkänner UD begränsningen och kommer inte att starta om virtuella datorer från olika UD (inom samma tillgänglighetsuppsättning).  Azure väntar även minst 30 minuter innan du flyttar till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [regioner och tillgänglighet för virtuella datorer i Azure](regions-and-availability.MD).

**F: hur jag Håll dig informerad om planerat underhåll?**

**S:** ett planerat underhåll wave startas genom att ange ett schema till en eller flera Azure-regioner. Strax efter ett e-postmeddelande skickas till prenumerationen ägare (en e-post per prenumeration). Ytterligare kanaler och mottagare för denna avisering kan konfigureras med hjälp av aktiviteten loggen aviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan schemalagts kommer inte att ta emot meddelandet men i stället måste du kontrollera underhållstillstånd av den virtuella datorn.

**F: inte visas någon uppgift om planerat underhåll i portalen, Powershell eller CLI, fel?**

**S:** Information som rör planerat underhåll som är tillgänglig under ett planerat underhåll wave endast för de virtuella datorerna som kommer att påverkas av den. Med andra ord, om du ser inte data, kan det vara att Underhåll wave har redan slutförts (eller inte startats) eller att den virtuella datorn finns redan i en uppdaterad server.

**F: finns det ett sätt att veta exakt när påverkas min virtuella dator?**

**S:** när schemat kan vi definierar ett tidsfönster på flera dagar. Dock är exakt Sekvenseringen av servrar (och virtuella datorer) i det här fönstret okänd. Kunder som vill veta exakt tid för sina virtuella datorer kan använda [schemalagda händelser](scheduled-events.md) och fråga från den virtuella datorn och ta emot ett meddelande om 15 minut innan en VM-omstart.

**F: hur lång tid tar det att datorn startas om min virtuella dator?**

**S:** beroende på storleken på den virtuella datorn omstart kan ta flera minuter under självbetjäning underhållsperiod. Under Azure initierade omstarter i fönstret schemalagt underhåll omstarten normalt tar cirka 25 minuter. Observera att om du använder molntjänster (Web/Worker-rollen), Skalningsuppsättningarna för virtuella datorer eller tillgänglighetsuppsättningar, får du 30 minuter mellan varje grupp av virtuella datorer (UD) under den schemalagda underhållsperioden.

**F: Vad är upplevelsen för molntjänster (Web/Worker-rollen), Service Fabric och Skalningsuppsättningar i virtuella datorer?**

**S:** när dessa plattformar som påverkas av planerat underhåll, kunder som använder dessa plattformar anses vara säkert anges som endast virtuella datorer i en enda uppgradera domän (UD) påverkas vid en given tidpunkt. Självbetjäning Underhåll är inte tillgängligt för molntjänster (Web/Worker-rollen), Service Fabric och virtuella datorer.

**F: Jag har fått ett e-postmeddelande om hur du inaktiverar maskinvara, är det samma som planerat underhåll?**

**S:** inaktivering av maskinvaran är en planerad underhållshändelse, har vi ännu inte publicerats så användningsfallet till den nya upplevelsen.  

**F: visas inte någon underhållsinformation på min virtuella datorer. Vad som gick fel?**

**S:** det finns flera orsaker till varför det inte uppstår några underhållsinformation på dina virtuella datorer:
1.  Du använder en prenumeration som markerats som Microsoft internt.
2.  Din virtuella dator är inte schemalagda för underhåll. Det kan vara att Underhåll wave har slutförts, avbröts eller ändras så att dina virtuella datorer som inte längre påverkas av den.
3.  Du har inte den **Underhåll** kolumn läggs till VM-listvy. När vi har lagt till den här kolumnen till standardvy kan kunder som konfigurerats för att se kolumner som inte är standard måste manuellt lägga till den **Underhåll** kolumn till deras VM-listvy.

**F: den virtuella datorn är Underhåll schemalagt för en andra gång. Varför?**

**S:** det finns flera användningsområden där du kan se din virtuella dator Underhåll schemalagt för när du redan har slutfört din Underhåll Omdistributionen:
1.  Vi har avbrutits wave för underhåll och startas om den med en annan nyttolast. Det kan bero på att vi har upptäckt fel nyttolasten och vi behöver distribuera en ytterligare nyttolast.
2.  Den virtuella datorn har *service lagats* till en annan nod på grund av ett maskinvarufel
3.  Du har valt för att stoppa (frigöra) och starta om den virtuella datorn
4.  Du har **automatisk avstängning** aktiverat för den virtuella datorn


**F: Underhåll av min tillgänglighetsuppsättning tar lång tid och ”hoppas över” status för vissa av min tillgänglighet ange instanser syns nu. Varför?** 

**S:** om du har klickat på Uppdatera alla instanser i en tillgänglighetsuppsättning kort efter varandra Azure köar dessa begäranden och börjar uppdatera endast virtuella datorer i en uppdateringsdomän (UD) i taget. Eftersom det kan vara en paus mellan domäner för uppdateringen, visas uppdateringen tar längre tid. Om uppdateringen kön tar längre än 60 minuter, ibland visas den **hoppas över** tillstånd även om de har uppdaterats. För att undvika den här felaktig status, uppdatera din tillgänglighetsuppsättningar genom att klicka bara på instansen inom en tillgänglighet värdet vänta tills uppdateringen på den virtuella datorn ska slutföras innan du klickar på nästa VM i en annan uppdateringsdomän


## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan registrera dig för underhållshändelser från inom den virtuella datorn med hjälp av [schemalagda händelser](scheduled-events.md).
