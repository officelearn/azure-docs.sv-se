---
title: "Användningsfall för data Factory - produktrekommendationer"
description: "Läs mer om ett användningsfall som implementeras med hjälp av Azure Data Factory tillsammans med andra tjänster."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e72dd426f7af3d1539aad6a3499d2ce5f792c152
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-case---product-recommendations"></a>Användningsfall - Produktrekommendationer
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite solution Accelerator-verktyg.  Se [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) sidan för ytterligare information om det här paketet. I det här dokumentet beskrivs vanliga användningsfall som Azure användare redan har löst och implementeras med hjälp av Azure Data Factory och andra Cortana Intelligence component services.

## <a name="scenario"></a>Scenario
Online återförsäljare vill ofta locka kunderna att köpa produkter genom att presentera dem med produkter som de är antagligen är intresserad av och därför sannolikt att köpa. För att åstadkomma detta online återförsäljare som behöver anpassa sina online användarupplevelse med hjälp av anpassade produktrekommendationer för den specifika användaren. Dessa anpassade rekommendationer ska göras baserat på deras aktuella och historiska shopping beteendedata, produktinformation, nyligen införda varumärken och produkt- och segmentering data.  Dessutom kan de ger användaren produktrekommendationer baserat på analys av den övergripande användning beteende från sina användare kombineras.

Syftet med dessa återförsäljare är att optimera för användaren klicka till försäljning konverteringar och få högre försäljningsintäkter.  De uppnå den här konverteringen genom att leverera kontextuella, beteende-baserade produktrekommendationer baserat på kundens intressen och åtgärder. För den här användningsfall använder vi online återförsäljare som ett exempel på företag som vill optimera för sina kunder. Men dessa principer som gäller för alla företag som vill engagera kunderna runt dess varor och tjänster och förbättra sina kunder köpa med anpassade produktrekommendationer.

## <a name="challenges"></a>Utmaningar
Det finns många UTMANINGAR den online återförsäljare sida vid försök att implementera den här typen av användningsfall. 

Data av olika storlekar och former måste först vara inhämtas från flera datakällor, både lokalt och i molnet. Dessa data innehåller produktdata och historiska beteende kundinformation användardata som användaren bläddrar online retail-platsen. 

Andra, anpassade produktrekommendationer måste rimligen och korrekt beräknas och förutsade. Förutom produkten, varumärken och beteende och webbläsare kundinformation måste online återförsäljare också innehålla kundfeedback tidigare inköp i fastställandet av produktrekommendationerna om bästa för användaren. 

Det tredje måste rekommendationerna vara direkt leverans till användaren för att ange ett sömlös surfning och köpa upplevelse och ger rekommendationer för senaste och mest relevanta. 

Slutligen måste återförsäljare mäta effektiviteten i deras metod genom att spåra övergripande upp säljer mellan säljer Klicka till konvertering försäljning lyckade och justera sina framtida rekommendationer.

## <a name="solution-overview"></a>Lösning: översikt
Det här exemplet användningsfall har lösas och implementeras av verkliga Azure-användare med hjälp av Azure Data Factory och andra Cortana Intelligence component services, inklusive [HDInsight](https://azure.microsoft.com/services/hdinsight/) och [Power BI](https://powerbi.microsoft.com/).

Online återförsäljaren använder ett Azure Blob-lagring, en lokal SQLServer, Azure SQL DB och en relationella datamart som deras alternativ för lagring av data i hela arbetsflödet.  Blob-arkivet innehåller kundinformation, beteende kunddata och produkten information data. Produkten information data innefattar produktinformation märke och en produkt katalogen lagras lokalt i en SQL data warehouse. 

Alla data är kombinerade och skickas till ett system med produkten rekommendation att leverera anpassade rekommendationer baserat på kundens intressen och åtgärder, medan användaren bläddrar produkter i katalogen på webbplatsen. Kunder kan också se produkter som är relaterade till de tittar på produkten baserat på totala webbplats användningsmönster som inte är relaterade till alla användare.

![Använd case diagram](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabyte rådata web loggfiler skapas varje dag från online återförsäljaren webbplats som halvstrukturerade filer. Rådata web-loggfiler och kataloginformationen kund och produkt är inhämtas regelbundet till ett Azure Blob storage med hjälp av Data Factory globalt distribuerade data flyttas som en tjänst. Rådata loggfilerna för dagen partitioneras (per år och månad) i blob storage för långsiktig lagring.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) används för att partitionera rådata loggfilerna i blobstore och bearbeta de infogade loggfilerna i stor skala med hjälp av både Hive och Pig-skript. Den partitionerade webbloggar data bearbetas sedan för att extrahera indata som behövs för machine learning rekommendation system för att skapa anpassade produktrekommendationer.

Systemets rekommendation för maskininlärning i det här exemplet är en öppen källkod machine learning rekommendation plattform från [Apache Mahout](http://mahout.apache.org/).  Alla [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eller anpassade modell kan tillämpas på scenariot.  Mahout modellen för att förutsäga likheten mellan objekt på webbplatsen utifrån övergripande användningsmönster och att skapa anpassade rekommendationer baserat på den enskilda användaren.

Slutligen flyttas resultatuppsättningen för anpassade produktrekommendationer till en relationella datamart för användning av webbplatsen återförsäljare.  Resultatuppsättningen kan även nås direkt från blob-lagring av ett annat program eller flyttats till flera butiker för andra användare och användningsfall.

## <a name="benefits"></a>Fördelar
Genom att optimera sina produkten rekommendation strategi och justera med affärsmål uppfyllt lösningen online återförsäljaren försäljning och marknadsföring mål. Dessutom kan kunde de operationalisera och hantera produkten rekommendation arbetsflödet på ett effektivt, tillförlitligt och kostnadseffektivt sätt. Metoden som gjort det lättare att uppdatera deras modellen och finjustera effektivitet baserat på försäljning Klicka till konvertering lyckade åtgärder. Med hjälp av Azure Data Factory kunde de Avbryt resurshantering sina tidskrävande och kostsamt manuell moln och gå till molnet på begäran resurshantering. Därför kan kunde de spara tid, pengar, och minska tid till lösningsdistribution. Övriga datavyer och operativa tjänstens hälsa blev enkelt att visualisera och felsöka med intuitiv Data Factory-övervakning och hantering användargränssnitt som är tillgängliga från Azure-portalen. Lösningen kan nu schemalagda och hanteras så att klar data på ett tillförlitligt sätt produceras och levereras till användare och data och bearbetning beroenden hanteras automatiskt utan mänsklig inblandning.

Genom att tillhandahålla anpassade shopping upplevelsen online återförsäljaren skapas en mer konkurrenskraftiga, spännande kund får och därför att öka försäljning och övergripande nöjda.

