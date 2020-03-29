---
title: Visualisera Azure NSG-flödesloggar - Power BI
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du visualiserar NSG-flödesloggar med Power BI.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 955e13b88037aa42b59707698549b1c980720990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840612"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualisera flödesloggar för nätverkssäkerhetsgruppen med Power BI

Med flödesloggarna för nätverkssäkerhetsgruppen kan du visa information om inkommande och utgående IP-trafik i nätverkssäkerhetsgrupper. Dessa flödesloggar visar utgående och inkommande flöden per regel, nätverkskortet som flödet gäller för, 5-tuppelinformation om flödet (Source/Destination IP, Source/Destination Port, Protocol) och om trafiken tilläts eller nekades.

Det kan vara svårt att få insikter i flödesloggningsdata genom att söka i loggfilerna manuellt. I den här artikeln tillhandahåller vi en lösning för att visualisera dina senaste flödesloggar och lära oss mer om trafik i nätverket.

> [!Warning]  
> Följande steg fungerar med flödesloggar version 1. Mer information finns i [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfilerna, utan ändringar.

## <a name="scenario"></a>Scenario

I följande scenario ansluter vi Power BI-skrivbordet till det lagringskonto som vi har konfigurerat som diskbänk för våra NSG-flödesloggningsdata. När vi har anslutit till vårt lagringskonto hämtar och analyserar Power BI loggarna för att ge en visuell representation av trafiken som loggas av nätverkssäkerhetsgrupper.

Med hjälp av de visuella objekt som anges i mallen kan du undersöka:

* Bästa talare
* Flödesdata för tidsserier efter riktning och regelbeslut
* Flöden efter MAC-adress för nätverksgränssnitt
* Flöden efter NSG och regel
* Flöden efter destinationsport

Mallen som tillhandahålls kan redigeras så att du kan ändra den för att lägga till nya data, visuella objekt eller redigera frågor som passar dina behov.

## <a name="setup"></a>Installation

Innan du börjar måste du ha nätverkssäkerhetsgruppflödesloggning aktiverat på en eller flera nätverkssäkerhetsgrupper i ditt konto. Instruktioner om hur du aktiverar flödesloggar för nätverkssäkerhet finns i följande artikel: [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

Du måste också ha Power BI Desktop-klienten installerad på datorn och tillräckligt med ledigt utrymme på datorn för att kunna hämta och läsa in loggdata som finns i ditt lagringskonto.

![Visio-diagram][1]

### <a name="steps"></a>Steg

1. Hämta och öppna följande Power BI-mall i Power BI Desktop Application [Network Watcher PowerBI-flödesloggar](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Ange de frågeparametrar som krävs
   1. **StorageAccountName** – Anger namnet på lagringskontot som innehåller NSG-flödesloggarna som du vill läsa in och visualisera.
   1. **NumberOfLogFiles** – Anger antalet loggfiler som du vill hämta och visualisera i Power BI. Om till exempel 50 har angetts kommer de 50 senaste loggfilerna. Om vi har 2 NSGs aktiverat och konfigurerat för att skicka NSG-flödesloggar till det här kontot, kan de senaste 25 timmarna av loggar visas.

      ![ström BI huvud][2]

1. Ange åtkomstnyckeln för ditt lagringskonto. Du kan hitta giltiga åtkomstnycklar genom att navigera till ditt lagringskonto i Azure-portalen och välja **Åtkomstnycklar** på menyn Inställningar. Klicka på **Anslut** och sedan tillämpa ändringar.

    ![öppna nycklar][3]

    ![åtkomstnyckel 2][4]

4. Dina loggar är hämtade och tolkade och du kan nu använda den förskapade grafiken.

## <a name="understanding-the-visuals"></a>Förstå det visuella

I mallen finns en uppsättning visuella objekt som hjälper till att förstå NSG-flödesloggdata. Följande bilder visar ett exempel på hur instrumentpanelen ser ut när den fylls med data. Nedan undersöker vi varje visuell i större detalj 

![powerbi][5]
 
Den övre talare visuella visar de IPs som har initierat flest anslutningar under den angivna perioden. Storleken på rutorna motsvarar det relativa antalet anslutningar. 

![toptalkers (toptalkers)][6]

Följande tidsseriediagram visar antalet flöden under perioden. Det övre diagrammet segmenteras av flödesriktningen och det lägre segmenteras av det beslut som fattas (tillåt eller neka). Med det här visuella objektet kan du undersöka dina trafiktrender över tid och upptäcka eventuella onormala toppar eller minskning av trafik- eller trafiksegmentering.

![flödenöverperiod][7]

Följande diagram visar flödena per nätverksgränssnitt, där de övre segmenterade efter flödesriktning och de lägre segmenterade efter beslut fattas. Med den här informationen kan du få insikter om vilka av dina virtuella datorer som kommunicerats mest i förhållande till andra, och om trafik till en viss virtuell dator tillåts eller nekas.

![flödenpernic][8]

Följande donuthjulsdiagram visar en uppdelning av flöden efter målport. Med den här informationen kan du visa de vanligaste målportarna som används inom den angivna perioden.

![toroid][9]

Följande stapeldiagram visar flödet efter NSG och Regel. Med den här informationen kan du se de nationella användargrupperna som ansvarar för mest trafik och fördelningen av trafiken på en NSG-regel.

![barchart (ar)][10]
 
I följande informationsdiagram visas information om nsg:erna som finns i loggarna, antalet flöden som samlas in under perioden och datumet för den tidigaste infångade loggen. Den här informationen ger dig en uppfattning om vilka NSG:er som loggas och datumintervallet för flöden.

![infochart1][11]

![infochart2][12]

Den här mallen innehåller följande utsnitt så att du bara kan visa de data du är mest intresserad av. Du kan filtrera på resursgrupper, NSG:er och regler. Du kan också filtrera på 5-tuppelinformation, beslut och den tid då loggen skrevs.

![Utsnitt][13]

## <a name="conclusion"></a>Slutsats

Vi visade i det här scenariot att genom att använda Network Security Group Flow loggar från Network Watcher och Power BI, kan vi visualisera och förstå trafiken. Med hjälp av den medföljande mallen hämtar Power BI loggarna direkt från lagring och bearbetar dem lokalt. Den tid det tar att läsa in mallen varierar beroende på antalet begärda filer och den totala storleken på de filer som hämtats.

Anpassa gärna den här mallen för dina behov. Det finns många olika sätt att använda Power BI med flödesloggar för nätverkssäkerhetsgrupper. 

## <a name="notes"></a>Anteckningar

* Loggar som standard lagras i`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Om det finns andra data i en annan katalog måste de frågor som ska hämtas och bearbetas data ändras.

* Den medföljande mallen rekommenderas inte för användning med mer än 1 GB loggar.

* Om du har en stor mängd loggar rekommenderar vi att du undersöker en lösning med ett annat datalager som Data Lake eller SQL-server.

## <a name="next-steps"></a>Efterföljande moment

Lär dig hur du visualiserar dina NSG-flödesloggar med elastisk stack genom att besöka [Visualisera Azure Network Watcher NSG-flödesloggar med verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
