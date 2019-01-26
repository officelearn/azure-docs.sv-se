---
title: Visualizing Azure Network Security Group flödesloggar med Power BI | Microsoft Docs
description: Den här sidan beskriver hur du kan visualisera NSG-flödesloggar med Power BI.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 6b3f3217f90c2c5a72ba9576b5e0cc89e6133cd8
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913316"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualizing Nätverkssäkerhetsgrupp flödesloggar med Power BI

Flödesloggar för Nätverkssäkerhetsgruppen kan du visa information om ingående och utgående IP-trafik på Nätverkssäkerhetsgrupper. Dessa flödar loggar Visa utgående och inkommande flöden på basis av per regel, NIC flödet som gäller för, 5-tuppel information om flödet (käll-och mål IP, källa/mål-Port, protokoll), och om trafik tillåts eller nekas.

Det kan vara svårt att få insikter om flow loggningsdata genom att manuellt söka loggfilerna. Den här artikeln innehåller en lösning för att visualisera dina senaste flödesloggar och lär dig mer om trafik i nätverket.

> [!Warning]  
> Följande steg fungerar med flow loggar version 1. Mer information finns i [introduktion till flödesloggar för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfiler, utan modifiering.

## <a name="scenario"></a>Scenario

I följande scenario ansluter vi Power BI desktop till storage-kontot som vi har konfigurerat som mottagare för våra data för NSG Flow loggning. När vi har anslutit till våra storage-konto laddar ned Power BI och Parsar loggarna för att tillhandahålla en visuell representation av den trafik som loggas av nätverkssäkerhetsgrupper.

Med hjälp av de visuella objekten som angetts i mallen som du kan kontrollera:

* Toppkommunikatörer
* Time Series-flödesdata per riktning och regelbeslut
* Flöden per nätverksgränssnittet MAC-adress
* Flöden per NSG och regel
* Flöden per målport

Mallen som tillhandahölls redigeras så att du kan ändra den för att lägga till nya data, visuella objekt, eller redigera frågor efter dina behov.

## <a name="setup"></a>Konfiguration

Du måste ha Network Security grupp Flow Logging har aktiverats på en eller flera Nätverkssäkerhetsgrupper i ditt konto innan du börjar. Mer information om hur du aktiverar flödesloggar för nätverkssäkerhet finns i följande artikel: [Introduktion till flödesloggar för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

Du måste också ha Power BI Desktop klienten är installerad på din dator och tillräckligt med ledigt utrymme på din dator för att ladda ned och läsa in loggdata som finns i ditt storage-konto.

![Visio-diagram][1]

### <a name="steps"></a>Steg

1. Ladda ned och öppna följande Power BI-mall i Power BI Desktop programmet [flödesloggarna i Network Watcher PowerBI mall](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Ange de obligatoriska parametrarna i frågan
    1. **StorageAccountName** – anger namnet på lagringskontot som innehåller NSG-flödesloggar som du vill läsa in och visualisera.
    1. **NumberOfLogFiles** – anger hur många loggfiler som du vill ladda ned och visualisera i Power BI. Om 50 anges, till exempel de 50 senaste loggfilerna. Om vi har 2 NSG: er aktiveras och konfigureras att skicka NSG-flödesloggar till det här kontot kan de senaste 25 timmarna av loggar ses.

    ![Power BI main][2]

1. Ange åtkomstnyckel för lagringskontot. Du kan hitta giltiga åtkomstnycklar genom att gå till ditt lagringskonto i Azure-portalen och välja **åtkomstnycklar** från menyn Inställningar. Klicka på **Connect** tillämpa ändringarna.

    ![Åtkomstnycklar][3]

    ![få åtkomst till nyckel 2][4]

4.  Loggarna är ladda ned och parsa och du kan nu använda de färdiga visuella objekt.

## <a name="understanding-the-visuals"></a>Förstå de visuella objekten

I mallen är en uppsättning visuell information som hjälper dig att göra uppfattning om loggdata NSG Flow. Följande bilder visar ett exempel på hur instrumentpanelen som ser ut när du har fyllts med data. Nedan närmare vi på varje visuellt objekt i större detalj 

![powerbi][5]
 
I visual visas på översta Talkers som angetts för IP-adresser som har initierat de flesta anslutningar under perioden. Storleken på rutorna motsvarar det relativa antalet anslutningar. 

![toptalkers][6]

I följande time series diagram visas hur många flöden under perioden. Övre diagrammet är uppdelat efter flödesriktningen och lägre uppdelat efter beslutet (Tillåt eller neka). Med det här visuella objektet du granska din trafik trender över tid, och upptäcka eventuella onormala toppar eller neka trafik eller trafik segmentering.

![flowsoverperiod][7]

Följande diagram visar flöden per nätverksgränssnitt, med upp till uppdelat efter flödesriktning och lägre uppdelat efter beslut. Med den här informationen kan du få insikter om trafik till en specifik virtuell dator håller och vilka av dina virtuella datorer kommer mest i förhållande till andra tillåts eller nekas.

![flowspernic][8]

Följande hjul ringdiagrammet visar en sammanfattning av flöden per målport. Med den här informationen kan du visa de vanligaste mål-portarna som används inom den angivna perioden.

![donut][9]

Följande stapeldiagrammet visar flödet genom NSG och regel. Med den här informationen kan se du ansvarar för de flesta trafiken och fördelningen av trafiken NSG: er på en NSG av regeln.

![barchart][10]
 
Följande information diagrammen visar information om Nätverkssäkerhetsgrupper finns i loggarna, Antal flöden som hämtats under perioden och datumet då den tidigaste loggen avbildas. Den här informationen ger dig en uppfattning om vilken NSG: er är som du loggar in och datumintervall för flöden.

![infochart1][11]

![infochart2][12]

Den här mallen innehåller följande utsnitt så att du kan visa de data som du är mest intresserad av. Du kan filtrera efter dina resursgrupper, Nätverkssäkerhetsgrupper och regler. Du kan också filtrera på 5-tuppel information, beslut och den tid som har skrivits i loggen.

![utsnitt][13]

## <a name="conclusion"></a>Sammanfattning

Vi visade i det här scenariot att med hjälp av Network Security Group Flow loggar som tillhandahålls av Network Watcher och Power BI, kommer du att visualisera och förstå trafiken. Med den angivna mallen kan Power BI hämtar loggarna direkt från storage och bearbetar dem lokalt. Åtgången tid för att läsa in mallen varierar beroende på hur många filer som begärs och totala storleken på filerna ned.

Passa på att anpassa den här mallen för dina behov. Det finns många olika sätt att du kan använda Power BI med Flow loggar för Nätverkssäkerhetsgrupper. 

## <a name="notes"></a>Anteckningar

* Loggar som standard lagras i `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Om det finns andra data i en annan katalog som de frågorna till pull och bearbeta data måste ändras.

* Den angivna mallen rekommenderas inte för användning med mer än 1 GB av loggar.

* Om du har en stor mängd loggar, rekommenderar vi att du undersöker en lösning med ett annat datalager som Data Lake eller SQL server.

## <a name="next-steps"></a>Nästa steg

Lär dig att visualisera dina NSG-flödesloggar med Elastic Stack genom att besöka [visualisera Azure Network Watcher NSG-flödesloggar med verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
