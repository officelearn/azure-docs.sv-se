---
title: Frågor och svar en matchande med hjälp av Azure Machine Learning Workbench | Microsoft Docs
description: Hur du använder olika effektiva machine learning-metoder för att matcha öppna avslutades frågor till ett befintligt vanliga frågor och svar/svar på säkerhetsfrågor par.
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 33d95e0c17e8b9b18313cb0854532337ec76cfd1
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057694"
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Frågor och svar en matchande med hjälp av Azure Machine Learning workbench
Svarar öppna avslutade frågorna är svår och kräver ofta manuellt arbete från ämnesexperter (SME). För att minska kraven på interna små och medelstora företag kan skapa företag ofta en lista över vanliga frågor och svar (FAQ) för att hjälpa användare. Det här exemplet visar olika effektiva machine learning-metoder för att matcha öppna avslutade frågor till befintlig fråga/svar-par för vanliga frågor och svar. Det här exemplet visar en enkel utveckling process för att skapa en lösning med hjälp av Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen galleriet
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Översikt

Det här exemplet tar itu med problem för att matcha användaren frågor mot befintlig fråga och svar (frågor och svar)-par som tillhandahålls vanligtvis i en lista över vanliga frågor och svar (det vill säga en FAQ) eller i frågor och & ett par som finns på webbplatser som [Stack Overflow](https://stackoverflow.com/). Det finns många sätt att matcha en fråga till dess rätt svar, till exempel att söka efter svar som liknar den mest frågan. Men i det här exemplet matchas öppna avslutade frågor på tidigare frågorna av förutsatt att varje svar i den vanliga frågor och svar kan besvara frågor för flera betyda samma sak.

Viktiga steg som krävs för att leverera den här lösningen är följande:

1. Rensa och bearbeta textdata.
2. Lär dig mer informativa fraser som är flera word-sekvenser som innehåller mer information när den visas i sekvensen än när behandlas oberoende av varandra.
3. Extrahera funktioner från textdata.
4. Skapa modeller för klassificering av text och utvärderar du modellens prestanda.


## <a name="prerequisites"></a>Förutsättningar

Förutsättningar för att kunna köra det här exemplet är följande:

1. En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
2. En installerad kopia av [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](../service/quickstart-installation.md) att installera programmet och skapa en arbetsyta.
3. Det här exemplet kan köras på alla beräkningskontexten. Men det rekommenderas att köra den på en dator med flera kärnor med minst 16GB minne och 5GB ledigt diskutrymme.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök efter projektmallar** sökrutan skriver du ”Q & A matchar” och väljer mallen
5.  Klicka på **Skapa**

## <a name="data-description"></a>Databeskrivning

Den datauppsättning som används i det här exemplet är en Stack Exchange Data dumpa lagras på [archive.org](https://archive.org/details/stackexchange). Dessa data är en maskerade dump av allt innehåll som har bidragit med användare på den [Stack Exchange nätverk](https://stackexchange.com/). Varje plats i nätverket som är formaterade som ett separat Arkiv som består av XML-filer zippade 7 zip-komprimering för bzip2. Varje plats-arkivet innehåller inlägg, användare, röster, kommentarer, PostHistory och PostLinks. 

### <a name="data-source"></a>Datakälla

Det här exemplet används den [publicerar data (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) och [PostLinks data (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) från webbplatsen Stack Overflow. Fullständigt schemainformation finns i den [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

Den `PostTypeId` fält i Datainsamlingstjänsten data anger om ett inlägg är en `Question` eller en `Answer`. Den `PostLinkTypeId` fält i PostLinks data anger om två inlägg är länkade eller duplicera. Fråga inlägg omfattar normalt vissa taggar är nyckelord som kategoriserar en fråga med andra liknande/dubblett-frågor. Det finns vissa taggar med hög frekvens, till exempel `javascript`, `java`, `c#`, `php` osv., består av ett större antal inlägg i fråga. I det här exemplet, en delmängd av Q & ett par med den `javascript` taggen ska extraheras.

Additionionally, ett inlägg i fråga kan vara relaterade till flera svar inlägg eller dubbla fråga inlägg. Om du vill skapa en lista över vanliga frågor och svar från dessa två datamängder, anses vissa kriterier för insamling av data. De tre datauppsättningarna kompilerade markeras med ett SQL-skript som inte ingår i det här exemplet. Resulterande databeskrivningen är följande:

- `Original Questions (Q)`: Inlägg av fråga ställs och besvaras på webbplatsen Stack overflow!.
- `Duplications (D)`: Fråga inlägg duplicerade andra befintliga frågor (`PostLinkTypeId = 3`), som är de ursprungliga frågorna. Upprepningar betraktas som samma sak på ursprungliga frågorna i den mening att svaret till den ursprungliga frågan får även svar på den nya duplicera frågan.
- `Answers (A)`: Ursprungliga frågorna och dess upprepningar kan länkar till mer än en svar inlägg. Det här exemplet väljer endast svar inlägget som antingen har godkänts av den ursprungliga författaren (filtreras efter `AcceptedAnswerId`) eller har högst poäng (rankade efter `Score`) i de ursprungliga frågorna. 

En kombination av dessa tre datauppsättningar skapar frågor och svar -par där ett svar (A) mappas till en ursprunglig fråga (Q) och flera duplicerade frågor (D) som du ser i följande diagram för data:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Datastruktur

Dataschema och direkt nedladdningslänkarna av de tre datauppsättningarna finns i följande tabell:

| Datauppsättning | Fält | Typ | Beskrivning
| ----------|------------|------------|--------
| [Frågor](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | Id | Sträng | Unikt fråge-ID (primärnyckel)
|  | AnswerId | Sträng | Unika svar-ID per fråga
|  | Text0 | Sträng | Rå textdata inklusive frågans rubrik och brödtext
|  | CreationDate | Tidsstämpel | Tidsstämpel för när frågan har ombetts
| [dubbla](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | Id | Sträng | Unikt duplicering-ID (primärnyckel)
|  | AnswerId | Sträng | Svar-ID som är associerade med dupliceringen
|  | Text0 | Sträng | Rå textdata inklusive dupliceringen rubrik och brödtext
|  | CreationDate | Tidsstämpel | Tidsstämpel för när risken har ombetts
| [svar](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | Id | Sträng | Unika svar-ID (primärnyckel)
|  | text0 | Sträng | Rå textdata för svaret


## <a name="scenario-structure"></a>Scenario-struktur

Frågor och svar matchande exemplet visas tre typer av filer. Den första typen är en serie med Jupyter-anteckningsböcker som visar de stegvisa beskrivningarna i hela arbetsflödet. Den andra typen är en uppsättning Python filer innehåller anpassad Python-moduler för inlärning och funktionen diskussionsämne. Dessa Python-moduler är tillgängliga med tillräckligt fungerar inte bara det här exemplet, men även andra användningsfall. Den tredje typen är en uppsättning Python-filer att finjustera hyperparametrar och spåra modellens prestanda med hjälp av Azure Machine Learning Workbench.

Så här organiseras filerna i det här exemplet.

| Filnamn | Typ | Beskrivning
| ----------|------------|--------
| `Image` | Mapp | Mappen som används för att spara bilder för filen Viktigt
| `notebooks` | Mapp | Mappen Jupyter-anteckningsböcker
| `modules` | Mapp | Mappen Python-moduler
| `scripts` | Mapp | Python-mappen
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | Få åtkomst till exempeldata, Förbearbeta texten, och förbereda utbildning och testa datauppsättningar
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | Lär dig informativa fraser och tokenize text i egenskapsuppsättning ord (pilbågar) framställningar
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | Extrahera funktioner, träna modeller för text-klassificering och utvärdering modellprestanda
| `modules/__init__.py` | Python-fil | Python-paketet init-fil
| `modules/phrase_learning.py` | Python-fil | Python-moduler som används för att omvandla rådata och lär dig mer informativa fraser
| `modules/feature_extractor.py` | Python-fil | Python-moduler extrahera funktioner för modellträning
| `scripts/naive_bayes.py` | Python-fil | Python för att finjustera hyperparametrar i modellen Naive Bayes
| `scripts/random_forest.py` | Python-fil | Python för att finjustera hyperparametrar i slumpmässiga Skogsmodell
| `README.md` | Markdown-fil | Filen Viktigt markdown

> [!NOTE]
> Serie anteckningsböcker skapas under Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Datainmatning och transformering

Tre kompilerade datauppsättningarna lagras i ett Blob storage och hämtas i `Part_1_Data_Preparation.ipynb` anteckningsboken.  

Innan utbildning text klassificering modeller är texten i frågorna rensade och förbearbetats om du vill exkludera kodfragment. En oövervakad frasen learning används över utbildningsmaterial mer informativa flera ord sekvenser. Dessa fraser representeras som enheter för enstaka ord för sammansatt i den underordnade egenskapsuppsättning ord (pilbågar) funktionalisering som används av text klassificering modeller.

Den detaljerade steg för steg-beskrivning av förbearbetning av text och frasen learning finns i de bärbara datorerna `Part_1_Data_Preparation.ipynb` och `Part_2_Phrase_Learning.ipynb`respektive.

### <a name="modeling"></a>Modellering

Det här exemplet är utformad för att poängsätta nya frågor mot befintliga Q & ett par med utbildning klassificering modeller där varje befintliga frågor och svar ett par är en unik klass och en delmängd av de duplicera frågorna för varje par med frågor och svar är tillgängliga som utbildningsmaterial. I det här exemplet de ursprungliga frågorna och 75% av de duplicera frågorna bevaras för träning och den senast bokförda 25% av duplicerade frågor är hålls ut som utvärdering av data.

Klassificeringsmodellen använder en ensemble-metoden för att aggregera tre grundläggande klassificerare, inklusive **Naive Bayes**, **dator för Vektorstöd**, och **slumpmässiga skog**. I varje grundläggande klassificerare den `AnswerId` används som klass etiketten och pilbågar garantier används som funktionerna.

Träningsprocess modellen illustreras i `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Utvärdering

Två olika utvärderingsmått används för att utvärdera prestanda. 
1. `Average Rank (AR)`: Anger det genomsnittliga positionen där rätt svar finns i listan över hämtade frågor och svar -par (av den fullständiga uppsättningen 103 svar klasser). 
2. `Top 3 Percentage`: Visar procentandelen av test frågor att rätt svar kan hämtas i de tre översta val i den returnerade rangordnas lista. 

Utvärderingen visas i `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Sammanfattning

Det här exemplet visar hur du använder välkända text analytics-teknik, till exempel frasen learning och text klassificering, för att skapa en robust modell. Den visar också hur Azure Machine Learning Workbench kan hjälpa till med interaktiva lösning utvecklings- och spåra modellprestanda. 

Vissa viktiga höjdpunkter i det här exemplet är:

- Frågor och svar matchande problem kan effektivt lösas med frasen learning och text klassificering modeller.
- Visar interaktiva modellen utveckling med Azure Machine Learning Workbench och Jupyter-anteckningsboken.
- Azure Machine Learning Workbench hanterar körningshistorik och inlärda modeller med loggning av utvärderingsmått för jämförelse. De här funktionerna gör det möjligt att snabbt hyperparametrar och hjälper dig att identifiera den bästa modeller.


## <a name="references"></a>Referenser

David J. Hazen, Fred Richardson [ _modellering Multiword fraser med begränsad fraser-träd för förbättrad avsnittet modellering Konversationsanpassade tal_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Talat språk teknik Workshop (SLT), 2012 IEEE. IEEE 2012.

David J. Hazen [ _MCE utbildning tekniker för avsnittet Identifiering av talat ljud dokument_ ](http://ieeexplore.ieee.org/abstract/document/5742980/) IEEE transaktioner på ljud-, tal- och Språkbearbetning, vol. 19, inte. 8, sidor 2451-2460 november 2011.
