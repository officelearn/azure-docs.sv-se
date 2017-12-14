---
title: "Schemalagda händelser för virtuella Windows-datorer i Azure | Microsoft Docs"
description: "Schemalagda händelser som använder tjänsten Azure Metadata för på Windows-datorer."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 75e811f77bade3701cce2d9945cf35d6e14e376f
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-windows-vms"></a>Metadata Azure: Schemalagda händelser (förhandsversion) för virtuella Windows-datorer

> [!NOTE] 
> Förhandsgranskningar görs tillgängliga för dig under förutsättning att du godkänner användningsvillkoren. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Schemalagda händelser är en Azure Metadata som ger dina program tid att förbereda för underhåll av virtuell dator. Den ger information om kommande underhållshändelser (t.ex. Starta om) så att programmet kan förbereda för dem och begränsa avbrott. Den är tillgänglig för alla typer av Azure virtuella datorer inklusive PaaS och IaaS i både Windows och Linux. 

Information om schemalagda händelser på Linux finns [schemalagda händelser för virtuella Linux-datorer](../linux/scheduled-events.md).

## <a name="why-scheduled-events"></a>Varför schemalagda händelser?

Många program kan dra nytta av tid att förbereda för underhåll av virtuell dator. Tid kan användas för att utföra specifika åtgärder i program som kan förbättra tillgänglighet, pålitlighet och brukbarheten inklusive: 

- Kontrollpunkt och återställning
- Anslutningen tömmer
- Primära repliken växling vid fel 
- Tas bort från belastningen pool för belastningsutjämnare
- Händelseloggning
- Korrekt avslutning 

Med hjälp av schemalagda händelser programmet kan identifiera när Underhåll ska ske och utlösa uppgifter för att begränsa dess konsekvenser.  

Schemalagda händelser ger händelser i följande användningsområden:
- Plattform som initierade Underhåll (t.ex. värden OS uppdatering)
- Användaren initierade Underhåll (t.ex. användare startar om eller återdistribuerar en virtuell dator)

## <a name="the-basics"></a>Grunderna  

Metadata i Azure-tjänsten visar information om att köra virtuella datorer med en REST-slutpunkt som är tillgänglig från den virtuella datorn. Informationen är tillgänglig via en icke-dirigerbara IP-adress så att inte exponeras utanför den virtuella datorn.

### <a name="scope"></a>Omfång
Schemalagda händelser levereras till:
- Alla virtuella datorer i en tjänst i molnet
- Alla virtuella datorer i en Tillgänglighetsuppsättning
- Alla virtuella datorer på en skala Ange placering av grupp. 

Därför bör du kontrollera den `Resources` i händelsen för att identifiera vilka virtuella datorer kommer att påverkas. 

## <a name="discovering-the-endpoint"></a>Identifiering av slutpunkten
För virtuella nätverk aktiverat virtuella datorer är fullständig slutpunkten för den senaste versionen av schemalagda händelser: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

I de fall där en virtuell dator skapas ett virtuellt nätverk (VNet), metadatatjänsten är tillgänglig från en statisk icke-dirigerbara IP `169.254.169.254`.
Om den virtuella datorn inte har skapat ett virtuellt nätverk, standard-fall för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adressen ska användas. Referera till det här exemplet att lära dig hur du [identifiera värden slutpunkt](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Versionshantering 
Tjänsten schemalagda händelser skapas. Versioner är obligatoriska och den aktuella versionen är `2017-08-01`.

| Version | Viktig information | 
| - | - | 
| 2017-08-01 | <li> Ta bort inledd understreck från resursnamn för Iaas-VM<br><li>Metadatarubrik krav för alla begäranden | 
| 2017-03-01 | <li>Offentliga förhandsversionen

> [!NOTE] 
> Tidigare förhandsvisningarna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att inaktualiseras i framtiden.

### <a name="using-headers"></a>Med hjälp av rubriker
När du frågar Metadata Service måste du ange rubriken `Metadata:true` så begäran inte omdirigerades oavsiktligt. Den `Metadata:true` rubrik krävs för alla schemalagda händelser förfrågningar. Det gick inte att använda huvud i begäran resulterar i en felaktig begäran-svar från Metadata Service.

### <a name="enabling-scheduled-events"></a>Aktivera schemalagd händelser
Första gången du skapar en begäran om schemalagda händelser aktiverar Azure implicit funktionen på den virtuella datorn. Därför bör du förväntar dig en fördröjd svar i din första anropet av upp till två minuter.

> [!NOTE]
> Schemalagda händelser inaktiveras automatiskt för din tjänst om tjänsten inte anropa slutpunkten för 1 dag. När schemalagda händelser är inaktiverat för tjänsten, finns inga händelser som skapats för användarinitierad underhåll.

### <a name="user-initiated-maintenance"></a>Användarinitierad Underhåll
Användaren initierade underhålla virtuella datorer via Azure portal, API, CLI eller PowerShell resulterar i en schemalagd händelse. Det kan du testa Underhåll förberedelse av logiken i ditt program och att ditt program att förbereda för användarinitierad underhåll.

Starta om en virtuell dator schemalägger en händelse med typen `Reboot`. Omdistribuera en virtuell dator schemalägger en händelse med typen `Redeploy`.

> [!NOTE] 
> För närvarande kan högst 100 användarinitierad underhållsåtgärder samtidigt schemaläggas.

> [!NOTE] 
> Användarinitierad Underhåll ledde schemalagda händelser kan för närvarande inte konfigureras. Konfigurationsmöjligheter är planerad för framtida versioner.

## <a name="using-the-api"></a>Med hjälp av API

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att göra följande anrop:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
```

Svaret innehåller en matris med schemalagda händelser. En tom matris innebär att det inte finns för närvarande inga händelser som är schemalagda.
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

### <a name="event-properties"></a>Egenskaper för händelse
|Egenskap  |  Beskrivning |
| - | - |
| Händelse-ID | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Händelsetyp | Inverkan som gör att den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn kommer att pausa under några sekunder. Processorn har pausats, men det finns ingen inverkan på minnet, öppna filer eller nätverksanslutningar. <li>`Reboot`: Den virtuella datorn är schemalagt för omstart (icke-beständig minne är förlorade). <li>`Redeploy`: Den virtuella datorn kommer att flytta till en annan nod (tillfälliga diskar går förlorade). |
| ResourceType | Typ av resurs som påverkar den här händelsen. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som påverkar den här händelsen. Detta är säkert att innehålla datorer från högst ett [Uppdateringsdomän](manage-availability.md), men får inte innehålla alla datorer i UD. <br><br> Exempel: <br><ul><li> [”FrontEnd_IN_0”, ”BackEnd_IN_0”] |
| Händelsestatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen har schemalagts att starta efter den tid som anges i den `NotBefore` egenskapen.<li>`Started`: Den här händelsen har startats.</ul> Inte `Completed` eller liknande status tillhandahålls någonsin; händelsen inte längre kommer att returneras när händelsen har slutförts.
| Inte före| Tid som den här händelsen kan starta. <br><br> Exempel: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Schemaläggning av händelse
Varje händelse schemaläggs en minimal mängd tidpunkt i framtiden baserat på händelsetyp. Nu visas i en händelse `NotBefore` egenskapen. 

|Händelsetyp  | Minsta meddelande |
| - | - |
| Lås| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |

### <a name="starting-an-event"></a>Starta en händelse 

När du har lärt dig i en kommande händelse och slutföra din logik för att korrekt avslutning, kan du godkänna utestående händelsen genom att göra en `POST` anrop till metadatatjänsten med i `EventId`. Detta anger att Azure att det korta meddelandet minsta (när det är möjligt). 

Följande är json förväntas i den `POST` begäran. Begäran ska innehålla en lista över `StartRequests`. Varje `StartRequest` innehåller den `EventId` för händelsen som du vill påskynda:
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
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Bekräfta en händelse kan händelsen för att fortsätta för alla `Resources` i händelseloggen, inte bara den virtuella datorn som om händelsen. Därför kan du välja att välja ledande koordinera bekräftelse, vilket kan vara så enkelt som den första datorn i den `Resources` fältet.


## <a name="powershell-sample"></a>PowerShell-exempel 

I följande exempel frågar metadatatjänsten för schemalagda händelser och godkänner alla utestående händelser.

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
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
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
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

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
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Nästa steg 

- Granska schemalagda händelser kodexemplen i den [Azure instans Metadata schemalagda händelser Github-lagringsplatsen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- Läs mer om API: er finns i den [instans Metadata tjänsten](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för Windows-datorer i Azure](planned-maintenance.md).
