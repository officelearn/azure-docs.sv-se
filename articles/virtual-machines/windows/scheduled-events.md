---
title: Schemalagda händelser för virtuella Windows-datorer i Azure
description: Schemalagda händelser med Azure-metadatatjänsten för på dina virtuella Windows-datorer.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 107233248e5d0a8d6b578d9395d4cdbade79a842
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772620"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure-Metadata Service: Schemalagda händelser för virtuella Windows-datorer

Schemalagda händelser är en Azure-Metadata Service som ger din program tid att förbereda för underhåll av virtuella datorer. Den innehåller information om kommande underhålls händelser (t. ex. omstart) så att ditt program kan förbereda sig för dem och begränsa avbrott. Den är tillgänglig för alla typer av virtuella Azure-datorer, inklusive PaaS och IaaS på både Windows och Linux. 

Information om Schemalagda händelser i Linux finns [schemalagda händelser för virtuella Linux-datorer](../linux/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgänglig i alla Azure-regioner. Se [version och region tillgänglighet](#version-and-region-availability) för senaste versions information.

## <a name="why-scheduled-events"></a>Varför Schemalagda händelser?

Många program kan dra nytta av tid för att förbereda för underhåll av virtuella datorer. Tiden kan användas för att utföra programspecifika uppgifter som förbättrar tillgängligheten, tillförlitligheten och service möjligheterna, inklusive: 

- Kontroll punkt och återställning
- Anslutningstömning
- Redundansväxling av primär replik 
- Borttagning från belastnings Utjämnings poolen
- Händelse loggning
- Korrekt avstängning 

Användning av Schemalagda händelser ditt program kan upptäcka när underhåll sker och utlösa aktiviteter för att begränsa dess påverkan. Om du aktiverar schemalagda händelser får den virtuella datorn en minimal tid innan underhålls aktiviteten utförs. Mer information finns i avsnittet händelse schemaläggning nedan.

Schemalagda händelser innehåller händelser i följande användnings fall:
- [Plattform initierat underhåll](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (till exempel VM-omstart, direktmigrering eller minnes konserverings uppdateringar för värd)
- Degraderad maskin vara
- Användarens initierade underhåll (t. ex. att användaren startar om eller distribuerar om en virtuell dator)
- Borttagning av instans av [virtuella datorer](spot-vms.md) och [dekor skalnings uppsättningar](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Grunderna  

Azure-metadatatjänsten visar information om att köra Virtual Machines att använda en REST-slutpunkt som är tillgänglig från den virtuella datorn. Informationen är tillgänglig via en icke-dirigerad IP-adress så att den inte exponeras utanför den virtuella datorn.

### <a name="endpoint-discovery"></a>Slut punkts identifiering
För VNET-aktiverade virtuella datorer är metadatatjänsten tillgänglig från en statisk icke-dirigerbart IP-adress `169.254.169.254`. Den fullständiga slut punkten för den senaste versionen av Schemalagda händelser är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Om den virtuella datorn inte har skapats inom en Virtual Network, krävs standard fall för moln tjänster och klassiska virtuella datorer, men ytterligare logik krävs för att identifiera IP-adressen som ska användas. Se det här exemplet för att lära dig hur du [identifierar värd slut punkten](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Tillgänglighet för version och region
Den Schemalagda händelser tjänsten har versions hantering. Versioner är obligatoriska och den aktuella versionen är `2017-11-01`.

| Version | Versions typ | Regioner | Viktig information | 
| - | - | - | - |
| 2017-11-01 | Allmän tillgänglighet | Alla | <li> Stöd har lagts till för VM-utavlägsning av händelse-Preempt för VM<br> | 
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Tog bort anpassningsprefix-understreck från resurs namn för virtuella IaaS-datorer<br><li>Krav för metadata-huvud tillämpas för alla begär Anden | 
| 2017-03-01 | Förhandsversion | Alla |<li>Första utgåvan

> [!NOTE] 
> Tidigare för hands versioner av schemalagda händelser som stöds {senaste} som API-version. Det här formatet stöds inte längre och kommer att bli inaktuellt i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera Schemalagda händelser
Schemalagda händelser aktive ras för din tjänst första gången du gör en begäran om händelser. Du bör förvänta dig ett fördröjt svar i ditt första anrop på upp till två minuter. Du bör fråga slut punkten regelbundet för att identifiera kommande underhålls händelser samt status för underhålls aktiviteter som utförs.

Schemalagda händelser har inaktiverats för tjänsten om den inte gör en begäran i 24 timmar.

### <a name="user-initiated-maintenance"></a>Användarinitierad underhåll
Användaren initierade underhåll av virtuella datorer via Azure Portal, API, CLI eller PowerShell resulterar i en schemalagd händelse. På så sätt kan du testa underhålls förberedelse logiken i programmet och göra det möjligt för ditt program att förbereda för att användaren har initierat underhåll.

När en virtuell dator startas om schemaläggs en händelse av typen `Reboot`. När du distribuerar en virtuell dator schemaläggs en händelse av typen `Redeploy`.

## <a name="using-the-api"></a>Använda API: et

### <a name="headers"></a>Rubriker
När du frågar Metadata Service måste du ange huvud `Metadata:true` för att se till att begäran inte oavsiktligt omdirigeras. `Metadata:true`s huvudet krävs för alla begär Anden om schemalagda händelser. Om du inte tar med rubriken i begäran får du ett felaktigt svar från begäran från Metadata Service.

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter Schemalagda händelser enkelt genom att göra följande anrop:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Ett svar innehåller en matris med schemalagda händelser. En tom matris innebär att det inte finns några schemalagda händelser för tillfället.
Om det finns schemalagda händelser innehåller svaret en händelse mat ris: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation är en ETag och ger ett enkelt sätt att kontrol lera om händelse nytto lasten har ändrats sedan den senaste frågan.

### <a name="event-properties"></a>Händelse egenskaper
|Egenskap  |  Beskrivning |
| - | - |
| EventId | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Typ | Påverkar den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: den virtuella datorn är schemalagd att pausas några sekunder. CPU-och nätverks anslutningen kan vara pausad, men det finns ingen inverkan på minnet eller öppna filer. <li>`Reboot`: den virtuella datorn är schemalagd för omstart (icke-beständigt minne går förlorad). <li>`Redeploy`: den virtuella datorn är schemalagd att flyttas till en annan nod (tillfälliga diskar går förlorade). <li>`Preempt`: den virtuella datorn håller på att tas bort (tillfälliga diskar går förlorade).|
| ResourceType | Typ av resurs som den här händelsen påverkar. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som den här händelsen påverkar. Detta är garanterat att innehålla datorer från högst en [uppdaterings domän](manage-availability.md), men de får inte innehålla alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Händelse status | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: den här händelsen är schemalagd att starta efter den tid som anges i egenskapen `NotBefore`.<li>`Started`: den här händelsen har startats.</ul> Ingen `Completed` eller liknande status har tillhandahållits. händelsen kommer inte längre att returneras när händelsen har slutförts.
| NotBefore| Tid när händelsen kan starta. <br><br> Exempel: <br><ul><li> Mån, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Händelse schemaläggning
Varje händelse schemaläggs en minimi period i framtiden baserat på händelse typ. Den här tiden visas i en händelses `NotBefore` egenskap. 

|Typ  | Minsta meddelande |
| - | - |
| Tina| 15 minuter |
| Omstart | 15 minuter |
| Omdistribuera | 10 minuter |
| Utfärdas | 30 sekunder |

### <a name="event-scope"></a>Händelse omfång     
Schemalagda händelser levereras till:
 - Fristående Virtual Machines
 - Alla Virtual Machines i en moln tjänst      
 - Alla Virtual Machines i en tillgänglighets uppsättning      
 - Alla Virtual Machines i en placerings grupp för skalnings uppsättningar.         

Därför bör du kontrol lera `Resources` fältet i händelsen för att identifiera vilka virtuella datorer som ska påverkas. 

### <a name="starting-an-event"></a>Starta en händelse 

När du har lärt dig en kommande händelse och slutfört din logik för en korrekt avstängning, kan du godkänna den utestående händelsen genom att göra ett `POST` anrop till metadata-tjänsten med `EventId`. Detta visar att Azure kan förkorta den minsta meddelande tiden (om möjligt). 

Följande är den JSON som förväntas i `POST` begär ande texten. Begäran bör innehålla en lista över `StartRequests`. Varje `StartRequest` innehåller `EventId` för den händelse som du vill påskynda:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Genom att bekräfta en händelse kan händelsen fortsätta för alla `Resources` i händelsen, inte bara den virtuella dator som bekräftar händelsen. Du kan därför välja att välja en ledare för att samordna bekräftelsen, som kan vara så enkel som den första datorn i fältet `Resources`.


## <a name="powershell-sample"></a>PowerShell-exempel 

I följande exempel frågar metadata-tjänsten efter schemalagda händelser och godkänner varje utestående händelse.

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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

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

- Titta på en [schemalagda händelser demo](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) på Azure fredagen. 
- Granska Schemalagda händelser kod exempel i [Azure instance Metadata schemalagda händelser GitHub-lagringsplatsen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Läs mer om de API: er som är tillgängliga i [instansen metadata service](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för virtuella Windows-datorer i Azure](planned-maintenance.md).
