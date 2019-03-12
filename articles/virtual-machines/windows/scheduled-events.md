---
title: Schemalagda händelser för Windows-datorer i Azure | Microsoft Docs
description: Schemalagda händelser med hjälp av tjänsten Azure Metadata för på din Windows-datorer.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: c9bd14128a6874f06983aa99ebb5a8a9a85843a2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550699"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Schemalagda händelser för virtuella Windows-datorer

Scheduled Events är en Azure Metadata Service som ger programmet tiden till att förbereda för VM-underhåll. Hittar du information om kommande Underhåll (t.ex. en omstart) så att ditt program kan förbereda för dem och begränsa avbrott. Det är tillgängligt för alla typer av Azure virtuella datorer, inklusive PaaS och IaaS på både Windows och Linux. 

Information om Scheduled Events i Linux finns i [schemalagda händelser för virtuella Linux-datorer](../linux/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgänglig i alla Azure-regioner. Se [Version och Regiontillgänglighet](#version-and-region-availability) senaste viktig information.

## <a name="why-scheduled-events"></a>Varför schemalagda händelser?

Många program kan dra nytta av tid för att förbereda för VM-underhåll. Tiden kan användas för att utföra specifika uppgifter för programmet som förbättrar tillgänglighet, pålitlighet och brukbarheten, inklusive: 

- Kontrollpunkt och återställning
- Anslutningstömning
- Primära replikeringsredundans 
- Tas bort från belastningsutjämningstrafik
- Händelseloggning
- Avslutning 

Med schemalagda händelser ditt program kan identifiera när Underhåll ska inträffa och utlösa uppgifter för att begränsa dess inverkan. Aktivera schemalagda händelser ger din virtuella dator en minimal mängd tid innan aktiviteten underhåll utförs. Se avsnittet händelse schemaläggning nedan för information.

Schemalagda händelser innehåller händelser i följande användningsfall:
- Plattform som initierade Underhåll (t.ex. värden OS uppdatering)
- Användarinitierad Underhåll (t.ex. användare startar om eller distribuerar om en virtuell dator)

## <a name="the-basics"></a>Grunderna  

Azure Metadata service visar information om att köra virtuella datorer med en REST-slutpunkt som är tillgängliga inifrån den virtuella datorn. Informationen är tillgänglig via en icke-dirigerbara IP-adress så att den inte är exponerad utanför den virtuella datorn.

### <a name="endpoint-discovery"></a>Slutpunktsidentifiering
För VNET-aktiverade virtuella datorer, metadatatjänsten är tillgänglig från en statisk icke-dirigerbara IP `169.254.169.254`. Fullständig slutpunkten för den senaste versionen av Scheduled Events är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Om den virtuella datorn inte har skapats i ett virtuellt nätverk, standard-fall för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adress som ska användas. Referera till det här exemplet vill lära dig hur du [identifiera värd-slutpunkt](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Version och Regiontillgänglighet
Tjänsten schemalagda händelser skapas. Versioner är obligatoriska och den aktuella versionen är `2017-08-01`.

| Version | Versionstyp | Regioner | Viktig information | 
| - | - | - | - |
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Bort föregås av ett anpassningsprefix understreck från resursnamn för virtuella IaaS-datorer<br><li>Metadatarubrik krav som gäller för alla begäranden | 
| 2017-03-01 | Förhandsversion | Alla |<li>Första utgåvan

> [!NOTE] 
> Föregående förhandsversionerna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att bli inaktuella i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera schemalagda händelser
Schemalagda händelser är aktiverat för din tjänst först gången du gör en begäran om händelser. Du bör förvänta sig något fördröjda svar i ditt första anrop på upp till två minuter. Du bör fråga slutpunkten med jämna mellanrum för att identifiera kommande underhållshändelser, samt status för underhållsaktiviteter som utförs.

Schemalagda händelser är inaktiverad för tjänsten om den inte gör en begäran i 24 timmar.

### <a name="user-initiated-maintenance"></a>Användarinitierad Underhåll
Användarinitierad underhålla virtuella datorer via Azure-portalen, API, CLI eller PowerShell resulterar i en schemalagd händelse. Detta kan du testa Underhåll förberedelse av logiken i ditt program och tillåter programmet att förbereda för Användarinitierat underhåll.

Starta om en virtuell dator schemalägger en händelse med typen `Reboot`. Omdistribuera en virtuell dator schemalägger en händelse med typen `Redeploy`.

## <a name="using-the-api"></a>Med hjälp av API

### <a name="headers"></a>Rubriker
När du frågar Metadata Service måste du ange rubriken `Metadata:true` att se till att begäran inte har omdirigerats oavsiktligt. Den `Metadata:true` rubrik krävs för alla schemalagda händelser förfrågningar. Det gick inte att använda huvud i begäran resulterar i en felaktig begäran-svar från Metadata Service.

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att göra följande anrop:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Svaret innehåller en matris med schemalagda händelser. En tom matris innebär att det finns för närvarande inga schemalagda händelser.
I fall där det finns schemalagda händelser, svaret innehåller en matris med händelser: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation är en ETag och ger ett enkelt sätt att kontrollera om händelser nyttolasten har ändrats sedan den senaste frågan.

### <a name="event-properties"></a>Egenskaper för händelse
|Egenskap   |  Beskrivning |
| - | - |
| EventId | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Händelsetyp | Påverkan som gör att den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn är schemalagd att pausa under några sekunder. Processorn är inaktiverad, men det finns ingen inverkan på minne, öppna filer eller nätverksanslutningar. <li>`Reboot`: Den virtuella datorn är schemalagd för omstart (icke-beständiga minne är förlorad). <li>`Redeploy`: Den virtuella datorn kommer att flytta till en annan nod (differentierande diskar förloras). |
| ResourceType | Typ av resurs som påverkar den här händelsen. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som påverkar den här händelsen. Detta garanterar att de innehåller datorerna från högst ett [Uppdateringsdomän](manage-availability.md), men får inte innehålla alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Händelsestatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen är schemalagd att starta efter den tid som anges i den `NotBefore` egenskapen.<li>`Started`: Den här händelsen har startats.</ul> Inte `Completed` eller liknande status någonsin har angetts, kommer inte längre att returneras händelsen när händelsen har slutförts.
| NotBefore| Tid efter vilken den här händelsen startar. <br><br> Exempel: <br><ul><li> Mån, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Schemaläggning av händelse
Varje händelse schemaläggs en minimal mängd tidpunkt i framtiden utifrån händelsetyp. Nu visas i en händelse `NotBefore` egenskapen. 

|Händelsetyp  | Minsta meddelande |
| - | - |
| Lås| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |

### <a name="event-scope"></a>Händelsen omfång     
Schemalagda händelser levereras till:        
 - Alla virtuella datorer i en molntjänst      
 - Alla virtuella datorer i en Tillgänglighetsuppsättning      
 - Alla virtuella datorer i en Skalningsuppsättning ange Placeringsgrupp.         

Därför ska du kontrollera den `Resources` i händelsen för att identifiera vilka virtuella datorer kommer att påverkas. 

### <a name="starting-an-event"></a>Starta en händelse 

När du har lärt dig för ett kommande evenemang och slutfört din logik för avslutning, kan du godkänna utestående händelsen genom att göra en `POST` anrop till metadatatjänsten med den `EventId`. Detta upplyser Azure att den kan förkorta minsta meddelandet (när det är möjligt). 

Följande är den json som förväntas i den `POST` brödtext i begäran. Begäran bör innehålla en lista över `StartRequests`. Varje `StartRequest` innehåller den `EventId` för händelsen som du vill påskynda:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Kanske uttrycka en händelse kan händelsen att gå vidare för alla `Resources` i händelseloggen, inte bara den virtuella datorn som om händelsen. Du kan därför välja att välja en ledare att samordna bekräftelse, vilket kan vara lika enkelt som att den första datorn i den `Resources` fält.


## <a name="powershell-sample"></a>PowerShell-exempel 

I följande exempel frågar metadatatjänsten för schemalagda händelser och godkänner varje utestående händelse.

```PowerShell
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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-08-01' -f $localHostIP 

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

- Titta på en [schemalagda händelser Demo](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) på Azure Friday. 
- Granska Scheduled Events-kodexempel i den [Azure instans Metadata schemalagda händelser GitHub-lagringsplats](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Läs mer om API: erna i den [Instance Metadata service](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för Windows-datorer i Azure](planned-maintenance.md).
