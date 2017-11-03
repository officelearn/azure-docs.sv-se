---
title: Dokumentera samling analys - Azure | Microsoft Docs
description: "Så här sammanfatta och analysera en stor mängd dokument, inklusive tekniker som frasen learning, avsnittet modellering och avsnittet modellen analysis använder Azure ML-arbetsstationen."
services: machine-learning
documentationcenter: 
author: kehuan
ms.author: kehuan
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 5ef1589e28c01d750641873d3c8482f61d90a887
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="document-collection-analysis"></a>Dokumentet samling analys

Det här scenariot visar hur du sammanfatta och analysera en stor mängd dokument, inklusive tekniker som frasen learning, avsnittet modellering och avsnittet modellen analysis använder Azure ML-arbetsstationen. Azure Machine Learning arbetsstationen tillhandahåller för att enkelt skala upp för mycket stora dokumentsamlingen och ger metoder för att träna och finjustera modeller i en mängd olika kontexter för beräkning, allt från lokala beräkning till datavetenskap virtuella datorer till Spark-kluster. Enkel utveckling tillhandahålls via Jupyter-anteckningsböcker Azure Machine Learning-arbetsstationen.

## <a name="link-to-the-gallery-github-repository"></a>Länka till galleriet GitHub-lagringsplatsen

Offentliga GitHub-lagringsplatsen för den här verkligt scenario innehåller alla material, inklusive kodexempel som behövs för det här exemplet:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Översikt

Med en stor mängd data (särskilt Ostrukturerade textdata) som samlas in varje dag, är en betydande utmaning att ordna, söka och förstå stora mängder texter. Det här dokumentet samling analys scenariot visar en effektiv och automatisk slutpunkt till slutpunkt-arbetsflöde för analys av stora dokumentsamlingen och aktiverar underordnade NLP uppgifter.

De viktigaste beståndsdelarna som levereras av det här scenariot är:

1. Learning viktigaste flera ord frasen dokument.

1. Identifiering av underliggande avsnitt presenteras i dokumentsamlingen.

1. Som representerar dokument av aktuell distribution.

1. Presentera metoder för att ordna, söka och sammanfatta dokument baserat på aktuell innehållet.

De metoder som visas i det här scenariot kan aktivera olika kritiska industriella arbetsbelastningar, t.ex identifiering av avsnittet trender avvikelseidentifiering, dokumentet samling sammanfattningen och liknande dokument Sök. Det kan tillämpas på många olika typer av dokument analys, till exempel statliga lagar, nyheter, produktgranskningar, kunden feedback och forskning artiklar.

De tekniker/maskininlärningsalgoritmer används i det här scenariot omfattar:

1. Bearbetning av text och rensning

1. Frasen Learning

1. Avsnittet modellering

1. Kristi sammanfattning

1. Aktuell trender och avvikelseidentifiering

## <a name="prerequisites"></a>Krav

Förutsättningar för att kunna köra det här exemplet är följande:

* Kontrollera att du har installerat korrekt [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md) genom att följa den [installera och skapa Quickstart](quickstart-installation.md).

* Det här exemplet skulle kunna köras på valfri beräknings-kontext. Men det rekommenderas att köra den på en dator med flera kärnor med minst 16GB minne och 5GB ledigt diskutrymme.

## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning-arbetsstationen
2.  På den **projekt** klickar du på den  **+**  och markera **nytt projekt**
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet
4.  I den **Sök projektmallar** sökrutan, Skriv ”dokumentet samling Analysis” och välj mallen
5.  Klicka på **Skapa**

## <a name="data-description"></a>Beskrivning av data

Dataset som används i detta scenario innehåller text sammanfattningar och associerade metadata för varje lagar åtgärd som tar med Congress oss. Data samlas in från [GovTrack.us](https://www.govtrack.us/), som spårar aktiviteter för USA Congress och hjälper Americans delta i sina nationella lagstiftningen. Stora mängder data kan hämtas [länken](https://www.govtrack.us/data/congress/) med hjälp av en manuell skript som inte ingår i det här scenariot. Information om hur du hämtar data hittades i den [GovTrack API-dokumentationen](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Datakälla

I det här scenariot rådata som samlas in är en serie rättsliga åtgärder som introducerades av den oss Congress (föreslagna fakturor och lösningar) från 1973 till juni 2017 (93rd till 115th kongresser). Insamlade data är i JSON-format och innehåller en omfattande uppsättning information om de rättsliga åtgärderna. Referera till [GitHub länken](https://github.com/unitedstates/congress/wiki/bills) detaljerad beskrivning av datafält. För demonstration ändamål i det här scenariot, bara en del av datafält extraherades från JSON-filer. En tidigare kompilerade TVS fil `CongressionalDataAll_Jun_2017.tsv` som innehåller poster för dessa rättsliga åtgärder finns i det här scenariot. Filen TVS kan laddas ned automatiskt genom de bärbara datorerna `1_Preprocess_Text.ipynb` under anteckningsboksmappen eller `preprocessText.py` i Python-paketet.

### <a name="data-structure"></a>Datastruktur

Det finns nio datafält i datafilen. Namnen på data och beskrivningar finns på följande sätt.

| Fältnamn | Typ | Beskrivning | Innehåller värden som saknas |
|------------|------|-------------|---------------|
| `ID` | Sträng | ID för faktura/lösning. Formatet på det här fältet är [bill_type] [nummer]-[congress]. Till exempel ”hconres1-93” innebär faktura är ”hconres” (stöd för House samtidiga upplösning, referera till [det här dokumentet](https://github.com/unitedstates/congress/wiki/bills#basic-information)), faktura-värdet är ”1” och congress talet är ' 93 ”. | Nej |
| `Text` | Sträng | Innehållet i faktura/lösning. | Nej |
| `Date` | Sträng | Det datum som ursprungligen föreslagna faktura/lösning. I formatet ”åååå-mm-dd'. | Nej |
| `SponsorName` | Sträng | Namnet på den primära sponsor som föreslås faktura/lösning. | Ja |
| `Type` | Sträng | Rubrik-typ av primärt sponsra 'rep' (representant) eller 'Skic' (senator). | Ja |
| `State` | Sträng | Tillståndet för den primära sponsorn. | Ja |
| `District` | Integer | Distrikt antal primära sponsor om rubriken för sponsorn är ett ombud. | Ja |
| `Party` | Sträng | Part för den primära sponsorn. | Ja |
| `Subjects` | Sträng | Ämne villkoren läggs kumulativt av biblioteket Congress till växeln. Villkoren sammanfogas med kommatecken. Dessa villkor är skrivna med en mänskliga i biblioteket Congress och finns inte vanligtvis när information om växeln först publiceras. De kan läggas till när som helst. I slutet av livslängden för en faktura därför vissa ämne inte relevanta längre. | Ja |

## <a name="scenario-structure"></a>Scenario-struktur

Dokumentet samling analys exempel är uppdelad i två typer av produkterna. Den första typen är en serie iPython anteckningsböcker som visar detaljerade beskrivningar av hela arbetsflödet. Den andra typen är en Python-paket som koden exempel på hur du använder det paketet. Python-paket är generisk att hantera många användningsområden.

Filerna i det här exemplet är uppdelade enligt följande.

| Filnamn | Typ | Beskrivning |
|-----------|------|-------------|
| `aml_config` | Mapp | Azure Machine Learning arbetsstationen konfigurationsmappen avser [denna dokumentation](./experimentation-service-configuration-reference.md) för detaljerad experiment körning konfiguration |
| `Code` | Mapp | Mappen kod används för att spara Python-skript och Python-paket |
| `Data` | Mapp | Datamappen används för att spara mellanliggande filer |
| `notebooks` | Mapp | Mappen Jupyter-anteckningsböcker |
| `Code/documentAnalysis/__init__.py` | Python-fil | Python package init-fil |
| `Code/documentAnalysis/configs.py` | Python-fil | Konfigurationsfilen som används av dokumentet analysen Python-paket, inklusive fördefinierade konstanter |
| `Code/documentAnalysis/preprocessText.py` | Python-fil | Python-fil som används för att Förbearbeta data för underordnade aktiviteter |
| `Code/documentAnalysis/phraseLearning.py` | Python-fil | Python-fil som används för att lära dig fraser från data och transformera rådata |
| `Code/documentAnalysis/topicModeling.py` | Python-fil | Python-fil som används för att träna en modell för avsnittet Latenta Dirichlet allokering (LDA) |
| `Code/step1.py` | Python-fil | Steg 1 av dokumentet samling analys: Förbearbeta text |
| `Code/step2.py` | Python-fil | Steg 2 av dokumentet samling analys: fras learning |
| `Code/step3.py` | Python-fil | Steg 3 i dokumentet samling analys: träna och utvärdera LDA avsnittet modellen |
| `Code/runme.py` | Python-fil | Exempel på köra alla steg i en fil |
| `notebooks/1_Preprocess_Text.ipynb` | iPython bärbara datorer | Förbearbeta text och transformera rådata |
| `notebooks/2_Phrase_Learning.ipynb` | iPython bärbara datorer | Läs fraser från textdata (efter Transformera data) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython bärbara datorer | Träningsmodell LDA avsnittet |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython bärbara datorer | Sammanfatta innehållet i dokumentsamlingen baserat på en tränad LDA avsnittet modell |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython bärbara datorer | Analysera aktuell innehållet i en mängd textdokument och korrelera aktuell information mot andra metadata som associeras med dokumentsamlingen |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython bärbara datorer | Interaktiva visualisering av inlärda avsnittet modellen |
| `notebooks/winprocess.py` | Python-fil | Python-skriptet för Fleruppdragskörning som används av bärbara datorer |
| `README.md` | Markdown-fil | Filen Viktigt markdown |

### <a name="data-ingestion-and-transformation"></a>Datapåfyllning och omvandling

Den kompilerade datamängden `CongressionalDataAll_Jun_2017.tsv` sparas i Blob Storage och är tillgänglig både från i de bärbara datorerna och Python-skript. Det finns två steg för datapåfyllning och omvandling: förbearbetning textdata och frasen learning.

#### <a name="preprocess-text-data"></a>Förbearbeta textdata

Steget förbearbetning gäller bearbetning av naturligt språk tekniker för att rensa och förbereda rådata textdata. Den fungerar som en ozonbildande för oövervakade frasen learning och Latenta avsnittet modellering. Detaljerade steg för steg-beskrivningen finns i anteckningsboken `1_Preprocess_Text.ipynb`. Det finns också en Python-skriptet `step1.py` motsvarar anteckningsboken.

I det här steget datafilen TVS hämtas från Azure Blob Storage och importeras som en Pandas DataFrame. Varje rad-element i DataFrame är en enda sammanhängande lång sträng eller ett dokument. Varje dokument delas sedan in textblock som kommer att vara meningar, fraser eller subphrases. Uppdelningen av är utformat för att förhindra frasen learning processen från med hjälp av cross-mening eller mellan fras word strängar när learning fraser.

Det finns flera funktioner som är definierade för förbearbetning steg både i anteckningsboken och Python-paketet. Merparten av jobbet kan uppnås genom att anropa dessa två rader med koder.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Frasen learning

Steget frasen learning implementerar ett ramverk för grundläggande information om viktiga fraser mellan en stor mängd dokument. Beskrivningen i dokumentet har rätt ”[Modeling Multiword fraser med begränsad fraser-träd för förbättrad avsnittet modellering av vardagliga samtalsuttryck tal](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)”, som ursprungligen angavs i 2012 IEEE Workshop på talas språk Teknik. Detaljerad implementeringen av frasen learning steg visas i iPython anteckningsboken `2_Phrase_Learning.ipynb` och Python-skriptet `step2.py`.

Det här steget tar rensade texten som indata och lär sig de mest viktigaste fraserna som finns i en stor mängd dokument. Frasen learning är en återkommande process som kan delas in i tre uppgifter: antal n gram rangordnas möjliga fraser av viktat Pointwise ömsesidig informationen för sina ingående ord och omarbetning frasen till text. De tre aktiviteterna körs sekventiellt i flera iterationer tills de angivna fraserna har lärt.

I dokumentet analysis-Python-paketet, en Python-klass `PhraseLearner` har definierats i den `phraseLearning.py` filen. Nedan visas ett kodfragment som används för att lära dig fraser.

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
> Steget frasen learning har implementerats med flera. Dock mer CPU-kärnor gör **inte** innebär en snabbare körningstid. I våra tester bättre prestanda inte med mer än åtta kärnor på grund av arbetet med Fleruppdragskörning. Det tog ungefär två och en halv timme för att lära dig 25 000 fraser på en dator med åtta kärnor (3,6 GHz).
>

### <a name="topic-modeling"></a>Avsnittet modellering

Lär dig en Latenta avsnittet modellen använder är LDA det tredje steget i det här scenariot. Den [gensim](https://radimrehurek.com/gensim/) Python-paketet krävs i det här steget för att lära dig ett [LDA avsnittet modellen](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Motsvarande anteckningsboken för det här steget är `3_Topic_Model_Training.ipynb`. Du kan också läsa `step3.py` för hur du använder dokumentet analys paketet.

I det här steget är viktigaste uppgift att lära sig en LDA avsnittet modell och justera parametrarna hyper. Det finns flera parametrar behöver justeras när träna en LDA modell, men parametern viktigaste är antalet avsnitt. För få avsnitt kanske inte har insyn i dokumentsamlingen; När du väljer för leder många ”över samordna” en Kristi i många små, hög liknande ämnen. Syftet med det här scenariot är att visa hur du ställer in antalet hjälpavsnitt. Azure Machine Learning arbetsstationen ger friheten att köra experiment med annan konfiguration på olika beräkning sammanhang.

I dokumentet analys Python-paketet, några funktioner har definierats för att hjälpa användarna ta reda på det bästa antalet avsnitt. Det första tillvägagångssättet är att utvärdera samordning av avsnittet. Det finns fyra utvärdering matriser stöds: `u_mass`, `c_v`, `c_uci`, och `c_npmi`. Information om de fyra mätvärdena diskuteras i [det här dokumentet](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Det andra sättet är att utvärdera perplexity på en hålls out Kristi.

För utvärdering perplexity förväntas en ”U” form kurva ta reda på det bästa antalet avsnitt. Den vinkel positionen är det bästa valet. Det rekommenderas att utvärdera flera gånger med olika slumpmässiga startvärde och hämta medelvärdet. Utvärdera enhetligheten förväntas vara en ”n” form, vilket innebär att den enhetliga ökar med ökande antal avsnitt och minskar. En exempel-område för perplexity och `c_v` enhetlighet visas på följande sätt.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v överensstämmelse](./media/scenario-document-collection-analysis/c_v_Coherence.png)

I det här scenariot ökar i perplexity avsevärt efter 200 ämnen, medan enhetlighet värdet minskar avsevärt efter samt 200 avsnitt. Baserat på dessa diagram och önskan för mer allmän information jämfört med över klustrade avsnitt, Välj 200 avsnitt ska vara ett bra alternativ.

Du kan lära LDA avsnittet modellen i ett experiment kör, eller träna och utvärdera flera LDA modeller med olika avsnittet nummer konfigurationer i en enda körningen av experimentet. Det rekommenderas att köra flera gånger för en konfiguration och sedan hämta den genomsnittliga konsekvens och/eller perplexity utvärderingen. Information om hur du använder dokumentet analys paketet finns i `step3.py` fil. Ett exempel kodstycke är som följer.

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
> Körningstid för att träna en LDA avsnittet modell beror på flera faktorer, till exempel storleken på Kristi, hyper parametern konfiguration, samt antalet kärnor på datorn. Med hjälp av flera processorkärnor tränar en modell snabbare. Men med samma hyper-parametern innebär anger flera kärnor färre uppdateringar vid träning. Det rekommenderas att du har **minst 100 uppdateringar för att träna modellen för konvergerade LDA**. Förhållandet mellan antalet uppdateringar och hyper parametrar beskrivs i [inlägget](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) och [inlägget](http://miningthedetails.com/blog/python/lda/GensimLDA/). I våra tester tog ca 3 timmar för att träna modellen en LDA med 200 avsnitt med konfigurationen av `workers=15`, `passes=10`, `chunksize=1000` på en dator med 16 kärnor (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Avsnittet sammanfattningen och analys

Avsnittet sammanfattningen och analys består av två bärbara datorer, medan det finns inga motsvarande funktioner i dokumentet analys paketet.

I `4_Topic_Model_Summarization.ipynb`, visas hur du sammanfatta innehållet i dokument baserat på en tränad LDA avsnittet modell. Sammanfattningen tillämpas på en modell för avsnittet av LDA lärt dig i steg 3. Den visar hur du mäter prioritet eller kvaliteten på ett avsnitt med avsnittet för att dokumentet renhet mått. Det här måttet renhet förutsätts Latenta avsnitt som dominerar dokument som de visas mer semantiskt viktigt än Latenta information som är svagt fördelade på flera dokument. Detta begrepp introducerades i dokumentet ”[Latenta avsnittet modellering för ljud Kristi sammanfattning](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)”.

Bärbar dator `5_Topic_Model_Analysis.ipynb` visar hur du analyserar aktuell innehållet i en samling dokument och korrelera aktuell information mot andra metadata som associeras med dokumentsamlingen. Några områden införs i den här anteckningsboken för att bättre förstå inlärda avsnittet och dokumentsamlingen användarna.

Bärbar dator `6_Interactive_Visualization.ipynb` visar hur du interaktivt visualisera inlärda avsnittet modellen. Den omfattar fyra interaktiva visualiseringen uppgifter.

## <a name="conclusion"></a>Slutsats

Den här verkligt scenario illustrerar hur du använder välkänd text analytics metoder (i det här fallet frasen learning och LDA avsnittet modellering) som ger en robust modell och Azure Machine Learning arbetsstationen hur spåra modellen prestanda och köras utan problem inlärning på högre nivå. I större detalj:

* Använd frasen learning och avsnittet modellering att bearbeta en samling dokument och skapa en stabil modell. Om samlingen av dokument är stort, Azure Machine Learning arbetsstationen enkelt skala det in och ut. Användare har dessutom friheten att köra experiment på olika beräkning kontexten enkelt från Azure Machine Learning-arbetsstationen.

* Azure Machine Learning arbetsstationen innehåller båda alternativen för att köra bärbara datorer i en steg-för-steg sätt och Python-skriptet att köra ett hela experiment.

* Hyper-parametern prestandajustering med Azure Machine Learning-arbetsstationen för att hitta det bästa antalet avsnitt behövs för att lära dig. Azure Machine Learning arbetsstationen kan du spåra modellen prestanda och sömlöst kör olika inlärning på högre nivå.

* Azure Machine Learning arbetsstationen kan hantera körningshistorik och inlärda modeller. Den gör att data forskare att snabbt identifiera det bästa utför modeller och hitta skript och data som används för att generera dessa.

## <a name="references"></a>Referenser

* **David J. Hazen, Fred Richardson**, [ _Modeling Multiword fraser med begränsad fraser träd för förbättrad avsnittet modellering av vardagliga samtalsuttryck tal_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Talas teknik Workshop (SLT), 2012 IEEE. IEEE 2012.

* **David J. Hazen**, [ _Latenta avsnittet modellering för ljud Kristi sammanfattning_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12 årliga konferens internationella tal kommunikation kopplingen. 2011.

* **Michael Roder, Andreas båda, Alexander Hinneburg**, [ _utforska utrymme för avsnittet enhetlighet åtgärder_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Förfarandet för ACM åttonde internationella konferens om webbsökning och datautvinning. ACM 2015.
