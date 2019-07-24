---
title: Hantera underhålls meddelanden för virtuella Windows-datorer i Azure | Microsoft Docs
description: Visa underhålls meddelanden för virtuella Windows-datorer som körs i Azure och starta självbetjänings underhåll.
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
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: b388c600e5eb9331fe22d1a8e9aac8f47edb3ef2
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849607"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Hantera meddelanden för planerat underhåll för virtuella Windows-datorer

Azure utför med jämna mellanrum uppdateringar för att förbättra värdinfrastrukturens tillförlitlighet, prestanda och säkerhet för virtuella datorer. Uppdateringar är ändringar som att korrigera värd miljön eller uppgradera och inaktivera maskin vara. En majoritet av dessa uppdateringar utförs utan påverkan på de värdbaserade virtuella datorerna. Det finns dock fall där uppdateringar påverkar:

- Om underhållet inte kräver en omstart använder Azure migrering på plats för att pausa den virtuella datorn medan värden uppdateras. Dessa underhålls åtgärder som inte är omstartade tillämpas på fel domän av feldomän och förlopp stoppas om eventuella varnings hälso signaler tas emot. 

- Om underhåll kräver en omstart får du ett meddelande om när underhållet planeras. I dessa fall får du ett tidsfönster som vanligt vis är 30 dagar där du kan starta underhållet själv när det passar dig.


Planerat underhåll som kräver en omstart är schemalagt i vågor. Varje våg har olika omfång (regioner).

- En våg börjar med ett meddelande till kunderna. Som standard skickas ett meddelande till Prenumerationens ägare och medägare. Du kan lägga till fler mottagare och meddelande alternativ, t. ex. e-post, SMS och Webhooks, till meddelanden med hjälp av Azure [aktivitets logg aviseringar](../../azure-monitor/platform/activity-logs-overview.md).  
- Vid tidpunkten för meddelandet görs ett självbetjänings *fönster* tillgängligt. Under det här fönstret som vanligt vis är 30 dagar kan du se vilken av dina virtuella datorer som ingår i den här vågen och proaktivt starta underhåll enligt dina egna schemaläggnings behov.
- Efter självbetjänings fönstret startar en *schemalagd underhålls period* . Vid något tillfälle under det här fönstret schemalägger Azure och tillämpar det nödvändiga underhållet på den virtuella datorn. 

Målet med två Windows är att ge dig tillräckligt med tid för att starta underhållet och starta om den virtuella datorn samtidigt som du vet när Azure startar underhåll automatiskt.


Du kan använda Azure Portal, PowerShell, REST API och CLI för att fråga efter underhålls Fönstren för dina virtuella datorer och starta självbetjänings underhållet.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Bör du starta underhåll under självbetjänings fönstret?  

Följande rikt linjer hjälper dig att bestämma om du vill använda den här funktionen och starta underhållet vid din egen tidpunkt.

> [!NOTE] 
> Självbetjänings underhåll kanske inte är tillgängligt för alla dina virtuella datorer. Du kan ta reda på om proaktiv omdistribution är tillgängligt för den virtuella datorn genom att leta reda på **Start nu** i underhålls status. Självbetjänings underhåll är för närvarande inte tillgängligt för Cloud Services (webb-och arbets roll) och Service Fabric.


Självbetjänings underhåll rekommenderas inte för distributioner med hjälp av **tillgänglighets uppsättningar** eftersom dessa är hög tillgängliga-installationer, där endast en uppdaterings domän påverkas vid en bestämd tidpunkt. 
- Låt Azure utlösa underhållet. För underhåll som kräver omstart bör du vara medveten om att underhållet kommer att vara en uppdaterings domän av en uppdaterings domän, att uppdaterings domänerna inte nödvändigt vis får underhållet sekventiellt, och att det finns en 30 minuters paus mellan uppdaterings domäner. 
- Om en tillfällig förlust av en del av din kapacitet (1/uppdatering av domän antal) är ett problem, kan det enkelt kompenseras genom att allokera ytterligare instanser under underhålls perioden.
- För underhåll som inte kräver omstart tillämpas uppdateringar på fel domän nivå.
    
Använd **inte** självbetjänings underhåll i följande scenarier: 
- Om du stänger av de virtuella datorerna ofta, antingen manuellt, med DevTest Labs, med automatisk avstängning eller efter ett schema, kan det återställa underhålls statusen och därmed orsaka ytterligare nedtid.
- På virtuella datorer med korta livs längder som du vet kommer att tas bort innan underhålls vågen är slut. 
- För arbets belastningar med stor status lagrad på den lokala (tillfälliga) disken som du vill behålla vid uppdateringen. 
- I de fall där du ändrar storlek på den virtuella datorn ofta, eftersom det kan återställa underhålls statusen.
- Om du har antagit schemalagda händelser som aktiverar proaktiv redundans eller en korrekt avstängning av arbets belastningen, 15 minuter innan underhålls avstängning påbörjas

**Använd** självbetjänings underhåll, om du planerar att köra den virtuella datorn utan avbrott under fasen för schemalagt underhåll och ingen av räknarna som anges ovan är tillämpliga. 

Det är bäst att använda självbetjänings underhåll i följande fall:

- Du måste kommunicera med en exakt underhålls period till din hantering eller till slut kunden. 
- Du måste slutföra underhållet vid ett visst datum. 
- Du måste kontrol lera sekvensen av underhåll, till exempel ett program på flera nivåer för att garantera säker återställning.
- Du behöver mer än 30 minuter återställnings tid för datorer mellan två uppdaterings domäner (UDs). Om du vill kontrol lera tiden mellan uppdaterings domäner måste du aktivera underhåll av virtuella datorer en uppdaterings domän (UD) i taget.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Kontrol lera underhålls status med hjälp av PowerShell

Du kan också använda Azure PowerShell för att se när virtuella datorer är schemalagda för underhåll. Information om planerat underhåll är tillgänglig från cmdleten [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) när du använder `-status` -parametern.
 
Underhålls information returneras endast om underhållet är planerat. Om inget underhåll har schemalagts som påverkar den virtuella datorn returnerar cmdleten ingen underhålls information. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

Följande egenskaper returneras under MaintenanceRedeployStatus: 

| Value | Beskrivning   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Anger om du kan starta underhåll på den virtuella datorn just nu |
| PreMaintenanceWindowStartTime         | Början av självbetjänings fönstret för underhåll när du kan initiera underhåll på den virtuella datorn |
| PreMaintenanceWindowEndTime           | Slutet av självbetjänings fönstret för underhåll när du kan initiera underhåll på den virtuella datorn |
| MaintenanceWindowStartTime            | Början av det underhåll som schemalagts för Azure initierar underhåll på den virtuella datorn |
| MaintenanceWindowEndTime              | Slutet av den schemalagda underhålls perioden i vilken Azure initierar underhåll på den virtuella datorn |
| LastOperationResultCode               | Resultatet av det senaste försöket att initiera underhåll på den virtuella datorn |



Du kan också hämta underhålls status för alla virtuella datorer i en resurs grupp med hjälp av [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) och inte ange en virtuell dator.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Följande PowerShell-funktion tar ditt prenumerations-ID och skriver ut en lista över virtuella datorer som är schemalagda för underhåll.

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

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Starta underhåll på den virtuella datorn med PowerShell

Med hjälp av informationen från funktionen i föregående avsnitt, påbörjas följande underhåll på en virtuell dator om **IsCustomerInitiatedMaintenanceAllowed** är inställt på True.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassiska distributioner

Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributions modellen kan du använda PowerShell för att fråga efter virtuella datorer och initiera underhåll.

Om du vill hämta underhålls statusen för en virtuell dator skriver du:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Starta underhållet på den klassiska virtuella datorn genom att skriva:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR


**F: Varför måste du starta om mina virtuella datorer nu?**

**S:** Majoriteten av uppdateringar och uppgraderingar av Azure-plattformen påverkar inte den virtuella datorns tillgänglighet, men det finns fall där vi inte kan undvika att starta om virtuella datorer som finns i Azure. Vi har samlat flera ändringar som kräver att vi startar om våra servrar som kommer att leda till att virtuella datorer startas om.

**F: Är jag säker om jag följer dina rekommendationer för hög tillgänglighet med hjälp av en tillgänglighets uppsättning?**

**S:** Virtuella datorer som distribueras i en tillgänglighetsuppsättning eller i en skalningsuppsättning för virtuella datorer har begreppet uppdateringsdomän (UD). När du utför underhåll följer Azure UD-begränsningen och startar inte om virtuella datorer från olika UD (inom samma tillgänglighets uppsättning).  Azure väntar också i minst 30 minuter innan det flyttas till nästa grupp med virtuella datorer. 

Mer information om hög tillgänglighet finns i [tillgänglighet för virtuella datorer i Azure](availability.MD).

**F: Hur gör jag för att få ett meddelande om planerat underhåll?**

**S:** En planerad underhålls våg börjar genom att ange ett schema för en eller flera Azure-regioner. Strax efter skickas ett e-postmeddelande till Prenumerationens ägare (ett e-postmeddelande per prenumeration). Ytterligare kanaler och mottagare för det här meddelandet kan konfigureras med aktivitets logg aviseringar. Om du distribuerar en virtuell dator till en region där planerat underhåll redan har schemalagts, får du inte meddelandet, utan du behöver kontrol lera underhålls statusen för den virtuella datorn.

**F: Jag kan inte se någon indikation på planerat underhåll i portalen, PowerShell eller CLI. Vad är fel?**

**S:** Information som rör planerat underhåll är bara tillgänglig under en planerad underhålls våg för de virtuella datorer som ska påverkas av den. Om du inte ser några data kan det bero på att underhålls vågen redan har slutförts (eller inte startats) eller att den virtuella datorn redan finns på en uppdaterad Server.

**F: Är det något sätt att veta exakt när min virtuella dator påverkas?**

**S:** När du anger ett schema definierar vi en tids period på flera dagar. Men den exakta ordningsföljden av servrar (och virtuella datorer) i det här fönstret är okänd. Kunder som vill veta exakt hur lång tid det tar för sina virtuella datorer att använda [schemalagda händelser](scheduled-events.md) och frågor från den virtuella datorn och få en 15 minuters avisering innan en virtuell dator startas om.

**F: Hur lång tid tar det att starta om min virtuella dator?**

**S:**  Beroende på storleken på den virtuella datorn kan det ta upp till flera minuter att starta om i fönstret för självbetjänings underhåll. Under den Azure-initierade omstarten i det schemalagda underhålls fönstret tar det vanligt vis ungefär 25 minuter att starta om. Observera att om du använder Cloud Services (webb-eller arbets roll), Virtual Machine Scale Sets eller tillgänglighets uppsättningar får du 30 minuter mellan varje grupp av virtuella datorer (UD) under den schemalagda underhålls perioden. 

**F: Vad händer om Virtual Machine Scale Sets?**

**S:** Planerat underhåll är nu tillgängligt för Virtual Machine Scale Sets. Instruktioner för hur du startar självbetjänings underhållet hänvisa [till planerat underhåll för VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) -dokument.

**F: Vad händer om Cloud Services (webb-och arbets roll) och Service Fabric?**

**S:** När de här plattformarna påverkas av planerat underhåll betraktas kunder som använder plattformarna som säkra eftersom endast enskilda virtuella datorer i en enda uppgraderingsdomän (UD) påverkas vid en given tidpunkt. Självbetjänings underhåll är för närvarande inte tillgängligt för Cloud Services (webb-och arbets roll) och Service Fabric.

**F: Jag ser ingen underhålls information på mina virtuella datorer. Vad gick fel?**

**S:** Det finns flera orsaker till varför du inte ser någon underhålls information på dina virtuella datorer:
1.  Du använder en prenumeration som marker ATS som Microsoft Internal.
2.  De virtuella datorerna är inte schemalagda för underhåll. Det kan vara att underhålls vågen har slutat, avbrutits eller ändrats så att dina virtuella datorer inte längre påverkas av den.
3.  Du har inte lagt  till underhålls kolumnen i listvyn för din VM. Vi har lagt till den här kolumnen i standardvyn, men kunder som har konfigurerat för att se kolumner som inte är standard måste  manuellt lägga till underhålls kolumnen i sin listvy för virtuella datorer.

**F: Min virtuella dator är schemalagd för underhåll för den andra gången. Varför?**

**S:** Det finns flera användningsscenarier då du kan se att den virtuella datorn är schemalagd för underhåll efter att du redan har genomfört underhåll och omdistribution:
1.  Vi har avbrutit underhålls vågen och startat om den med en annan nytto Last. Det kan bero på att vi har upptäckt en felaktig nytto last och att vi bara behöver distribuera ytterligare en nytto Last.
2.  Den virtuella datorn har *tjänsten lagad* till en annan nod på grund av ett maskin varu fel
3.  Du har valt att stoppa (frigöra) och starta om den virtuella datorn
4.  Du har aktiverat **Automatisk avstängning** för den virtuella datorn


## <a name="next-steps"></a>Nästa steg

Lär dig hur du kan registrera dig för underhålls händelser inifrån den virtuella datorn med hjälp av [schemalagda händelser](scheduled-events.md).
