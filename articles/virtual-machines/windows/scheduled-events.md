---
title: Schemalagda händelser för virtuella Windows-datorer i Azure
description: Schemalagda händelser med azure-metadatatjänsten för på dina virtuella Windows-datorer.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: c1e9ef8de65912c4f33e17ee2bb2175c76e7ea07
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258695"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Schemalagda händelser för Virtuella Windows-datorer

Schemalagda händelser är en Azure Metadata Service som ger ditt program tid att förbereda för underhåll av virtuella datorer. Den innehåller information om kommande underhållshändelser (t.ex. omstart) så att ditt program kan förbereda sig för dem och begränsa störningar. Den är tillgänglig för alla Azure Virtual Machine-typer, inklusive PaaS och IaaS på både Windows och Linux. 

Information om schemalagda händelser på Linux finns i [Schemalagda händelser för virtuella Linux-datorer](../linux/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgängliga i alla Azure-regioner. Se [Version och Region Tillgänglighet](#version-and-region-availability) för senaste versionen information.

## <a name="why-scheduled-events"></a>Varför schemalagda händelser?

Många program kan dra nytta av tid för att förbereda sig för underhåll av virtuella datorer. Tiden kan användas för att utföra programspecifika uppgifter som förbättrar tillgänglighet, tillförlitlighet och servicebarhet, inklusive: 

- Kontrollpunkt och återställning
- Anslutningstömning
- Primär växlingsöver redundans för primär replik 
- Borttagning från belastningsutjämnad pool
- Händelseloggning
- Graciös avstängning 

Med schemalagda händelser kan ditt program identifiera när underhåll kommer att ske och utlösa aktiviteter för att begränsa dess påverkan. Om du aktiverar schemalagda händelser får den virtuella datorn en minsta tid innan underhållsaktiviteten utförs. Mer information finns i avsnittet Händelseplanering nedan.

Schemalagda händelser innehåller händelser i följande användningsfall:
- [Plattformsinitierat underhåll](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (till exempel omstart av virtuella datorer, livemigrering eller minneskonering av uppdateringar för värd)
- Virtuell dator körs på [skadad värdmaskinvara](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) som förutspås misslyckas snart
- Användarinitierat underhåll (t.ex. startar om eller distribuerar om en virtuell dator)
- [Spot-vm-](spot-vms.md) och [spotskalauppsättning](../../virtual-machine-scale-sets/use-spot.md) instansvräkningar

## <a name="the-basics"></a>Grunderna  

Azure Metadata-tjänsten visar information om hur du kör virtuella datorer med hjälp av en REST-slutpunkt som är tillgänglig inifrån den virtuella datorn. Informationen är tillgänglig via en icke-dirigerbar IP så att den inte exponeras utanför den virtuella datorn.

### <a name="endpoint-discovery"></a>Identifiering av slutpunkt
För virtuella datorer som är aktiverade är metadatatjänsten tillgänglig från `169.254.169.254`en statisk IP som inte är dirigerbar. Den fullständiga slutpunkten för den senaste versionen av schemalagda händelser är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Om den virtuella datorn inte skapas i ett virtuellt nätverk, standardfallen för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adressen som ska användas. I det här exemplet får du lära dig hur du [identifierar värdslutpunkten](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Tillgänglighet för version och region
Tjänsten Schemalagda händelser är version. Versioner är obligatoriska och den `2019-01-01`aktuella versionen är .

| Version | Typ av utgivning | Regioner | Viktig information | 
| - | - | - | - |
| 2019-01-01 | Allmän tillgänglighet | Alla | <li> Lagt till stöd för skalningsuppsättningar för virtuella datorer EventType 'Avsluta' |
| 2017-11-01 | Allmän tillgänglighet | Alla | <li> Lagt till stöd för Spot VM vräkning EventType 'Preempt'<br> | 
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Borttagen prepended understreck från resursnamn för IaaS virtuella datorer<br><li>Metadata header krav tillämpas för alla begäranden | 
| 2017-03-01 | Förhandsversion | Alla |<li>Första utgåvan |

> [!NOTE] 
> Tidigare förhandsversioner av schemalagda händelser stöds {senaste} som api-version. Det här formatet stöds inte längre och kommer att vara inaktuellt i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera schemalagda händelser
Schemalagda händelser är aktiverade för din tjänst första gången du gör en begäran om händelser. Du bör förvänta dig ett försenat svar i ditt första samtal på upp till två minuter. Du bör fråga slutpunkten regelbundet för att identifiera kommande underhållshändelser samt status för underhållsaktiviteter som utförs.

Schemalagda händelser är inaktiverat för din tjänst om den inte gör en begäran på 24 timmar.

### <a name="user-initiated-maintenance"></a>Underhåll av användaren
Användaren initierade underhåll av virtuella datorer via Azure-portalen, API, CLI eller PowerShell resulterar i en schemalagd händelse. På så sätt kan du testa underhållsförberedelslogiken i ditt program och gör att ditt program kan förbereda sig för användarinitierat underhåll.

Om du startar om en virtuell `Reboot`dator schemaläggs en händelse med typ . Omdevisera om en virtuell dator schemalägger en händelse med typ `Redeploy`.

## <a name="using-the-api"></a>Använda API:et

### <a name="headers"></a>Rubriker
När du frågar metadatatjänsten måste `Metadata:true` du ange huvudet för att säkerställa att begäran inte omdirigerades oavsiktligt. Huvudet `Metadata:true` krävs för alla schemalagda händelsebegäranden. Om du inte inkluderar huvudet i begäran resulterar det i ett svar på felaktig begäran från metadatatjänsten.

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att helt enkelt ringa följande samtal:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

Ett svar innehåller en matris med schemalagda händelser. En tom matris innebär att det för närvarande inte finns några schemalagda händelser.
Om det finns schemalagda händelser innehåller svaret en matris med händelser: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentInkarnationen är en ETag och är ett enkelt sätt att kontrollera om nyttolasten Händelser har ändrats sedan den senaste frågan.

### <a name="event-properties"></a>Egenskaper för händelser
|Egenskap  |  Beskrivning |
| - | - |
| EventId (på)EventId ) | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Eventtype | Påverka den här händelsen orsaker. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn är schemalagd att pausa i några sekunder. CPU- och nätverksanslutningen kan avbrytas, men det finns ingen inverkan på minnet eller öppna filer. <li>`Reboot`: Den virtuella datorn är schemalagd för omstart (icke-beständigt minne går förlorat). <li>`Redeploy`: Den virtuella datorn är schemalagd att flyttas till en annan nod (efemära diskar går förlorade). <li>`Preempt`: Spot Virtual Machine tas bort (efemära diskar går förlorade). <li> `Terminate`: Den virtuella datorn är schemalagd att tas bort. |
| ResourceType | Typ av resurs som den här händelsen påverkar. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som den här händelsen påverkar. Detta är garanterat att innehålla datorer från högst en [Uppdateringsdomän](manage-availability.md), men kanske inte innehåller alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Händelsestatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen är schemalagd att `NotBefore` starta efter den tid som anges i egenskapen.<li>`Started`: Den här händelsen har startat.</ul> Ingen `Completed` eller liknande status tillhandahålls någonsin. händelsen kommer inte längre att returneras när händelsen är klar.
| NotBefore| Tid efter vilken den här händelsen kan starta. <br><br> Exempel: <br><ul><li> mån, 19 sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Schemaläggning av händelser
Varje händelse schemaläggs en minsta tid i framtiden baserat på händelsetyp. Den här gången återspeglas `NotBefore` i en händelse egenskap. 

|Eventtype  | Minsta meddelande |
| - | - |
| Frysa| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |
| Föregripa | 30 sekunder |
| Avsluta | [Användaren konfigurerbar:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 till 15 minuter |

> [!NOTE] 
> I vissa fall kan Azure förutsäga värdfel på grund av försämrad maskinvara och kommer att försöka minska störningar i tjänsten genom att schemalägga en migrering. Berörda virtuella datorer får en `NotBefore` schemalagd händelse med en som vanligtvis är några dagar i framtiden. Den faktiska tiden varierar beroende på den förväntade riskbedömningen. Azure försöker ge 7 dagars förvarning när det är möjligt, men den faktiska tiden varierar och kan vara mindre om förutsägelsen är att det finns en stor chans att maskinvaran misslyckas inom kort. För att minimera risken för din tjänst om maskinvaran misslyckas innan systemet initierade migreringen, rekommenderas att du distribuerar om den virtuella datorn så snart som möjligt.

### <a name="event-scope"></a>Händelseomfattning     
Schemalagda händelser levereras till:
 - Fristående virtuella datorer
 - Alla virtuella datorer i en molntjänst      
 - Alla virtuella datorer i en tillgänglighetsuppsättning      
 - Alla virtuella datorer i en placeringsgrupp för skaluppsättning.         

Därför bör du kontrollera `Resources` fältet i händelsen för att identifiera vilka virtuella datorer som kommer att påverkas. 

### <a name="starting-an-event"></a>Starta en händelse 

När du har fått reda på en kommande händelse och slutfört din logik för `POST` graciös avstängning, `EventId`kan du godkänna den utestående händelsen genom att ringa ett samtal till metadatatjänsten med . Detta indikerar för Azure att det kan förkorta den minsta meddelandetiden (när det är möjligt). 

Följande är json förväntas `POST` i begäran kroppen. Begäran bör innehålla en `StartRequests`lista över . Var `StartRequest` och `EventId` en innehåller för den händelse som du vill påskynda:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Om du bekräftar en händelse kan `Resources` händelsen fortsätta för alla i händelsen, inte bara den virtuella datorn som bekräftar händelsen. Du kan därför välja en ledare för att samordna bekräftelsen, som kan `Resources` vara lika enkel som den första maskinen i fältet.


## <a name="powershell-sample"></a>PowerShell-exempel 

Följande exempel frågar metadatatjänsten för schemalagda händelser och godkänner varje utestående händelse.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Nästa steg 

- Titta på en [schemalagd händelser Demo](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) på Azure fredag. 
- Granska kodexemplen för schemalagda händelser i [Azure Instance Metadata Scheduled Events GitHub Repository](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Läs mer om api:erna som är tillgängliga i [tjänsten Instansmetadata](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för virtuella Windows-datorer i Azure](planned-maintenance.md).
