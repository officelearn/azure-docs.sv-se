---
title: Azure Time Series Insights – översikt | Microsoft Docs
description: Azure Time Series Insights – översikt
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/03/2018
ms.openlocfilehash: 83513ab9f7bee5c51fc909e5f4246105b6f145ca
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876139"
---
# <a name="azure-time-series-insights-overview"></a>Azure Time Series Insights – översikt

Azure Time Series Insights (TSI) är en plattform som en tjänst slutpunkt till slutpunkt för att mata in, lagra och köra frågor för mycket kontextualiserade tidsserieoptimerade data i IoT-skala. Därför passar Azure TSI perfekt för både ad hoc-datagranskning och operativ analys. TSI är en unikt utbyggbar, anpassad tjänst som uppfyller de omfattande behoven för industriella IoT-distributioner.

## <a name="what-is-iot-data"></a>Vad är IoT-data?

IoT-data är alla ”industriella” data som finns i resursintensiva organisationer. IoT-data är ofta ostrukturerade eftersom de skickas från en mängd olika tillgångar som registrerar mått med ganska mycket störningar som temperatur, rörelser och luftfuktighet. Dessutom kännetecknas dessa dataströmmar ofta av stora luckor, skadade meddelanden och falska värden. Data från dessa strömmar måste rensas innan någon analys kan ske. IoT-data har ofta bara betydelse i samband med ytterligare datainmatningar som kommer från datakällor från första part (t.ex. CRM eller ERP) eller tredje part (t.ex. väder eller plats).

Därför används bara en bråkdel av dessa data för drifts- och affärsbehov. Sådana data ger konsekvent, omfattande, aktuell och korrekt information för företagsrapportering och -analys. Att omvandla insamlade IoT-data till användbara insikter kräver därför några av de viktigaste funktionerna:

* Databehandling för att rensa, filtrera, interpolera, transformera och förbereda data för analys
* Struktur för att navigera och förstå data (för att normalisera och kontextualisera data)
* Kostnadseffektiv lagring för lång/obegränsad kvarhållning för bearbetade (härledda) data och rådata från flera årtionden

Vanligt IoT-dataflöde kan beskrivas på följande sätt:

  ![IoT-dataflöde][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights för industriell IoT

Dagens IoT datalandskap är mångskiftande. Det innehåller kunder inom tillverkning, bilindustrin, olja och gas, energi, smarta byggnader och rådgivning. Scenarier omfattar ad hoc-datagranskning där formen för data är okänd samt operativ analys över schematiserade (uttryckligen modellerade) data för att driva operativ effektivitet. Dessa scenarier finns normalt bredvid varandra och stöder olika användningsfall. Plattformsfunktioner som lagring i flera nivåer (varm och kall) förmågan att lagra tidsseriedata för flera årtionden och möjligheten att uttryckligen modellera och optimera frågor för tillgångsbaserad driftsinformation blir nyckeln till framgång för IoT-företag och deras digitala revolution.

Azure TSI är en heltäckande plattform som en tjänst slutpunkt till slutpunkt för både IoT-datagranskning och åtgärdsinformation. Time Series Insights är en fullständigt hanterad molntjänst för analys av tidsseriedata i IoT-skala.

Kunder kan lagra rådata i ett schemalöst, minnesinternt arkiv och köra interaktiva ad hoc-frågor genom en distribuerad frågemotor och ett API samt utnyttja vår funktionsrika användarmiljö för att visualisera flera miljarder händelser på några få sekunder. Läs mer om våra [datagranskningsfunktioner](./time-series-insights-overview.md).

TSI tillhandahåller även funktioner för åtgärdsinformation som för närvarande är en allmänt tillgänglig förhandsversion. Tillsammans med interaktiv datagranskning och driftsinformation kan kunder med Time Series Insights härleda mer värde ur data som samlas in från IoT-tillgångar. Mer specifikt stöder den allmänt tillgängliga förhandsversionen följande viktiga funktioner:

* En skalbar, prestandaoptimerad och kostnadsoptimerad tidsseriedatalagring som gör det möjligt för en molnbaserad IoT-lösning att se trender för flera års tidsseriedata på några sekunder.
* Stöd för semantikmodell för att beskriva domänen och metadata som associeras till de härledda och icke-härledda signalerna från tillgångar och enheter.
* En förbättrad användarupplevelse som kombinerar tillgångsbaserade datainsikter med omfattande ad hoc-dataanalys för att driva business intelligence och driftsinformation
* Integrering med avancerade maskininlärnings- och analysverktyg som Azure Databricks, Apache Spark, Azure Machine Learning, Jupyter notebooks, Power BI osv. för att hjälpa kunder att hantera utmaningar med tidsseriedata och driva operativ effektivitet.

Tillsammans tillhandahålls åtgärdsinformation och datagranskning med en enkel användningsbaserad prismodell för databehandling, lagring och frågor, vilket ger kunder en mer skalbar modell för att passa föränderliga affärsbehov.

Nedanför är ett dataflödesdiagram på hög nivå som visar de uppdaterade funktionerna:

  ![De viktigaste funktionerna][2]

Med introduktionen av dessa viktiga funktioner för industriell IoT tillhandahåller Azure TSI följande viktiga fördelar.

* Lagring på flera nivåer för tidsseriedata i IoT-skala

  * Med en vanlig databehandlingspipeline för inmatning av data kan kunder lagra data i varm lagring för interaktiva frågor och/eller kall lagring för att lagra stora mängder data och utnyttja tillgångsbaserade frågor med mycket höga prestanda.

  * Dynamisk routning i lagringsnivåer kommer snart.

* Tidsseriemodell för kontextualisering av råtelemetridata och härleda tillgångsbaserade insikter

  * Kunder kan kontextualisera råtelemetridata med beskrivande tidsseriemodell och härleda avancerad driftsinformation med mycket prestanda- och kostnadsoptimerade enhetsbaserade frågor.

* Smidig integrering med andra datalösningar
  
  * Eftersom data i Azure Time Series Insights lagras i Apache Parquet-filer med öppen källkod kan kunder enkelt integrera med andra datalösningar (från första och tredje part) för scenarier slutpunkt till slutpunkt, som business intelligence, avancerad maskininlärning, förutsägelseanalys osv.

* Datagranskning i nära realtid

  * Användargränssnittet i Azure Time Series Insights Explorer innehåller visualisering för alla data som strömmas genom datainmatningspipeline. Kort efter att ha anslutit en händelsekälla kan kunderna visa, utforska och köra frågor på händelsedata för att validera om en enhet genererar data som förväntat och övervaka en IoT-tillgång för hälsa, produktivitet och övergripande effektivitet.

* Rotorsaksanalys och avvikelseidentifiering

  * Azure Time Series Insights Explorer stöder mönster och perspektivvyer för att genomföra och spara rotorsaksanalys i flera steg. I kombination med Azure Stream Analytics kan kunder använda Time Series Insights för att upptäcka varningar och avvikelser i nära realtid.

* Skapa anpassade program på Time Series Insights-plattformen

  * Azure Time Series Insights stöder JavaScript SDK med omfattande kontroller och förenklad åtkomst till frågor så att kunder kan skapa anpassade IoT-appar på Time Series Insights-plattformen för att passa enskilda företags behov.
  * Kunder kan även använda fråge-API:er för Time Series Insights för att driva data till IoT-program.

## <a name="next-steps"></a>Nästa steg

Du är redo att komma igång med Azure TSI (förhandsversion):

> [!div class="nextstepaction"]
> [Läs snabbstartsguiden](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png