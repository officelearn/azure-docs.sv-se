---
title: "Schemalagda händelser för Linux virtuella datorer i Azure | Microsoft Docs"
description: "Schemalagda händelser som använder tjänsten Azure Metadata för på din virtuella Linux-datorer."
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
ms.openlocfilehash: 75e509a7e39f5b268ce550d0c4dea2261d4fe56f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Metadata Azure: Schemalagda händelser (förhandsversion) för virtuella Linux-datorer

> [!NOTE] 
> Förhandsgranskningar görs tillgängliga för dig under förutsättning att du godkänner användningsvillkoren. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Schemalagda händelser är en av subservices under tjänsten Azure Metadata. Ansvarar för att visa information om kommande händelser (till exempel omstart) så att programmet kan förbereda för dem och begränsa avbrott. Den är tillgänglig för alla typer av Azure virtuella datorer inklusive PaaS och IaaS. Schemalagda händelser ger din virtuella tid att utföra förebyggande åtgärder för att minimera effekten av en händelse. 

Schemalagda händelser är tillgänglig för både Windows- och Linux virtuella datorer. Information om schemalagda händelser i Windows finns i [schemalagda händelser för virtuella Windows-datorer](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>Varför schemalagda händelser?

Du kan vidta åtgärder för att begränsa effekten av plattform intiated underhåll eller användare utför åtgärder på din tjänst med schemalagda händelser. 

Flera instanser arbetsbelastningar, som använder replikeringstekniker för för att upprätthålla tillstånd, kan vara utsatt för avbrott som händer i flera instanser. Exempel driftsavbrott kan resultera i dyra aktiviteter (till exempel återskapande index) eller även en replik går förlorade. 

I många andra fall totala tjänsttillgängligheten kan förbättras genom att utföra en korrekt avslutningssekvens som Slutför (eller annullerar) pågående transaktioner, tilldela uppgifter till andra virtuella datorer i klustret (manuell redundans) eller ta bort virtuellt Datorn från poolen network load belastningsutjämnaren. 

Finns det fall där tala med administratören om en kommande händelse eller logga sådan händelse att förbättra brukbarheten av program finns i molnet.

Azure Metadata Service hämtar schemalagda händelser i följande användningsområden:
-   Plattform som initierade Underhåll (till exempel värd-OS-installationen)
-   Användarinitierad anrop (till exempel användaren startar om eller återdistribuerar en virtuell dator)


## <a name="the-basics"></a>Grunderna  

Metadata i Azure-tjänsten visar information om att köra virtuella datorer med en REST-slutpunkt som är tillgänglig från den virtuella datorn. Informationen är tillgänglig via en icke-dirigerbara IP-adress så att inte exponeras utanför den virtuella datorn.

### <a name="scope"></a>Omfång
Schemalagda händelser är anslutna till alla virtuella datorer i en molnbaserad tjänst eller till alla virtuella datorer i en Tillgänglighetsuppsättning. Därför bör du kontrollera den `Resources` i händelsen för att identifiera vilka virtuella datorer kommer att påverkas. 

### <a name="discovering-the-endpoint"></a>Identifiering av slutpunkten
I de fall där en virtuell dator skapas ett virtuellt nätverk (VNet), metadatatjänsten är tillgänglig från en statisk icke-dirigerbara IP `169.254.169.254`.
Om den virtuella datorn inte har skapat ett virtuellt nätverk, standard-fall för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera slutpunkt för att använda. Referera till det här exemplet att lära dig hur du [identifiera värden slutpunkt](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Versionshantering 
Tjänsten instans Metadata är en ny version. Versioner är obligatoriska och den aktuella versionen är `2017-03-01`.

> [!NOTE] 
> Tidigare förhandsvisningarna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att inaktualiseras i framtiden.

### <a name="using-headers"></a>Med hjälp av rubriker
När du frågar Metadata Service måste du ange rubriken `Metadata: true` så begäran inte omdirigerades oavsiktligt.

### <a name="enabling-scheduled-events"></a>Aktivera schemalagd händelser
Första gången du skapar en begäran om schemalagda händelser aktiverar Azure implicit funktionen på den virtuella datorn. Därför bör du förväntar dig en fördröjd svar i din första anropet av upp till två minuter.

### <a name="user-initiated-maintenance"></a>Användarinitierad Underhåll
Användaren initierade underhålla virtuella datorer via Azure portal, API, CLI eller PowerShell resulterar i en schemalagd händelse. Det kan du testa Underhåll förberedelse av logiken i ditt program och att ditt program att förbereda för användarinitierad underhåll.

Starta om en virtuell dator schemalägger en händelse med typen `Reboot`. Omdistribuera en virtuell dator schemalägger en händelse med typen `Redeploy`.

> [!NOTE] 
> För närvarande kan högst 10 användarinitierad underhållsåtgärder samtidigt schemaläggas. Den här gränsen mjukas upp före schemalagda händelser allmän tillgänglighet.

> [!NOTE] 
> Användarinitierad Underhåll ledde schemalagda händelser kan för närvarande inte konfigureras. Konfigurationsmöjligheter är planerad för framtida versioner.

## <a name="using-the-api"></a>Med hjälp av API

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att göra följande anrop:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
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

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> Bekräfta en händelse kan händelsen för att fortsätta för alla `Resources` i händelseloggen, inte bara den virtuella datorn som om händelsen. Därför kan du välja att välja ledande koordinera bekräftelse, vilket kan vara så enkelt som den första datorn i den `Resources` fältet.




## <a name="python-sample"></a>Python-exempel 

I följande exempel frågar metadatatjänsten för schemalagda händelser och godkänner alla utestående händelser.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
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
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Nästa steg 

- Läs mer om API: er finns i den [instans Metadata tjänsten](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för Linux virtuella datorer i Azure](planned-maintenance.md).
