---
title: "Schemalagda händelser för Linux virtuella datorer i Azure | Microsoft Docs"
description: "Schemalägga händelser med hjälp av Azure Metadata Service för din virtuella Linux-datorer."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: ae9955253647f3277729e7905baf7bb07645de42
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2018
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure Metadata Service: Schemalagda händelser (förhandsversion) för virtuella Linux-datorer

> [!NOTE] 
> Förhandsgranskningar görs tillgängliga för dig under förutsättning att du godkänner användningsvillkoren. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Schemalagda händelser är en subservice under Azure Metadata tjänst som ger dina program tid att förbereda för underhåll av virtuell dator (VM). Den ger information om kommande underhållshändelser (till exempel omstart) så att programmet kan förbereda för dem och begränsa avbrott. Den är tillgänglig för alla typer av virtuella datorer i Azure, inklusive PaaS och IaaS i både Windows och Linux. 

Information om schemalagda händelser i Windows finns i [schemalagda händelser för virtuella Windows-datorer](../windows/scheduled-events.md).

## <a name="why-use-scheduled-events"></a>Varför använda schemalagda händelser?

Många program kan dra nytta av tid att förbereda för VM-underhåll. Tid som kan användas för att utföra programspecifika uppgifter som förbättra tillgänglighet, pålitlighet och brukbarheten, inklusive: 

- Kontrollpunkt och återställning.
- Tömmer anslutning.
- Primära replikeringsredundans.
- Tas bort från en pool för belastningsutjämnare belastningen.
- Händelseloggning.
- Korrekt avslutning.

Programmet kan identifiera när Underhåll ska ske och utlösa uppgifter för att begränsa dess inverkan med schemalagda händelser.  

Schemalagda händelser ger händelser i följande användningsområden:

- Plattform-initierad Underhåll (till exempel en uppdatering för värdens operativsystem)
- Användarinitierad Underhåll (till exempel en användare startar om eller återdistribuerar en virtuell dator)

## <a name="the-basics"></a>Grunderna  

  Metadata Service visar information om virtuella datorer som körs med hjälp av en REST-slutpunkt som är tillgänglig från den virtuella datorn. Informationen är tillgänglig via en nonroutable IP-adress så att inte exponeras utanför den virtuella datorn.

### <a name="scope"></a>Omfång
Schemalagda händelser levereras till:

- Alla de virtuella datorerna i en tjänst i molnet.
- Alla de virtuella datorerna i en tillgänglighetsuppsättning.
- Alla virtuella datorer i en skala Ange placering av grupp. 

Kontrollera därför den `Resources` i händelsen för att identifiera vilka virtuella datorer som påverkas.

### <a name="discover-the-endpoint"></a>Identifiera slutpunkten
För virtuella datorer som är aktiverade för virtuella nätverk, är fullständig slutpunkten för den senaste versionen av schemalagda händelser: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

I de fall där en virtuell dator skapas ett virtuellt nätverk, Metadata Service är tillgänglig från en statisk nonroutable IP-adress, `169.254.169.254`.
Om den virtuella datorn inte har skapat ett virtuellt nätverk, standard-fall för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adressen ska användas. Att lära dig hur du [identifiera värden slutpunkt](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), finns i det här exemplet.

### <a name="versioning"></a>Versionshantering 
Tjänsten schemalagda händelser skapas. Versioner är obligatoriska och den aktuella versionen är `2017-08-01`.

| Version | Viktig information | 
| - | - | 
| 2017-08-01 | <li> Ta bort inledd understreck från resursnamn för Iaas-VM<br><li>Metadata-huvud krav för alla begäranden | 
| 2017-03-01 | <li>Offentliga förhandsversionen


> [!NOTE] 
> Tidigare förhandsvisningarna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att inaktualiseras i framtiden.

### <a name="use-headers"></a>Använd rubriker
När du frågar Metadata Service måste du ange rubriken `Metadata:true` så begäran inte oavsiktligt omdirigeras. Den `Metadata:true` rubrik krävs för alla schemalagda händelser förfrågningar. Det gick inte att använda huvud i begäran resulterar i ett ”felaktig begäran” svar från Metadata Service.

### <a name="enable-scheduled-events"></a>Aktivera schemalagd händelser
Första gången du skapar en begäran om schemalagda händelser aktiverar Azure implicit funktionen på den virtuella datorn. Därför förvänta dig en fördröjd svar i din första anropet av upp till två minuter.

> [!NOTE]
> Schemalagda händelser inaktiveras automatiskt för din tjänst om tjänsten inte anropa slutpunkten för en dag. När schemalagda händelser har inaktiverats för din tjänst, skapas inga händelser för användarinitierad underhåll.

### <a name="user-initiated-maintenance"></a>Användarinitierad Underhåll
Användarinitierad VM Underhåll via Azure-portalen, API, CLI eller PowerShell resulterar i en schemalagd händelse. Du kan sedan testa Underhåll förberedelse av logiken i ditt program och programmet kan förbereda för användarinitierad underhåll.

Om du startar om en virtuell dator, en händelse med typen `Reboot` har schemalagts. Om du distribuerar en virtuell dator, en händelse med typen `Redeploy` har schemalagts.

> [!NOTE] 
> För närvarande kan högst 100 användarinitierad underhållsåtgärder samtidigt schemaläggas.

> [!NOTE] 
> Användarinitierad underhåll som resulterar i schemalagda händelser kan för närvarande inte konfigureras. Konfigurationsmöjligheter är planerad för framtida versioner.

## <a name="use-the-api"></a>Använd API

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att göra följande anrop:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Svaret innehåller en matris med schemalagda händelser. En tom matris innebär att inga händelser är schemalagd.
I fall där det finns schemalagda händelser, svaret innehåller en matris av händelser. 
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
| Händelsetyp | Inverkan som gör att den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn kommer att pausa under några sekunder. Processorn är avbruten, men det finns ingen effekt på minne, öppna filer eller nätverksanslutningar. <li>`Reboot`: Den virtuella datorn är schemalagt för omstart. (Uppdateringsvärdet minne går förlorad.) <li>`Redeploy`: Den virtuella datorn kommer att flytta till en annan nod. (Tillfälliga diskar går förlorade.) |
| ResourceType | Typ av resurs som påverkar den här händelsen. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som påverkar den här händelsen. Listan innehåller datorerna från högst ett garanterat [uppdateringsdomän](manage-availability.md), men det kanske inte innehåller alla datorer i UD. <br><br> Exempel: <br><ul><li> [”FrontEnd_IN_0”, ”BackEnd_IN_0”] |
| EventStatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen har schemalagts att starta efter den tid som anges i den `NotBefore` egenskapen.<li>`Started`: Den här händelsen har startats.</ul> Inte `Completed` eller liknande status tillhandahålls någonsin. Händelsen returneras inte längre när händelsen är klar.
| Inte före| Tid som den här händelsen kan starta. <br><br> Exempel: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Schemaläggning av händelse
Varje händelse schemaläggs en minimal mängd tidpunkt i framtiden baserat på vilken typ av händelse. Nu visas i en händelse `NotBefore` egenskapen. 

|Händelsetyp  | Minsta meddelande |
| - | - |
| Lås| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |

### <a name="start-an-event"></a>Starta en händelse 

När du lär dig i en kommande händelse och slutför din logik för att korrekt avslutning, kan du godkänna utestående händelsen genom att göra en `POST` anropa Metadata Service med `EventId`. Anger det här anropet till Azure att det korta meddelandet minsta (när det är möjligt). 

I följande exempel JSON förväntas i den `POST` begäran. Begäran ska innehålla en lista över `StartRequests`. Varje `StartRequest` innehåller `EventId` för händelsen som du vill påskynda:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash-exempel
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Bekräfta en händelse kan händelsen för att fortsätta för alla `Resources` i händelseloggen, inte bara den virtuella datorn som om händelsen. Därför kan du välja en ledande koordinera bekräftelse, som kan vara så enkelt som den första datorn i den `Resources` fältet.

## <a name="python-sample"></a>Python-exempel 

I följande exempel frågar Metadata Service för schemalagda händelser och godkänner varje utestående händelse:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Nästa steg 
- Granska schemalagda händelser kodexemplen i den [Azure instans Metadata schemalagda händelser Github-lagringsplatsen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Läs mer om de API: er som är tillgängliga i den [instans Metadata tjänsten](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för Linux virtuella datorer i Azure](planned-maintenance.md).
