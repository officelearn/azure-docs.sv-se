---
title: "Offentliga datamängder för Azure analytics | Microsoft Docs"
description: "Läs mer om allmänna uppgifter som att du kan använda som prototyp och testa Azure Analystjänster och lösningar."
services: sql-database
documentationcenter: 
author: douglaslMS
manager: craigg
editor: 
tags: 
ms.custom: reference
ms.assetid: 
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 03/20/2017
ms.author: douglasl
ms.openlocfilehash: ffb8c7cb4a5337e075c69f5e74552f7ead7c7565
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Offentliga datauppsättningar för testning och prototyper

Bläddra i listan över offentliga datauppsättningar för data som du kan använda för att prototyp och testa lagring och analytics-tjänster och lösningar.

## <a name="us-government-and-agency-data"></a>Amerikansk Sektorn och agency data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [Amerikanska myndigheter data](https://www.census.gov/data.html) | Över 190,000 datauppsättningar som omfattar jordbruket, klimatförändringar, konsumenten, ekosystem, education, energi, ekonomi, hälsa, lokala myndigheter, tillverkning, sjöfartsorganisationen, oceanen, allmän säkerhet och vetenskap och forskning i USA | Filer med olika storlekar i olika format, inklusive HTML-, XML-, CSV, JSON, Excel och många andra. Du kan filtrera tillgängliga datauppsättningar av filformatet. |
| [USA inventering data](https://www.census.gov/data.html) | Statistisk information om befolkningen i USA | Uppgifter som är i olika format. |
| [Earth vetenskap data från NASA](https://earthdata.nasa.gov/) | Över 32000 datasamlingar som omfattar jordbruket, luften, biosfärreservat, klimatförändringar, cryosphere, mänsklig dimensioner, hydrosphere, mark yta, oceans, sun earth interaktioner, och mer. | Uppgifter som är i olika format. |
| [Flygbolag svarta fördröjningar och andra data som transport](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | ”USA Avdelning transport har (punkt) som administreras av transport statistik (BTS) spårar prestanda i tid för nationella flygplan drivs av stora flygbolag. Sammanfattningsinformation för antalet i tid, fördröjd, avbrutna och distribueras flygplan visas... i översikt över tabeller som anslås på den här webbplatsen ”. | Filerna finns i CSV-format. |
| [Trafik skadades - oss Fatality analys Reporting System (FARS)](http://www.nhtsa.gov/FARS) | ”FARS är att tillhandahålla NHTSA Congress nationell inventering och American offentliga årlig data om allvarliga skador haft i fordon trafik krascher”. | ”Skapa dina egna fatality data köras online med hjälp av FARS frågan System. Eller hämta alla FARS data från 1975 presentera från FTP-platsen ”. |
| [Giftiga kemiska data - EPA toxicitet prognosmodell (ToxCast™) data](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | ”Miljövårdsmyndighetens allra senaste, offentligt tillgänglig hög genomströmning toxicitet data på tusentals kemikalier. Informationen genereras via den EPA ToxCast research arbete ”. | Uppgifter som är tillgängliga i olika format, inklusive kalkylblad, R-paket och MySQL-databasfiler. |
| [Giftiga kemiska data - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | ”2014 Tox21 data challenge är utformade för att förstå potentialen av kemikalier och föreningar testas via toxikologi i 2000-talet initiativet att störa biologiska vägar på ett sätt som kan resultera i Giftverkningar forskare”. | Uppgifter som är tillgängliga i LEENDEN och SDF format. Data innehåller ”assay aktivitetsdata och kemiska strukturer på Tox21 mängden ~ 10 000 föreningar (Tox21 10K)”. |
| [Bioteknik och påvisa data från NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Flera datauppsättningar som omfattar gener, genom och protein. | Uppgifter som är i text, XML, snabbt POSTMEDDELANDE och andra format. Ett snabbt POSTMEDDELANDE appen är tillgänglig. |

## <a name="other-statistical-and-scientific-data"></a>Andra statistiska och vetenskapliga data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [New York City taxi data](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | ”Taxi resa poster inkluderar fält som avbildar är Samlingsbibliotek datum/tid, vill hämta och Samlingsbibliotek platser, resa avstånd, specificerade priser, hastighet typer, betalningstyper och drivrutinen rapporterat passagerare räknar”. | Uppgifter som är i CSV-filer per månad. |
| [Microsoft Research datauppsättningar - ”datavetenskap för forskning”](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Flera datauppsättningar som omfattar interaktion mellan människa-dator, ljud och video, datautvinning/information datahämtning, geospatiala/plats, bearbetning av naturligt språk och robotics eller datorn vision. | Uppgifter som är i olika format, zippade för hämtning. |
| [Offentliga påvisa data](http://www.completegenomics.com/public-data/) | ”En mängd olika människor genom data är tillgängligt för allmänheten att förbättra den genom analys...” Providern slutförd genomik är en privat for-profit corporation. | Datauppsättningar är efter extrahering, i UNIX-format. Det finns också verktyg för analys. |
| [Öppna vetenskap Data molndata](https://www.opensciencedatacloud.org/) | ”Öppna vetenskap Data molnet ger vetenskapliga gemenskapen med resurser för att lagra, dela och analysera terabyte och petabyte skala vetenskapliga datauppsättningar”.| Uppgifter som är i olika format. |
| [Globala klimatförändringar data - WorldcLIM](http://worldclim.org/) | ”WorldClim är en uppsättning globala klimatförändringar lager (förnätade klimatförändringar data) med en spatial upplösning på ca 1 km2. Dessa data kan användas för mappning och spatial modellering ”. | Dessa filer innehåller geospatiala data. Mer information finns i [dataformat](http://worldclim.org/formats1). |
| [Data om mänsklig society - det GDELT-projekt](http://www.gdeltproject.org/data.html) | ”GDELT-projekt är det största, mest omfattande och högsta upplösning öppna databasen för mänsklig society någonsin skapats”. | Rådata-filerna finns i CSV-format. |
| [Reklam klickar du på förutsägelse data för machine learning från Criteo](http://labs.criteo.com/2013/12/download-terabyte-click-logs/) | ”Största någonsin offentligt utgivna ML datauppsättningen”. Mer information finns i [Criteo's 1 TB klickar du på förutsägelse Dataset](https://blogs.technet.microsoft.com/machinelearning/2015/04/01/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [Datauppsättning för ClueWeb09 text datautvinning från det Lemur-projekt](http://www.lemurproject.org/clueweb09.php/) | ”ClueWeb09 datauppsättningen har skapats för att stödja referensinformation om hämtning av information och relaterade mänsklig språk tekniker. Det består av ungefär 1 miljard webbsidor på 10 språk som samlades in i januari och februari 2009 ”. | Se [Datamängdsinformation](http://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online-tjänstdata

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [GitHub-Arkiv](https://www.githubarchive.org/) | ”GitHub är ett projekt för att registrera den offentliga GitHub tidslinjen [händelser], arkivera den och gör det lätt tillgängliga för ytterligare analys”. | Hämta JSON-encloded händelse Arkiv formatet .gz (Gzip) från en webbklient. |
| [GitHub-aktivitetsdata från den GHTorrent projekt](http://ghtorrent.org/) | ”GHTorrent projektet [är] för att skapa en skalbar, queriable offline spegling av data som erbjuds via GitHub REST API. GHTorrent övervakar i tidslinjen för GitHub offentlig händelse. För varje händelse hämtar den innehållet och deras beroenden omfattande ”. | MySQL-databas Dumpar finns i CSV-format. |
| [Stackdump dataspill](https://archive.org/details/stackexchange) | ”Det här är en anonymiserade dump av alla användare har bidragit innehåll i stacken Exchange-nätverk [inklusive Stack Overflow]”. | ”Varje plats [till exempel Stack Overflow] är formaterade som ett separat Arkiv som består av XML-filer zippade 7 zip-med bzip2 komprimering. Varje plats-arkivet innehåller inlägg, användare, rösterna, kommentarer, PostHistory och PostLinks ”. |
