---
title: Meddelanden om Underhåll för virtuella datorer som anger i Azure | Microsoft Docs
description: Visa meddelanden om underhåll och starta självbetjäningsunderhållet för skalningsuppsättningar för virtuella datorer i Azure.
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
ms.openlocfilehash: 727ae9bbea4cabc5d27c32baff2123a7c03b531c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546869"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meddelanden för planerat underhåll för VM-skalningsuppsättningar

Azure utför regelbundet uppdateringar för att förbättra tillförlitligheten, prestanda och säkerheten för infrastrukturen för värd för virtuella datorer (VM). Uppdateringar kan innehålla korrigeringar värdmiljön eller uppgradera och inaktivering av maskinvara. De flesta uppdateringar påverkar inte de värdbaserade virtuella datorerna. Uppdateringar av påverkar dock virtuella datorer i dessa scenarier:

- Om underhållet kräver ingen omstart, använder Azure migrering på plats för att pausa den virtuella datorn när värden har uppdaterats. Underhållsåtgärder som inte kräver en omstart är tillämpad feldomän av feldomän. Förlopp stoppas om någon varning hälsotillstånd signaler tas emot.

- Om en omstart krävs för underhåll, får du ett meddelande som visar när Underhåll är planerat. I dessa fall kan ges ett tidsfönster som du kan starta underhållet själv när det passar dig bäst.


Planerat underhåll som kräver omstart schemaläggs i vågor. Varje våg har olika omfång (regioner):

- En våg börjar med ett meddelande till kunderna. Som standard skickas meddelandet till Prenumerationens ägare och Medägare. Du kan lägga till mottagarna och meddelandeinställningarna som e-post, SMS och webhooks meddelanden med hjälp av Azure [aktivitetsloggsaviseringar](../azure-monitor/platform/activity-logs-overview.md).  
- Med meddelande, en *självbetjäning fönstret* är tillgänglig. Under det här fönstret kan hitta du vilka av dina virtuella datorer som ingår i wave. Du kan starta Underhåll proaktivt efter behov schemaläggning.
- När fönstret självbetjäning en *schemalagda underhållsperiod* börjar. Någon gång under det här fönstret Azure schemalägger och gäller det nödvändiga underhållet för den virtuella datorn. 

Målet i att ha två windows är att ge dig tid att starta underhåll och starta om den virtuella datorn samtidigt som du vet när Azure startar automatiskt underhåll.


Du kan använda Azure portal, PowerShell, REST API och Azure CLI att fråga efter underhållsfönster för din VM-skalningsuppsättning virtuella datorer, samt att starta självbetjäningsunderhållet.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Bör du starta Underhåll under fönstret Self service?  

Följande riktlinjer kan hjälpa dig att bestämma om du vill starta Underhåll samtidigt som du väljer.

> [!NOTE] 
> Självbetjäningsunderhållet kanske inte är tillgänglig för alla dina virtuella datorer. För att avgöra om förebyggande omdistribution är tillgänglig för den virtuella datorn, leta efter **starta nu** i status för underhåll. Självbetjäningsunderhållet är för närvarande inte tillgängligt för Azure Cloud Services (Web/Worker-roll) och Azure Service Fabric.


Självbetjäningsunderhållet rekommenderas inte för distributioner som använder *tillgänglighetsuppsättningar*. Tillgänglighetsuppsättningar är högtillgänglig inställningar i som endast en uppdateringshantering domän påverkas när som helst. För tillgänglighetsuppsättningar:

- Låt Azure utlöser underhåll. Underhåll är klar uppdateringsdomän av uppdateringsdomänen för underhåll som kräver en omstart. Uppdateringsdomäner får inte nödvändigtvis underhållet sekventiellt. Det finns en 30-minuters paus mellan uppdateringsdomäner.
- Om en temporär förlust av några av din kapacitet (1/uppdatera antalet) är ett problem kan kompensera du enkelt för dataförlust genom att allokera ytterligare instanser under underhållsperioden.
- Uppdateringar tillämpas på domännivå fel för underhåll som inte kräver en omstart. 
    
**Inte** använder självbetjäningsunderhållet i följande scenarier: 

- Om du stänger dina virtuella datorer, antingen manuellt via DevTest Labs, med hjälp av automatisk avstängning eller genom att följa ett schema. Självbetjäningsunderhållet i dessa scenarier kan återställa status för underhåll och orsakar ytterligare driftstopp.
- På tillfällig virtuella datorer tas att du känner till bort innan underhållet är slut. 
- För arbetsbelastningar med en stor tillstånd som lagras på hårddisken (tillfälliga) som du vill ha kvar efter uppdateringen. 
- Om du ändrar storlek på den virtuella datorn ofta. Det här scenariot kan återställa status för underhåll. 
- Om du har valt schemalagda händelser som möjliggör proaktiv redundans eller avslutning av din arbetsbelastning 15 minuter innan du börjar Underhåll avstängning.

**Gör** använda självbetjäningsunderhållet om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av de föregående counterindications gäller. 

Det är bäst att använda självbetjäningsunderhållet i följande fall:

- Du måste kommunicera en exakt underhållsperiod management eller kunden. 
- Du måste du ha slutfört underhållet av ett visst datum. 
- Du behöver styra sekvensen av underhåll, till exempel i ett flerskiktat program att garantera säker omstart.
- Du behöver mer än 30 minuter från tiden för återställning av virtuell dator mellan två uppdateringsdomäner. För att styra tiden mellan uppdateringsdomäner, måste du utlöser Underhåll på dina virtuella datorer en uppdateringsdomän i taget.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Visa VM-skalningsuppsättningar som påverkas av underhåll i portalen

När en planerade underhållet har schemalagts kan du visa listan över skalningsuppsättningar för virtuella datorer som påverkas av den kommande underhållet med hjälp av Azure portal. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn väljer du **alla tjänster**, och välj sedan **VM-skalningsuppsättningar**.
3. Under **VM-skalningsuppsättningar**väljer **Redigera kolumner** att öppna listan över tillgängliga kolumner.
4. I den **tillgängliga kolumner** väljer **självbetjäningsunderhållet**, och flytta den till den **markerade kolumner** lista. Välj **Använd**.  

    Att göra den **självbetjäningsunderhållet** objektet lättare att hitta, du kan ändra alternativet i nedrullningsbara den **tillgängliga kolumner** avsnittet från **alla** till  **Egenskaper för**.

Den **självbetjäningsunderhållet** kolumnen visas nu i listan över VM-skalningsuppsättningar. Varje VM-skalningsuppsättning kan ha något av följande värden för kolumnen självbetjäningsunderhållet:

| Värde | Beskrivning |
|-------|-------------|
| Ja | Minst en virtuell dator i din skalningsuppsättning för virtuell dator är i ett fönster med självbetjäning. Du kan starta Underhåll när som helst under det här fönstret för självbetjäning. | 
| Nej | Inga virtuella datorer anges i ett fönster med självbetjäning i berörda VM-skalningsuppsättningen. | 
| - | Din VM scale sets är inte en del av en planerade underhållet senare.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meddelanden och aviseringar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till gruppen prenumeration ägare och Medägare. Du kan lägga till mottagarna och kanaler denna kommunikation genom att skapa aviseringar för aktivitetsloggen. Mer information finns i [övervaka prenumeration med Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra menyn väljer du **övervakaren**. 
3. I den **övervaka – aviseringar (klassisk)** väljer **+ Lägg till aktivitetsloggavisering**.
4. På den **Lägg till aktivitetsloggavisering** väljer eller anger den begärda informationen. I **kriterier**, måste du ange följande värden:
   - **Händelsekategori**: Välj **Tjänsthälsan**.
   - **Tjänster**: Välj **Virtual Machine Scale Sets och virtuella datorer**.
   - **Typ**: Välj **planerat underhåll**. 
    
Mer information om hur du konfigurerar aviseringar för aktivitetsloggen finns [skapa aktivitetsloggsaviseringar](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Starta Underhåll på VM-skalningsuppsättning från portalen

Du kan se mer underhållsrelaterade information finns i översikt över VM-skalningsuppsättningar. Om minst en virtuell dator i virtual machine scale Sets ingår i det planerade underhållet senare, läggs en ny avisering menyfliksområdet längst upp på sidan. Välj meddelande menyfliksområdet för att gå till den **Underhåll** sidan. 

På den **Underhåll** sidan ser du vilka VM-instans påverkas av planerat underhåll. För att starta underhåll, markerar du kryssrutan som motsvarar den berörda virtuella datorn. Välj **starta Underhåll**.

När du startar Underhåll berörda virtuella datorer i virtual machine scale Sets genomgår underhåll och är inte tillgänglig för tillfället. Om du missade fönstret självbetjäning kan se du fortfarande tidsfönstret när virtual machine scale Sets upprätthålls av Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Kontrollera status för underhåll med hjälp av PowerShell

Du kan använda Azure PowerShell för att se när virtuella datorer i VM-skalningsuppsättningar är schemalagda för underhåll. Planerat underhållsinformation är tillgänglig med hjälp av den [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) cmdlet när du använder den `-InstanceView` parametern.
 
Underhållsinformation returneras bara om Underhåll är planerat. Om inget Underhåll har schemalagts som påverkas av den Virtuella datorinstansen returnerar cmdlet inte underhållsinformation om. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Följande egenskaper returneras **MaintenanceRedeployStatus**: 
| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta Underhåll på den virtuella datorn just nu. ||
| PreMaintenanceWindowStartTime         | I början av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn. ||
| PreMaintenanceWindowEndTime           | Slutet av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn. ||
| MaintenanceWindowStartTime            | I början av schemalagt underhåll där Azure initierar Underhåll på den virtuella datorn. ||
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhållsperiod där Azure initierar Underhåll på den virtuella datorn. ||
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn. ||



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Starta Underhåll på VM-instans med hjälp av PowerShell

Du kan starta Underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** är inställd på **SANT**. Använd den [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) cmdlet med `-PerformMaintenance` parametern.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Kontrollera status för underhåll med hjälp av CLI

Du kan visa information om planerat underhåll med hjälp av [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Underhållsinformation returneras bara om Underhåll är planerat. Om inget underhåll som påverkas av den Virtuella datorinstansen har schemalagts returnerar kommandot inte underhållsinformation om. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Följande egenskaper returneras **MaintenanceRedeployStatus** för varje virtuell datorinstans: 
| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta Underhåll på den virtuella datorn just nu. ||
| PreMaintenanceWindowStartTime         | I början av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn. ||
| PreMaintenanceWindowEndTime           | Slutet av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn. ||
| MaintenanceWindowStartTime            | I början av schemalagt underhåll där Azure initierar Underhåll på den virtuella datorn. ||
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhållsperiod där Azure initierar Underhåll på den virtuella datorn. ||
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn. ||


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Starta Underhåll på VM-instans med hjälp av CLI

Följande anrop initierar Underhåll på en VM-instans om `IsCustomerInitiatedMaintenanceAllowed` är inställd på **SANT**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: Varför behöver du starta om Mina virtuella datorer nu?**

**S:** Även om de flesta uppdateringar och uppgraderingar till Azure-plattformen inte påverkar VM-tillgänglighet, i vissa fall kan inte går att vi undvika att starta om virtuella datorer i Azure. Vi har ackumulerat flera ändringar som kräver att vi startar om våra servrar som leder till omstart av virtuell dator.

**F: Om jag följer rekommendationer för hög tillgänglighet med hjälp av en tillgänglighetsuppsättning ange, är jag säker?**

**S:** Virtuella datorer som distribueras i en tillgänglighetsuppsättning ange eller i VM-skalningsuppsättningar använder uppdateringsdomäner. När underhåll utförs följer domänbegränsningar update Azure och starta om inte virtuella datorer från en annan uppdateringsdomän (inom samma tillgänglighetsuppsättning). Azure också väntar minst 30 minuter innan du flyttar till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [regioner och tillgänglighet för virtuella datorer i Azure](../virtual-machines/windows/regions-and-availability.md).

**F: Hur kan jag bli meddelad om planerat underhåll?**

**S:** En planerade underhållet senare startar genom att ange ett schema till en eller flera Azure-regioner. Strax efter ett e-postmeddelande skickas till prenumerationsägarna (en e-post per prenumeration). Du kan lägga till kanaler och mottagare för det här meddelandet med hjälp av aviseringar för aktivitetsloggen. Om du distribuerar en virtuell dator till en region där planerat underhåll har redan schemalagts kan får du inte meddelandet. Kontrollera i stället Underhåll tillståndet för den virtuella datorn.

**F: Jag får ingen indikation på planerat underhåll i portalen, PowerShell eller CLI. Vad är fel?**

**S:** Information relaterad till planerat underhåll är tillgänglig under en planerade underhållet senare endast för de virtuella datorerna som påverkas av planerat underhåll. Om du inte ser data underhållet kan redan vara klar (eller inte startats) eller den virtuella datorn kan redan finnas på en uppdaterad server.

**F: Finns det ett sätt att veta exakt när den virtuella datorn kommer att påverkas?**

**S:** När vi ställer in schemat definierar vi ett tidsfönster på flera dagar. Den exakta sekvenseringen av servrar (och virtuella datorer) under den här perioden är okänd. Om du vill veta den exakta tidpunkt som dina virtuella datorer kommer att uppdateras, kan du använda [schemalagda händelser](../virtual-machines/windows/scheduled-events.md). När du använder schemalagda händelser kan du fråga från den virtuella datorn och få ett meddelande för 15 minuter innan en omstart av virtuell dator.

**F: Hur lång tid tar det att datorn startas om min virtuella dator?**

**S:**  Beroende på storleken på den virtuella datorn kan omstart ta upp till flera minuter under självbetjäning underhållsfönstret. Omstarten tar normalt ungefär 25 minuter under omstarter initierades av Azure i det schemalagda underhållsfönstret. Om du använder Cloud Services (Web/Worker-rollen), VM-skalningsuppsättningar, eller tillgänglighetsuppsättningar, ges 30 minuter mellan varje grupp med virtuella datorer (uppdateringsdomän) under underhållsperioden. 

**F: Jag ser inte någon underhållsinformation på Mina virtuella datorer. Vad som gick fel?**

**S:** Det finns flera orsaker till varför du inte kan se all underhållsinformation på dina virtuella datorer:
   - Du använder en prenumeration som markerats som *Microsoft Internal*.
   - Dina virtuella datorer inte är schemalagda för underhåll. Det kan vara att underhållet avslutades, avbröts eller har ändrats så att dina virtuella datorer påverkas inte längre av den.
   - Du behöver inte den **Underhåll** kolumnen som lagts till i VM-listvyn. Även om vi lagt till den här kolumnen till standardvyn, om du konfigurerar vyn om du vill se kolumner som inte är standard, måste du manuellt lägga till den **Underhåll** kolumnen till VM-listvyn.

**F: Den virtuella datorn är schemalagd för underhåll en andra gång. Varför?**

**S:** I flera Användningsscenarier, är den virtuella datorn schemalagd för underhåll när du har redan slutfört din underhåll och omdistribueras:
   - Vi har avbrutit underhållet och startas om den med en annan nyttolast. Det kan vara att vi har upptäckt en felaktig nyttolast och vi behöver att distribuera en ytterligare nyttolast.
   - Din virtuella dator *tjänst har åtgärdats* till en annan nod på grund av ett maskinvarufel.
   - Du har valt för att stoppa (frigöra) och starta om den virtuella datorn.
   - Du har **automatisk avstängning** aktiverad för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du registrera dig för av underhållshändelser från den virtuella datorn med hjälp av [schemalagda händelser](../virtual-machines/windows/scheduled-events.md).
