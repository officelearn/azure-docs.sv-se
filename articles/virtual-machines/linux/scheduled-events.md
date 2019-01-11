---
title: Schemalagda händelser för virtuella Linux-datorer i Azure | Microsoft Docs
description: Schemalägga händelser med hjälp av Azure Metadata Service för Linux-datorer.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
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
ms.openlocfilehash: df7f3dfa525c59ff8862c3b1a46f70be53a93a32
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198753"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Schemalagda händelser för virtuella Linux-datorer

Scheduled Events är en Azure Metadata Service som ger programmet tiden till att förbereda för underhåll av virtuell dator (VM). Det ger information om kommande underhållshändelser (till exempel en omstart) så att ditt program kan förbereda för dem och begränsa avbrott. Det är tillgängligt för alla typer av virtuella datorer i Azure, inklusive PaaS och IaaS på både Windows och Linux. 

Information om Scheduled Events i Windows finns i [schemalagda händelser för Windows virtuella datorer](../windows/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgänglig i alla Azure-regioner. Se [Version och Regiontillgänglighet](#version-and-region-availability) senaste viktig information.

## <a name="why-use-scheduled-events"></a>Varför använda schemalagda händelser?

Många program kan dra nytta av tid för att förbereda för underhåll av virtuell dator. Tid som kan användas för att utföra programspecifika uppgifter som att förbättra tillgänglighet, pålitlighet och brukbarheten, inklusive: 

- Kontrollpunkt och återställning.
- Anslutningstömning.
- Primära replikeringsredundans.
- Tas bort från poolen load balancer.
- Händelseloggning.
- Avslutning.

Programmet kan identifiera när Underhåll ska inträffa och utlösa uppgifter för att begränsa dess inverkan med schemalagda händelser.  

Schemalagda händelser innehåller händelser i följande användningsfall:

- Plattform-kundinitierat Underhåll (till exempel en uppdatering för värdens operativsystem)
- Användarinitierat Underhåll (till exempel en användare startar om eller distribuerar om en virtuell dator)

## <a name="the-basics"></a>Grunderna  

  Metadata Service visar information om att köra virtuella datorer med hjälp av en REST-slutpunkt som kan nås från den virtuella datorn. Informationen är tillgänglig via en nonroutable IP-adress så att den inte är exponerad utanför den virtuella datorn.

### <a name="scope"></a>Scope
Schemalagda händelser levereras till:

- Alla virtuella datorer i en molntjänst.
- Alla virtuella datorer i en tillgänglighetsuppsättning.
- Alla virtuella datorer i en skalningsuppsättning ange placeringsgrupp. 

Därför kan kontrollera den `Resources` i händelsen för att identifiera vilka virtuella datorer som påverkas.

### <a name="endpoint-discovery"></a>Slutpunktsidentifiering
För VNET-aktiverade virtuella datorer, Metadata Service är tillgänglig från en statisk nonroutable IP-adress, `169.254.169.254`. Fullständig slutpunkten för den senaste versionen av Scheduled Events är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Om den virtuella datorn inte har skapats i ett virtuellt nätverk, standard-fall för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adress som ska användas. Att lära dig hur du [identifiera värd-slutpunkt](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), finns i det här exemplet.

### <a name="version-and-region-availability"></a>Version och Regiontillgänglighet
Tjänsten Scheduled Events är en ny version. Versioner är obligatoriska. den aktuella versionen är `2017-08-01`.

| Version | Versionstyp | Regioner | Viktig information | 
| - | - | - | - | 
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Bort föregås av ett anpassningsprefix understreck från resursnamn för virtuella IaaS-datorer<br><li>Metadata-huvud kravet gäller för alla begäranden | 
| 2017-03-01 | Förhandsversion | Alla | <li>Första utgåvan


> [!NOTE] 
> Föregående förhandsversionerna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att bli inaktuella i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera schemalagda händelser
Schemalagda händelser är aktiverat för din tjänst först gången du gör en begäran om händelser. Du bör förvänta sig något fördröjda svar i ditt första anrop på upp till två minuter.

Schemalagda händelser är inaktiverad för tjänsten om den inte gör en begäran i 24 timmar.

### <a name="user-initiated-maintenance"></a>Användarinitierat Underhåll
Användarinitierat underhåll av virtuell dator via Azure-portalen, API, CLI eller PowerShell resulterar i en schemalagd händelse. Du kan sedan testa Underhåll förberedelse av logiken i ditt program och ditt program kan förbereda för användarinitierad underhåll.

Om du startar om en virtuell dator, en händelse med typen `Reboot` har schemalagts. Om du distribuerar om en virtuell dator, en händelse med typen `Redeploy` har schemalagts.

## <a name="use-the-api"></a>Använda API: et

### <a name="headers"></a>Rubriker
När du frågar Metadata Service måste du ange rubriken `Metadata:true` att se till att begäran inte oavsiktligt omdirigeras. Den `Metadata:true` rubrik krävs för alla schemalagda händelser förfrågningar. Det gick inte att använda huvud i begäran resulterar i ett ”felaktig begäran”-svar från Metadata Service.

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att göra följande anrop:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Svaret innehåller en matris med schemalagda händelser. En tom matris innebär att för närvarande inga händelser schemaläggs.
I fall där det finns schemalagda händelser, svaret innehåller en matris med händelser. 
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
|Egenskap   |  Beskrivning |
| - | - |
| EventId | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Händelsetyp | Påverkan som gör att den här händelsen. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn är schemalagd att pausa under några sekunder. Processorn är inaktiverad, men det finns ingen effekt på minne, öppna filer och nätverksanslutningar. <li>`Reboot`: Den virtuella datorn är schemalagd för omstart. (Ickebeständig minne förloras.) <li>`Redeploy`: Den virtuella datorn kommer att flytta till en annan nod. (Differentierande diskar går förlorade.) |
| ResourceType | Typ av resurs som påverkar den här händelsen. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som påverkar den här händelsen. Listan kommer att innehålla datorer från högst ett [uppdateringsdomän](manage-availability.md), men det kanske inte innehåller alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen är schemalagd att starta efter den tid som anges i den `NotBefore` egenskapen.<li>`Started`: Den här händelsen har startats.</ul> Inte `Completed` eller liknande status någonsin har angetts. Händelsen är inte längre returneras när händelsen är klar.
| NotBefore| Tid efter vilken den här händelsen kan starta. <br><br> Exempel: <br><ul><li> Mån, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Schemaläggning av händelse
Varje händelse schemaläggs en minimal mängd tidpunkt i framtiden baserat på vilken typ av händelse. Nu visas i en händelse `NotBefore` egenskapen. 

|Händelsetyp  | Minsta meddelande |
| - | - |
| Lås| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |

### <a name="start-an-event"></a>Starta en händelse 

När du lära dig om en kommande händelse och Slutför för avslutning logik, kan du godkänna utestående händelsen genom att göra en `POST` anrop till Metadata Service med `EventId`. Anger det här anropet till Azure att den kan förkorta minsta meddelandet (när det är möjligt). 

I följande JSON-exempel förväntas i den `POST` brödtext i begäran. Begäran bör innehålla en lista över `StartRequests`. Varje `StartRequest` innehåller `EventId` för händelsen som du vill påskynda:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Kanske uttrycka en händelse kan händelsen att gå vidare för alla `Resources` i händelseloggen, inte bara den virtuella datorn som om händelsen. Därför kan du välja en ledare att samordna bekräftelse som kan vara lika enkelt som att den första datorn i den `Resources` fält.

## <a name="python-sample"></a>Python-exempel 

I följande exempel frågar Metadata Service efter schemalagda händelser och godkänner varje utestående händelse:

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
- Titta på [schemalagda händelser på Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) att se en demonstration. 
- Granska Scheduled Events-kodexempel i den [Azure instans Metadata schemalagda händelser GitHub-lagringsplatsen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Läs mer om API: er som är tillgängliga i den [Instance Metadata Service](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för Linux-datorer i Azure](planned-maintenance.md).
