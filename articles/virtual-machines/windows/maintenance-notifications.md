---
title: Hantering av meddelanden om Underhåll för Windows-datorer i Azure | Microsoft Docs
description: Visa meddelanden om Underhåll för Windows-datorer som körs i Azure och börja självbetjäningsunderhållet.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: cfd88118b483067f4c6ee62ee013a2202f0d7e67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301771"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Hantera meddelanden planerat underhåll för Windows-datorer

Azure utför med jämna mellanrum uppdateringar för att förbättra värdinfrastrukturens tillförlitlighet, prestanda och säkerhet för virtuella datorer. Uppdateringarna är ändringar som uppdatering värdmiljön eller uppgradera och inaktivering av maskinvara. En majoritet av dessa uppdateringar genomförs utan någon inverkan på de virtuella datorerna. Men finns det fall där uppdateringar påverka:

- Om underhållet inte kräver en omstart, använder Azure migrering på plats för att pausa den virtuella datorn när värden har uppdaterats. Dessa icke rebootful underhållsåtgärder är tillämpad feldomän av feldomän och förloppet stoppas om någon varning hälsotillstånd signaler tas emot. 

- Om en omstart krävs för underhåll, får du ett meddelande om när det planerade underhållet. I dessa fall kan ges ett tidsfönster som där du kan starta underhållet själv när det passar dig.


Planerat underhåll som kräver en omstart schemaläggs i vågor. Varje våg har olika omfång (regioner).

- En våg börjar med ett meddelande till kunderna. Som standard skickas meddelandet till Prenumerationens ägare och Medägare. Du kan lägga till fler mottagare och meddelandeinställningarna som e-post, SMS och Webhooks, till meddelanden med hjälp av Azure [Aktivitetsloggaviseringar](../../azure-monitor/platform/activity-logs-overview.md).  
- Vid tidpunkten för anmälan, en *självbetjäning fönstret* är tillgänglig. Under det här fönstret kan du hitta vilka av dina virtuella datorer som ingår i den här wave och proaktivt starta Underhåll efter behov schemaläggning.
- När fönstret självbetjäning en *schemalagda underhållsperiod* börjar. Någon gång under det här fönstret Azure schemalägger och gäller det nödvändiga underhållet för den virtuella datorn. 

Målet i att ha två windows är att ge dig tid att starta underhåll och starta om den virtuella datorn samtidigt som du vet när Azure startar automatiskt underhåll.


Du kan använda Azure portal, PowerShell, REST API och CLI för att fråga efter underhållsperioderna för dina virtuella datorer och starta självbetjäningsunderhållet.

  
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
- Om du har valt schemalagda händelser som möjliggör proaktiv redundans eller avslutning av din arbetsbelastning 15 minuter innan Underhåll avstängning

**Använd** självbetjäningsunderhållet, om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av de avdelningar uppgifter som nämns ovan gäller. 

Det är bäst att använda självbetjäningsunderhållet i följande fall:

- Du måste kommunicera en exakt underhållsperiod till hantering eller end-kund. 
- Du måste du ha slutfört underhållet med ett visst datum. 
- Du behöver styra sekvensen av underhåll, till exempel flernivåapp att garantera säker omstart.
- Du behöver mer än 30 minuter från tiden för återställning av virtuell dator mellan två uppdateringsdomäner (ud). För att styra tiden mellan uppdateringsdomäner, måste du utlöser Underhåll på dina virtuella datorer en uppdateringsdomän (UD) i taget.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Kontrollera status för underhåll med hjälp av PowerShell

Du kan också använda Azure Powershell för att se när virtuella datorer som är schemalagda för underhåll. Planerat underhållsinformation är tillgänglig på den [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) cmdlet när du använder den `-status` parametern.
 
Underhållsinformation returneras bara om det är planerat underhåll. Om inget Underhåll är schemalagd som påverkar den virtuella datorn, returnerar cmdlet: en inte någon underhållsinformation. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

Under MaintenanceRedeployStatus returneras följande egenskaper: 

| Värde | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta Underhåll på den virtuella datorn just nu |
| PreMaintenanceWindowStartTime         | I början av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn |
| PreMaintenanceWindowEndTime           | Slutet av självbetjäning underhållsfönstret när du kan starta Underhåll på den virtuella datorn |
| MaintenanceWindowStartTime            | I början av schemalagt underhåll där Azure initierar Underhåll på den virtuella datorn |
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhållsperiod där Azure initierar Underhåll på den virtuella datorn |
| LastOperationResultCode               | Resultatet av det senaste försöket att starta underhållet på den virtuella datorn |



Du kan också visa status för underhåll för alla virtuella datorer i en resursgrupp med hjälp av [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) och inte ange en virtuell dator.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Följande PowerShell-funktionen tar ditt prenumerations-ID och skriver ut en lista över virtuella datorer som är schemalagda för underhåll.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Starta Underhåll på den virtuella datorn med hjälp av PowerShell

Med informationen från funktionen i föregående avsnitt kan följande börjar Underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** har angetts till true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassiska distributioner

Om du fortfarande har äldre virtuella datorer som har kan distribueras med den klassiska distributionsmodellen du använda PowerShell för att fråga för virtuella datorer och starta underhållet.

Om du vill hämta status för underhåll av en virtuell dator, skriver du:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Om du vill starta Underhåll på den klassiska virtuella datorn, skriver du:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR


**F: Varför behöver du starta om Mina virtuella datorer nu?**

**S:** Flesta uppdateringar och uppgraderingar till Azure-plattformen inte påverkar virtuella datorns tillgänglighet, finns men det fall där vi kan undvika att starta om virtuella datorer som finns i Azure. Vi har ackumulerat flera ändringar som kräver att vi startar om våra servrar som leder till virtuella datorerna startas om.

**F: Om jag följer rekommendationer för hög tillgänglighet med hjälp av en Tillgänglighetsuppsättning är jag säker?**

**S:** Virtuella datorer som distribueras i en tillgänglighetsuppsättning eller i en skalningsuppsättning för virtuella datorer har begreppet uppdateringsdomän (UD). När underhåll utförs följer UD-begränsningen i Azure och kommer inte att starta om virtuella datorer från en annan Uppdateringsdomän (inom samma tillgänglighetsuppsättning).  Azure också väntar minst 30 minuter innan du flyttar till nästa grupp av virtuella datorer. 

Mer information om hög tillgänglighet finns i [regioner och tillgänglighet för virtuella datorer i Azure](regions-and-availability.MD).

**F: Hur jag bli meddelad om planerat underhåll?**

**S:** En planerade underhållet senare startar genom att ange ett schema till en eller flera Azure-regioner. Strax efter ett e-postmeddelande skickas till prenumerationsägarna (en e-post per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med hjälp av Aktivitetsloggaviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll har redan schemalagts du kommer inte att ta emot meddelandet, men i stället måste du kontrollera underhållstillstånd för av den virtuella datorn.

**F: Jag får ingen indikation på planerat underhåll i portalen, Powershell eller CLI. Vad är fel?**

**S:** Information som rör planerat underhåll är tillgänglig under en planerade underhållet senare endast för de virtuella datorerna som kommer att påverkas. Med andra ord, om du ser inte data, kan det vara att underhållet har slutförts (eller inte startats) eller att den virtuella datorn finns redan i en uppdaterad server.

**F: Finns det ett sätt att veta exakt när min virtuella dator kommer att påverkas?**

**S:** När du ställer in schemat definierar vi ett tidsfönster på flera dagar. Men är den exakta Sekvenseringen av servrar (och virtuella datorer) i det här fönstret okänd. Kunder som vill veta exakt tid för sina virtuella datorer kan använda [schemalagda händelser](scheduled-events.md) och fråga från den virtuella datorn och ta emot ett meddelande för 15 minuter innan en omstart av virtuell dator.

**F: Hur lång tid tar det att datorn startas om min virtuella dator?**

**S:**  Beroende på storleken på den virtuella datorn kan omstart ta upp till flera minuter under självbetjäning underhållsfönstret. Under Azure initierade omstarter i det schemalagda underhållsfönstret tar vanligtvis omstarten ungefär 25 minuter. Observera att om du använder Cloud Services (Web/Worker-roll), Virtual Machine Scale Sets eller tillgänglighetsuppsättningar kan du får 30 minuter mellan varje grupp av virtuella datorer (UD) under underhållsperioden. 

**F: Vad är händer med Virtual Machine Scale Sets?**

**S:** Planerat underhåll är nu tillgänglig för Virtual Machine Scale Sets. Instruktioner för hur du startar självbetjäningsunderhållet [planerat underhåll för VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) dokumentet.

**F: Vad är händer med Cloud Services (Web/Worker-roll) och Service Fabric?**

**S:** När de här plattformarna påverkas av planerat underhåll betraktas kunder som använder plattformarna som säkra eftersom endast enskilda virtuella datorer i en enda uppgraderingsdomän (UD) påverkas vid en given tidpunkt. Självbetjäningsunderhållet är för närvarande inte tillgänglig för molntjänster (Web/Worker-roll) och Service Fabric.

**F: Jag ser inte någon underhållsinformation på Mina virtuella datorer. Vad som gick fel?**

**S:** Det finns flera orsaker till varför du inte ser någon underhållsinformation på dina virtuella datorer:
1.  Du använder en prenumeration som markerats som Microsoft internt.
2.  Dina virtuella datorer är inte schemalagda för underhåll. Det kan bero på att underhållet har slutförts har avbrutits eller ändras så att dina virtuella datorer som inte längre påverkas av den.
3.  Du behöver inte den **Underhåll** kolumnen som lagts till i VM-listvyn. Vi har lagt till den här kolumnen till standardvyn, kunder som konfigurerats för att se icke-standard-kolumner måste manuellt lägga till den **Underhåll** kolumnen till deras VM-listvyn.

**F: Den virtuella datorn är schemalagd för underhåll en andra gång. Varför?**

**S:** Det finns flera användningsscenarier då du kan se att den virtuella datorn är schemalagd för underhåll efter att du redan har genomfört underhåll och omdistribution:
1.  Vi har avbrutit underhållet och startas om den med en annan nyttolast. Det kan bero på att vi har upptäckt felaktig nyttolasten och vi behöver att distribuera en ytterligare nyttolast.
2.  Din virtuella dator *tjänst har åtgärdats* till en annan nod på grund av ett maskinvarufel
3.  Du har valt för att stoppa (frigöra) och starta om den virtuella datorn
4.  Du har **automatisk avstängning** aktiverad för den virtuella datorn


## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan registrera dig för underhållshändelser från den virtuella datorn med hjälp av [Scheduled Events](scheduled-events.md).
