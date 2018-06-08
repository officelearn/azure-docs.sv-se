---
title: Biomedicinska entitet recognition - teamet datavetenskap Process - Azure Machine Learning | Microsoft Docs
description: Ett Team av vetenskapliga data projektet Snabbstart som använder djup learning för biomedicinsk entitet recognition i Azure Machine Learning-arbetsstationen.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: ae8c8ed9d397df0c82b74f051ff14729c0f41623
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837290"
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Biomedicinska entitet bokföring med hjälp av mallen för Team Data vetenskap processen (TDSP)

Entiteten extrahering är en underaktivitet för att utvinna information (även kallat [namngiven entitet recognition (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), entitet högoptimerat och enhetens identifiering). Syftet med den här verkligt scenario är att fokusera på hur du använder Azure Machine Learning-arbetsstationen för att lösa en komplicerad naturligt språk bearbetning (NLP)-aktivitet, till exempel entitet extrahering från Ostrukturerade text:

1. Hur för att träna neural word inbäddningar modell en text Kristi av cirka 18 miljoner PubMed sammanfattningar med [Spark Word2Vec implementering](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Hur du skapar en djup lång kortvariga minne (LSTM) återkommande neurala nätverket modell för entiteten extrahering på en GPU-aktiverade Azure datavetenskap virtuell dator (GPU DS VM) på Azure.
2. Visa den domänspecifika word inbäddningar modellen kan överträffar allmänt ord inbäddningar modeller i entiteten recognition uppgiften. 
3. Visa hur du tränar och operationalisera djup learning-modeller med hjälp av Azure Machine Learning-arbetsstationen.

4. Visa Azure Machine Learning-arbetsstationen följande funktioner:

    * Instansieringen av [Team Data vetenskap processen (TDSP) struktur och mallar](how-to-use-tdsp-in-azure-ml.md)
    * Automatisk hantering av din projektberoenden inklusive hämtning och installation
    * Körningen av Python-skript på olika beräknings-miljöer
    * Kör historikspårning för Python-skript
    * Körningen av jobb på fjärranslutna Spark compute kontexter med HDInsight Spark 2.1 kluster
    * Körningen av jobb i fjärranslutna GPU virtuella datorer på Azure
    * Enkelt operationalization djup learning-modeller som webbtjänster på Azure Container insamlingstjänsten (ACS)

## <a name="use-case-overview"></a>Använd case-översikt
Biomedicinska namngiven enhet recognition är ett viktigt steg för komplexa biomedicinska NLP uppgifter som: 
* Extraherar nämns i namngivna enheter sådana sjukdomar, narkotika, kemikalier och symptom från elektroniska medicinska eller hälsa poster.
* Identifiering av läkemedel
* Förstå samverkan mellan olika entitet typer som läkemedel läkemedel interaktion, läkemedel sjukdom relationen och Gen protein relationen.

Vår Användarscenario fokuserar på hur stora mängder Ostrukturerade data Kristi, till exempel Medline PubMed sammanfattningar kan analyseras för att träna ett ord bädda in modell. Sedan betraktas utdata inbäddningar som skapas automatiskt funktioner för att träna neural entitet-extraktor.

Vår resultatet visar att biomedicinska entitet extrahering modellen utbildning på domänspecifika ordet bädda in funktioner överlägsen modellen med allmänna funktionstyp. Domän-specifika modellen kan identifiera 7012 entiteter korrekt (out-of 9475) med F1-poängen för 0.73 jämfört med 5274 entiteter med F1-poängen för 0.61 för allmän modell.

Följande bild illustrerar arkitekturen som användes för att bearbeta data och träna modeller.

![Arkitektur](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Beskrivning av data

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec modelldata utbildning
Vi hämtade MEDLINE abstrakt rådata från [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Data är offentligt tillgängliga i form av XML-filer på sina [FTP-servern](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Det finns 892 XML-filer på servern och varje XML-filerna har informationen om 30 000 artiklar. Mer information om data collection steg finns i avsnittet projektstruktur. Fält som finns i varje fil är 
        
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

### <a name="2-lstm-model-training-data"></a>2. LSTM modelldata utbildning

Neural entitet extrahering modellen har tränas och utvärderas på offentligt tillgängliga datauppsättningar. Om du vill ha en detaljerad beskrivning av dessa data kan du referera till följande källor:
 * [Bio entitet Recognition uppgiften på BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR uppgiften Kristi](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - uppgiften 9.1 (läkemedel Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen för Azure-galleriet
Följande är en länk till den offentliga GitHub-databasen i verkligt scenario som innehåller koden och mer detaljerad beskrivning: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Förutsättningar 

* En Azure [prenumeration](https://azure.microsoft.com/free/)
* Azure Machine Learning-arbetsstationen. Se [installationsguiden](../service/quickstart-installation.md). För närvarande kan i Azure Machine Learning-arbetsstationen installeras på endast följande operativsystem: 
    * Windows 10 eller Windows Server 2016
    * macOS Sierra (eller senare)


### <a name="azure-services"></a>Azure-tjänster
* [HDInsight Spark-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) version 2.1 Spark på Linux (HDI 3,6) för skalbara beräkning. För att bearbeta hela mängden MEDLINE sammanfattningar som beskrivs nedan, måste den lägsta konfigurationen av: 
    * Huvudnod: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) storlek
    * Arbetsnoderna: minst 4 i [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). I vårt arbete använde vi 11 arbetarnoder D12_V2 storlek.
* [NC6 Data vetenskap virtuell dator (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) för skala upp beräkning.

### <a name="python-packages"></a>Python-paket

De nödvändiga beroendena definieras i filen aml_config/conda_dependencies.yml under projektmappen scenario. Beroenden som definierats i den här filen tillhandahålls automatiskt för körs mot docker, virtuell dator och HDI-kluster mål. Mer information om formatet Conda miljö, referera till [här](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Grundläggande anvisningar för Azure Machine Learning (AML) arbetsstationen
* [Översikt](../service/overview-what-is-azure-ml.md)
* [Installation](../service/quickstart-installation.md)
* [Med hjälp av TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Läsa och skriva filer](how-to-read-write-files.md)
* [Hur du använder Jupyter-anteckningsböcker](how-to-use-jupyter-notebooks.md)
* [Hur du använder GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Scenario-struktur
För scenariot har vi använder TDSP struktur och dokumentation projektmallarna (bild 1), vilket följer den [TDSP livscykel](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projektet har skapats utifrån anvisningarna [här](./how-to-use-tdsp-in-azure-ml.md).


![Fyll i projektet](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Stegvisa datavetenskap arbetsflödet är följande:

### <a name="1-data-acquisition-and-understanding"></a>1. Förvärv och förståelse av data

Se [Data förvärv och förstå](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Rådata MEDLINE Kristi har totalt 27 miljoner sammanfattningar där cirka 10 miljoner artiklar har ett tomt abstrakt fält. Azure HDInsight Spark används för att bearbeta big data som kan läsas in i minnet på en dator som en [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Data hämtas först i Spark-kluster. Sedan följande steg utförs på den [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* tolka XML-filerna med hjälp av Medline XML-parsern
* Förbearbeta abstrakta texten, inklusive meningen dela tokenisering och case normalisering.
* Exkludera artiklar där abstrakt fältet är tomt eller har kort text 
* Skapa en word-terminologi från utbildning sammanfattningar
* Träna ordet bädda in neural modell. Mer information finns i [GitHub kod länken](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) att komma igång.


Efter parsning av XML-filer har data följande format: 

![Data, exempel](./media/scenario-tdsp-biomedical-recognition/datasample.png)

Neural entitet extrahering modellen har tränas och utvärderas på offentligt tillgängliga datauppsättningar. Om du vill ha en detaljerad beskrivning av dessa data kan du referera till följande källor:
 * [Bio entitet Recognition uppgiften på BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR uppgiften Kristi](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - uppgiften 9.1 (läkemedel Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modellering

Se [Modeling](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modellering är steget där vi visar hur du använder data som hämtades i föregående avsnitt för att träna din egen word bädda in modell och använda den för andra underordnade aktiviteter. Även om vi använder informationen PubMed rörledning för att generera inbäddningar är generisk och kan användas för att träna word inbäddningar för andra domäner. Det är viktigt att word2vec har installerats på en stor mängd data för inbäddningar ska vara en exakt representation av data.
När vi har den word inbäddningar redo kan vi tränar en modell för djupa neurala nätverk som använder inlärda inbäddningar initiera inbäddning lagret. Vi Markera inbäddning lagret som icke-trainable men som inte är obligatoriskt. Utbildning av word bädda in modellen är oövervakad och därför kommer du att dra nytta av namnlösa texter. Dock utbildning av entiteten recognition modellen är en övervakad inlärning aktivitet och dess noggrannhet beror på hur mycket och kvaliteten på en manuellt kommenterats data. 


#### <a name="21-feature-generation"></a>2.1. Funktionen generation

Se [funktion generation](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec är ordet bädda in oövervakade Inlärningsalgoritmen som tränar en modell för neurala nätverket från en namnlösa utbildning Kristi. Den genererar en kontinuerlig vektor för varje ord i Kristi som representerar dess semantisk information. Dessa modeller är enkla neurala nätverk med en enda dolda lagret. Word angreppsmetoderna/inbäddningar lärs genom backpropagation och stokastisk toning härstammar. Det finns två typer av word2vec modeller, nämligen Skip-Gram och kontinuerlig-egenskapsuppsättning-för-orden (kontrollera [här](https://arxiv.org/pdf/1301.3781.pdf) för mer information). Eftersom vi använder den MLlib implementeringen av word2vec som stöder hoppa över gram modellen vi Beskriv den här (bild från [länk](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Hoppa över Gram modellen](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Modellen använder hierarkiska Softmax och negativa provtagning för att optimera prestanda. Hierarkiska SoftMax (H SoftMax) är en uppskattning inspirerat av binär träd. H SoftMax ersätter huvudsakligen platt SoftMax lagret med en hierarkisk lagret som har ord som lämnar. Detta gör att vi kan dela upp beräkna sannolikheten att ett ord i en sekvens av sannolikhetsberäkningar, vilket slipper oss beräkna dyra normalisering över alla ord. Eftersom ett belastningsutjämnade binära träd har ett djup på log2 (| V |) (V är ordlistan), behöver vi bara utvärdera högst log2 (| V |) noder för att hämta den slutliga sannolikheten för en word. Sannolikheten för en word-w anges dess kontext c sedan är helt enkelt produkten av troliga med rätt vänster stängs respektive att leda till dess lövnod. Vi kan skapa ett huffmanträd baserat på frekvensen av orden i dataset för att säkerställa att oftare ord hämta kortare garantier. Mer information finns i [länken](http://sebastianruder.com/word-embeddings-softmax/).
Bild som hämtats från [här](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualisering

När vi har word-inbäddningar vill vi visualisera dem och visa relationer mellan semantiskt liknande ord. 

![W2V likhet](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Vi har visas två olika sätt att visualisera inbäddningar. Den första som använder en PCA projicera hög dimensionell vektorn till en 2D-vektor adressutrymme. Detta leder till en betydande förlust av information och visualiseringen är inte lika exakt. Andra är att använda PCA med [t Experten](https://distill.pub/2016/misread-tsne/). t Experten är en teknik för minskning av linjära dimensionalitet som lämpar sig väl för inbäddning hög endimensionell data i ett utrymme av två eller tre dimensioner, som kan sedan visualiseras i punktdiagram ritning.  Den modeller varje hög endimensionell objekt med en två - eller och tredimensionell punkt så att liknande objekt modelleras efter närliggande punkter och modelleras olika objekt genom att avlägsna punkter. Det fungerar i två delar. Den skapar en sannolikhetsfördelningen över par i högre dimensionell utrymme på ett sätt liknande objekt har en hög sannolikhet för tas och olika punkter ha låg sannolikheten för att hämta plockats först. Därefter definierar en liknande sannolikhetsfördelningen över punkter i en låg dimensionell karta och minimerar KL skillnaderna mellan de två distributioner med avseende på platsen för datapunkter på kartan. Platsen för punkter i dimensionen låg hämtas genom att minimera KL skillnaderna med hjälp av toning härstammar. Men t Experten kanske inte alltid tillförlitlig. Implementeringsdetaljer hittar [här](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


I följande bild visas t Experten visualiseringen tillhandahåller mer avgränsning av word angreppsmetoderna och potentiella klustring mönster. 


* Visualisering med PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Visualisering med t-Experten

![t-Experten](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Återställningspunkter som är närmast ”Bröstcancerdiagnoser” (det är alla undertyper av Bröstcancerdiagnoser)

![Återställningspunkter som är närmast Bröstcancerdiagnoser](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Träna neural entiteten extraktor

Se [träna neural entiteten extraktor](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Neural feed vidarebefordra nätverksarkitektur lider av ett problem att de behandlas varje indata och utdata som oberoende av andra indata och utdata. Den här arkitekturen kan inte modell sequence-sequence märkning uppgifter som maskinöversättning och extrahering av entiteten. Återkommande neurala nätverket modeller lösa detta problem Eftersom de kan skicka information beräknad tills nu till nästa nod. Den här egenskapen anropas med minne i nätverket eftersom det är kan du använda den tidigare beräknad informationen som visas i följande bild:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Vanliga RNNs faktiskt drabbas av den [försvinner toning problemet](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) på grund av som de kan inte använda all information som de tidigare. Problemet blir tydligt bara när en stor mängd kontext krävs för att göra en förutsägelse. Men modeller som LSTM lider inte från sådana problem, i själva verket de har utformats för att komma ihåg långsiktiga beroenden. Till skillnad från vanilj RNNs som har ett enda neurala nätverk har LSTMs samverkan mellan fyra neurala nätverk för varje cell. En detaljerad förklaring av hur LSTM fungerar finns i [inlägget](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM Cell](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Vi försöker sätta ihop våra egna LSTM återkommande neurala nätverk och att extrahera entitetstyper som läkemedel, nämns sjukdom och symtom från PubMed data. Det första steget är att hämta stora mängder märkta data och som du skulle ha gissa, som inte är enkelt! De flesta medicinska data innehåller mycket känslig information om personen och därför är inte tillgänglig. Vi är beroende av en kombination av två olika datauppsättningar som är offentligt tillgängliga. Den första datauppsättningen kommer från Semeval 2013 - uppgiften 9.1 (läkemedel Recognition) och den andra är från BioCreative V CDR aktiviteten. Vi kombinerar och automatiskt etiketter dessa två datamängder så att vi kan identifiera både narkotika och sjukdomar från medicinska texter och utvärdera vår word-inbäddningar. För implementeringsinformation, se [GitHub kod länk](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

Modell-arkitektur som vi har använt över alla koder och jämförelse visas nedan. Parametern som ändras för olika DataSet är den maximala-sekvenslängd (här 613).

![LSTM modellen](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Modellen utvärdering
Se [modell utvärdering](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Vi använder skriptet utvärdering från den delade aktiviteten [Bio entitet Recognition uppgiften på Bio NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) att utvärdera precision, återkallande och F1 poängen för modellen. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Domän jämfört med allmänna word bädda in modeller

Följande är en jämförelse mellan korrektheten i två typer av funktion: (1) word inbäddningar tränats på PubMed sammanfattningar och (2) word inbäddningar tränats på Google nyheter. Tydligt se att modellen i domän överlägsen allmän modell. Därför har ett specifikt ord bädda in modellen snarare än är det mer praktiskt att använda en generisk. 

* Uppgift #1: Narkotika och sjukdomar identifiering

![Modellen jämförelse 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Vi utföra utvärderingen av word-inbäddningar på andra datauppsättningar på liknande sätt och se den i domän modellen är alltid bättre.

* Uppgift #2: Protein, Cell rad, celltyp, DNA och RNA identifiering

![Modellen jämförelse 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Uppgift #3: Kemikalier och sjukdomar identifiering

![Modellen jämförelse 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Uppgift #4: Narkotika identifiering

![Modellen jämförelse 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Uppgift #5: Gener identifiering

![Modellen jämförelse 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow jämfört med CNTK
Alla rapporterade modeller tränas med TensorFlow Keras som backend. Keras med CNTK backend stöder inte ”omvänd” när denna åtgärd utfördes. För jämförelse, har vi därför tränats en enkelriktad LSTM modell med CNTK serverdelen och jämfört med en enkelriktad LSTM modell med TensorFlow backend. Installera CNTK 2.0 för Keras från [här](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Modellen jämförelse 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Vi slutsatsen att CNTK utförs som bra som Tensorflow både vad gäller utbildning tidsåtgång per epok (60 sekunder för CNTK och 75 sek för Tensorflow) och antalet test-enheter som upptäckts. Vi använder enkelriktade lager för utvärdering.


### <a name="3-deployment"></a>3. Distribution

Se [distribution](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Distribuerade vi en webbtjänst på ett kluster i den [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Operationalization miljö bestämmelserna Docker och Kubernetes i klustret för att hantera distributionen av webbtjänsten. Du hittar mer information om hur operationalization [här](model-management-service-deploy.md ).


## <a name="conclusion"></a>Sammanfattning

Vi har gått över information om hur du tränar en inbäddning word-modell med Word2Vec algoritm på Spark och använda den extraherade inbäddningar som funktioner för att träna ett djupa neurala nätverk för extrahering av entiteten. Vi har tillämpats utbildning-pipeline på biomedicinska domänen. Pipeline är generisk som ska användas för att identifiera anpassade entitetstyper i andra domäner. Du behöver tillräckligt med data och du kan enkelt anpassa arbetsflödet som presenteras här för en annan domän.

## <a name="references"></a>Referenser

* Tomas Mikolov Kai Hasselgren, Greg Corrado och Jeffrey Dean. 2013a. Effektiv uppskattning av word garantier i vector utrymme. I förfaranden i ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Hasselgren, Greg S Corrado och Jeff Dean. 2013b. Distribuerade representationer av ord och fraser och deras compositionality. Sidorna 3111 – 3119 i förfaranden NIPS.
* Bengt Chiu, Gamal Crichton, Anna Korhonen och Sampo Pyysalo. 2016. [Så här Train bra ord inbäddningar för biomedicinsk NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), förfaranden i femtonde Workshop på biomedicinska naturligt språk bearbetning, sidor 166 – 174.
* [Vektorn representationer av ord](https://www.tensorflow.org/tutorials/word2vec)
* [Återkommande Neurala nätverk](https://www.tensorflow.org/tutorials/recurrent)
* [Problem med Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: erfarenheterna](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

