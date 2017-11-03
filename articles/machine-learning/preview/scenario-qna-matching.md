---
title: "Q & en matchande med hjälp av Azure Machine Learning arbetsstationen | Microsoft Docs"
description: "Hur du använder olika effektiva maskininlärning metoder för att matcha öppna avslutade frågor till ett befintligt vanliga frågor och svar fråga/svar par."
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
ms.manager: tihazen
ms.openlocfilehash: 8edc21fb8f42ee5897c4e938045cc1f42aedb3ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Q & en matchande med hjälp av Azure Machine Learning-arbetsstationen
Svara på Öppna avslutades frågor är svår och kräver ofta manuellt arbete från ämnesexperter (SME). Om du vill minska kraven på interna små och medelstora företag skapa företag ofta en lista över vanliga frågor och svar (FAQ) för att hjälpa användare. Det här exemplet visar olika effektiva machine learning metoder för att matcha öppna avslutades frågor till ett befintligt vanliga frågor och svar fråga/svar-par. Det här exemplet visar ett enkelt utvecklingsprocessen för att skapa en sådan lösning med hjälp av Azure Machine Learning-arbetsstationen. 

## <a name="link-to-the-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen galleri
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Översikt

Det här exemplet löser problemet för att matcha användaren frågor mot befintlig fråga och svar (Q & A)-par som anges vanligtvis i en lista med vanliga frågor och svar (det vill säga FAQ) eller Q & ett par som finns på webbplatser som [Stack Overflow](https://stackoverflow.com/). Det finns många sätt att matcha en fråga till dess rätt svar, till exempel söka efter svar som liknar den mest frågan. Men i det här exemplet matchas öppna avslutades frågor på tidigare frågorna av förutsatt att varje svar i vanliga frågor kan besvara flera frågor från samma sak.

Viktiga steg som krävs för att leverera den här lösningen är följande:

1. Rensa och bearbeta textdata.
2. Lär dig mer informativa fraser som är flera word-sekvenser som innehåller mer information när den visas i sekvensen än när behandlas oberoende av varandra.
3. Extrahera funktioner från textdata.
4. Träna modeller för klassificering av text och utvärdera modellen prestanda.


## <a name="prerequisites"></a>Krav

Förutsättningar för att kunna köra det här exemplet är följande:

1. En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
2. En installerad kopia av [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](./quickstart-installation.md) att installera programmet och skapa en arbetsyta.
3. Det här exemplet skulle kunna köras på valfri beräknings-kontext. Men det rekommenderas att köra den på en dator med flera kärnor med minst 16GB minne och 5GB ledigt diskutrymme.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den  **+**  och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”Q & A matchar” och välj mallen
5.  Klicka på **Skapa**

## <a name="data-description"></a>Beskrivning av data

Dataset som används i det här exemplet är en Stack Exchange Data Dump lagras på [archive.org](https://archive.org/details/stackexchange). Dessa data är en anonymiserade dump av alla användare har bidragit innehåll på den [Stack Exchange nätverket](https://stackexchange.com/). Varje plats i nätverket är formaterade som ett separat Arkiv som består av XML-filer zippade 7 zip-med bzip2 komprimering. Varje plats-arkivet innehåller inlägg, användare, rösterna, kommentarer, PostHistory och PostLinks. 

### <a name="data-source"></a>Datakälla

Det här exemplet används den [skickar data (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) och [PostLinks data (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) från webbplatsen stackspill. För fullständig schemainformation finns i [viktigt.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

Den `PostTypeId` fält i inlägg data anger om en post är en `Question` eller en `Answer`. Den `PostLinkTypeId` fält i PostLinks data anger om två tjänster är länkade eller duplicerat. Fråga inlägg inkluderar vanligtvis vissa taggar som är nyckelord som kategoriserar en fråga med andra liknande/duplicera frågor. Det finns vissa taggar med hög frekvens som `javascript`, `java`, `c#`, `php` osv., består av ett stort antal fråga meddelandena. I det här exemplet, en delmängd av Q & ett par med den `javascript` taggen ska extraheras.

Additionionally, fråga post kan vara relaterade till flera svar inlägg eller duplicerade fråga meddelandena. Om du vill skapa en lista över vanliga frågor och svar från dessa två datamängder, kriterier vissa data collection. De tre datauppsättningarna kompilerade markeras med ett SQL-skript som inte ingår i det här exemplet. Beskrivningen av resulterande data är följande:

- `Original Questions (Q)`: Inlägg fråga ställs och besvaras på Stack Overflow platsen.
- `Duplications (D)`: Fråga anslår dubbla andra befintliga frågor (`PostLinkTypeId = 3`), vilket är de ursprungliga frågorna. Upprepningar anses vara samma sak ursprungliga frågorna i den mening att svaret till den ursprungliga frågan också svarar den nya dubbla frågan.
- `Answers (A)`: Varje ursprungliga fråga och dess upprepningar kan länkar till mer än ett inlägg i svaret. Det här exemplet väljer endast efter svar som antingen har godkänts av den ursprungliga författaren (filtrerade efter `AcceptedAnswerId`) eller också har den högsta poängen (rangordnas av `Score`) i de ursprungliga frågorna. 

Kombinationen av dessa tre datauppsättningar skapar Q & A-par där ett svar (A) mappas till en ursprungliga fråga (Q) och flera duplicerade frågor (D) enligt följande diagram för data:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Datastruktur

Dataschemat och länkar för direkta hämtning av de tre datauppsättningarna finns i följande tabell:

| Datauppsättning | Fält | Typ | Beskrivning
| ----------|------------|------------|--------
| [frågor](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Id | Sträng | Unik fråge-ID (primärnyckel)
|  | AnswerId | Sträng | Unik svar-ID per fråga
|  | Text0 | Sträng | Rådata textdata inklusive frågans rubrik och text
|  | CreationDate | tidsstämpel | Tidsstämpel när frågan har ombetts
| [dubbla](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Id | Sträng | Duplicering unikt ID (primärnyckel)
|  | AnswerId | Sträng | Svaret-ID som är associerade med dupliceringen
|  | Text0 | Sträng | Rådata textdata, inklusive dupliceringen rubrik och text
|  | CreationDate | tidsstämpel | Tidsstämpel när dupliceringen har ombetts
| [svar](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Id | Sträng | Unik svar-ID (primärnyckel)
|  | text0 | Sträng | Rådata textdata i svaret


## <a name="scenario-structure"></a>Scenario-struktur

Frågor och svar matchande exemplet visas via tre typer av filer. Den första typen är en serie med Jupyter-anteckningsböcker som visar detaljerade beskrivningar av hela arbetsflödet. Den andra typen är en uppsättning Python-filer innehåller egna Python-moduler för frasen inlärning och funktionen extrahering. Dessa Python-moduler är generisk som fungerar inte bara det här exemplet, men även andra användningsfall. Tredje typen är en uppsättning Python filer att finjustera hyper-parametrar och spåra modellen prestanda med hjälp av Azure Machine Learning-arbetsstationen.

Filerna i det här exemplet är uppdelade enligt följande.

| Filnamn | Typ | Beskrivning
| ----------|------------|--------
| `Image` | Mapp | Mappen som används för att spara avbildningar för filen Viktigt
| `notebooks` | Mapp | Mappen Jupyter-anteckningsböcker
| `modules` | Mapp | Mappen Python-moduler
| `scripts` | Mapp | Python-mappen
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | Gå till exempeldata, Förbearbeta text, och förbereda utbildning och testa datauppsättningar
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | Lär dig mer informativa fraser och tokenize text i egenskapsuppsättning ord (pilbågar) framställningar
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | Extrahera funktioner, träna text klassificering modeller och utvärdering av modellen prestanda
| `modules/__init__.py` | Python-fil | Python package init-fil
| `modules/phrase_learning.py` | Python-fil | Python-moduler som används för att omvandla rådata och lär dig mer informativa fraser
| `modules/feature_extractor.py` | Python-fil | Python-moduler extrahera funktioner för modellen utbildning
| `scripts/naive_bayes.py` | Python-fil | Python att justera hyper-parametrar i Naive Bayes modellen
| `scripts/random_forest.py` | Python-fil | Python att justera hyper-parametrar i slumpmässiga Skogsmodell
| `README.md` | Markdown-fil | Filen Viktigt markdown

> [!NOTE]
> Antal anteckningsböcker bygger under Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Datapåfyllning och omvandling

Tre kompilerade datauppsättningar som är lagrade i en Blob storage och hämtas i `Part_1_Data_Preparation.ipynb` bärbar dator.  

Innan utbildning text klassificering modeller, rensade texten i frågor och meddelandefilsposter om du vill exkludera kodstycken. En oövervakad frasen learning tillämpas över utbildningsmaterial mer informativa flera word sekvenser. Dessa fraser visas i form av enstaka ord för sammansatt enheter i underordnade egenskapsuppsättning ord (pilbågar) featurization som används av text klassificering modeller.

Detaljerade steg för steg-beskrivningar av text förbearbetning och frasen learning finns i de bärbara datorerna `Part_1_Data_Preparation.ipynb` och `Part_2_Phrase_Learning.ipynb`respektive.

### <a name="modeling"></a>Modellering

Det här exemplet är utformat för att poängsätta nya frågor mot befintliga frågor och ett par med utbildning klassificering modeller där varje befintlig frågor och ett par är en unik klass och en delmängd av de duplicera frågorna för varje par med frågor och svar är tillgängliga som utbildningsmaterial. I det här exemplet ursprungliga frågor och 75% av de duplicera frågorna bevaras för träning och nyligen anslagna 25% av dubbla frågor är hålls ut som en utvärdering av data.

Klassificering modellen använder en ensemble metod för att sammanställa tre grundläggande klassificerare, inklusive **Naive Bayes**, **Support Vector Machine**, och **slumpmässiga skog**. I varje grundläggande klassificerare den `AnswerId` används som klass etikett och pilbågar garantier används som funktionerna.

Modellen utbildning processen illustreras i `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Utvärdering

Två olika utvärdering mått för att bedöma prestanda. 
1. `Average Rank (AR)`: visar genomsnittlig position där rätt svar finns i listan över hämtade Q & A par (av den fullständiga uppsättningen av klasser för 103 svar). 
2. `Top 3 Percentage`: Visar procentandelen av test frågor att rätt svar kan hämtas i de tre översta valen i den returnerade rangordnas lista. 

Utvärderingen visas i `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Slutsats

Det här exemplet visar hur du använder välkänd text analytics tekniker, till exempel frasen learning och text klassificering, som ger en robust modell. Den visar även hur Azure Machine Learning arbetsstationen kan hjälpa med interaktiv lösning utveckling och spåra modellen prestanda. 

Vissa viktiga markeringar i det här exemplet är:

- Fråga och svar matchande problem kan effektivt lösas med frasen learning och text klassificering modeller.
- Visar interaktiva modellen utveckling med Azure Machine Learning arbetsstationen och Jupyter-anteckningsbok.
- Azure Machine Learning arbetsstationen hanterar körningshistorik och inlärda modeller med loggning av utvärdering mätvärden för jämförelse. Dessa funktioner kan snabbt hyper parametern justera och hjälper till att identifiera det bästa utför modeller.


## <a name="references"></a>Referenser

David J. Hazen, Fred Richardson [ _Modeling Multiword fraser med begränsad fraser träd för förbättrad avsnittet modellering av vardagliga samtalsuttryck tal_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Talas teknik Workshop (SLT), 2012 IEEE. IEEE 2012.

David J. Hazen [ _MCE utbildning tekniker för att identifiera ämnet talade ljud dokument_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) IEEE transaktioner på ljud, tal och bearbetning, vol. 19, inte språk. 8 sidor 2451-2460 november 2011.
