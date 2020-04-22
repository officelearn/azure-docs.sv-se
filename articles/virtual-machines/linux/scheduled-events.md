---
title: Schemalagda händelser för virtuella Linux-datorer i Azure
description: Schemalägg händelser med hjälp av Azure Metadata Service för dina virtuella Linux-datorer.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: ee600d7524af27a0e9e2ce0176e7bd4d1f60bc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758552"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Schemalagda händelser för Virtuella Linux-datorer

Schemalagda händelser är en Azure Metadata Service som ger ditt program tid att förbereda för virtuell dator (VM) underhåll. Den innehåller information om kommande underhållshändelser (till exempel omstart) så att ditt program kan förbereda sig för dem och begränsa störningar. Den är tillgänglig för alla Azure Virtual Machines-typer, inklusive PaaS och IaaS på både Windows och Linux. 

Information om schemalagda händelser i Windows finns i [Schemalagda händelser för virtuella windows-datorer](../windows/scheduled-events.md).

> [!Note] 
> Schemalagda händelser är allmänt tillgängliga i alla Azure-regioner. Se [Version och Region Tillgänglighet](#version-and-region-availability) för senaste versionen information.

## <a name="why-use-scheduled-events"></a>Varför använda schemalagda händelser?

Många program kan dra nytta av tid för att förbereda för VM-underhåll. Tiden kan användas för att utföra programspecifika uppgifter som förbättrar tillgänglighet, tillförlitlighet och servicebarhet, inklusive: 

- Kontrollpunkt och återställning.
- Anslutningsdränning.
- Primär replik redundans.
- Borttagning från en belastningsutjämnad pool.
- Händelseloggning.
- Graciös avstängning.

Med schemalagda händelser kan ditt program identifiera när underhåll kommer att ske och utlösa aktiviteter för att begränsa dess påverkan.  

Schemalagda händelser innehåller händelser i följande användningsfall:

- [Plattformsinitierat underhåll](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (till exempel omstart av virtuella datorer, livemigrering eller minneskonering av uppdateringar för värd)
- Virtuell dator körs på [skadad värdmaskinvara](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) som förutspås misslyckas snart
- Användarinitierat underhåll (till exempel startar en användare om eller distribuerar om en virtuell dator)
- [Spot VM](spot-vms.md) och [Spot skala uppsättning](../../virtual-machine-scale-sets/use-spot.md) instans vräkningar.

## <a name="the-basics"></a>Grunderna  

  Metadatatjänsten visar information om hur du kör virtuella datorer med hjälp av en REST-slutpunkt som är tillgänglig inifrån den virtuella datorn. Informationen är tillgänglig via en ip som inte kan tillhandahållas så att den inte exponeras utanför den virtuella datorn.

### <a name="scope"></a>Omfång
Schemalagda händelser levereras till:

- Fristående virtuella datorer.
- Alla virtuella datorer i en molntjänst.
- Alla virtuella datorer i en tillgänglighetsuppsättning.
- Alla virtuella datorer i en skalningsuppsättningsplaceringsgrupp. 

Det kan leda `Resources` till att du kontrollerar fältet i händelsen för att identifiera vilka virtuella datorer som påverkas.

### <a name="endpoint-discovery"></a>Identifiering av slutpunkt
För virtuella datorer som är aktiverade är metadatatjänsten tillgänglig `169.254.169.254`från en statisk IP som inte är tillgänglig. Den fullständiga slutpunkten för den senaste versionen av schemalagda händelser är: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

Om den virtuella datorn inte skapas i ett virtuellt nätverk, standardfallen för molntjänster och klassiska virtuella datorer, krävs ytterligare logik för att identifiera IP-adressen som ska användas. Mer information om hur du [identifierar värdslutpunkten](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)finns i det här exemplet.

### <a name="version-and-region-availability"></a>Tillgänglighet för version och region
Tjänsten Schemalagda händelser är version. Versioner är obligatoriska; den aktuella `2019-01-01`versionen är .

| Version | Typ av utgivning | Regioner | Viktig information | 
| - | - | - | - | 
| 2019-01-01 | Allmän tillgänglighet | Alla | <li> Lagt till stöd för skalningsuppsättningar för virtuella datorer EventType 'Avsluta' |
| 2017-11-01 | Allmän tillgänglighet | Alla | <li> Lagt till stöd för Spot VM vräkning EventType 'Preempt'<br> | 
| 2017-08-01 | Allmän tillgänglighet | Alla | <li> Borttagen prepended understreck från resursnamn för IaaS virtuella datorer<br><li>Metadatahuvudkravet tillämpas för alla begäranden | 
| 2017-03-01 | Förhandsversion | Alla | <li>Första utgåvan |


> [!NOTE] 
> Tidigare förhandsversioner av schemalagda händelser stöds {senaste} som api-version. Det här formatet stöds inte längre och kommer att vara inaktuellt i framtiden.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivera och inaktivera schemalagda händelser
Schemalagda händelser är aktiverade för din tjänst första gången du gör en begäran om händelser. Du bör förvänta dig ett försenat svar i ditt första samtal på upp till två minuter.

Schemalagda händelser är inaktiverat för din tjänst om den inte gör en begäran på 24 timmar.

### <a name="user-initiated-maintenance"></a>Användarinitierat underhåll
Användarinitierat VM-underhåll via Azure-portalen, API, CLI eller PowerShell resulterar i en schemalagd händelse. Du kan sedan testa underhållsförberedelselogiken i ditt program och ditt program kan förbereda för användarinitierat underhåll.

Om du startar om en virtuell `Reboot` dator schemaläggs en händelse med typen. Om du distribuerar om en virtuell dator `Redeploy` schemaläggs en händelse med typen.

## <a name="use-the-api"></a>Använda API:et

### <a name="headers"></a>Rubriker
När du frågar metadatatjänsten måste `Metadata:true` du ange huvudet för att säkerställa att begäran inte omdirigerades oavsiktligt. Huvudet `Metadata:true` krävs för alla schemalagda händelsebegäranden. Underlåtenhet att inkludera huvudet i begäran resulterar i ett "Bad Request" svar från Metadata Service.

### <a name="query-for-events"></a>Fråga efter händelser
Du kan fråga efter schemalagda händelser genom att ringa följande samtal:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

Ett svar innehåller en matris med schemalagda händelser. En tom matris innebär att för närvarande inga händelser är schemalagda.
Om det finns schemalagda händelser innehåller svaret en matris med händelser. 
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

### <a name="event-properties"></a>Egenskaper för händelser
|Egenskap  |  Beskrivning |
| - | - |
| EventId (på)EventId ) | Globalt unik identifierare för den här händelsen. <br><br> Exempel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Eventtype | Påverka den här händelsen orsaker. <br><br> Värden: <br><ul><li> `Freeze`: Den virtuella datorn är schemalagd att pausa i några sekunder. CPU- och nätverksanslutningen kan avbrytas, men det finns ingen inverkan på minnet eller öppna filer.<li>`Reboot`: Den virtuella datorn är schemalagd för omstart (icke-beständigt minne går förlorat). <li>`Redeploy`: Den virtuella datorn är schemalagd att flyttas till en annan nod (efemära diskar går förlorade). <li>`Preempt`: Spot Virtual Machine tas bort (efemära diskar går förlorade). <li> `Terminate`: Den virtuella datorn är schemalagd att tas bort. |
| ResourceType | Typ av resurs som den här händelsen påverkar. <br><br> Värden: <ul><li>`VirtualMachine`|
| Resurser| Lista över resurser som den här händelsen påverkar. Listan är garanterad att innehålla datorer från högst en [uppdateringsdomän,](manage-availability.md)men den kanske inte innehåller alla datorer i UD. <br><br> Exempel: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Status för den här händelsen. <br><br> Värden: <ul><li>`Scheduled`: Den här händelsen är schemalagd att `NotBefore` starta efter den tid som anges i egenskapen.<li>`Started`: Den här händelsen har startat.</ul> Ingen `Completed` eller liknande status tillhandahålls någonsin. Händelsen returneras inte längre när händelsen är klar.
| NotBefore| Tid efter vilken den här händelsen kan starta. <br><br> Exempel: <br><ul><li> mån, 19 sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Schemaläggning av händelser
Varje händelse schemaläggs en minsta tid i framtiden baserat på händelsetypen. Den här gången återspeglas `NotBefore` i en händelse egenskap. 

|Eventtype  | Minsta varsel |
| - | - |
| Frysa| 15 minuter |
| Starta om | 15 minuter |
| Omdistribuera | 10 minuter |
| Föregripa | 30 sekunder |
| Avsluta | [Användaren konfigurerbar:](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)5 till 15 minuter |

> [!NOTE] 
> I vissa fall kan Azure förutsäga värdfel på grund av försämrad maskinvara och kommer att försöka minska störningar i tjänsten genom att schemalägga en migrering. Berörda virtuella datorer får en `NotBefore` schemalagd händelse med en som vanligtvis är några dagar i framtiden. Den faktiska tiden varierar beroende på den förväntade riskbedömningen. Azure försöker ge 7 dagars förvarning när det är möjligt, men den faktiska tiden varierar och kan vara mindre om förutsägelsen är att det finns en stor chans att maskinvaran misslyckas inom kort. För att minimera risken för din tjänst om maskinvaran misslyckas innan den systeminitierade migreringen rekommenderar vi att du distribuerar om den virtuella datorn så snart som möjligt.

### <a name="start-an-event"></a>Starta en händelse 

När du har fått reda på en kommande händelse och avslutat logiken `POST` för graciös `EventId`avstängning kan du godkänna den utestående händelsen genom att ringa ett anrop till metadatatjänsten med . Det här anropet anger för Azure att det kan förkorta den minsta meddelandetiden (när det är möjligt). 

Följande JSON-prov förväntas `POST` i begärandeorganet. Begäran bör innehålla en `StartRequests`lista över . Varje `StartRequest` innehåller `EventId` för den händelse som du vill påskynda:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Om du bekräftar en händelse kan `Resources` händelsen fortsätta för alla i händelsen, inte bara den virtuella datorn som bekräftar händelsen. Därför kan du välja att välja en ledare för att samordna bekräftelsen, `Resources` vilket kan vara lika enkelt som den första datorn i fältet.

## <a name="python-sample"></a>Python-exempel 

Följande exempel frågar metadatatjänsten för schemalagda händelser och godkänner varje utestående händelse:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01"
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
        notbefore = evt['NotBefore'].replace(" ", "_")
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Nästa steg 
- Titta [på schemalagda händelser på Azure Friday](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) för att se en demo. 
- Granska kodexemplen för schemalagda händelser i [Azure Instance Metadata Scheduled Events GitHub-databasen](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Läs mer om de API:er som är tillgängliga i [instansmetadatatjänsten](instance-metadata-service.md).
- Lär dig mer om [planerat underhåll för virtuella Linux-datorer i Azure](planned-maintenance.md).
