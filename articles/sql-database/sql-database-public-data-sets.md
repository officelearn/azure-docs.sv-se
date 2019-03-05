---
title: Offentliga datauppsättningar för Azure analys | Microsoft Docs
description: Läs mer om offentliga datauppsättningar som du kan använda som prototyp och testa Azure analytics tjänster och lösningar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/01/2018
ms.openlocfilehash: 4963114f3646453510e20c7071157c952e52c40f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307147"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Offentliga datauppsättningar för testning och prototyper

Bläddra i den här listan över offentliga datauppsättningar för data som du kan använda för att skapa prototyper och testa lagring och analys tjänster och lösningar.

## <a name="us-government-and-agency-data"></a>Amerikansk Myndigheter och agency data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [US Government data](https://www.census.gov/data.html) | Över 190,000 datauppsättningar som täcker jordbruk, klimat, konsument, ekosystem, utbildning, energi, ekonomi, hälsa, lokala myndigheter, tillverkning, havsbaserade, hav, allmän säkerhet och vetenskap och undersök i USA | Filer i olika storlekar i olika format, även HTML-, XML, CSV, JSON, Excel och många andra. Du kan filtrera tillgängliga datauppsättningar för filformatet. |
| [USA insamlade data](https://www.census.gov/data.html) | Statistiska data om befolkningen i USA | Uppgifter som är i olika format. |
| [Earth science data från NASA](https://earthdata.nasa.gov/) | Över 32 000 datasamlingar som täcker jordbruk, atmosfär, biosfärreservat, klimat, cryosphere, mänskliga dimensioner, hydrosphere, mark surface, hav, sun earth interaktioner med mera. | Uppgifter som är i olika format. |
| [Flygbolag flygförseningar och andra transport-data](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "The U.S. : S Transportmyndighet är (punkt) som administreras av transport-statistik (BTS) spårar punktlighetsinformation inrikes flygningar drivs av stort flygbolag. Sammanfattningsinformation för antalet i tid, fördröjd, avbrutna och distribueras flyg visas... i översikt över tabeller som publiceras på den här webbplatsen ”. | Filer är CSV-format. |
| [Trafik skadades - oss Fatality Analysis Reporting System (FARS)](http://www.nhtsa.gov/FARS) | ”FARS är en nationell inventering ger NHTSA, den amerikanska kongressen och, American offentliga årliga data om allvarliga skador haft i fordon trafik krascher”. | ”Skapa dina egna fatality data kör online genom att använda FARS fråga systemet. Eller ladda ned alla FARS data från 1975 att presentera från FTP-platsen ”. |
| [Giftiga kemiska data – EPA toxicitet prognosmodell (ToxCast™)-data](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | ”Miljövårdsmyndighetens mest uppdaterade, offentligt tillgängliga högt dataflöde toxicitet data på tusentals kemikalier. Dessa data skapas via den EPA ToxCast research arbete ”. | Uppgifter som är tillgängliga i olika format, inklusive kalkylblad, R-paket och MySQL-databasfiler. |
| [Giftiga kemiska data – NIH Tox21 Data utmaning 2014](https://tripod.nih.gov/tox21/challenge/) | ”2014 Tox21 data utmaningen är utformad för att forskare införstådd med risken för kemikalier och föreningar som har testats via toxikologi i initiativet 2000-talet att störa bioteknik sökvägar på ett sätt som kan leda till Giftverkningar”. | Uppgifter som är tillgängliga i LEENDEN och SDF format. Data innehåller ”assay aktivitetsdata och kemiska strukturer på Tox21 uppsättning ~ 10 000 föreningar (Tox21 10K)”. |
| [Bioteknik och genome data från NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Flera datauppsättningar som täcker gener och genomer proteiner. | Uppgifter som är i text, XML, POSTKAMPANJ och andra format. En POSTKAMPANJ appen är tillgänglig. |

## <a name="other-statistical-and-scientific-data"></a>Andra statistiska och vetenskapliga data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [New York City taxi-data](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | ”Taxi resans posterna innehåller fält som avbildning du vill hämta och dropoff datum/tider, vill hämta och dropoff platser, resa avstånd, specificerade priser, priset typer, betalningstyper och drivrutinen rapporterat passagerartrafik räknar”. | Uppgifter som är i CSV-filer per månad. |
| [Microsoft Research datauppsättningar – ”Data Science för forskning”](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Flera datauppsättningar som täcker datorn interaktion, ljud/video, data-utvinning/informationshämtning, geospatiala/plats, bearbetning av naturligt språk och robotteknik/visuellt. | Uppgifter som är i olika format, zippade för hämtning. |
| [Offentliga genom data](http://www.completegenomics.com/public-data/) | ”En mängd olika människor genomer data är tillgängligt för allmänt bruk att förbättra den stora uppsättningar genomiska analys...” Providern, fullständig Genomics är en privat vinstdrivande corporation. | Datauppsättningar, är efter extrahering, i textformat för UNIX. Analysverktyg är också tillgängliga. |
| [Öppna Science Data molndata](https://www.opensciencedatacloud.org/) | ”Öppna Science Data molnet erbjuder vetenskapliga community med resurser för att lagra, dela och analysera terabyte och petabyte-skala vetenskapliga datauppsättningar”.| Uppgifter som är i olika format. |
| [Global klimat-data – WorldClim](http://worldclim.org/) | ”WorldClim är en uppsättning globala klimat lager (förnätade klimat data) med en spatial upplösning på ca 1 km2. Dessa data kan användas för mappning och rumsliga modellering ”. | De här filerna innehåller geospatiala data. Mer information finns i [dataformat](http://worldclim.org/formats1). |
| [Data om mänskliga society – The GDELT projekt](http://www.gdeltproject.org/data.html) | ”GDELT projektet är störst, mest omfattande och högsta upplösning öppna databasen för mänskliga society gång har skapat”. | De rådata-filerna finns i CSV-format. |
| [Reklam klickar du på förutsägelsedata för maskininlärning från Criteo](http://labs.criteo.com/2013/12/download-terabyte-click-logs/) | ”Den största någonsin offentligt utgivna ML datauppsättningen”. Mer information finns i [Criteo's 1 TB klickar du på förutsägelse datauppsättning](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09 text utvinning datauppsättning från The Lemur projekt](http://www.lemurproject.org/clueweb09.php/) | ”ClueWeb09 datauppsättningen har skapats för att stödja research på informationshämtning och relaterade mänskliga språk tekniker. Den består av cirka 1 miljard webbsidor i 10 språk som samlades in i januari och februari 2009 ”. | Se [Datamängdsinformation](http://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online-tjänstdata

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [GitHub-Arkiv](https://www.githubarchive.org/) | ”GitHub är ett projekt för att registrera den offentliga GitHub-tidslinjen [av händelser], arkivera den och göra den lättillgängliga för vidare analys”. | Hämta JSON-kodad händelse Arkiv i .gz (Gzip)-format från en webbklient. |
| [GitHub-aktivitetsdata från The GHTorrent projekt](http://ghtorrent.org/) | ”GHTorrent projektet är [] för att skapa en skalbar, frågningsbart, offline spegling av data som erbjuds via GitHub REST API. GHTorrent övervakar tidslinjen för GitHub publik händelse. För varje händelse hämtar den innehållet och deras beroenden, omfattande ”. | MySQL-databas Dumpar är CSV-format. |
| [Stack Overflow datadumpen](https://archive.org/details/stackexchange) | ”Det är en maskerade dump av allt innehåll som har bidragit med användare i nätverket Stack Exchange [inklusive Stack Overflow]”. | ”Varje plats [till exempel Stack Overflow] är formaterade som ett separat Arkiv som består av XML-filer zippade 7 zip-komprimering för bzip2. Varje plats-arkivet innehåller inlägg, användare, röster, kommentarer, PostHistory och PostLinks ”. |
