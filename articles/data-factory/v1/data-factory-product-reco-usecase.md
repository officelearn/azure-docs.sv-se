---
title: Användningsfall för datafabrik – produktrekommendationer
description: Lär dig mer om ett användningsfall som implementerats med hjälp av Azure Data Factory tillsammans med andra tjänster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139146"
---
# <a name="use-case---product-recommendations"></a>Användningsfall - Produktrekommendationer
Azure Data Factory är en av många tjänster som används för att implementera Cortana Intelligence Suite med lösningsacceleratorer.  Mer information om den här sviten finns på sidan [Cortana Intelligence Suite.](https://www.microsoft.com/cortanaanalytics) I det här dokumentet beskriver vi ett vanligt användningsfall som Azure-användare redan har löst och implementerat med Hjälp av Azure Data Factory och andra Cortana Intelligence-komponenttjänster.

## <a name="scenario"></a>Scenario
Online-återförsäljare vill ofta locka sina kunder att köpa produkter genom att presentera dem med produkter som de är mest benägna att vara intresserade av, och därför mest sannolikt att köpa. För att åstadkomma detta måste online-återförsäljare anpassa sina användares online-upplevelse genom att använda personliga produktrekommendationer för den specifika användaren. Dessa personliga rekommendationer ska göras baserat på deras nuvarande och historiska shoppingbeteendedata, produktinformation, nyligen introducerade varumärken samt produkt- och kundsegmenteringsdata.  Dessutom kan de ge användarproduktrekommendationerna baserat på analys av övergripande användningsbeteende från alla användare tillsammans.

Målet med dessa återförsäljare är att optimera för användare klicka-till-försäljning omvandlingar och tjäna högre försäljningsintäkter.  De uppnår den här konverteringen genom att leverera kontextuella, beteendebaserade produktrekommendationer baserade på kundens intressen och åtgärder. I det här användningsfallet använder vi onlineåterförsäljare som ett exempel på företag som vill optimera för sina kunder. Dessa principer gäller dock för alla företag som vill engagera sina kunder kring sina varor och tjänster och förbättra sina kunders köpupplevelse med personliga produktrekommendationer.

## <a name="challenges"></a>Utmaningar
Det finns många utmaningar som online-återförsäljare står inför när man försöker genomföra denna typ av användningsfall. 

För det första måste data av olika storlekar och former intas från flera datakällor, både lokalt och i molnet. Dessa data omfattar produktdata, historiska kundbeteendedata och användardata när användaren bläddrar på webbplatsen för onlinebutik. 

För det andra måste personliga produktrekommendationer vara rimligt och korrekt beräknade och förutsedda. Förutom produkt, varumärke, och kundbeteende och webbläsardata, online-återförsäljare måste också inkludera kundfeedback på tidigare inköp för att faktor i fastställandet av de bästa produktrekommendationerna för användaren. 

För det tredje måste rekommendationerna omedelbart kunna levereras till användaren för att ge en sömlös webb- och inköpsupplevelse och ge de senaste och relevanta rekommendationerna. 

Slutligen, återförsäljare måste mäta effektiviteten i sin strategi genom att spåra övergripande up-sell och korsförsäljning klicka-till-konvertering försäljningsframgångar, och anpassa sig till sina framtida rekommendationer.

## <a name="solution-overview"></a>Lösning: översikt
Det här exemplet användningsfall har lösts och implementerats av riktiga Azure-användare med hjälp av Azure Data Factory och andra Cortana Intelligence-komponenttjänster, inklusive [HDInsight](https://azure.microsoft.com/services/hdinsight/) och [Power BI](https://powerbi.microsoft.com/).

Onlineåterförsäljaren använder en Azure Blob-butik, en lokal SQL-server, Azure SQL DB och en relationsdataaffär som sina datalagringsalternativ i hela arbetsflödet.  Blob-arkivet innehåller kundinformation, kundbeteendedata och produktinformationsdata. Produktinformationsdata omfattar produktvarumärkesinformation och en produktkatalog som lagras lokalt i ett SQL-datalager. 

Alla data kombineras och matas in i ett produktrekommendationssystem för att leverera personliga rekommendationer baserat på kundens intressen och åtgärder, medan användaren bläddrar bland produkter i katalogen på webbplatsen. Kunderna ser också produkter som är relaterade till den produkt de tittar på baserat på övergripande webbplatsanvändningsmönster som inte är relaterade till en användare.

![använda ärendediagram](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabyte av råa webbloggfiler genereras dagligen från online-återförsäljarens webbplats som semi-strukturerade filer. De råa webbloggfilerna och informationen om kund- och produktkatalogen förtärs regelbundet i en Azure Blob-lagring med Data Factorys globalt distribuerade dataförflyttning som en tjänst. Dagens råloggfiler partitioneras (efter år och månad) i blob-lagring för långsiktig lagring.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) används för att partitionera råloggfilerna i blob-arkivet och bearbeta de intagna loggarna i stor skala med både Hive- och Pig-skript. De partitionerade webbloggdata bearbetas sedan för att extrahera de nödvändiga ingångarna för ett machine learning-rekommendationssystem för att generera de personliga produktrekommendationerna.

Rekommendationssystemet som används för maskininlärning i det här exemplet är en rekommendationsplattform för maskininlärning med öppen källkod från [Apache Mahout](https://mahout.apache.org/).  Alla [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eller anpassade modeller kan tillämpas på scenariot.  Mahout-modellen används för att förutsäga likheten mellan objekt på webbplatsen baserat på övergripande användningsmönster och för att generera de personliga rekommendationerna baserat på den enskilda användaren.

Slutligen flyttas resultatuppsättningen av personliga produktrekommendationer till ett relationsdatavar för konsumtion av återförsäljarens webbplats.  Resultatuppsättningen kan också nås direkt från blob-lagring av ett annat program, eller flyttas till ytterligare butiker för andra konsumenter och användningsfall.

## <a name="benefits"></a>Fördelar
Genom att optimera sin produktrekommendationsstrategi och anpassa den till affärsmålen uppfyllde lösningen online-återförsäljarens merchandising- och marknadsföringsmål. Dessutom kunde de operationalisera och hantera arbetsflödet för produktrekommendation på ett effektivt, tillförlitligt och kostnadseffektivt sätt. Tillvägagångssättet gjorde det enkelt för dem att uppdatera sin modell och finjustera dess effektivitet baserat på måtten på försäljning klicka-till-konvertering framgångar. Genom att använda Azure Data Factory kunde de överge sin tidskrävande och dyra manuella molnresurshantering och gå över till molnresurshantering på begäran. Därför kunde de spara tid, pengar och minska sin tid till lösningsdistribution. Datahärstamningsvyer och hälsotillstånd för driftstjänster blev lätta att visualisera och felsöka med det intuitiva datafabriksgränssnittet som är tillgängligt från Azure-portalen. Deras lösning kan nu schemaläggas och hanteras så att färdiga data produceras och levereras på ett tillförlitligt sätt till användare, och data- och bearbetningsberoenden hanteras automatiskt utan mänsklig inblandning.

Genom att tillhandahålla denna personliga shoppingupplevelse skapade online-återförsäljaren en mer konkurrenskraftig, engagerande kundupplevelse och ökar därför försäljningen och den totala kundnöjdheten.

