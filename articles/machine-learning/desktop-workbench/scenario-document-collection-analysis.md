---
title: Dokumentera analys – Azure | Microsoft Docs
description: Så här att sammanfatta och analysera en stor samling dokument, inklusive tekniker som frasen learning, avsnittet modellering och avsnittet modellen analys med Azure ML Workbench.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 762955103aeb48eb8a9b62f4e3ffe193bba71a38
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947225"
---
# <a name="document-collection-analysis"></a>Analys av dokumentsamling

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Det här scenariot visar hur du sammanfatta och analysera en stor samling dokument, inklusive tekniker som frasen learning, avsnittet modellering och avsnittet modellen analys med Azure ML Workbench. Azure Machine Learning Workbench tillhandahåller för att enkelt skala upp för mycket stora dokumentsamling och tillhandahåller mekanismer för att träna och finjustera modeller i en mängd olika beräkningskontext sträcker sig från lokala beräkning till virtuella datorer för datavetenskap till Spark-kluster. Enkel utveckling tillhandahålls via Jupyter notebooks i Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Offentliga GitHub-lagringsplatsen för den här verkligt scenario innehåller allt material, inklusive kodexempel som behövs för det här exemplet:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Översikt

Med en stor mängd data (särskilt ostrukturerad textdata) som samlas in varje dag, är en stor utmaning att sortera, söka och förstå enorma mängder texter. Det här dokumentet samling dataanalysscenario visar ett effektivt och automatiserade slutpunkt till slutpunkt-arbetsflöde för att analysera stora dokumentsamling och aktivera nedströms NLP-aktiviteter.

De viktigaste elementen som levereras av det här scenariot är:

1. Learning viktigaste flera ord fras från dokument.

1. Identifiering av underliggande ämnen som presenteras i dokumentsamlingen.

1. Som representerar dokument med lokal distribution.

1. Presentera metoder för att ordna, söka och sammanfatta dokument baserat på lokal innehållet.

De metoder som visas i det här scenariot möjliggör en mängd olika industriella arbetsbelastningar, till exempel identifiering av avsnittet trender avvikelseidentifiering, dokumentet samling sammanfattning och liknande Dokumentsökning. Det kan tillämpas på många olika typer av dokument analys, till exempel statliga lagar, nyheter, produktrecensioner, kundernas feedback och vetenskaplig forskning artiklar.

De tekniker/maskininlärningsalgoritmer används i det här scenariot omfattar:

1. Bearbetning av text och rensning

1. Fras Learning

1. Avsnittet modellering

1. Kristi sammanfattning

1. Ämnes trender och avvikelseidentifiering

## <a name="prerequisites"></a>Förutsättningar

Förutsättningar för att kunna köra det här exemplet är följande:

* Se till att du har installerat korrekt [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) genom att följa den [installera snabbstarten och skapa](quickstart-installation.md).

* Det här exemplet kan köras på alla beräkningskontexten. Men det rekommenderas att köra den på en dator med flera kärnor med minst 16GB minne och 5GB ledigt diskutrymme.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt Workbench-projekt

Skapa ett nytt projekt med hjälp av det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök efter projektmallar** sökrutan skriver du ”analys av dokumentsamling” och väljer mallen
5.  Klicka på **Skapa**

## <a name="data-description"></a>Databeskrivning

Den datauppsättning som används i det här scenariot innehåller text sammanfattningar och associerade metadata för varje lagstiftande åtgärd som den oss den amerikanska kongressen och. Data samlas in från [GovTrack.us](https://www.govtrack.us/), som spårar aktiviteter för den amerikanska kongressen och USA och hjälper till att Amerikanarna delta i sina nationella lagstiftningen. Stora mängder data kan hämtas [den här länken](https://www.govtrack.us/data/congress/) med hjälp av en manuell skript som inte ingår i det här scenariot. Information om hur du hämtar data hittades i den [GovTrack API-dokumentation](https://www.govtrack.us/developers).

### <a name="data-source"></a>Datakälla

I det här scenariot rådata som samlas in är en serie rättsliga åtgärder som introducerats av det oss den amerikanska kongressen och (föreslagna fakturor och lösningar) från 1973 i juni 2017 (93rd till 115th kongresser). Data som samlas in i JSON-format och innehåller en omfattande uppsättning information om rättsliga åtgärder. Referera till [GitHub länken](https://github.com/unitedstates/congress/wiki/bills) detaljerad beskrivning av datafält. Demonstration syfte i det här scenariot, endast en delmängd av datafält extraherades från JSON-filerna. En förkompilerad TSV-fil `CongressionalDataAll_Jun_2017.tsv` som innehåller poster för dessa rättsliga åtgärder finns i det här scenariot. TSV-fil kan laddas ned automatiskt antingen genom att de bärbara datorerna `1_Preprocess_Text.ipynb` under anteckningsboksmappen eller `preprocessText.py` i Python-paketet.

### <a name="data-structure"></a>Datastruktur

Det finns nio datafält i datafilen. Namnen på data och beskrivningar visas på följande sätt.

| Fältnamn | Typ | Beskrivning | Innehåller värden som saknas |
|------------|------|-------------|---------------|
| `ID` | Sträng | ID för fakturan/lösning. Formatet för det här fältet är [bill_type] [antal]-[den amerikanska kongressen och]. Till exempel ”hconres1 93” innebär faktura är ”hconres” (står för House samtidiga upplösning, referera till [det här dokumentet](https://github.com/unitedstates/congress/wiki/bills#basic-information)), faktura-värdet är ”1” och den amerikanska kongressen och talet är ' 93 ”. | Nej |
| `Text` | Sträng | Innehållet i på fakturan/lösningen. | Nej |
| `Date` | Sträng | Det datum då fakturan/lösning föreslagna inledningsvis. I formatet ”åååå-mm-dd'. | Nej |
| `SponsorName` | Sträng | Namnet på primär sponsor som föreslås faktura/lösning. | Ja |
| `Type` | Sträng | Rubrik-typ av primärt sponsra 'rep ”(representant) eller” skicka ”(senator). | Ja |
| `State` | Sträng | Tillståndet för den primära sponsorn. | Ja |
| `District` | Integer | Distrikt antal primära sponsor om rubriken för sponsor är en representant. | Ja |
| `Party` | Sträng | Parten primära sponsor. | Ja |
| `Subjects` | Sträng | Ämne villkoren läggs till kumulativt av i biblioteket för den amerikanska kongressen och att fakturan. Villkoren sammanfogas med kommatecken. Dessa villkor skrivs av en människa i i biblioteket för den amerikanska kongressen och och är inte vanligtvis tillgängliga när information om fakturan publiceras först. De kan läggas till när som helst. I slutet av en faktura livslängd därför vissa ämne inte relevanta längre. | Ja |

## <a name="scenario-structure"></a>Scenario-struktur

Samling analysis-exemplet för dokumentet är uppdelad i två typer av slutprodukter. Den första typen är en serie iPython-anteckningsböcker som visar de stegvisa beskrivningarna i hela arbetsflödet. Den andra typen är en Python-paketet samt vissa kodexempel på hur du använder det paketet. Python-paketet är tillräckligt generisk för att hantera många användningsområden.

Så här organiseras filerna i det här exemplet.

| Filnamn | Typ | Beskrivning |
|-----------|------|-------------|
| `aml_config` | Mapp | Azure Machine Learning Workbench konfigurationsmappen avser [den här dokumentationen](./experimentation-service-configuration-reference.md) för detaljerad experiment köra konfiguration |
| `Code` | Mapp | Mappen kod används för att spara Python-skript och Python-paketet |
| `Data` | Mapp | Den datamapp som används för att spara mellanliggande filer |
| `notebooks` | Mapp | Mappen Jupyter-anteckningsböcker |
| `Code/documentAnalysis/__init__.py` | Python-fil | Python-paketet init-fil |
| `Code/documentAnalysis/configs.py` | Python-fil | Konfigurationsfilen som används av dokumentet analysen Python-paketet, inklusive fördefinierade konstanterna |
| `Code/documentAnalysis/preprocessText.py` | Python-fil | Python-filen som används för att Förbearbeta data för nedströms aktiviteter |
| `Code/documentAnalysis/phraseLearning.py` | Python-fil | Python-filen som används för att lära dig fraser från data och transformera rådata |
| `Code/documentAnalysis/topicModeling.py` | Python-fil | Python-filen som används för att träna en modell för avsnittet av Latent Dirichlet allokering (LDA) |
| `Code/step1.py` | Python-fil | Steg 1 av analys av dokumentsamling: Förbearbeta text |
| `Code/step2.py` | Python-fil | Steg 2 av analys av dokumentsamling: fras learning |
| `Code/step3.py` | Python-fil | Steg 3 av analys av dokumentsamling: träna och utvärdera LDA avsnittet modell |
| `Code/runme.py` | Python-fil | Exempel på Kör alla steg i en fil |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | Förbearbeta text och transformera rådata |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | Lär dig fraser från textdata (efter Datatransformering) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | Träningsmodell LDA avsnittet |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | Sammanfatta innehållet i dokumentsamling baserat på en LDA avsnittet träningsmodell |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | Analysera ämnes innehållet i en samling av dokument och korrelera ämnes informationen mot andra metadata som är associerade med dokumentsamlingen |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | Interaktiv visualisering av inlärda avsnittet modell |
| `notebooks/winprocess.py` | Python-fil | Python-skriptet för Fleruppdragskörning som används av bärbara datorer |
| `README.md` | Markdown-fil | Filen Viktigt markdown |

### <a name="data-ingestion-and-transformation"></a>Datainmatning och transformering

Den kompilerade datauppsättningen `CongressionalDataAll_Jun_2017.tsv` sparas i Blob Storage och är tillgänglig både från inom de bärbara datorerna och Python-skript. Det finns två steg för datainmatning och transformering: Förbearbeta textdata och frasen learning.

#### <a name="preprocess-text-data"></a>Förbearbeta textdata

Förbearbetning steget gäller tekniker för bearbetning av naturligt språk för att rensa och förbereda data rå text. Den fungerar som en tidigare för oövervakade frasen learning och latent avsnittet modellering. Detaljerade steg för steg-beskrivningen kan hittas i anteckningsboken `1_Preprocess_Text.ipynb`. Det finns också en Python-skriptet `step1.py` motsvarar den här anteckningsboken.

I det här steget datafilen TSV hämtas från Azure Blob Storage och importeras som en Pandas-DataFrame. Varje rad-element i DataFrame är en enda sammanhängande lång sträng med text eller ett dokument. Varje dokument är sedan indelat i segment om text som sannolikt kommer att vara meningar, fraser eller subphrases. Uppdelningen av är utformad för att förhindra frasen inlärningsprocessen från att använda flera meningen eller mellan frasen word strängar när learning fraser.

Det finns flera funktioner som definierats för förbearbetning steg både i anteckningsboken och Python-paketet. Merparten av jobbet kan uppnås genom att anropa dessa två rader med koder.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Fras learning

Steget fras learning implementerar en grundläggande ramverket för att lära dig viktiga fraser bland ett stort antal dokument. Det beskrivs i dokumentet berättigade ”[modellering Multiword fraser med begränsad fraser-träd för förbättrad avsnittet modellering av Konversationsanpassade tal](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)”, som ursprungligen angavs i 2012 IEEE-Workshop om talat språk Teknik. Detaljerad implementeringen av frasen learning steg visas i iPython Notebook `2_Phrase_Learning.ipynb` och Python-skriptet `step2.py`.

Det här steget tar rensad text som indata och lär sig de mest viktigaste fraserna som finns i ett stort antal dokument. Fras-utbildning är en iterativ process som kan delas in i tre uppgifter: antal n-gram, rangordnas möjliga fraser genom viktad Pointwise ömsesidig Information om deras konstituerande ord och Skriv frasen till text. Dessa tre aktiviteter utförs sekventiellt i flera iterationer tills de angivna fraserna har har lärt dig.

I dokumentet analysis-Python-paketet, en Python-klass `PhraseLearner` definieras i den `phraseLearning.py` filen. Nedan visas i kodfragmentet som används för att lära dig fraser.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> Steget fras learning implementeras med flera. Men mer CPU-kärnor göra **inte** innebär en snabbare körningstid. I våra tester kan förbättras prestanda inte med fler än åtta kärnor på grund av arbetet med Fleruppdragskörning. Det tog ungefär två och en halv timme för att lära dig 25 000 fraser på en dator med åtta kärnor (3.6 GHz).
>

### <a name="topic-modeling"></a>Avsnittet modellering

Learning en latent avsnittet modellen använder är LDA det tredje steget i det här scenariot. Den [gensim](https://radimrehurek.com/gensim/) Python-paketet krävs i det här steget för att lära dig en [LDA avsnittet modellen](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Motsvarande anteckningsboken för det här steget är `3_Topic_Model_Training.ipynb`. Du kan även gå till `step3.py` för hur du använder dokumentet analysis-paketet.

I det här steget är den huvudsakliga uppgiften att lära dig en LDA avsnittet modell och justera parametrarna hyper. Det finns flera parametrar behöver justeras när träna en modell för LDA, men den viktigaste parametern är antalet ämnen. För få ämnen kanske inte har insyn i dokumentsamlingen; När du väljer för leder många den ”över kluster” av en Kristi i många små, mycket liknande ämnen. Syftet med det här scenariot är att visa hur du ställer in antalet ämnen. Azure Machine Learning Workbench erbjuder dig friheten att köra experiment med olika parametern konfiguration på olika beräkningskontext.

I Python-paketet dokumentet analysis några funktioner har definierats för att hjälpa användarna ta reda på det bästa antalet ämnen. Den första metoden är genom att utvärdera enhetligheten i avsnittet modellen. Det finns fyra utvärdering matriser stöds: `u_mass`, `c_v`, `c_uci`, och `c_npmi`. Information om dessa fyra mätvärdena diskuteras i [det här dokumentet](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Den andra metoden är att utvärdera perplexity på en hålls ut Kristi.

För perplexity-utvärdering förväntas en ”U” form kurva ta reda på det bästa antalet ämnen. Och den vinkel positionen är det bästa valet. Vi rekommenderar att utvärdera flera gånger med olika slumpmässig dirigering och få medelvärdet. Utvärdera enhetligheten förväntas vara ett x forma, vilket innebär att konsekvens ökar med öka antalet ämnen och sedan minskar. Ett exempel exempeldiagram av perplexity och `c_v` konsekvens visas på följande sätt.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v konsekvens](./media/scenario-document-collection-analysis/c_v_Coherence.png)

I det här scenariot ökar i perplexity avsevärt efter 200 ämnen, medan konsekvens värdet minskar avsevärt efter samt 200 ämnen. Baserat på dessa diagram och önskan om mer allmän information jämfört med över klustrade ämnen, Välj 200 avsnitt ska vara ett bra alternativ.

Du kan träna en LDA avsnittet modell i ett experiment körs, eller träna och utvärdera flera LDA modeller med olika avsnittet antal konfigurationer i ett enda experiment som kör. Vi rekommenderar att köra flera gånger för en konfiguration och sedan hämta de genomsnittliga konsekvens och/eller perplexity utvärderingarna. Information om hur du använder dokumentet analysis-paketet finns i `step3.py` fil. Ett exempel kodfragment är som följer.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> Körningstid för att träna en modell för LDA avsnittet är beroende av flera faktorer, till exempel storleken på Kristi, hyper parametern konfiguration, samt antalet kärnor på datorn. Med hjälp av flera processorkärnor träna en modell som är snabbare. Men med samma hyper parametern innebär ställa in fler kärnor färre uppdateringar vid träning. Vi rekommenderar att ha **minst 100 uppdateringar för att träna en modell för konvergerade LDA**. Förhållandet mellan antalet uppdateringar och hyper parametrar beskrivs i [det här inlägget](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) och [det här inlägget](http://miningthedetails.com/blog/python/lda/GensimLDA/). I våra tester kan det tog cirka 3 timmar för att träna en modell för LDA med 200 ämnen med hjälp av konfigurationen av `workers=15`, `passes=10`, `chunksize=1000` på en dator med 16 kärnor (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Avsnittet Sammanfattning och analys

Avsnittet Sammanfattning och analys består av två notebooks, det finns inga motsvarande funktioner i dokumentet analysis-paketet.

I `4_Topic_Model_Summarization.ipynb`, den visar hur du summerar innehållet i dokument baserat på en träningsmodell i LDA avsnittet. Sammanfattningen tillämpas på en modell för avsnittet av LDA lärt dig i steg 3. Den visar hur du använder prioritet eller kvaliteten på ett ämne med avsnittet för att dokumentet renhet mått. Det här måttet renhet förutsätter att latent ämnen som dominerar dokument som de visas är mer semantiskt viktigt än latent ämnen som är svagt sprids över flera dokument. Det här konceptet introducerades i dokumentet ”[Latent avsnittet modellering för ljud Kristi sammanfattning](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)”.

Anteckningsboken `5_Topic_Model_Analysis.ipynb` visar hur du analyserar ämnes innehållet i en samling dokument och korrelera ämnes informationen mot andra metadata som är associerade med dokumentsamlingen. Ett par områden införs i den här anteckningsboken för att hjälpa användarna att bättre förstå inlärda ämnet och dokumentsamlingen.

Anteckningsboken `6_Interactive_Visualization.ipynb` visar hur du interaktivt visualisera inlärda avsnittet modellen. Den innehåller fyra interaktiv visualisering aktiviteter.

## <a name="conclusion"></a>Sammanfattning

Den här verkligt scenario illustrerar hur du använder välkända text analytics-tekniker (i det här fallet frasen learning och LDA avsnittet modellering) för att skapa en robust modell och hur Azure Machine Learning Workbench kan hjälpa dig spåra modellprestanda och köra elever i hög skala. I detalj:

* Använd frasen learning och avsnittet modellering för att bearbeta en samling dokument och skapa en robust modell. Om insamling av dokument är stor, Azure Machine Learning Workbench kan enkelt skala upp och ut. Dessutom kan har användare frihet att köra experiment på olika beräkningskontext direkt i Azure Machine Learning Workbench.

* Azure Machine Learning Workbench innehåller båda alternativen för att köra anteckningsböcker i ett steg för steg sätt och Python-skriptet att köra ett hela experiment.

* Hyper-parametern prestandajustering med Azure Machine Learning Workbench för att hitta det bästa antalet ämnen behövs för att lära dig. Azure Machine Learning Workbench kan spåra modellens prestanda och köra olika inlärning i hög skala.

* Azure Machine Learning Workbench kan hantera körningshistorik och inlärda modeller. Det gör det möjligt för dataexperter att snabbt identifiera den bästa modeller och hitta de skript och data som används för att generera dem.

## <a name="references"></a>Referenser

* **David J. Hazen, Fred Richardson**, [ _modellering Multiword fraser med begränsad fraser-träd för förbättrad avsnittet modellering Konversationsanpassade tal_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Talat språk teknik Workshop (SLT), 2012 IEEE. IEEE 2012.

* **David J. Hazen**, [ _Latent avsnittet modellering för ljud Kristi sammanfattning_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12: e årliga konferensen internationella tal kommunikation kopplingen. 2011.

* **Michael Roder, Andreas båda, Alexander Hinneburg**, [ _utforska utrymme för avsnittet konsekvens mått_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Handlingar från den åttonde ACM internationella konferensen om webbsökning och datautvinning. ACM 2015.
