---
title: Övervaka Azure FXT Edge Filer
description: Så här övervakar du maskinvarustatus för Azure FXT Edge Filer hybridlagringscache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254882"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Övervaka azure FXT Edge Filer maskinvarustatus

Azure FXT Edge Filer hybrid lagring cachesystem har flera statuslampor inbyggda i chassit för att hjälpa administratörer att förstå hur maskinvaran fungerar.

## <a name="system-health-status"></a>Systemets hälsostatus

Om du vill övervaka cacheåtgärder på en högre nivå använder du **instrumentpanelens instrumentpanelssida** för programvaran enligt beskrivningen i [instrumentpanelsguiden för Kontrollpanelen](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Lysdioder för maskinvarustatus

I det här avsnittet beskrivs de olika statuslampor som är inbyggda i Azure FXT Edge Filer-maskinvaran.

### <a name="hard-drive-status-leds"></a>Lysdioder för hårddiskstatus

![bild av hårddiskens främre, vågräta, med bildtextetiketter 2 (övre vänstra hörnet), 1 (nedre vänstra hörnet) och 3 (höger sida)](media/fxt-monitor/fxt-drive-callouts.png)

Varje drivhållare har två statuslysdioder: en aktivitetsindikator (1) och en statusindikator (2). 

* Aktivitetslampan (1) tänds när enheten används.  
* Statuslampan (2) anger enhetens skick med hjälp av koderna i tabellen nedan.

| LED-tillstånd för enhetsstatus              | Betydelse  |
|-------------------------------------|----------------------------------------------------------|
| Blinkar grönt två gånger per sekund      | Identifiera enhet *eller* <br> Förbereda enhet för borttagning  |
| Av (ej upplyst)                         | Systemet har inte startats *eller* <br>Enheten är klar att tas bort |
| Blinkar grönt, gult och av       | Enhetsfel förutses   |
| Blinkar gult fyra gånger per sekund | Enheten misslyckades   |
| Fast grönt                         | Drive är online |

Enhetens högra sida (3) är märkt med enhetens kapacitet och annan information.

Enhetsnummer skrivs ut på utrymmet mellan enheterna. I Azure FXT Edge Filer är enhet 0 den övre vänstra enheten och enhet 1 är direkt under den. Numrering fortsätter i det mönstret. 

![foto av ett hårddiskfack i FXT-chassit, som visar drivnummer och kapacitetsetiketter](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Vänster kontrollpanel

Den vänstra främre kontrollpanelen har olika status LED-indikatorer (1) och en stor upplyst systemhälsoindikator (2). 

![vänster statuspanel, med 1 märkning statusindikatorer till vänster, och 2 märkning av det stora systemet hälsoindikatorn lampa på höger](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Statusindikatorer för kontrollpanelen 

Statusindikatorerna till vänster visar ett fast gult ljus om det finns ett fel i det systemet. I tabellen nedan beskrivs möjliga orsaker och lösningar för felen. 

Om du fortfarande har felet när du har provat dessa lösningar [kontaktar du supporten](fxt-support-ticket.md) för att få hjälp. 

| Ikon | Beskrivning | Feltillstånd | Möjliga lösningar |
|----------------|---------------|--------------------|----------------------|
| ![enhetsikon](media/fxt-monitor/fxt-hd-icon.jpg) | Enhetsstatus | Enhetsfel | Kontrollera systemhändelseloggen för att avgöra om enheten har ett fel, eller <br>Kör lämpligt onlinediagnostiktest. starta om systemet och köra inbäddade diagnostik (ePSA), eller <br>Om enheterna är konfigurerade i en RAID-matris startar du om systemet och går in i värdkortets konfigurationsverktygsprogram |
|![temperaturikon](media/fxt-monitor/fxt-temp-icon.jpg) | Temperaturstatus | Termiskt fel - till exempel har en fläkt gått sönder eller omgivningstemperaturen är utom räckhåll | Kontrollera följande adresserbara villkor: <br>En kylfläkt saknas eller har misslyckats <br>Systemets lock, lufthölje, minnesmodul tom eller bakre fyllnadsfästet tas bort <br>Omgivningstemperaturen är för hög <br>Externt luftflöde hindras |
|![el ikon](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrisk status | Elektriskt fel - till exempel spänning utanför intervallet, misslyckat nätagg eller en misslyckad spänningsregulator |  Kontrollera systemhändelseloggen eller systemmeddelandena för det specifika problemet. Om det finns ett PSU-problem, kontrollera lysdioden för nätaggsstatus och sätt tillbaka psu:et om det behövs. | 
|![minnesikon](media/fxt-monitor/fxt-memory-icon.jpg) | Minnesstatus | Minnesfel | Kontrollera systemhändelseloggen eller systemmeddelandena för platsen för det felaktiga minnet. på nytt i minnesmodulen. |
|![Ikon för PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-status | PCIe-kortfel | Starta om systemet. uppdatera PCIe-kortdrivrutiner; installera om kortet |


### <a name="system-health-status-indicator"></a>Statusindikator för systemets hälsotillstånd

Den stora tända knappen till höger om den vänstra kontrollpanelen indikerar övergripande systemstatus och används även som enhetslokaliseringslampa i system-ID-läge.

Tryck på systemets hälso- och ID-knapp för att växla mellan system-ID-läge och systemhälsoläge.

|Statustillstånd för systemet | Villkor |
|-------------------------------------------|-----------------------------------------------|
| Helblått | Normal drift: systemet är aktiverat, fungerar normalt och system-ID-läget är inte aktivt. <br/>Tryck på systemets hälso- och ID-knapp om du vill växla till system-ID-läge. |
| Blinkande blått | System-ID-läget är aktivt. Tryck på systemhälso- och system-ID-knappen om du vill växla till systemhälsoläge. |
| Fast gult | Systemet är i felsäkert läge. Om problemet kvarstår [kontaktar du Microsofts kundtjänst och support](fxt-support-ticket.md). |
| Blinkande gult | Systemfel. Kontrollera om det finns specifika felmeddelanden i systemhändelseloggen i systemhändelseloggen. Information om händelsen och felmeddelanden som genereras av systemets inbyggda programvara och agenter som övervakar systemkomponenter finns på sidan Felkodsökning på qrl.dell.com. |


