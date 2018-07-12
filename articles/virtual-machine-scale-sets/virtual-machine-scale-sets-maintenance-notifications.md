---
title: Hantering av meddelanden om Underhåll för VM-Skalningsuppsättningar i Azure | Microsoft Docs
description: Visa meddelanden om Underhåll för VM-Skalningsuppsättningar i Azure och börja självbetjäningsunderhållet.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38314408"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Hantera meddelanden planerat underhåll för Virtual Machine Scale Sets

Azure utför med jämna mellanrum uppdateringar för att förbättra värdinfrastrukturens tillförlitlighet, prestanda och säkerhet för virtuella datorer. Uppdateringarna är ändringar som uppdatering värdmiljön eller uppgradera och inaktivering av maskinvara. En majoritet av dessa uppdateringar genomförs utan någon inverkan på de virtuella datorerna. Men finns det fall där uppdateringar påverka:

- Om underhållet inte kräver en omstart, använder Azure migrering på plats för att pausa den virtuella datorn när värden har uppdaterats. Dessa icke rebootful underhållsåtgärder är tillämpad feldomän av feldomän och förloppet stoppas om någon varning hälsotillstånd signaler tas emot.

- Om en omstart krävs för underhåll, får du ett meddelande om när det planerade underhållet. I dessa fall kan ges ett tidsfönster som där du kan starta underhållet själv när det passar dig.


Planerat underhåll som kräver en omstart schemaläggs i vågor. Varje våg har olika omfång (regioner).

- En våg börjar med ett meddelande till kunderna. Som standard skickas meddelandet till Prenumerationens ägare och Medägare. Du kan lägga till fler mottagare och meddelandeinställningarna som e-post, SMS och Webhooks, till meddelanden med hjälp av Azure [Aktivitetsloggaviseringar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- Vid tidpunkten för anmälan, en *självbetjäning fönstret* är tillgänglig. Under det här fönstret kan du hitta vilka av dina virtuella datorer som ingår i den här wave och proaktivt starta Underhåll efter behov schemaläggning.
- När fönstret självbetjäning en *schemalagda underhållsperiod* börjar. Någon gång under det här fönstret Azure schemalägger och gäller det nödvändiga underhållet för den virtuella datorn. 

Målet i att ha två windows är att ge dig tid att starta underhåll och starta om den virtuella datorn samtidigt som du vet när Azure startar automatiskt underhåll.


Du kan använda Azure portal, PowerShell, REST API och CLI för att fråga efter underhållsperioder för VM-skalningsuppsättning de virtuella datorerna och startar självbetjäningsunderhållet.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Bör du starta Underhåll under fönstret Self service?  

Följande riktlinjer hjälper dig att bestämma om du vill använda den här funktionen och starta Underhåll just din egen takt.

> [!NOTE] 
> Självbetjäningsunderhållet kanske inte är tillgänglig för alla dina virtuella datorer. Om du vill fastställa om förebyggande omdistribution är tillgänglig för den virtuella datorn genom att leta efter den **starta nu** i status för underhåll. Självbetjäningsunderhållet är för närvarande inte tillgänglig för molntjänster (Web/Worker-roll) och Service Fabric.


Självbetjäningsunderhållet rekommenderas inte för distributioner med **tillgänglighetsuppsättningar** eftersom dessa är högtillgänglig inställningarna, där endast en uppdateringsdomän påverkas vid en given tidpunkt. 

- Låt Azure utlöser underhåll. För underhåll som kräver omstart, Tänk på att underhållet görs uppdateringsdomän av uppdateringsdomänen och att uppdateringsdomäner inte nödvändigtvis får underhållet sekventiellt, och att det finns en 30-minuters paus mellan uppdateringsdomäner.
- Om en temporär förlust av några av din kapacitet (1/uppdatera antalet) är ett problem, kan det enkelt kompenseras genom att allokera ytterligare instanser under underhållsperioden.
- Underhåll som inte kräver omstart, uppdateringar tillämpas på domännivå fel. 
    
**Inte** använder självbetjäningsunderhållet i följande scenarier: 

- Om du stänger av dina virtuella datorer, antingen manuellt, med DevTest Labs med hjälp av automatisk avstängning eller enligt ett schema, det kan återställa status för underhåll och därför orsakar ytterligare driftstopp.
- På tillfällig virtuella datorer tas att du känner till bort innan underhållet är slut. 
- För arbetsbelastningar med en stor tillstånd som lagras på hårddisken (tillfälliga) som är det önskade bevaras vid uppdatering. 
- I de fall där du ändrar storlek på den virtuella datorn ofta, som det kunde återställa status för underhåll. 
- Om du har valt schemalagda händelser som möjliggör proaktiv redundans eller avslutning av din arbetsbelastning 15 minuter innan Underhåll avstängning.

**Använd** självbetjäningsunderhållet, om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av de avdelningar uppgifter som nämns ovan gäller. 

Det är bäst att använda självbetjäningsunderhållet i följande fall:

- Du måste kommunicera en exakt underhållsperiod till hantering eller end-kund. 
- Du måste du ha slutfört underhållet med ett visst datum. 
- Du behöver styra sekvensen av underhåll, till exempel flernivåapp att garantera säker omstart.
- Du behöver mer än 30 minuter från tiden för återställning av virtuell dator mellan två uppdateringsdomäner (ud). För att styra tiden mellan uppdateringsdomäner, måste du utlöser Underhåll på dina virtuella datorer en uppdateringsdomän (UD) i taget.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>Visa VM-skalningsuppsättningar som påverkas av underhåll i portalen

När en planerade underhållet har schemalagts kan du visa listan över skalningsuppsättningar för virtuella datorer som påverkas av kommande underhållet med Azure portal. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** i det vänstra navigeringsfönstret och välj **VM-skalningsuppsättningar**.
3. På den **VM-skalningsuppsättningar** väljer du den **Redigera kolumner** alternativet överst för att öppna listan med tillgängliga kolumner.
4. I den **tillgängliga kolumner**väljer den **självbetjäningsunderhållet** objektet och Använd pilknapparna för att flytta den till den **markerade kolumner** lista. Du kan växla i listrutan i det **tillgängliga kolumner** avsnittet från **alla** till **egenskaper** att göra den **självbetjäningsunderhållet** objektet lättare att hitta. När du har den **självbetjäningsunderhållet** objektet i den **markerade kolumner** väljer den **tillämpa** längst ned på sidan. 

När du har följt stegen ovan, den **självbetjäningsunderhållet** kolumnen ska visas i listan över VM-skalningsuppsättningar. Varje VM-skalningsuppsättning kan ha något av följande värden för kolumnen självbetjäningsunderhållet:

| Värde | Beskrivning |
|-------|-------------|
| Ja | Minst en virtuell dator i virtual machine scale Sets är i ett fönster med självbetjäning. Du kan starta Underhåll när som helst under det här fönstret för självbetjäning. | 
| Nej | Det finns inga virtuella datorer som är i ett fönster med självbetjäning i berörda VM-skalningsuppsättningen. | 
| - | Dina skalningsuppsättningar för virtuella datorer är inte en del av en planerade underhållet senare.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meddelanden och aviseringar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till gruppen prenumeration ägare och Medägare. Du kan lägga till ytterligare mottagare och kanaler denna kommunikation genom att skapa aviseringar för Azure-aktivitet. Mer information finns i [övervaka prenumerationsaktivitet med Azure-aktivitetsloggen] (.. / monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I menyn till vänster väljer **övervakaren**. 
3. I den **övervaka – aviseringar (klassisk)** fönstret klickar du på **+ Lägg till aktivitetsloggavisering**.
4. Fyll i den **Lägg till aktivitetsloggavisering** sidan och se till att ange följande **kriterier**:
   - **Händelsekategori**: Tjänstehälsa
   - **Tjänster**: Virtual Machine Scale Sets och virtuella datorer
   - **Typ**: planerat underhåll 
    
Mer information om hur du konfigurerar aviseringar för aktivitetsloggar finns [skapa aviseringar för aktivitetsloggen](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Starta Underhåll på VM-skalningsuppsättning från portalen

Medan de tittar på en översikt över VM-skalningsuppsättningar, kommer du att kunna se mer Underhåll relaterade detaljer. Om minst en virtuell dator i virtual machine scale Sets ingår i det planerade underhållet senare, läggs en ny avisering menyfliksområdet längst upp på sidan. Du kan klicka på menyfliken meddelande att gå till den **Underhåll** och se vilka virtuella datorinstansen påverkas av planerat underhåll. 

Därifrån kan du kommer att kunna starta Underhåll genom att markera kryssrutan som motsvarar den berörda virtuella datorn och sedan klicka på **starta Underhåll** alternativet.

När du startar underhåll, kommer de berörda virtuella datorerna i virtual machine scale Sets genomgår underhåll och vara temporärt otillgänglig. Om du missade fönstret självbetjäning kan att du fortfarande kunna se fönstret när virtual machine scale Sets upprätthålls av Azure.
 
## <a name="check-maintenance-status-using-powershell"></a>Kontrollera status för underhåll med hjälp av PowerShell

Du kan använda Azure Powershell för att se när virtuella datorer i VM-skalningsuppsättningar är schemalagda för underhåll. Planerat underhållsinformation är tillgänglig på den [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) cmdlet när du använder den `-InstanceView` parametern.
 
Underhållsinformation returneras bara om det är planerat underhåll. Om inget Underhåll har schemalagts som påverkar den Virtuella datorinstansen cmdlet inte returnerar underhållsinformation om. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Under MaintenanceRedeployStatus returneras följande egenskaper: 
| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta Underhåll på den virtuella datorn just nu ||
| PreMaintenanceWindowStartTime         | I början av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn ||
| PreMaintenanceWindowEndTime           | Slutet av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn ||
| MaintenanceWindowStartTime            | I början av schemalagt underhåll där Azure initierar Underhåll på den virtuella datorn ||
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhållsperiod där Azure initierar Underhåll på den virtuella datorn ||
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Starta Underhåll på VM-instansen med hjälp av PowerShell

Du kan starta Underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** anges till true genom att använda [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) cmdlet med `-PerformMaintenance` parametern.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Kontrollera status för underhåll med CLI

Planerat underhållsinformation kan ses med [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Underhållsinformation returneras bara om det är planerat underhåll. Om det finns inget schemalagt underhåll som påverkar den Virtuella datorinstansen, kommandot returnerar inte underhållsinformation om. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Följande egenskaper returneras under MaintenanceRedeployStatus för varje virtuell datorinstans: 
| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta Underhåll på den virtuella datorn just nu ||
| PreMaintenanceWindowStartTime         | I början av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn ||
| PreMaintenanceWindowEndTime           | Slutet av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn ||
| MaintenanceWindowStartTime            | I början av schemalagt underhåll där Azure initierar Underhåll på den virtuella datorn ||
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhållsperiod där Azure initierar Underhåll på den virtuella datorn ||
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Starta Underhåll på VM-instansen med hjälp av CLI

Följande anrop initierar Underhåll på en VM-instans om `IsCustomerInitiatedMaintenanceAllowed` har angetts till true.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR


**F: Varför behöver du starta om Mina virtuella datorer nu?**

**S:** merparten av uppdateringar och uppgraderingar till Azure-plattformen inte påverkar virtuella datorns tillgänglighet, men det finns fall där vi kan undvika att starta om virtuella datorer som finns i Azure. Vi har ackumulerat flera ändringar som kräver att vi startar om våra servrar som leder till virtuella datorerna startas om.

**F: Jag följer rekommendationer för hög tillgänglighet med hjälp av en Tillgänglighetsuppsättning jag om säker?**

**S:** virtuella datorer som distribueras i en tillgänglighetsuppsättning eller skalningsuppsättningar för virtuella datorer har begreppet av Uppdateringsdomän (UD). När underhåll utförs följer UD-begränsningen i Azure och kommer inte att starta om virtuella datorer från en annan Uppdateringsdomän (inom samma tillgänglighetsuppsättning).  Azure också väntar minst 30 minuter innan du flyttar till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [regioner och tillgänglighet för virtuella datorer i Azure](../virtual-machines/windows/regions-and-availability.md).

**F: hur jag bli meddelad om planerat underhåll?**

**S:** en planerade underhållet senare startar genom att ange ett schema till en eller flera Azure-regioner. Strax efter ett e-postmeddelande skickas till prenumerationsägarna (en e-post per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med hjälp av Aktivitetsloggaviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll har redan schemalagts du kommer inte att ta emot meddelandet, men i stället måste du kontrollera underhållstillstånd för av den virtuella datorn.

**F: Jag får ingen indikation på planerat underhåll i portalen, Powershell eller CLI. Vad är fel?**

**S:** Information som rör planerat underhåll är tillgänglig under en planerade underhållet senare endast för de virtuella datorerna som kommer att påverkas. Med andra ord, om du ser inte data, kan det vara att underhållet har slutförts (eller inte startats) eller att den virtuella datorn finns redan i en uppdaterad server.

**F: finns det ett sätt att veta exakt när min virtuella dator kommer att påverkas?**

**S:** när du ställer in schemat definierar vi ett tidsfönster på flera dagar. Men är den exakta Sekvenseringen av servrar (och virtuella datorer) i det här fönstret okänd. Kunder som vill veta exakt tid för sina virtuella datorer kan använda [schemalagda händelser](../virtual-machines/windows/scheduled-events.md) och fråga från den virtuella datorn och ta emot ett meddelande för 15 minuter innan en omstart av virtuell dator.

**F: hur lång tid tar det att datorn startas om min virtuella dator?**

**S:** beroende på storleken på din virtuella dator, omstart kan ta upp till flera minuter under självbetjäning underhållsfönstret. Under Azure initierade omstarter i det schemalagda underhållsfönstret tar vanligtvis omstarten ungefär 25 minuter. Observera att om du använder Cloud Services (Web/Worker-roll), Virtual Machine Scale Sets eller tillgänglighetsuppsättningar kan du får 30 minuter mellan varje grupp av virtuella datorer (UD) under underhållsperioden. 

**F: Jag ser inte någon underhållsinformation på Mina virtuella datorer. Vad som gick fel?**

**S:** det finns flera orsaker till varför du inte ser någon underhållsinformation på dina virtuella datorer:
   - Du använder en prenumeration som markerats som Microsoft internt.
   - Dina virtuella datorer är inte schemalagda för underhåll. Det kan bero på att underhållet har slutförts har avbrutits eller ändras så att dina virtuella datorer som inte längre påverkas av den.
   - Du behöver inte den **Underhåll** kolumnen som lagts till i VM-listvyn. Vi har lagt till den här kolumnen till standardvyn, kunder som konfigurerats för att se icke-standard-kolumner måste manuellt lägga till den **Underhåll** kolumnen till deras VM-listvyn.

**F: min virtuella dator är schemalagd för underhåll en andra gång. Varför?**

**S:** det finns flera Användningsscenarier där du kan se den virtuella datorn är schemalagd för underhåll efter att du redan har slutfört underhåll och omdistribution:
   - Vi har avbrutit underhållet och startas om den med en annan nyttolast. Det kan bero på att vi har upptäckt felaktig nyttolasten och vi behöver att distribuera en ytterligare nyttolast.
   - Din virtuella dator *tjänst har åtgärdats* till en annan nod på grund av ett maskinvarufel.
   - Du har valt för att stoppa (frigöra) och starta om den virtuella datorn.
   - Du har **automatisk avstängning** aktiverad för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan registrera dig för underhållshändelser från den virtuella datorn med hjälp av [Scheduled Events](../virtual-machines/windows/scheduled-events.md).
