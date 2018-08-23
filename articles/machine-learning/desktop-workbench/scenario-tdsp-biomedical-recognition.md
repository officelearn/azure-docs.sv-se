---
title: Biomedicinsk Enhetsidentifiering – Tdsp - Azure Machine Learning | Microsoft Docs
description: Ett Team Data Science Process projekt Snabbstart som använder djupinlärning för biomedicinsk i Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f6ce43c2d290bacee10e102cc6c382981db9917f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061129"
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Biomedicinsk Enhetsidentifiering med Team Data Science Process TDSP ()-mall

Entitetextrahering är en underaktivitet för extrahering av information (även kallat [igenkänning av namngivna-entiteter (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), entitet storlekar och enhetens identifiering). Syftet med den här verkligt scenario är att fokusera på hur du använder Azure Machine Learning Workbench för att lösa en komplicerad uppgift naturligt språk bearbetning (NLP), till exempel entitetextrahering från ostrukturerad text:

1. Så här för att träna ett neural ord inbäddningar modellera på en text Kristi av cirka 18 miljoner PubMed sammanfattningar med [Spark Word2Vec implementering](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Hur du skapar en djupgående lång kortvariga minne (LSTM) återkommande neuralt nätverk modell för entitetextrahering på en GPU-aktiverade Azure Data Science Virtual Machine (GPU DS VM) på Azure.
2. Visa den domänspecifika word inbäddningar modellen kan överträffar allmän word inbäddningar modeller i entiteten igenkänning av uppgiften. 
3. Visa hur du tränar och operationalisera modeller för djupinlärning med hjälp av Azure Machine Learning Workbench.

4. Visa följande funktioner i Azure Machine Learning Workbench:

    * Tabellkolumnerna [Team Data Science Process (TDSP) struktur och mallar](how-to-use-tdsp-in-azure-ml.md)
    * Automatisk hantering av dina projektberoenden, inklusive hämtningen och installationen
    * Körning av Python-skript i olika beräkningsmiljöer
    * Kör historikspårning för Python-skript
    * Körningen av jobb på fjärranslutna Spark compute-sammanhang med HDInsight Spark 2.1-kluster
    * Körningen av jobb i remote GPU virtuella datorer på Azure
    * Enkelt driftsättning av deep learning-modeller som webbtjänster på Azure Container Services (ACS)

## <a name="use-case-overview"></a>Översikt över användningsfall
Biomedicinsk Enhetsidentifiering namngivna är ett viktigt steg för komplexa biomedicinsk NLP-uppgifter som: 
* Extraherar omnämnanden av namngivna entiteter sådana sjukdomar, droger, kemikalier och symptom från elektroniska poster för medicin och hälsa.
* Drug identifiering
* Förstå samverkan mellan olika entitet typer som drug drug interaktion, drug sjukdomar relationen och gene protein relation.

Vår användningsfall fokuserar på hur stora mängder Ostrukturerade data Kristi, till exempel Medline PubMed sammanfattningar kan analyseras för att träna ett ord som bäddar in modellen. Sedan betraktas utdata-inbäddningar som skapas automatiskt funktioner för att träna en neural entitet extraktor.

Vår resultatet visar att modellträning för biomedicinsk entitet extrahering på domänspecifika ordet bäddar in funktioner överlägsen modellen tränas på den allmänna funktionstyp. Domänspecifika modellen kan identifiera 7012 entiteter (av 9475) med F1-poängen för 0.73 jämfört med 5274 entiteter med F1-poängen för 0.61 för allmän modell.

Följande bild illustrerar arkitekturen som används för att bearbeta data och träna modeller.

![Arkitektur](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Databeskrivning

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec modelldata för utbildning
Vi laddade ned MEDLINE abstrakt rådata från [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Data är tillgängligt för allmänheten i form av XML-filer på sina [FTP-servern](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Det finns 892 XML-filer på servern och var och en av XML-filerna har information över 30 000 artiklar. Mer information om data collection steget finns i avsnittet projektstruktur. Fälten i varje fil är 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. LSTM modelldata för utbildning

Neural entitet extrahering modellen har tränas och utvärderas på offentligt tillgängliga datauppsättningar. Om du vill ha en detaljerad beskrivning om dessa datauppsättningar, kan du referera till följande källor:
 * [Biografi entitet igenkänning av aktivitet i BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR uppgift Kristi](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - uppgift 9.1 (Drug teckenläsning)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen Azure-galleriet
Följande är en länk till den offentliga GitHub-lagringsplatsen för verkligt scenario som innehåller koden och mer detaljerad beskrivning: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Förutsättningar 

* En Azure [prenumeration](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench. Se [installationsguide](../service/quickstart-installation.md). För närvarande kan Azure Machine Learning Workbench installeras på endast följande operativsystem: 
    * Windows 10 eller Windows Server 2016
    * macOS Sierra (eller senare)


### <a name="azure-services"></a>Azure-tjänster
* [HDInsight Spark-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) version Spark 2.1 för Linux (HDI 3.6) för skalbar beräkning. För att bearbeta med MEDLINE sammanfattningar som beskrivs nedan, måste den lägsta konfigurationen av: 
    * Huvudnod: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) storlek
    * Arbetsnoderna: minst 4 av [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). I vårt arbete använde vi 11 arbetsnoder D12_V2 storlek.
* [NC6 Data Science Virtual Machine (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) för beräkning, skala upp.

### <a name="python-packages"></a>Python-paket

De nödvändiga beroendena definieras i filen aml_config/conda_dependencies.yml under projektmappen scenario. De beroenden som definierats i den här filen etableras automatiskt för körs mot docker, virtuell dator och HDI-kluster mål. Information om formatet Conda-miljö i [här](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Grundläggande anvisningar om hur Azure Machine Learning (AML) workbench
* [Översikt](../service/overview-what-is-azure-ml.md)
* [Installation](../service/quickstart-installation.md)
* [Använda TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Läsa och skriva filer](how-to-read-write-files.md)
* [Så här använder du Jupyter-anteckningsböcker](how-to-use-jupyter-notebooks.md)
* [Hur du använder GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Scenario-struktur
I scenariot vi använder TDSP struktur och dokumentation projektmallarna (bild 1), som följer den [TDSP livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projektet har skapats utifrån instruktionerna [här](./how-to-use-tdsp-in-azure-ml.md).


![Fyll i projektinformation](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Den stegvisa arbetsflöden för datavetenskap är följande:

### <a name="1-data-acquisition-and-understanding"></a>1. Förvärv och förståelse av data

Se [Data förvärv och förståelse av](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Rå MEDLINE Kristi har totalt 27 miljoner sammanfattningar där cirka 10 miljoner artiklar har ett tomt abstrakt fält. Azure HDInsight Spark används för att bearbeta stordata som inte kan läsas in i minnet för en enskild dator som en [Pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Data hämtas först till Spark-klustret. Sedan följande steg utförs på den [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* parsa XML-filerna med hjälp av Medline XML-parsern
* Förbearbeta abstrakt texten inklusive mening delning, tokenisering och användningsfall normalisering.
* Exkludera artiklar där abstrakt fältet är tomt eller har en kort text 
* Skapa en word-terminologi från sammanfattningar för utbildning
* Träna ordet inbäddning neural modellen. Mer information finns i [GitHub kod länken](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) att komma igång.


Efter parsning av XML-filer har data följande format: 

![Data, exempel](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Neural entitet extrahering modellen har tränas och utvärderas på offentligt tillgängliga datauppsättningar. Om du vill ha en detaljerad beskrivning om dessa datauppsättningar, kan du referera till följande källor:
 * [Biografi entitet igenkänning av aktivitet i BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR uppgift Kristi](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - uppgift 9.1 (Drug teckenläsning)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modellering

Se [modellering](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modellering är scenen där vi visar hur du kan använda de data som hämtades i föregående avsnitt för att träna dina egna ord inbäddning modell och använda den för andra nedströms aktiviteter. Även om vi använder PubMed data, pipeline för att generera inbäddningar är generisk och kan återanvändas för att träna ordinbäddningar för andra domäner. Det är viktigt att word2vec har tränats på en stor mängd data för inbäddningar ska vara en korrekt återgivning av dessa data.
När vi har ordinbäddningar som är redo kan tränar vi en modell för djupa neurala nätverk som använder inlärda inbäddningar för att initiera bädda in lagret. Vi Markera bädda in lagret som icke-trainable men som inte är obligatoriskt. Utbildning av ordet bädda in modellen är oövervakad och därför kommer du att kunna utnyttja utan etikett texter. Dock utbildning av igenkänningsfunktion för entiteten för är en övervakad inlärning-aktivitet och dess precision beror på hur mycket och kvaliteten på en manuellt kommenterade data. 


#### <a name="21-feature-generation"></a>2.1. Funktionen generation

Se [funktionen generation](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec är ordet bädda in oövervakade Inlärningsalgoritmen som tränar en modell för neuralt nätverk från en utan etikett utbildning Kristi. Den genererar en kontinuerlig vektor för varje ord i Kristi som representerar dess semantisk information. De här modellerna är enkla neurala nätverk med ett enda dolda lager. Vektorer/ordinbäddningar lärs genom backpropagation och stokastisk brantaste lutningsmetoden. Det finns två typer av word2vec modeller, nämligen Skip-Gram och kontinuerlig-egenskapsuppsättning-för-orden (kontrollera [här](https://arxiv.org/pdf/1301.3781.pdf) för mer information). Eftersom vi använder den MLlib implementeringen av word2vec som har stöd för Skip-gram-modellen, vi Beskriv den här (bild som kommer från [länk](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Hoppa över Gram modell](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Modellen använder hierarkisk Softmax och negativa sampling för att optimera prestanda. Hierarkisk SoftMax (H-SoftMax) är en uppskattning INSPIRERAD av binära träd. H-SoftMax ersätter i stort sett fast SoftMax lagret med en hierarkisk layer med orden som lämnar. På så sätt kan vi dela upp beräkna sannolikheten för ett ord i en sekvens med sannolikhetsberäkningar, vilket sparar oss slipper beräkna dyra normalisering över alla orden. Eftersom ett balanserat binära träd har en mängd log2 (| V |) (V är vokabulär), vi behöver bara att utvärdera högst log2 (| V |) noder för att hämta den slutliga sannolikheten för ett ord. Sannolikheten för en word-w får sin kontext c är sedan bara produkten av sannolikheten för rätt och vänster stängs respektive den leda till dess lövnod. Vi kan skapa ett huffmanträd baserat på antalet ord i datauppsättningen för att säkerställa att oftare ord får kortare garantier. Mer information finns i [den här länken](http://sebastianruder.com/word-embeddings-softmax/).
Bild som kommer från [här](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualisering

När vi har ordinbäddningar vill vi visualisera dem och visa relationer mellan semantiskt liknande ord. 

![W2V likheter](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Vi har visat två olika sätt att visualisera inbäddningar. Den första som använder en PCA projicera hög dimensionell vektor till en 2D-vektor utrymme. Detta leder till betydande förlust av information och visualiseringen är inte lika exakt. Andra är att använda PCA med [nationell t EXPERT](https://distill.pub/2016/misread-tsne/). nationell t EXPERT är en icke-linjära dimensionalitet minskning teknik som lämpar sig väl för att bädda in hög-dimensionell data i ett utrymme av två eller tre dimensioner, som sedan kan visualiseras i punktdiagram ritning.  Varje hög-dimensionell objekt modellerar genom en två - eller och tredimensionell tidpunkt på ett sätt att liknande objekt modelleras av närliggande punkter och olika objekt modelleras av avlägsna punkter. Det fungerar i två delar. Först skapar den en sannolikhetsfördelning över par i högre dimensionell utrymmet på ett sätt som liknande objekt har en hög sannolikhet för tas och olika punkter har låg sannolikheten för att hämta valt. Därefter definierar en liknande sannolikhetsfördelning över punkter på en låg dimensionell karta och minimerar KL skillnaderna mellan de två distributionerna med avseende på plats av punkter på kartan. Platsen för punkter i dimensionen med låg hämtas genom att minimera KL skillnaderna med brantaste Lutningsmetoden. Men nationell t EXPERT kanske inte alltid tillförlitlig. Mer information om implementeringen finns [här](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


I följande bild visas visualiseringen nationell t EXPERT ger mer uppdelning av word vektorer och potentiella klustring mönster. 


* Visualisering med PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Visualisering med t-EXPERT

![t-EXPERT](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Återställningspunkter som är närmast ”Cancer” (de är alla typer av Cancer)

![Återställningspunkter som är närmast Cancer](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Träna neural entitet-extraktor

Se [träna neural entitet extraktor](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Feed-forward neuralt nätverksarkitekturen kännetecknas av ett problem som de behandlar varje indata och utdata som är oberoende av andra indata och utdata. Den här arkitekturen kan inte utforma sequence-sequence etikettering uppgifter, till exempel maskinöversättning och entitetextrahering. Återkommande neuralt nätverk modeller lösa problemet som de kan skicka information som beräknas tills nu till nästa nod. Den här egenskapen anropas med minne i nätverket eftersom det är att kunna använda den tidigare beräknade informationen som visas i följande bild:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Vanliga rnn: er som faktiskt drabbas av den [försvinner toning problemet](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) på grund av som de kan inte använda all information som de har läst. Problemet blir tydligt endast när en stor mängd kontext krävs för att göra en förutsägelse. Men har inte några modeller som LSTM sådant problem, i själva verket de har utformats för att komma ihåg långsiktig beroenden. Till skillnad från vanilj rnn: er som har ett enda neurala nätverk, har de lstm: er samverkan mellan fyra neurala nätverk för varje cell. En detaljerad förklaring av hur LSTM fungerar finns i [det här inlägget](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM Cell](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Nu ska vi prova att sätta ihop våra standarder LSTM-baserade återkommande neuralt nätverk och försök att extrahera entitetstyper som drug, sjukdomar och symtom omnämnanden från PubMed data. Det första steget är att hämta en stor mängd märkta data och som du skulle ha gissa, som inte är enkelt! De flesta av medicinska data innehåller mycket känsliga information om personen och därför är inte offentligt tillgängliga. Vi förlitar sig på en kombination av två olika datauppsättningar som är allmänt tillgängliga. Den första datauppsättningen från Semeval 2013 - uppgift 9.1 (Drug teckenläsning) och den andra är från BioCreative V CDR aktiviteten. Vi kombinerar och automatisk märkning dessa två datauppsättningar så att vi kan identifiera både läkemedel och sjukdomar från medicinska texter och utvärdera vår ordinbäddningar. Mer information om implementeringen, finns i [GitHub kod länk](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Modell-arkitektur som vi har använt över alla koder och jämförelse visas nedan. Parametern som ändras för olika datauppsättningar är den maximala sekvens (här 613).

![LSTM modell](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Modell-utvärdering
Se [modellera utvärdering](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Vi använder skriptet utvärdering från delade uppgiften [biografi entitet igenkänning av aktivitet i biografi NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) att utvärdera den precision och återkallande F1 poängen för modellen. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Domän jämfört med allmänna word inbäddning modeller

Följande är en jämförelse mellan det arbete du utfört två typer av funktionen: (1) ordinbäddningar tränats på PubMed sammanfattningar och (2) ordinbäddningar tränats på Google nyheter. Tydligt ser vi att modellen i domänen är överlägsen allmän modell. Därför har ett specifikt ord bädda in modellen i stället för att är mycket mer praktiskt att använda en allmän. 

* Uppgift #1: Läkemedel och sjukdomar identifiering

![Modellen jämförelse 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Vi utför utvärderingen av ordinbäddningar på andra datauppsättningar på liknande sätt och se den i domän modellen är alltid bättre.

* Uppgift #2: Proteiner, Cell rad, celltyp, DNA och RNA identifiering

![Modellen jämförelse 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Uppgift #3: Kemikalier och sjukdomar identifiering

![Modellen jämförelse 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Uppgift #4: Droger identifiering

![Modellen jämförelse 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Uppgift #5: Gener identifiering

![Modellen jämförelse 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow jämfört med CNTK
Alla rapporterade modeller tränas med TensorFlow Keras som serverdel. Keras med CNTK-serverdel stöder inte ”omvänd” när denna åtgärd utfördes. För jämförelse, har vi därför tränas en enkelriktad LSTM modell med CNTK-serverdel och jämfört med det en enkelriktad LSTM modell med TensorFlow-serverdel. Installera CNTK 2.0 för Keras från [här](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Modellen jämförelse 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Vi har avslutat som CNTK utför som bra som Tensorflow både vad gäller utbildningstid per epoch (60 sekunder för CNTK och 75 sekunder för Tensorflow) och antalet test-entiteter som har identifierats. Vi använder enkelriktade lager för utvärdering.


### <a name="3-deployment"></a>3. Distribution

Se [distribution](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Vi har distribuerat en webbtjänst på ett kluster i den [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Driftsättning miljön etablerar Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om processen för driftsättning [här](model-management-service-deploy.md ).


## <a name="conclusion"></a>Sammanfattning

Vi gick över information om hur du tränar en bädda in word-modell med algoritmen Word2Vec på Spark och använda extraherade inbäddningar som funktioner för att träna ett djupa neurala nätverk för entitetextrahering. Vi har tillämpat pipelinen utbildning på biomedicinsk domänen. Pipelinen är dock allmän som ska användas för att identifiera anpassad entitetstyper av andra domäner. Du behöver bara tillräckligt med data och du kan enkelt anpassa arbetsflödet som presenteras här för en annan domän.

## <a name="references"></a>Referenser

* Tomas Mikolov, Kai Hasselgren, Greg Corrado och Jeffrey Dean. 2013a. Effektiv uppskattning av ord garantier i vektor utrymme. I förfaranden i ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Hasselgren, Greg S Corrado och Jeff Dean. 2013b. Distribuerade framställningar av ord och fraser och deras compositionality. Sidor 3111 – 3119 i åtal NIPS.
* Bengt Chiu, Gamal Crichton, Anna Korhonen och Sampo Pyysalo. 2016. [Hur du träna bra Ordinbäddningar för biomedicinsk NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), handlingar i från den femtonde workshopen om biomedicinsk naturlig Språkbearbetning, sidor 166 – 174.
* [Vektor framställningar av ord](https://www.tensorflow.org/tutorials/word2vec)
* [Återkommande Neurala nätverk](https://www.tensorflow.org/tutorials/recurrent)
* [Problem som kan uppstå med Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: lärdomar](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

