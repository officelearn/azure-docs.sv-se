---
title: Börja utforska Metrics Advisor-demon
titleSuffix: Azure Cognitive Services
description: Lär dig mer om Metrics Advisor-webbgränssnittet med den demo vi tillhandahåller
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 39748bacf784af0b3f162f960cd7d87cbd03a2eb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047598"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Snabb start: utforska Metrics Advisor-demon med exempel data

> [!Note]
> Metrics Advisor-demon är skrivskyddad och du kommer inte att kunna publicera dina data. Om du vill använda tjänsten på dina data måste du först [skapa en Metrics Advisor-resurs](web-portal.md).

Använd den här artikeln för att snabbt börja utforska viktiga funktioner i Metrics Advisor. Vi tillhandahåller en demo webbplats med exempel data och förinställda konfigurationer, så att du kan bekanta dig med den helt aktuella webb portalen.

Klicka på [den här länken](https://aka.ms/MetricsAdvisor/Demo) för att gå till demonstrations webbplatsen.

## <a name="view-the-available-sample-data"></a>Visa tillgängliga exempel data

När du har loggat in på demonstrations webbplatsen visas en **datafeed** -sida. en datafeed är en logisk grupp tids serie data som efter frågas från data källan. Mer information om termer och begrepp som används i Metric Advisor finns i [ord](../glossary.md)listan. 

Det finns flera datafeeds i listan, som matas in från olika typer av data källor, till exempel Azure SQL Database eller Azure Table. Varje använder olika konfigurations inställningar för att ansluta till associerade data lager.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Exempel på data lista" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Utforska konfigurationen av datafeeds

Klicka på data flödet *exempel-Cost/intäkt-stad/kategori* . Du ser flera avsnitt med information om feeden:

* Namn på data flöde och inmatnings status.
* En lista över mått som har frågats från data källan. Till exempel *kostnad* och *intäkter*. 
* Aviserings historik för när datafeeden blir otillgänglig. 
* Loggar av när datafeeden uppdaterades.   
* Information och inställningar för data flöde.

:::image type="content" source="../media/data-feed-view.png" alt-text="Exempel på data lista" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Visa tids serie visualiseringar och konfigurationer

Klicka i *kostnads* måttet i *exemplet-Cost/intäkt-stad/kategori* datafeed. Du ser den associerade tids serien segmenterad med dimensioner, med visualiseringar enligt historiska mått data. Det blå bandet runt mått data representerar det förväntade värde intervallet från metrics Advisor Machine Learning-modeller. Punkter som faller utanför det här bandet markeras som röda punkter, vilket har identifierat avvikelser. 

:::image type="content" source="../media/series-visualization.png" alt-text="Exempel på data lista" lightbox="../media/series-visualization.png":::

Avvikelse identifieringen kan konfigureras genom att du justerar **konfigurationen för identifiering** på den vänstra sidan av mått informations sidan. Det finns flera metoder för avvikelse identifiering och du kan kombinera dem. Du kan också prova olika sensitivities, upptäcka vägvisningar och andra konfigurationer. Länken **Avancerad konfiguration** längst ned i identifiering av **konfigurationer** gör att du kan skapa mer komplexa och anpassade identifierings inställningar, som kan användas i grupper eller enskilda serier. 

Du kan också justera avvikelse identifiering genom att ge feedback till detektions algoritmen. Klicka i en avvikelse och Använd panelen **Lägg till feedback** för att konfigurera dess avvikelse status, säsongs beroende och status för ändrings punkt. Den här feedbacken kommer att ingå i identifieringen av framtida punkter.  

Längst ned på panelen **Lägg till feedback** finns en länk till **incident Hub**, som leder dig till analys sidan för incidenter och analyserar rotor saken för incidenten.  

:::image type="content" source="../media/incident-link.png" alt-text="Exempel på data lista" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Utforska avvikelse identifierings resultat och utför rotor Saks analys

När du klickar på länken **till incident Hub** från en avvikelse visas en incident analys sida som innehåller diagnostiska insikter om incidenten, till exempel allvarlighets grad, antalet avvikelser som ingår och start-/slut tid. I avsnittet **rotor saken** visas automatiserad rådgivning genom att analysera incident trädet, med beaktande av: avvikelse, distribution och bidrag till överordnade avvikelser, som kan vara den bakomliggande orsaken till incidenten.

Avsnittet **diagnostik** visar ett träd av incidenten, tillsammans med flera flikar för att diagnosticera incidenten.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Exempel på data lista" lightbox="../media/incident-diagnostic.png":::

Genom att hitta den bakomliggande orsaken till incidenten kan du vidta åtgärder och minimera problemet innan situationen blir sämre. Du kan också utforska mer insikter genom att klicka på de andra diagnostiska funktioner som tillhandahålls. 

## <a name="next-steps"></a>Nästa steg

- [Använda webbportalen](web-portal.md)
- [Använda REST API](rest-api.md)
- [Publicera dina data strömmar](../how-tos/onboard-your-data.md)
    - [Hantera datafeeds](../how-tos/manage-data-feeds.md)
    - [Konfigurationer för olika data källor](../data-feeds-from-different-sources.md)
