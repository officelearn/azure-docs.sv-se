---
title: Data Factory användningsfall - produktrekommendationer
description: Läs mer om en användningsfall som implementeras med hjälp av Azure Data Factory tillsammans med andra tjänster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8ff100cd3fc1c9def10b4e585119414281b90d92
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017386"
---
# <a name="use-case---product-recommendations"></a>Användningsfall - Produktrekommendationer
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite med Lösningsacceleratorer.  Se [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) för information om de här. I det här dokumentet beskrivs ett vanligt användningsfall som Azure-användare som redan har löst och implementeras med hjälp av Azure Data Factory och andra tjänster för Cortana Intelligence-komponenten.

## <a name="scenario"></a>Scenario
Onlineåterförsäljare vill ofta locka sina kunder att köpa produkter genom att presentera dem med produkter som de är antagligen är intresserad av och därför sannolikt att köpa. Onlineåterförsäljare behöver anpassa sina online användarupplevelsen med hjälp av anpassade produktrekommendationer för den specifika användaren för att åstadkomma detta. De här personanpassade rekommendationer ska fattas baserat på deras aktuella och historiska shopping beteendedata, produktinformation, nyligen införda varumärken och produkt- och segmentering data.  Dessutom får användaren produktrekommendationer baserat på analyser av övergripande användningsbeteende från sina användare kombineras.

Målet med dessa återförsäljare är att optimera för användaren klickar på till försäljning konverteringar och du betalar, desto högre försäljningsintäkter.  De kan få den här konverteringen genom att tillhandahålla sammanhangsberoende, beteende-baserad produktrekommendationer baserat på kundernas intresse och åtgärder. Det här användningsfallet används onlineåterförsäljare som ett exempel på företag som vill optimera för sina kunder. Dessa principer gäller dock för alla verksamheter som vill engagera kunderna runt dess varor och tjänster och förbättra sina kunders köpupplevelsen med anpassade produktrekommendationer.

## <a name="challenges"></a>Utmaningar
Det finns många UTMANINGAR den sida onlinebutiker vid försök att implementera den här typen av användningsfall. 

Data av olika storlekar och former måste först matas in från flera källor, både lokalt och i molnet. Dessa data innehåller produktdata och historiska kunddata för beteende användardata som användaren bläddrar onlinebutiker platsen. 

Andra, anpassade produktrekommendationer måste rimligen och korrekt beräknas och förutse. Förutom produkt, varumärke och kundernas beteende och webbläsaren data måste också onlineåterförsäljare för kundfeedback tidigare inköp i fastställandet av de bästa produktrekommendationerna för användaren. 

Det tredje måste rekommendationerna vara direkt leverans för användaren att ge en sömlös Bläddra och köpa upplevelse och ger rekommendationer för de senaste och mest relevanta. 

Slutligen måste återförsäljare Mät effektiviteten i sina metoden genom att spåra övergripande merförsäljning och korsförsäljning klickar du på att konvertering försäljning lyckade och anpassa sig till sina framtida rekommendationer.

## <a name="solution-overview"></a>Lösningsöversikt
Det här exemplet användningsfallet har löst och implementeras av riktig Azure-användare med hjälp av Azure Data Factory och andra Cortana Intelligence-Komponenttjänster, inklusive [HDInsight](https://azure.microsoft.com/services/hdinsight/) och [Power BI](https://powerbi.microsoft.com/).

Näthandelsföretagen använder en Azure Blob store, en lokal SQLServer, Azure SQL DB och en relationella datamart som deras alternativen för datalagring i hela arbetsflödet.  Blob-arkivet innehåller kundinformation, beteende kunddata och produktdata för information. Produkten information innehåller produktinformation varumärke och en produkt katalogisera lagras lokalt i ett SQL data warehouse. 

Alla data kombineras och skickas till en produkt rekommendation systemet att leverera personligt anpassade rekommendationer baserat på kundernas intresse och åtgärder, medan användaren bläddrar produkter i katalogen på webbplatsen. Kunderna kan också se produkter som är relaterade till de tittar på produkten baserat på övergripande webbplats användningsmönster som inte är relaterade till någon en användare.

![använda diagram över ärende](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabyte raw web loggfiler genereras per dag från den näthandelsföretagen webbplats som semistrukturerade filer. Rå web-loggfiler och kataloginformation kunden och produkten matas regelbundet in i Azure Blob storage med hjälp av Data Factorys globalt distribuerade data flyttas som en tjänst. Råa loggfiler för dagen partitioneras (per år och månad) i blob storage för långsiktig lagring.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) används för att partitionera råa loggfiler i blob store och bearbeta inmatade loggar i stor skala med både Hive och Pig-skript. Den partitionerade webbloggar data bearbetas sedan för att extrahera nödvändiga indata för en rekommendation system för att generera personliga produktrekommendationer för maskininlärning.

Rekommendationen systemet används för maskininlärning i det här exemplet är en maskininlärningsfunktion rekommendation plattform från [Apache Mahout](http://mahout.apache.org/).  Alla [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eller anpassade modellen kan tillämpas på scenariot.  Mahout modellen används för att förutsäga likheten mellan objekt på den webbplats som baseras på allmänna användningsmönster och för att ge personanpassade rekommendationer baserat på den enskilda användaren.

Slutligen flyttas resultatuppsättningen för personliga produktrekommendationer till en relationella datamart för användning med webbplatsen återförsäljaren.  Resultatuppsättningen kan även nås direkt från blob-lagring av ett annat program eller flyttas till ytterligare lager för andra konsumenter och användningsfall.

## <a name="benefits"></a>Fördelar
Genom att optimera sin produkt rekommendation strategi och justerar detta till affärsmålen uppfyllt lösningen den näthandelsföretagen försäljning och marknadsföring mål. Dessutom har de operationalisera och hantera produkten rekommendation arbetsflödet på ett effektivt, tillförlitlig och kostnadseffektivt sätt. Metoden som gjort det lättare att uppdatera sin modell och finjustera dess effektivitet baserat på mått för försäljning klickar du på att konvertering lyckade försök. Med hjälp av Azure Data Factory kan kunna de lämna sina tidsödande och dyrt manuell cloud-resurshantering och flytta till hantering av molnresurser på begäran. Därför var de kan spara tid, pengar och minska tiden till distribution. Härkomst datavyer och operativa tjänsthälsa blev enkelt att visualisera och felsöka med intuitiva Data Factory-övervakning och hantering av användargränssnitt som är tillgängliga från Azure-portalen. Deras lösning kan nu schemalagda och hanteras så att klar data på ett tillförlitligt sätt produceras och levereras till användare och data och bearbetning av beroenden hanteras automatiskt utan mänsklig inblandning.

Genom att tillhandahålla den här anpassade handelsmiljö näthandelsföretagen som skapade sin konkurrenskraft, engagerande upplevelse och därför att öka försäljning och hela kundnöjdhet.

