---
title: Schemalagda händelser för Linux virtuella datorer i Azure | Microsoft Docs
description: Schemalägga händelser med hjälp av Azure Metadata Service för din virtuella Linux-datorer.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: c87dd5bdbdc87bf238f34d145bd7380a61b90cb6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Metadata Azure: Schemalagda händelser för virtuella Linux-datorer

Schemalagda händelser är en Azure Metadata som ger dina program tid att förbereda för underhåll av virtuell dator (VM). Den ger information om kommande underhållshändelser (till exempel omstart) så att programmet kan förbereda för dem och begränsa avbrott. Den är tillgänglig för alla typer av virtuella datorer i Azure, inklusive PaaS och IaaS i både Windows och Linux. 

Information om schemalagda händelser i Windows finns i [schemalagda händelser för virtuella Windows-datorer](../windows/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgänglig i alla Azure-regioner. Se [Version och regional tillgänglighet](#version-and-region-availability) senaste viktig information.

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

### <a name="endpoint-discovery"></a>Identifiering av slutpunkten
För virtuella nätverk aktiverat VMs Metadata Service är tillgänglig från en statisk nonroutable IP-adress, `169.254.169.254`. Fullständig slutpunkten för den senaste versionen av schemalagda händelser är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Om den virtuella datorn inte har skapat ett virtuellt nätverk, standard-fall för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adressen ska användas. Att lära dig hur du [identifiera värden slutpunkt](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), finns i det här exemplet.

### <a name="version-and-region-availability"></a>Version och regional tillgänglighet
Tjänsten schemalagda händelser skapas. Versioner är obligatoriska. den aktuella versionen är `2017-08-01`.

| Version | Versionstyp | Regioner | Viktig information | 
| - | - | - | - | 
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Ta bort inledd understreck från resursnamn för Iaas-VM<br><li>Metadata-huvud krav för alla begäranden | 
| 2017-03-01 | Förhandsversion | Alla | <li>Första utgåvan


> [!NOTE] 
> Tidigare förhandsvisningarna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att inaktualiseras i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera schemalagda händelser
Schemalagda händelser har aktiverats för din service första gången du gör en begäran för händelser. Du kan förvänta fördröjd svar i din första anropet av upp till två minuter.

Schemalagda händelser är inaktiverad för tjänsten om den inte gör en begäran i 24 timmar.

### <a name="user-initiated-maintenance"></a>Användarinitierad Underhåll
Användarinitierad VM Underhåll via Azure-portalen, API, CLI eller PowerShell resulterar i en schemalagd händelse. Du kan sedan testa Underhåll förberedelse av logiken i ditt program och programmet kan förbereda för användarinitierad underhåll.

Om du startar om en virtuell dator, en händelse med typen `Reboot` har schemalagts. Om du distribuerar en virtuell dator, en händelse med typen `Redeploy` har schemalagts.

## <a name="use-the-api"></a>Använd API

### <a name="headers"></a>Sidhuvuden
När du frågar Metadata Service måste du ange rubriken `Metadata:true` så begäran inte oavsiktligt omdirigeras. Den `Metadata:true` rubrik krävs för alla schemalagda händelser förfrågningar. Det gick inte att använda huvud i begäran resulterar i ett ”felaktig begäran” svar från Metadata Service.

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
| EventId | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Inverkan som gör att den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn kommer att pausa under några sekunder. Processorn är avbruten, men det finns ingen effekt på minne, öppna filer eller nätverksanslutningar. <li>`Reboot`: Den virtuella datorn är schemalagt för omstart. (Uppdateringsvärdet minne går förlorad.) <li>`Redeploy`: Den virtuella datorn kommer att flytta till en annan nod. (Tillfälliga diskar går förlorade.) |
| ResourceType | Typ av resurs som påverkar den här händelsen. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som påverkar den här händelsen. Listan innehåller datorerna från högst ett garanterat [uppdateringsdomän](manage-availability.md), men det kanske inte innehåller alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen har schemalagts att starta efter den tid som anges i den `NotBefore` egenskapen.<li>`Started`: Den här händelsen har startats.</ul> Inte `Completed` eller liknande status tillhandahålls någonsin. Händelsen returneras inte längre när händelsen är klar.
| Inte före| Tid som den här händelsen kan starta. <br><br> Exempel: <br><ul><li> Mån, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Schemaläggning av händelse
Varje händelse schemaläggs en minimal mängd tidpunkt i framtiden baserat på vilken typ av händelse. Nu visas i en händelse `NotBefore` egenskapen. 

|EventType  | Minsta meddelande |
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
- Titta på [schemalagda händelser på Azure fredag](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) att se en demonstration. 
- Granska schemalagda händelser kodexemplen i den [Azure instans Metadata schemalagda händelser Github-lagringsplatsen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Läs mer om de API: er som är tillgängliga i den [instans Metadata tjänsten](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för Linux virtuella datorer i Azure](planned-maintenance.md).
