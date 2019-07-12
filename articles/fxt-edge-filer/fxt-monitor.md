---
title: Övervaka Azure FXT Edge-Filer
description: Så här övervakar du maskinvarustatus för Azure FXT Edge Filer hybrid storage cache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827446"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Övervaka Azure FXT Edge Filer maskinvarustatus

Azure FXT Edge Filer hybrid lagringssystemet cache har flera status lamporna inbyggd i chassi för att hjälpa administratörer att förstå hur maskinvaran fungerar.

## <a name="system-health-status"></a>Hälsostatus för system

Du kan övervaka cache-åtgärder på en högre nivå genom att använda program på Kontrollpanelen **instrumentpanelen** sidan, enligt beskrivningen i den [Kontrollpanelen Dashboard Guide](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Maskinvarustatus led: ar

Det här avsnittet beskrivs olika statuslamporna inbyggda i Azure FXT Edge Filer maskinvara.

### <a name="hard-drive-status-leds"></a>Hårddisken status led: ar

![Bild av hårddisken front, horisontell, med bildtext etiketter 2 (övre vänstra hörnet), 1 (längst ned till vänster) och 3 (höger sida)](media/fxt-monitor/fxt-drive-callouts.png)

Varje enhet operatör har två status led: ar: en indikator för aktiviteten (1) och en statusindikator (2). 

* Aktiviteten LEDDE (1) lamporna när enheten används.  
* Statusen LED (2) anger villkoret för enheten med koder i tabellen nedan.

| Driv status LED tillstånd              | Betydelse  |
|-------------------------------------|----------------------------------------------------------|
| Gånger grön två gånger per sekund      | Identifiera enhet *eller* <br> Förbereda enheten för borttagning  |
| Inaktivera (Obelyst)                         | Systemet har inte slutförts Start *eller* <br>Enheten är redo att tas bort |
| Gånger grönt, gul, och inaktiverad       | Enhetsfel förväntas   |
| Gånger gul fyra gånger per sekund | Enheten misslyckades   |
| Fast grönt                         | Enheten är online |

Höger sida av enheten (3) är märkt med enhetens kapacitet och annan information.

Enheten siffror trycks på utrymmet på enheter. Enheten 0 är längst ned till vänster enhet i Azure FXT Edge filservern, och enheten 1 är direkt under den. Numrering fortsätter i mönstret. 

![Foto av en hårddisk fack i FXT chassi, som visar enheten siffror och kapacitet etiketter](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Vänstra Kontrollpanelen

Vänstra front Kontrollpanelen har olika LED indikatorer (1) och en indikator för hälsotillstånd stora upplysta system (2). 

![vänstra statuspanel med 1 märkning indikatorer till vänster och 2 märkning stora systemindikatorn för hälsotillstånd lätta till höger](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indikatorer för Kontrollpanelen status 

Statusindikatorer till vänster visar en solid gult ljus om det finns ett fel i systemet. Tabellen nedan beskrivs möjliga orsaker och lösningar för felen. 

Om du fortfarande har felet när du har försökt dessa lösningar [supporten](fxt-support-ticket.md) om du behöver hjälp. 

| Ikon | Beskrivning | Feltillstånd | Möjliga lösningar |
|----------------|---------------|--------------------|----------------------|
| ![enhetsikon](media/fxt-monitor/fxt-hd-icon.jpg) | Status för enheten | Enhetsfel | Kontrollera systemhändelseloggen för att avgöra om enheten har ett fel, eller <br>Kör lämplig online diagnostiktest; Starta om systemet och kör embedded diagnostik (ePSA), eller <br>Om enheterna som har konfigurerats i en RAID-matris, startar om systemet och ange värden nätverkskort konfigurationsprogrammet-verktyget |
|![ikon för temperatur](media/fxt-monitor/fxt-temp-icon.jpg) | Status för temperatur | Termiska fel – till exempel en fläkt har misslyckats eller temperatur ligger utanför intervallet | Sök efter följande adresserbara villkor: <br>En fläkten saknas eller har misslyckats <br>Systemets cover i luften shroud minnesmodulen tomt eller tillbaka information hakparentes tas bort <br>Den omgivande temperaturen är för hög <br>Externa luftflödet hindras |
|![elektricitet ikon](media/fxt-monitor/fxt-electric-icon.jpg) | Elektriska status | Det gick inte att elektriska fel – till exempel spänning utanför intervallet, PSU eller en misslyckad spänning regulator |  Kontrollera systemhändelseloggen eller systemmeddelanden för det specifika felet. Om det finns ett problem med PSU, kontrollera status för PSU LED och sätt tillbaka PSU om det behövs. | 
|![ikon för minne](media/fxt-monitor/fxt-memory-icon.jpg) | Minnesstatus | Minnesfel | Kontrollera systemhändelseloggen eller systemmeddelanden för platsen för det misslyckade minnet; Placera minnesmodulen. |
|![PCIe-ikon](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-status | PCIe kortfel | Starta om systemet. Uppdatera PCIe drivrutiner; installera om kortet |


### <a name="system-health-status-indicator"></a>Status som indikerar systemets hälsa

Den stora upplysta knappen längst till höger i den vänstra Kontrollpanelen visar övergripande status för system och även används som en enhet positionerare ljus i läget för system-ID: T.

Tryck på systemhälsa och ID-knappen för att växla mellan system-ID och system health läge.

|Status för system hälsotillstånd | Tillstånd |
|-------------------------------------------|-----------------------------------------------|
| Heltäckande blått | Normal drift: systemet är aktiverad, fungerar normalt, och läget för system-ID är inte aktiv. <br/>Tryck på systemhälsa och ID-knappen om du vill växla till läget för system-ID: T. |
| Blinkande blå | System-ID-läget är aktivt. Tryck på systemhälsa och system-ID knappen om du vill växla till läget för system hälsotillstånd. |
| Solid gul | Systemet är i felsäkert läge. Om problemet kvarstår [kontakta Microsofts kundservice och Support](fxt-support-ticket.md). |
| Blinkande gul | Fel. Kontrollera systemhändelseloggen efter specifika felmeddelanden. Se sidan kod felsökning på qrl.dell.com information om händelse- och felmeddelanden som genereras av systemets inbyggda programvara och agenter som övervakar systemkomponenter. |


