---
title: Offentliga datauppsättningar för Azure-analys
description: Lär dig mer om offentliga datauppsättningar som du kan använda för att prototyp och testa Azure analytics-tjänster och -lösningar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 0fe20c90cf857ce09a83c6ac53e6fe77eb512153
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528195"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Offentliga datamängder för testning och prototyper

Bläddra i den här listan med offentliga datauppsättningar för data som du kan använda för att prototyp- och testlagrings- och analystjänster och lösningar.

## <a name="us-government-and-agency-data"></a>Amerikanska regeringen och byrådata

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [Uppgifter från den amerikanska regeringen](https://catalog.data.gov/dataset) | Över 250.000 datamängder som täcker jordbruk, klimat, konsument, ekosystem, utbildning, energi, finans, hälsa, lokala myndigheter, tillverkning, maritima, hav, allmän säkerhet, och vetenskap och forskning i USA. | Filer av olika storlekar i olika format, inklusive HTML, XML, CSV, JSON, Excel och många andra. Du kan filtrera tillgängliga datauppsättningar efter filformat. |
| [Uppgifter om usa-folkräkning](https://www.census.gov/data.html) | Statistiska uppgifter om befolkningen i USA | Datauppsättningar finns i olika format. |
| [Jordvetenskap data från NASA](https://earthdata.nasa.gov/) | Över 32.000 datasamlingar som täcker jordbruk, atmosfär, biosfär, klimat, kryosfär, mänskliga dimensioner, hydrosfär, landyta, hav, sol-jord interaktioner och mycket mer. | Datauppsättningar finns i olika format. |
| [Flygförseningar och andra transportdata för flygbolag](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "U.S. Department of Transportation's (DOT) Bureau of Transportation Statistics (BTS) spårar prestandan i tid för inrikesflygningar som utförs av stora lufttrafikföretag. Sammanfattande information om antalet i tid, försenade, inställda och omdirigerade flygningar visas ... i sammanfattande tabeller publiceras på denna webbplats." | Filerna är i CSV-format. |
| [Trafikolyckor dödsfall - USA Dödsfall Analysis Reporting System (FARS)](https://www.nhtsa.gov/FARS) | "FARS är en rikstäckande folkräkning som ger NHTSA, kongressen, och den amerikanska offentliga årliga uppgifter om dödliga skador som lidits i motorfordon trafikolyckor." | "Skapa dina egna dödsdata som körs online med hjälp av FARS Query System. Eller ladda ner alla FARS-data från 1975 till present från FTP-webbplatsen." |
| [Giftiga kemiska data - EPA Toxicitet ForeCaster (ToxCast&trade;) data](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA: s mest uppdaterade, allmänt tillgängliga hög genomströmning toxicitet data om tusentals kemikalier. Dessa data genereras genom EPA: s ToxCast forskningsinsats." | Datauppsättningar är tillgängliga i olika format, inklusive kalkylblad, R-paket och MySQL-databasfiler. |
| [Giftiga kemiska data - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "Den 2014 Tox21 data utmaning är utformad för att hjälpa forskare att förstå potentialen hos de kemikalier och föreningar som testas genom toxikologi i 21st Century initiativet att störa biologiska vägar på ett sätt som kan resultera i toxiska effekter." | Datauppsättningar är tillgängliga i SMILES- och SDF-format. Uppgifterna ger "analys aktivitetsdata och kemiska strukturer på Tox21 insamling av ~ 10.000 föreningar (Tox21 10K)." |
| [Biotekniska och genomdata från NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Flera datamängder som täcker gener, genom och proteiner. | Datauppsättningar finns i text, XML, BLAST och andra format. En BLAST-app är tillgänglig. |

## <a name="other-statistical-and-scientific-data"></a>Andra statistiska och vetenskapliga data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [New York City taxi data](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxi resa poster inkluderar fält fånga pick-up och dropoff datum / tider, pick-up och dropoff platser, avstånd resa, specificerade biljettpriser, pristyper, betalningstyper och förare rapporterade passagerarantal." | Datauppsättningar finns i CSV-filer per månad. |
| [Microsoft Research-datauppsättningar - "Data Science for Research"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Flera datamängder som täcker interaktion mellan människa och dator, ljud/video, datautvinning/informationshämtning, geospatial/plats, bearbetning av naturligt språk och robotik/datorseende. | Datamängder är i olika format, zippade för nedladdning. |
| [Öppna datadata för datamoln](https://www.opensciencedatacloud.org/projects/) | "Open Science Data Cloud förser det vetenskapliga samfundet med resurser för att lagra, dela och analysera vetenskapliga datauppsättningar i terabyte och petabyte-skala."| Datauppsättningar finns i olika format. |
| [Globala klimatdata - WorldClim](https://worldclim.org/) | "WorldClim är en uppsättning globala klimatlager (inrutade klimatdata) med en rumslig upplösning på ca 1 km2. Dessa data kan användas för kartläggning och rumslig modellering." | Dessa filer innehåller geospatiala data. Mer information finns i [Dataformat](https://worldclim.org/formats1). |
| [Data om det mänskliga samhället - GDELT-projektet](https://www.gdeltproject.org/data.html) | "GDELT-projektet är den största, mest omfattande och högsta upplösning öppen databas av det mänskliga samhället som någonsin skapats." | Rådatafilerna är i CSV-format. |
| [Reklam klicka förutsägelse data för maskininlärning från Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Den största någonsin offentligt släppt ML datauppsättning." Mer information finns i [Criteos datauppsättning för 1 TB klicka på förutsägelse](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 text mining datauppsättning från Lemur Project](https://www.lemurproject.org/clueweb09.php/) | "Den ClueWeb09 datauppsättning skapades för att stödja forskning om informationshämtning och relaterade mänskliga språk teknik. Den består av cirka 1 miljard webbsidor på 10 språk som samlades in i januari och februari 2009." | Se [Information om datauppsättning](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Data om onlinetjänster

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [GitHub-arkiv](https://www.githubarchive.org/) | "GitHub Archive är ett projekt för att spela in den offentliga GitHub-tidslinjen [av händelser], arkivera den och göra den lättillgänglig för vidare analys." | Ladda ner JSON-kodade händelsearkiv i .gz-format (Gzip) från en webbklient. |
| [GitHub-aktivitetsdata från GHTorrent-projektet](http://ghtorrent.org/) | "GHTorrent-projektet [är] ett försök att skapa en skalbar, frågebar, offlinespegling av data som erbjuds via GitHub REST API. GHTorrent övervakar GitHubs tidslinje för offentliga evenemang. För varje händelse hämtar den dess innehåll och deras beroenden, uttömmande." | MySQL databas dumpar är i CSV-format. |
| [Stack spill datadump](https://archive.org/details/stackexchange) | "Detta är en anonymiserad dump av allt innehåll som användaren har bidragit med i Stack Exchange-nätverket [inklusive Stack Overflow]." | "Varje plats [såsom Stack Overflow] är formaterad som ett separat arkiv bestående av XML-filer zippade via 7-zip med bzip2 komprimering. Varje webbplats arkiv innehåller inlägg, användare, röster, kommentarer, PostHistory och PostLinks." |
