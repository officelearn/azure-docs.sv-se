---
title: Genomgång av förebyggande underhåll | Microsoft Docs
description: En genomgång av den förkonfigurerade lösningen för förebyggande underhåll i Azure IoT.
services: ''
suite: iot-suite
documentationcenter: ''
author: aguilaaj
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: araguila

---
# Genomgång av den förkonfigurerade lösningen för förebyggande underhåll
## Introduktion
Den förkonfigurerade lösningen för förebyggande underhåll i IoT Suite är en lösning från slutpunkt till slutpunkt för ett affärsscenario som beräknar punkten när det är troligt att ett fel uppstår. Du kan använda denna förkonfigurerade lösning proaktivt för aktiviteter, till exempel för att optimera underhåll. Lösningen kombinerar viktiga tjänster i Azure IoT Suite, inklusive arbetsytan [Azure Machine Learning][lnk_machine_learning]. Den här arbetsytan innehåller experiment som baseras på en offentlig provdatauppsättning att förutsäga återstående driftstid (RUL) för en flygplansmotor. Lösningen implementerar IoT-affärsscenariot som startpunkt när du planerar och implementerar en lösning som uppfyller dina specifika affärskrav.

## Logisk arkitektur
Följande diagram illustrerar de logiska komponenterna i den förkonfigurerade lösningen:

![][img-architecture]

De blå objekten är Azure-tjänster som är etablerade på den plats som du väljer när du etablerar den förkonfigurerade lösningen. Du kan etablera den förkonfigurerade lösningen i östra USA, Nordeuropa eller Östasien.

Vissa resurser är inte tillgängliga i de regioner där du etablerar den förkonfigurerade lösningen. De orangefärgade objekten i diagrammet representerar Azure-tjänster som är etablerade i den närmast tillgängliga regionen (södra centrala USA, Västeuropa eller Sydostasien) beroende på den valda regionen.

Det gröna objektet är en simulerad enhet som representerar en flygplansmotor. Mer information om dessa simulerade enheter finns i följande avsnitt.

De grå objekten representerar komponenter som implementerar funktioner för *enhetsadministration*. Den aktuella versionen av den förkonfigurerade lösningen för förebyggande underhåll etablerar inte dessa resurser. Om du vill veta mer om enhetsadministration läser du mer om [den förkonfigurerade lösningen för fjärrövervakning][lnk-remote-monitoring].

## Simulerade enheter
I den förkonfigurerade lösningen representerar en simulerad enhet en flygplansmotor. Lösningen etableras med två motorer som mappar till ett enda flygplan. Varje motor sänder fyra typer av telemetri: Sensor 9, Sensor 11, Sensor 14 och Sensor 15 tillhandahåller de data som behövs för att Machine Learning-modellen ska kunna beräkna motorns återstående driftstid. Varje simulerad enhet skickar följande telemetrimeddelanden till IoT Hub:

*Antal cykler*. En cykel representerar en slutförd flygning med varierande längd på mellan 2–10 timmar då telemetridata samlas in varje halvtimme under flygningens längd.

*Telemetri*. Det finns fyra sensorer som representerar motorattribut. Sensorerna har allmänna beteckningar: Sensor 9, Sensor 11, Sensor 14 och Sensor 15. Dessa 4 sensorer representerar en tillräcklig mängd telemetri som krävs för att få fram användbara resultat från Machine Learning-modellen för den återstående användbara livslängden. Den här modellen har skapats från en offentlig datauppsättning som innehåller verkliga data från motorsensorer. Mer information om hur modellen har skapats från den ursprungliga datauppsättningen finns i [mallen för förebyggande underhåll i Cortana-informationsgalleriet][lnk-cortana-analytics].

De simulerade enheterna kan hantera följande kommandon som skickas från en IoT-hubb:

| Kommando | Beskrivning |
| --- | --- |
| StartTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Startar enheten som skickar telemetri |
| StopTelemetry |Kontrollerar tillståndet för simuleringen.<br/>Stoppar enheten som skickar telemetri |

IoT Hub bekräftar enhetskommandona.

## Azure Stream Analytics-jobb
**Jobb: Telemetri** körs på den inkommande telemetriströmmen för enheten med hjälp av två uttryck. Det första uttrycket väljer all telemetri från enheterna och skickar dessa data till Blob Storage. Härifrån kan de sedan visualiseras i webbappen. Den andra instruktionen beräknar genomsnittliga sensorvärden under en glidande tvåminutersperiod och skickar dem via händelsehubben till en **händelseprocessor**.

## Händelseprocessor
**Händelseprocessorn** tar de genomsnittliga sensorvärdena från en slutförd cykel. Den skickar dessa värden till en API som får den Machine Learning-tränade modellen att beräkna RUL för en motor.

## Azure Machine Learning
Mer information om hur modellen har skapats från den ursprungliga datauppsättningen finns i [mallen för förebyggande underhåll i Cortana-informationsgalleriet][lnk-cortana-analytics].

## Vi sätter igång
Det här avsnittet beskriver komponenterna i lösningen, beskriver deras avsedda användning och innehåller exempel.

### Instrumentpanel för förebyggande underhåll
Den här sidan i webbappen använder PowerBI JavaScript-kontroller (finns i [PowerBI-visuals-databasen][lnk-powerbi]) för att visualisera:

* Utdata från Stream Analytics-jobben i Blob Storage.
* Den återstående användbara livslängden och antalet cykler per flygplansmotor.

### Observera molnlösningens beteende
I Azure Portal navigerar du till resursgruppen med det lösningsnamn som du har valt för att visa dina etablerade resurser.

![][img-resource-group]

När du etablerar den förkonfigurerade lösningen kan du få ett e-postmeddelande med en länk till Machine Learning-arbetsytan. Du kan också navigera till Machine Learning-arbetsytan från sidan [azureiotsuite.com][lnk-azureiotsuite] för din etablerade lösning när den har statusen **Klar**.

![][img-machine-learning]

På lösningsportalen kan du se att exemplet etableras med fyra simulerade enheter som representerar två flygplan med två motorer per plan, var och en med fyra sensorer. När du navigerar till lösningsportalen stoppas simuleringen.

![][img-simulation-stopped]

Klicka på **Starta simulering** för att starta simuleringen där du kan se hur sensorhistorik, RUL, cykler och RUL-historik fyller instrumentpanelen.

![][img-simulation-running]

Om RUL är mindre än 160 (ett godtyckligt tröskelvärde som valts i demonstrationssyfte) visas en varningssymbol bredvid RUL-skärmen på lösningsportalen och flygplansmotorn visas i gult. Lägg märke till att RUL-värdena har en nedåtgående trend generellt, men med många upp- och nedgångar. Detta mönster beror på de olika cykellängderna och modellens precision.

![][img-simulation-warning]

Den fullständiga simuleringen tar 35 minuter för att slutföra 148 cykler. RUL-tröskelvärdet på 160 nås för första gången vid cirka 5 minuter och båda motorerna når tröskelvärdet ungefär vid 8 minuter.

Simuleringen kör igenom den fullständiga datauppsättningen i 148 cykler och bestämmer den slutliga återstående användbara livslängden och cykelvärdena.

Du kan stoppa simuleringen när du vill, men om du klickar på **Starta simulering** spelas simuleringen upp igen från början av datauppsättningen.

## Nästa steg
Nu när du har kört den förkonfigurerade lösningen kanske du vill ändra den. Mer information finns i [Vägledning för att anpassa förkonfigurerade lösningar][lnk-customize].

Blogginlägget [IoT Suite - Under The Hood - Predictive Maintenance](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) på TechNet innehåller mer information om den förkonfigurerade lösningen för förebyggande underhåll.

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Vanliga frågor och svar om IoT Suite][lnk-faq]
* [IoT säkerhet från grunden][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Sep16_HO3-->


