---
title: Underhållsmeddelanden för skalningsuppsättningar för virtuella datorer i Azure
description: Visa underhållsmeddelanden och starta självbetjäningsunderhåll för skalningsuppsättningar för virtuella datorer i Azure.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 53ebb7c4710c5455ef90701dc7e94f1b846a874a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062708"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meddelanden för planerat underhåll för VM-skalningsuppsättningar


Azure utför regelbundet uppdateringar för att förbättra tillförlitligheten, prestandan och säkerheten för värdinfrastrukturen för virtuella datorer (VMs). Uppdateringar kan omfatta korrigering av värdmiljön eller uppgradering och avveckling av maskinvara. De flesta uppdateringar påverkar inte de värdbaserade virtuella datorerna. Uppdateringar påverkar dock virtuella datorer i dessa scenarier:

- Om underhållet inte kräver en omstart använder Azure migrering på plats för att pausa den virtuella datorn medan värden uppdateras. Underhållsåtgärder som inte kräver en omstart tillämpas feldomän av feldomänen. Förloppet stoppas om några varningshälsosignaler tas emot.

- Om underhåll kräver en omstart får du ett meddelande om när underhållet är planerat. I dessa fall får du ett tidsfönster som vanligtvis är 35 dagar där du kan starta underhållet själv, när det fungerar för dig.


Planerat underhåll som kräver en omstart schemaläggs i vågor. Varje våg har olika omfattning (regioner):

- En våg börjar med en avisering till kunder. Som standard skickas meddelandet till prenumerationsägaren och delägarna. Du kan lägga till mottagare och meddelandealternativ som e-post, SMS och webhooks i meddelandena med hjälp av Azure [Activity Log-aviseringar](../azure-monitor/platform/platform-logs-overview.md).  
- Med anmälan görs ett *självbetjäningsfönster* tillgängligt. Under det här fönstret som vanligtvis är 35 dagar kan du hitta vilka av dina virtuella datorer som ingår i vågen. Du kan proaktivt starta underhåll enligt dina egna schemaläggningsbehov.
- Efter självbetjäningsfönstret börjar ett *schemalagt underhållsfönster.* Vid något tillfälle under det här fönstret schemalägger Och tillämpar Azure det underhåll som krävs på din virtuella dator. 

Målet med att ha två fönster är att ge dig tillräckligt med tid för att starta underhåll och starta om din virtuella dator samtidigt som du vet när Azure automatiskt startar underhåll.

Du kan använda Azure-portalen, PowerShell, REST API och Azure CLI för att fråga efter underhållsfönster för virtuella datorskalauppsättning virtuella datorer och för att starta självbetjäningsunderhåll.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Ska du börja underhålla under självbetjäningsfönstret?  

Följande riktlinjer kan hjälpa dig att bestämma om du vill starta underhåll vid en tidpunkt som du väljer.

> [!NOTE] 
> Självbetjäningsunderhåll kanske inte är tillgängligt för alla dina virtuella datorer. Om du vill ta reda på om proaktiv omdistribution är tillgänglig för den virtuella datorn letar du efter **Start nu** i underhållsstatusen. För närvarande är självbetjäningsunderhåll inte tillgängligt för Azure Cloud Services (Webb/Arbetarroll) och Azure Service Fabric.


Underhåll med självbetjäning rekommenderas inte för distributioner som använder *tillgänglighetsuppsättningar*. Tillgänglighetsuppsättningar är högtillgänglighetsinställningar där endast en uppdateringsdomän påverkas när som helst. För tillgänglighetsuppsättningar:

- Låt Azure utlösa underhållet. För underhåll som kräver en omstart utförs underhåll uppdateringsdomänen efter uppdateringsdomän. Uppdatera domäner får inte nödvändigtvis underhållet sekventiellt. Det finns en 30-minuters paus mellan uppdateringsdomäner.
- Om en tillfällig förlust av en del av din kapacitet (1/update domänantal) är ett problem, kan du enkelt kompensera för förlusten genom att fördela ytterligare instanser under underhållsperioden.
- För underhåll som inte kräver en omstart tillämpas uppdateringar på feldomännivå. 
    
**Använd inte** självbetjäningsunderhåll i följande scenarier: 

- Om du stänger av dina virtuella datorer ofta, antingen manuellt, med hjälp av DevTest Labs, genom automatisk avstängning eller genom att följa ett schema. Självbetjäningsunderhåll i dessa scenarier kan återställa underhållsstatusen och orsaka ytterligare driftstopp.
- På kortlivade virtuella datorer som du vet kommer att tas bort före slutet av underhållsvågen. 
- För arbetsbelastningar med ett stort tillstånd som lagras i den lokala (tillfälliga) disken som du vill underhålla efter uppdatering. 
- Om du ändrar storlek på den virtuella datorn ofta. Det här scenariot kan återställa underhållsstatusen. 
- Om du har antagit schemalagda händelser som möjliggör proaktiv redundans eller smidig avstängning av din arbetsbelastning 15 minuter innan underhållsavstängningen börjar.

**Använd** underhåll med självbetjäning om du planerar att köra den virtuella datorn utan avbrott under den schemalagda underhållsfasen och ingen av de föregående motindikationerna gäller. 

Det är bäst att använda underhåll med självbetjäning i följande fall:

- Du måste kommunicera ett exakt underhållsfönster till ledningen eller din kund. 
- Du måste slutföra underhållet vid ett visst datum. 
- Du måste styra sekvensen av underhåll, till exempel i ett program med flera nivåer, för att garantera säker återställning.
- Du behöver mer än 30 minuters vm-återställningstid mellan två uppdateringsdomäner. Om du vill styra tiden mellan uppdateringsdomäner måste du utlösa underhåll på dina virtuella datorer en uppdateringsdomän i taget.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Visa skalningsuppsättningar för virtuella datorer som påverkas av underhåll i portalen

När en planerad underhållsvåg schemaläggs kan du visa listan över skalningsuppsättningar för virtuella datorer som påverkas av den kommande underhållsvågen med hjälp av Azure-portalen. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster**på den vänstra menyn och välj sedan **Skalningsuppsättningar för virtuella datorer**.
3. Under **Skalningsuppsättningar för virtuella**datorer väljer du Redigera **kolumner** för att öppna listan över tillgängliga kolumner.
4. I avsnittet **Tillgängliga kolumner** väljer du Underhåll **med självbetjäning**och flyttar det sedan till listan **Markerade kolumner.** Välj **Använd**.  

    Om du vill göra **självbetjäningsunderhållsartikeln** lättare att hitta kan du ändra listrutan i avsnittet **Tillgängliga kolumner** från **Alla** till **Egenskaper**.

Kolumnen **Underhåll med självbetjäning** visas nu i listan över skaluppsättningar för virtuella datorer. Varje skaluppsättning för virtuella datorer kan ha ett av följande värden för kolumnen underhåll för självbetjäning:

| Värde | Beskrivning |
|-------|-------------|
| Ja | Minst en virtuell dator i den virtuella datorns skalningsuppsättning finns i ett självbetjäningsfönster. Du kan börja underhåll när som helst under detta självbetjäningsfönster. | 
| Inga | Inga virtuella datorer finns i ett självbetjäningsfönster i den berörda skalningsuppsättningen för virtuella datorer. | 
| - | Skalningsuppsättningarna för virtuella datorer ingår inte i en planerad underhållsvåg.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meddelanden och varningar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till prenumerationsägaren och ägargruppen. Du kan lägga till mottagare och kanaler i den här kommunikationen genom att skapa aktivitetsloggaviseringar. Mer information finns i [Övervaka prenumerationsaktivitet med Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Övervaka**på den vänstra menyn . 
3. I fönstret **Övervakare - aviseringar (klassiskt)** väljer du **+Lägg till aktivitetsloggavisering**.
4. På sidan **Lägg till aktivitetsloggavisering** väljer eller anger du den begärda informationen. I **Villkor**kontrollerar du att du anger följande värden:
   - **Händelsekategori**: Välj **Tjänsthälsa**.
   - **Tjänster:** Välj **skaluppsättningar för virtuella datorer och virtuella datorer**.
   - **Typ:** Välj **Planerat underhåll**. 
    
Mer information om hur du konfigurerar aktivitetsloggaviseringar finns i [Skapa aviseringar för aktivitetsloggar](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Starta underhållet på din skala för virtuella datorer från portalen

Du kan se mer underhållsrelaterad information i översikten över skalningsuppsättningar för virtuella datorer. Om minst en virtuell dator i skaluppsättningen för den virtuella datorn ingår i den planerade underhållsvågen läggs ett nytt meddelandemenyor till högst upp på sidan. Markera meddelandefliksområdet om du vill gå till sidan **Underhåll.** 

På sidan **Underhåll** kan du se vilken VM-instans som påverkas av det planerade underhållet. Om du vill starta underhåll markerar du kryssrutan som motsvarar den berörda virtuella datorn. Välj sedan **Starta underhåll**.

När du har påbörjat underhållet genomgår de berörda virtuella datorerna i den virtuella datorns skaluppsättning underhåll och är inte tillgängliga för tillfället. Om du missade självbetjäningsfönstret kan du fortfarande se tidsfönstret när din virtuella datorskalauppsättning kommer att underhållas av Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Kontrollera underhållsstatus med hjälp av PowerShell

Du kan använda Azure PowerShell för att se när virtuella datorer i dina skaluppsättningar för virtuella datorer schemaläggs för underhåll. Planerad underhållsinformation är tillgänglig med hjälp av [Cmdlet Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) när du använder parametern. `-InstanceView`
 
Underhållsinformation returneras endast om underhåll planeras. Om inget underhåll har schemalagts som påverkar den virtuella datorn-instansen returnerar cmdleten ingen underhållsinformation. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Följande egenskaper returneras under **MaintenanceRedeployStatus:** 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceTilläms | Anger om du kan starta underhållet på den virtuella datorn just nu. |
| PreMaintenanceWindowStartTime         | Början av underhålls-självbetjäningsfönstret när du kan initiera underhåll på den virtuella datorn. |
| PreMaintenanceWindowEndTime           | Slutet av underhållet självbetjäning fönster när du kan initiera underhåll på din virtuella dator. |
| UnderhållWindowStartTime            | Början av det underhåll som planeras där Azure initierar underhåll på din virtuella dator. |
| UnderhållWindowEndTime              | Slutet av det planerade fönstret för underhåll där Azure initierar underhåll på den virtuella datorn. |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Starta underhållet på vm-instansen med PowerShell

Du kan starta underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** är inställt på **true**. Använd [cmdlet set-azvmss](/powershell/module/az.compute/set-azvmss) med `-PerformMaintenance` parametern.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Kontrollera underhållsstatus med hjälp av CLI

Du kan visa planerad underhållsinformation med hjälp av [az vmss list-instanser](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Underhållsinformation returneras endast om underhåll planeras. Om inget underhåll som påverkar den virtuella datorn-instansen är schemalagt returnerar kommandot inte någon underhållsinformation. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Följande egenskaper returneras under **MaintenanceRedeployStatus** för varje VM-instans: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceTilläms | Anger om du kan starta underhållet på den virtuella datorn just nu. |
| PreMaintenanceWindowStartTime         | Början av underhålls-självbetjäningsfönstret när du kan initiera underhåll på den virtuella datorn. |
| PreMaintenanceWindowEndTime           | Slutet av underhållet självbetjäning fönster när du kan initiera underhåll på din virtuella dator. |
| UnderhållWindowStartTime            | Början av det underhåll som planeras där Azure initierar underhåll på din virtuella dator. |
| UnderhållWindowEndTime              | Slutet av det planerade fönstret för underhåll där Azure initierar underhåll på den virtuella datorn. |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Starta underhållet på din VM-instans med hjälp av CLI

Följande anrop initierar underhåll på `IsCustomerInitiatedMaintenanceAllowed` en VM-instans om den är inställd på **true:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: Varför behöver du starta om mina virtuella datorer nu?**

**A.** Även om de flesta uppdateringar och uppgraderingar till Azure-plattformen inte påverkar vm-tillgänglighet, kan vi i vissa fall inte undvika att starta om virtuella datorer som finns i Azure. Vi har samlat flera ändringar som kräver att vi startar om våra servrar som kommer att resultera i vm omstart.

**F: Om jag följer dina rekommendationer för hög tillgänglighet med hjälp av en tillgänglighetsuppsättning, är jag säker?**

**A.** Virtuella datorer som distribueras i en tillgänglighetsuppsättning eller i skalningsuppsättningar för virtuella datorer använder uppdateringsdomäner. När du utför underhåll respekterar Azure begränsningen för uppdateringsdomänen och startar inte om virtuella datorer från en annan uppdateringsdomän (inom samma tillgänglighetsuppsättning). Azure väntar också i minst 30 minuter innan du flyttar till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [Regioner och tillgänglighet för virtuella datorer i Azure](../virtual-machines/windows/availability.md).

**F: Hur kan jag få ett meddelande om planerat underhåll?**

**A.** En planerad underhållsvåg börjar med att ange ett schema till en eller flera Azure-regioner. Strax därefter skickas ett e-postmeddelande till prenumerationsägarna (ett e-postmeddelande per prenumeration). Du kan lägga till kanaler och mottagare för det här meddelandet med hjälp av aktivitetsloggaviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan är schemalagt får du inte meddelandet. Kontrollera i stället underhållstillståndet för den virtuella datorn.

**F: Jag ser ingen indikation på planerat underhåll i portalen, PowerShell eller CLI. Vad är fel?**

**A.** Information om planerat underhåll är tillgänglig under en planerad underhållsvåg endast för de virtuella datorer som påverkas av det planerade underhållet. Om du inte ser data kan underhållsvågen redan vara klar (eller inte startad) eller så kanske den virtuella datorn redan finns på en uppdaterad server.

**F: Finns det något sätt att veta exakt när min virtuella dator kommer att påverkas?**

**A.** När vi ställer in schemat definierar vi ett tidsfönster på flera dagar. Den exakta sekvenseringen av servrar (och virtuella datorer) under den här perioden är okänd. Om du vill veta exakt när dina virtuella datorer kommer att uppdateras kan du använda [schemalagda händelser](../virtual-machines/windows/scheduled-events.md). När du använder schemalagda händelser kan du fråga inifrån den virtuella datorn och få ett 15-minutersmeddelande innan en virtuell dator startas om.

**F: Hur lång tid tar det för dig att starta om min virtuella dator?**

**A.**  Beroende på storleken på den virtuella datorn kan omstarten ta upp till flera minuter under underhållsfönstret för självbetjäning. Under de Azure-initierade omstarterna i det schemalagda underhållsfönstret tar omstarten vanligtvis cirka 25 minuter. Om du använder Molntjänster (webb-/arbetarroll), skalningsuppsättningar för virtuella datorer eller tillgänglighetsuppsättningar får du 30 minuter mellan varje grupp av virtuella datorer (uppdateringsdomän) under det schemalagda underhållsfönstret. 

**F: Jag ser ingen underhållsinformation på mina virtuella datorer. Vad gick fel?**

**A.** Det finns flera orsaker till att du kanske inte ser någon underhållsinformation på dina virtuella datorer:
   - Du använder en prenumeration som är markerad som *Microsoft Internal*.
   - Dina virtuella datorer är inte schemalagda för underhåll. Det kan bero på att underhållsvågen har avslutats, avbrutits eller ändrats så att dina virtuella datorer inte längre påverkas av den.
   - Du har inte kolumnen **Underhåll** tillagt i listvyn för den virtuella datorn. Även om vi har lagt till den här kolumnen i standardvyn måste du lägga till kolumnen **Underhåll** manuellt i listvyn för den virtuella datorn om du konfigurerar vyn för att se kolumner som inte är standard.

**F: Min virtuella dator är schemalagd för underhåll för andra gången. Varför?**

**A.** I flera användningsfall schemaläggs den virtuella datorn för underhåll när du redan har slutfört underhållet och distribuerats om:
   - Vi har avbrutit underhållsvågen och startat om den med en annan nyttolast. Det kan vara så att vi har upptäckt en felaktig nyttolast och vi behöver bara distribuera en extra nyttolast.
   - Den virtuella datorn *har läkts* till en annan nod på grund av ett maskinvarufel.
   - Du har valt att stoppa (deallocate) och starta om den virtuella datorn.
   - Du har **aktiverat automatisk avstängning** för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du registrerar dig för underhållshändelser inifrån den virtuella datorn med hjälp av [schemalagda händelser](../virtual-machines/windows/scheduled-events.md).
