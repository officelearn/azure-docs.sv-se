---
title: "Nätverkssäkerhetsgruppen för visualizing Azure flödet loggar med Power BI | Microsoft Docs"
description: "Den här sidan beskrivs visualisera NSG flödet loggar med Power BI."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 19bd7ed4bab915d7918a192a046653666cfaa498
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Visualizing Nätverkssäkerhetsgruppen flöde loggar med Power BI

Nätverkssäkerhetsgruppen flöde loggar kan du visa information om ingående och utgående IP-trafik på Nätverkssäkerhetsgrupper. Dessa trafikflödet loggar Visa utgående och inkommande flöden på grundval av per regel, NIC flödet gäller, 5-tuppel information om flödet (källan/målet IP, källan/målet Port Protocol), och om trafiken tillåts eller nekas.

Det kan vara svårt att få insikter om flödet loggningsdata genom att söka i loggfiler manuellt. I den här artikeln får en lösning för att visualisera dina senaste flödet loggar och lär dig mer om trafik i nätverket.

## <a name="scenario"></a>Scenario

I följande scenario ansluta vi Power BI desktop till lagringskontot som vi har konfigurerats som sink för våra data för NSG flöda loggning. När vi har anslutit till vår lagringskontot Power BI hämtar och Parsar loggarna för att ge en bild av trafiken som loggas av nätverkssäkerhetsgrupper.

Använda den visuella informationen som anges i mallen som du kan undersöka:

* Övre Talkers
* Serien flöda tidsdata genom riktning och regeln beslut
* Flöden av Network Interface MAC-adress
* Flöden av NSG och regel
* Flöden av målport

Den mall som kan redigeras så att du kan ändra den och lägga till nya data, visuell information, eller redigera frågor så att de passar dina behov.

## <a name="setup"></a>Konfiguration

Du måste ha nätverket grupp flöda säkerhetsloggning aktiverad på en eller flera Nätverkssäkerhetsgrupper i ditt konto innan du börjar. Anvisningar om hur du aktiverar Network Security flöda loggar, finns i följande artikel: [introduktion till flödet loggning för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

Du måste också ha Power BI Desktop klienten är installerad på din dator och tillräckligt med ledigt utrymme på din dator för att ladda ned och läsa in loggdata som finns i ditt lagringskonto.

![Visio-diagram][1]

### <a name="steps"></a>Steg

1. Ladda ned och öppna följande Power BI-mall i Power BI Desktop Application [nätverk Watcher PowerBI flöde loggar mall](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Ange de obligatoriska parametrarna i frågan
    1. **StorageAccountName** – anger namnet på lagringskontot som innehåller flödet NSG-loggar som du vill läsa in och visualisera.
    1. **NumberOfLogFiles** – anger antal loggfiler som du vill hämta och visualisera i Power BI. Om exempelvis 50 anges 50 senaste loggfilerna. FF har vi 2 NSG: er aktiverad och konfigurerad att skicka NSG flödet loggar till det här kontot och de senaste 25 timmarna loggar kan visas.

    ![Power BI main][2]

1. Ange åtkomstnyckeln för ditt lagringskonto. Du kan hitta giltig åtkomstnycklar genom att navigera till ditt lagringskonto i Azure portal och markera **åtkomstnycklar** på menyn Inställningar. Klicka på **Anslut** tillämpa ändringarna.

    ![Snabbtangenter][3]

    ![åtkomstnyckeln 2][4]

4.  Loggarna är hämta och analysera och du kan nu använda förskapade visuell information.

## <a name="understanding-the-visuals"></a>Förstå den visuella informationen

I mallen är en uppsättning visuell information som gör en uppfattning om loggdata NSG flöda. Följande bilder visar ett exempel på hur instrumentpanelen ser ut när fylls i med data. Nedan igenom vi varje visual i större detalj 

![powerbi][5]
 
Top Talkers visual visas i IP-adresser som har initierat de flesta anslutningar under perioden anges. Storleken på rutorna motsvarar det relativa antalet anslutningar. 

![toptalkers][6]

Följande tid serien diagram visar antal flöden under perioden. Övre diagrammet segmenterade av flödesriktning och lägst segmenterat genom beslut (Tillåt eller neka). Med den här visuella informationen du undersöka trenderna trafik över tid, och upptäcka eventuella onormala toppar eller neka trafik eller trafiksegmenteringen.

![flowsoverperiod][7]

Följande diagram visar flöden per nätverksgränssnitt med övre åtskilda med flödesriktning och lägst åtskilda med beslut. Med den här informationen kan du få insikter om vilka av dina virtuella datorer har kommunicerat mest i förhållande till andra och om trafik till en specifik VM som tillåts eller nekas.

![flowspernic][8]

Följande ringen hjul diagram visar en sammanfattning av flöden av målport. Med den här informationen kan visa du de vanligaste målportar som används inom den angivna tidsperioden.

![Ring][9]

Följande stapeldiagram visar flödet av NSG och regeln. Med denna information kan se du ansvarar för de flesta trafiken och fördelningen av trafiken NSG: er på en NSG av regeln.

![barchart][10]
 
Följande information diagram visar information om NSG: er finns i loggarna, Antal flöden som avbildas under tid och datum loggens tidigaste avbildas. Den här informationen ger dig en uppfattning om vilken NSG: er är loggas och datumintervallet för flöden.

![infochart1][11]

![infochart2][12]

Den här mallen innehåller följande utsnitt så att du kan visa endast de data du är mest intresserad av. Du kan filtrera på resursgrupper, NSG: er och regler. Du kan också filtrera på 5-tuppel information beslut och den tid som loggen har skrivits.

![utsnitt][13]

## <a name="conclusion"></a>Slutsats

Beskrevs i det här scenariot att med hjälp av Network Security Group flöda loggarna som tillhandahålls av Nätverksbevakaren och Power BI, kommer du att visualisera och förstå trafiken. Med den angivna mallen Power BI hämtar loggarna direkt från lagring och bearbetar dem lokalt. Åtgången tid för att läsa in mallen varierar beroende på antalet filer som begärs och totala storleken på filerna hämtas.

Du kan anpassa den här mallen för dina behov. Det finns många flera olika sätt som du kan använda Power BI med Network Security Group flöda loggar. 

## <a name="notes"></a>Anteckningar

* Loggar som standard lagras i`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Om det finns andra data i en annan katalog måste de frågor till pull och bearbeta data ändras.

* Den angivna mallen rekommenderas inte för användning med mer än 1 GB loggar.

* Om du har en stor mängd loggar rekommenderar vi att du undersöker en lösning med hjälp av ett annat datalager som Data Lake eller SQL server.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualisera dina NSG flödet loggar med Elastick-stacken genom att besöka [visualisera Azure Network Watcher NSG flödet loggar med öppen källkod verktyg](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
