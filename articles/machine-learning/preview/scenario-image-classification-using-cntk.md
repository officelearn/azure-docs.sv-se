---
title: Bild klassificering med CNTK i Azure Machine Learning arbetsstationen | Microsoft Docs
description: "Träna, utvärdera och distribuera en anpassad avbildning klassificering modellen med hjälp av Azure ML-arbetsstationen."
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
ms.reviewer: mawah, marhamil, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 2f8b2d9d2396c1f9c9e509257f3cd031a816729f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Bild klassificering med Azure Machine Learning arbetsstationen

Metoder för klassificering av avbildningen kan användas för att lösa ett stort antal datorn Vision problem.
Dessa omfattar att skapa modeller som besvara frågor som: *är ett objekt som finns i avbildningen?* där OBJEKTET kunde till exempel *hund*, *bilen*, eller  *leverera*. Eller mer komplexa frågor som: *vilken klass av öga sjukdom allvarlighetsgrad evinced av den här tålamod retinal genomsökning?*.

Den här självstudiekursen adresser lösa dessa problem. Visar vi hur du träna, utvärdera och distribuera din egen avbildning klassificering modellen med hjälp av den [Microsoft kognitiva Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) för djup.
Exempel avbildningar finns, men läsaren kan också ta med sina egna dataset och träna sina egna anpassade modeller.

Datorn Vision lösningar krävs traditionellt goda kunskaper för att identifiera och implementera s.k. manuellt *funktioner*, vilket Markera önskad information i bilder.
Den här manuella metoden ändras i 2012 med den berömda [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] djup papper, och för närvarande, djupa Neurala nätverk (DNN) används för att automatiskt söka efter dessa funktioner.
DNNs ledde till en enorm förbättring i fältet inte bara för avbildningen klassificering, utan även för andra dator Vision problem, till exempel identifiering av objekt och avbildningen likhet.


## <a name="link-to-the-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen galleri
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Översikt

Den här kursen är uppdelat i tre delar:

- Del 1 visar hur du träna, utvärdera och distribuera en avbildning klassificeringssystem med en före utbildade DNN som featurizer och utbildning en SVM på dess utdata.
- Del 2 visar hur du noggrannhet kan förbättras av, till exempel förfina DNN i stället för att använda det som en fast featurizer.
- Del 3 beskriver hur du använder en egen datauppsättning i stället för de angivna exempel avbildningarna och om det behövs, så att skapa en egen datauppsättning av skrapning bilder från nätverket.

Tidigare erfarenhet av machine learning och CNTK inte krävs, är det lättare att förstå de underliggande principerna. Noggrannhet siffror utbildning tid, etc. som rapporteras i kursen är endast för referens och de faktiska värdena när du kör koden sannolikt skiljer.


## <a name="prerequisites"></a>Krav

Förutsättningar för att kunna köra det här exemplet är följande:

1. En [Azure-konto](https://azure.microsoft.com/free/) (gratisutvärderingar finns).
2. Den [Azure Machine Learning arbetsstationen](./overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](./quickstart-installation.md) att installera programmet och skapa en arbetsyta.  
3. En Windows-dator. Windows-Operativsystemet är nödvändigt eftersom arbetsstationen stöder endast Windows och MacOS när Microsofts kognitiva Toolkit (som vi använder som djup learning bibliotek) stöder endast Windows och Linux.
4. En dedikerad GPU krävs inte att köra SVM utbildning del 1, men det är nödvändigt för att förfina av DNN som beskrivs i del 2. Om du saknar en stark GPU vill träna på flera GPU-kort eller har inte en Windows-dator, bör du använda Azures djup Learning virtuell dator med Windows-operativsystem. Se [här](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) för en 1-Klicka Distributionsguide. Distribution kan ansluta till den virtuella datorn via en anslutning till fjärrskrivbord, installera arbetsstationen det och kör kod lokalt från den virtuella datorn.
5. Olika Python-bibliotek, till exempel OpenCV behöver installeras. Klicka på *öppnar du kommandotolken* från den *filen* -menyn i arbetsstationen och kör följande kommandon för att installera dessa beroenden:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl`När du har hämtat OpenCV hjul från http://www.lfd.uci.edu/~gohlke/pythonlibs/ (exakt filnamnet och version kan ändra)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`

### <a name="troubleshooting--known-bugs"></a>Felsökning / kända fel
- En GPU krävs för del 2 och annars felet ”Batch normalisering utbildning på CPU inte har implementerats ännu” returneras vid försök att förfina DNN.
- Minnet är slut fel under DNN utbildning kan undvikas genom att minska storleken minibatch (variabeln `cntk_mb_size` i `PARAMETERS.py`).
- Koden har testats med CNTK 2.0 och 2.1 och bör köras även på senare versioner utan att någon (eller bara mindre) ändringar.
- Vid tidpunkten som skrivs hade i Azure Machine Learning-arbetsstationen problem med visar bärbara datorer som är större än 5 MB. Bärbara datorer i den här stora kan inträffa om den bärbara datorn sparas med alla celler utdata som visas. Om du får felet och sedan öppna Kommandotolken från Arkiv-menyn i arbetsstationen, köra `jupyter notebook`, öppna anteckningsboken, avmarkera alla utdata och spara den bärbara datorn. När du utför dessa steg måste öppnas anteckningsboken korrekt i Azure Machine Learning arbetsstationen igen.


## <a name="create-a-new-workbench-project"></a>Skapa ett nytt projekt arbetsstationen

Skapa ett nytt projekt med det här exemplet som mall:
1.  Öppna Azure Machine Learning Workbench.
2.  På den **projekt** klickar du på den  **+**  och markera **nytt projekt**.
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet.
4.  I den **Sök projektmallar** sökrutan, skriv ”bild klassificering” och välj mallen.
5.  Klicka på **Skapa**.

Utför dessa åtgärder skapar projektstrukturen som visas nedan. Projektkatalogen är begränsad till vara mindre än 25 MB eftersom Azure Machine Learning arbetsstationen skapar en kopia av den här mappen efter varje körning (för att aktivera körningshistorik). Därför måste alla avbildningen och temporära filer sparas till och från katalogen *~/Desktop/imgClassificationUsingCntk_data* (kallas *DATA_DIR* i det här dokumentet).

  Mapp| Beskrivning
  ---|---
  aml_config /|                           Katalogen som innehåller konfigurationsfiler Azure Machine Learning arbetsstationen
  bibliotek /|                              Katalogen som innehåller alla Python och Jupyter hjälpfunktioner
  bärbara datorer /|                              Katalogen som innehåller alla bärbara datorer
  resurser /|                              Katalogen som innehåller alla resurser (till exempel url sätt bilder)
  skript /|                              Katalogen som innehåller alla skript
  PARAMETERS.py|                       Python-skriptet som anger att alla parametrar
  Readme.md|                           Den här filen Viktigt


## <a name="data-description"></a>Beskrivning av data

Den här kursen används som kör exempel en övre brödtext kläder struktur datauppsättning som består av upp till 428 bilder. Varje avbildning kommenteras som en av tre olika strukturer (prickad, stripe, leopard). Vi förvaras antalet avbildningar små så att den här kursen kan utföras snabbt. Koden är väl testade och fungerar med tiotusentals bilder eller mer. Alla bilder har skrapats Bing avbildningen sökning och hand kommenteras som beskrivs i [del 3](#using-a-custom-dataset). Den bild som visas i URL: er med deras respektive attribut i */resources/fashionTextureUrls.tsv* fil.

Skriptet `0_downloadData.py` hämtar alla avbildningar till de *fashionTexture-DATA_DIR/bilder/* directory. Vissa av 428 URL: er är sannolikt brutna. Detta är inte ett problem och betyder bara att vi har något lägre avbildningar för träning och testning.

Följande bild visar exempel på de attribut (till vänster), prickad stripe (mellannivå) och leopard (höger). Anteckningar har utfärdat enligt det övre brödtext kläder objektet.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Del 1 - modellen träning och utvärdering

I den första delen av den här kursen vi utbildning ett system som används, men inte ändra en före utbildade djupa neurala nätverk. Den här före utbildade DNN används som en featurizer och en linjär SVM tränats att förutsäga attributet (prickad, stripe-volymer eller leopard) av en viss bild.

Vi nu beskrivs den här metoden i steg, detaljer och visa vilka skript som behöver utföras. Vi rekommenderar efter varje steg för att kontrollera vilka filer skrivs och där de skrivs till.

Alla viktiga parametrar har angetts och en kort förklaring finns i en enda plats: den `PARAMETERS.py` filen.




### <a name="step-1-data-preparation"></a>Steg 1: Förberedelse av Data
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Anteckningsboken `showImages.ipynb` kan användas för att visualisera avbildningar och korrigera sina anteckningen efter behov. Öppna den i Azure Machine Learning-arbetsstationen för att köra den bärbara datorn, klicka på ”börja anteckningsboken Server” om det här alternativet visas och sedan köra alla celler i den bärbara datorn. Om du får ett felmeddelande klagande att anteckningsboken är för stor för att visa finns i felsökningsavsnittet i det här dokumentet.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Nu köra skript som heter `1_prepareData.py`, vilket tilldelas alla bilder till antingen utbildning eller testet. Tilldelningen är ömsesidigt uteslutande - ingen utbildning bild används också för att testa eller vice versa. Som standard slumpmässig 75 procent av avbildningar från varje attributklassen tilldelas utbildning och de återstående 25% tilldelas testning. Alla data som genereras av skriptet sparas i den *fashionTexture-DATA_DIR/proc/* mapp.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Steg 2: Förfina djupa Neurala nätverket
`Script: 2_refineDNN.py`

Som vi förklaras i del 1 av den här kursen före utbildade DNN hålls fast (dvs, det är inte förfinade). Dock skriptet med namnet `2_refineDNN.py` körs fortfarande i del 1, som laddas före utbildade [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2]-modell och ändrar det, till exempel om du vill tillåta högre bildupplösningen som indata. Det här steget är snabb (sekunder) och kräver inte en GPU.

Del 2 av kursen en ändring i PARAMETERS.py filen orsaker i `2_refineDNN.py` skript för att också förfina före utbildade DNN. Som standard kör vi 45 utbildning epoker under förfining.

I båda fallen sista modellen sedan skrivs till filen *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Steg 3: Utvärdera DNN för alla bilder
`Script: 3_runDNN.py`

Vi kan nu använda (möjligen förfinade) DNN från det sista steget att featurize våra bilder. En avbildning är som indata till DNN, utdata 512 flyttal vector från det näst sista lagret av modellen. Den här vector är mycket mindre dimensionell än bilden. Dock bör den innehålla (och även markera) all information i bilden som är relevanta för att identifiera avbildningens attribut som är, om kläder artikeln har ett punktavgränsat stripe-volymer, eller leopard struktur.

Alla garantier för DNN avbildningen sparas i filen *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Steg 4: Support Vector Machine utbildning
`Script: 4_trainSVM.py`

512-flyttal garantier beräknad i det sista steget används nu för att träna en SVM klassificeraren: få en bild som indata i SVM matar ut en poäng för varje attribut måste finnas. I vårt exempel dataset innebär en poäng för 'stripe', 'prickad' och för 'leopard'.

Skriptet `4_trainSVM.py` laddar utbildning bilder, tränar en SVM för olika värden för parametern regularization (slack) C och behåller SVM med högsta noggrannhet. Klassificering noggrannhet skrivs ut på konsolen och ritas i arbetsstationen. För de angivna struktur data bör värdena vara cirka 100% och 88% respektive. Slutligen utbildade SVM skrivs till filen *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Steg 5: Utvärdering och visualisering
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Riktighet klassificeraren utbildade avbildningen kan mätas med skriptet `5_evaluate.py`. Skriptet poängen alla testbilder med utbildade SVM klassificeraren tilldelas varje bild attributet med den högsta poängen och jämför de förväntade attribut med grunden sanningen anteckningar.

Utdata från skriptet `5_evaluate.py` visas nedan. Noggrannheten klassificering för varje enskild klass beräknas samt precisionen för för hela uppsättningen (övergripande precision) och genomsnittliga över enskilda noggrannhet ('övergripande klass, ett genomsnitt noggrannhet'). 100% motsvarar den bästa möjliga noggrannheten och 0% till sämst. Slumpmässiga gissa i genomsnitt resulterar i en klass, ett genomsnitt riktighet 1 över antalet attribut: i det här fallet noggrannheten skulle vara 33,33%. De här resultaten returneras avsevärt förbättra när du använder en högre inkommande upplösning som `rf_inputResoluton = 1000`, men på bekostnad av längre DNN beräkning gånger.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Förutom Precision ritas ROC-kurvan med respektive område-under-kurvan (vänster). och matrisen förvirring visas (höger):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Slutligen anteckningsboken `showResults.py` tillhandahålls för att rulla igenom testbilder och visualisera sina respektive klassificering poäng:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Steg 6: distribution
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Utbildade systemet ska publiceras som en REST-API. Distribution förklaras i anteckningsboken `deploy.ipynb`, och baserat på funktionerna i Azure Machine Learning-arbetsstationen. Se även avsnittet utmärkt distributionen i den [IRIS kursen](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-3).

När har distribuerats, webbtjänsten kan anropas med skriptet `6_callWebservice.py`. Observera att IP-adressen (lokalt eller i molnet) för webbtjänsten måste anges först i skriptet. Anteckningsboken `deploy.ipynb` förklarar hur du hittar den här IP-adress.








## <a name="part-2---accuracy-improvements"></a>Del 2 - förbättringar av noggrannhet

Del 1 visade vi hur du klassificerar en bild av utbildning en linjär Support Vector Machine på 512 flyttal utdata från ett djupa Neurala nätverk. Den här DNN var före utbildade på miljontals avbildningar och näst sista lagret returneras som funktionen vector. Den här metoden är snabb eftersom DNN används som-är, men ändå ofta får goda resultat.

Vi nu finns flera sätt att förbättra modellen från del 1. Vi förfinar framför allt DNN i stället för att hålla den fasta.

### <a name="dnn-refinement"></a>DNN förfining

I stället för en SVM något kan du göra klassificeringen direkt i det neurala nätverket. Detta uppnås genom att lägga till ett nytt senaste lager före utbildade DNN, som tar 512-flyttal från det näst sista lagret som indata. Fördelen med att göra klassificering i DNN är nu kan retrained hela nätverket med hjälp av backpropagation. Den här metoden leder ofta till mycket bättre klassificering noggrannhet jämfört med före utbildade DNN som-är, men på bekostnad av mycket längre tid för träning (även med GPU).

Tränar det Neurala nätverket i stället för en SVM görs genom att ändra variabeln `classifier` i `PARAMETERS.py` från `svm` till `dnn`. Sedan enligt beskrivningen i del 1 måste alla skript förutom förberedelse av data (steg 1) och SVM utbildning (steg 3) köras igen. DNN förfining kräver en GPU. Om inga GPU hittades eller om GPU är låst (till exempel av en tidigare körning av CNTK) sedan skriptet `2_refineDNN.py` genererar ett fel. DNN utbildning orsaka fel om minnet är slut på vissa GPU-kort, som kan undvikas genom att minska storleken minibatch (variabeln `cntk_mb_size` i `PARAMETERS.py`).

När utbildning är klar förfinade modellen har sparats till *DATA_DIR/proc/fashionTexture/cntk_refined.model*, och en rityta ritas som visar hur träning och testning klassificering felen ändras vid träning. Observera i den ritytans felet på träningsmängden är mycket mindre än test-mängd. Problemet så kallade över passningsåtgärderna kan minskas, till exempel med ett högre värde för den annullerad hastigheten `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Kan ses i området nedan är noggrannhet med DNN förfining för den angivna datauppsättningen 92.35% jämfört med 88.92% innan (del 1). I synnerhet förbättra 'prickad' avbildningar avsevärt, med en ROC området under kurvan för 0,98 med förfining vs. 0.94 innan. Vi använder en liten datamängd och därför faktiska noggrannhet Kör koden är olika. Skillnaden är på grund av stokastisk effekter, till exempel slumpmässiga delningen av avbildningar till träning och testning uppsättningar.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Kör spårning av logg

Azure Machine Learning arbetsstationen butiker historiken för var och en körs på Azure att jämförelse av två eller flera kör som är även veckor från varandra. Detta beskrivs i detalj i den [Iris kursen](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-2). Illustreras också i följande skärmbilderna där vi jämföra två körningar av skriptet `5_evaluate.py`, med hjälp av antingen DNN förfining som är `classifier = "dnn"`(kör tal 148) eller SVM utbildning som är `classifier = "svm"` (kör tal 150).

I den första skärmbilden leder DNN förfining till att bättre noggrannhet än SVM utbildning för alla klasser. Andra skärmbilden visar alla mått som spåras, inklusive klassificeraren har. Den här spårning görs i skriptet `5_evaluate.py` genom att anropa loggaren Azure Machine Learning-arbetsstationen. Dessutom skriptet också sparar ROC-kurvan och förvirring matris till den *matar ut* mapp. Detta *matar ut* mappen är speciellt i att innehållet också spåras av funktionen tidigare arbetsstationen och därför utdatafilerna kan nås när som helst, oavsett om det har skrivits över lokala kopior.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Parametern justera
Som gäller för de flesta maskininlärning projekt, kräver hämta goda resultat för en ny datamängd noggrann parametern justera samt att utvärdera olika designbeslut. Om du vill hjälpa till med dessa uppgifter, alla viktiga parametrar har angetts och en kort förklaring finns i en enda plats: den `PARAMETERS.py` filen.

Några av de mest lovande vägar för förbättringar är:

- DQS: Kontrollera uppsättningar för träning och testning har hög kvalitet. Som är avbildningar är kommenterade korrekt, tvetydig bilder tas bort (till exempel kläder artiklar med såväl stripe punkter) och attribut är ömsesidigt uteslutande (det vill säga valt så att varje avbildning som hör till exakt ett attribut).
- Om objektet av intresse är liten bild kända metoder för klassificering av bilden inte ska fungera väl. I sådana fall Överväg att använda en metod för identifiering av objekt som beskrivs i det här [kursen](https://github.com/Azure/ObjectDetectionUsingCntk).
- DNN förfining: parametern tvekan viktigaste få rätt är inlärningsfrekvensen `rf_lrPerMb`. Om noggrannhet på utbildning (första bilden i del 2) är inte nära 0-5%, troligen är på grund av ett fel inlärningsfrekvensen. De parametrar som börjar med `rf_` är mindre viktiga. Utbildning fel bör normalt minska exponentiellt och vara nära 0% efter utbildning.
- Ange upplösning: standard bildupplösningen är 224 x 224 bildpunkter. Med högre upplösning (parameter: `rf_inputResoluton`), till exempel 448 x 448 eller 896 x 896 pixlar ofta betydande förbättrar noggrannhet men långsammare DNN förfining. **Med högre upplösning är nästan gratislunch och nästan alltid förstärker noggrannhet**.
- DNN över passning: undvika ett stort mellanrum mellan utbildning och testa noggrannhet under DNN förfining (först figur i del 2). Luckan kan minskas med annullerad priser `rf_dropoutRate` minst 0,5 och genom att öka vikten regularizer `rf_l2RegWeight`. Med en hög annullerad frekvens kan vara användbar om DNN inkommande bildupplösningen är hög.
- Försök med djupare DNNs genom att ändra `rf_pretrainedModelFilename` från `ResNet_18.model` antingen `ResNet_34.model` eller `ResNet_50.model`. Resnet 50 modellen är inte bara djupare, men dess utdata från näst sista lagret är av storleken 2048 flyttal (vs. 512 flyttal ResNet 18 och ResNet 34 modeller). Ökad dimensionen kan vara särskilt bra när utbildning SVM-klassificerare.

## <a name="part-3---custom-dataset"></a>Del 3 - anpassad datauppsättning

Del 1 och 2 vi tränas och utvärderas en modell för klassificering av avbildning med hjälp av angivna övre brödtext kläder strukturer avbildningar. Vi kan nu visa hur du använder en anpassad datauppsättning för användaren i stället. Eller, om det är inte tillgänglig, så att generera och kommentera exempelvis en datamängd med Bing avbildningen Sök.

### <a name="using-a-custom-dataset"></a>Med hjälp av en anpassad datauppsättning

Först ska vi ta en titt på mappstrukturen för kläder struktur data. Observera hur alla bilder för olika attribut i respektive undermappar *prickad*, * leopard, och *stripe* på *fashionTexture-DATA_DIR/bilder/*. Observera också hur mappnamnet avbildningen ingår även i den `PARAMETERS.py` filen:
```python
datasetName = "fashionTexture"
```

Med hjälp av en anpassad datauppsättning är så enkelt som efterliknar här mappstrukturen där alla bilder är i undermappar enligt deras attribut och kopiera dessa mappar till en ny katalog som användaren har angett *newDataSetName-DATA_DIR/bilder/*. Endast kodändring som krävs är att ange den `datasetName` variabeln *newDataSetName*. Skript 1-5 kan sedan köras i ordning och alla mellanliggande skrivs *newDataSetName-DATA_DIR/proc/*. Det krävs några andra ändringar i koden.

Det är viktigt att varje avbildning kan tilldelas exakt ett attribut. Till exempel vore det fel ha attribut för 'djuret' och 'leopard', eftersom en avbildning av 'leopard' skulle också tillhör 'djuret'. Dessutom är det bäst att ta bort avbildningar som är tvetydig och därför svårt att lägga till anteckningar.



### <a name="image-scraping-and-annotation"></a>Bild skrapning och anteckningar

Kan vara svårt att samla in ett tillräckligt stort antal kommenterade avbildningar för träning och testning. Ett sätt att lösa problemet är att skrapa bilder från Internet. Se exempelvis nedan sökresultatet Bing avbildningen för frågan *t-shirt stripe*. Som förväntat, de flesta bilder verkligen stripe t-shirts. Några fel eller är tvetydig avbildningar (t.ex kolumn 1, rad 1; eller kolumn 3, rad 2) kan identifieras och svårt att ta bort:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Flera frågor ska användas för att generera en stor och olika dataset. Till exempel 7\*3 = 21 frågor kan syntetiskt automatiskt med alla kombinationer av kläder {BLUS, hoodie, tröja, tröja, shirt, t-shirt, undertröjor} och {stripe, prickad, leopard}-attribut. Sedan hämtar upp 50 bilder per fråga skulle leda till högst 21 * 50 = 1050 bilder.

I stället för att manuellt hämtar bilder från Bing Image-sökning, är det mycket enklare att i stället använda den [kognitiva Services Bing-API för sökning av avbildningen](https://www.microsoft.com/cognitive-services/bing-image-search-api) som returnerar en uppsättning bild-URL: er får en frågesträng.

Vissa av de hämta bilderna är exakt eller nära dubbletter (till exempel skiljer sig bara avbildningen lösning eller jpg artefakter). Dessa dubbletter bör tas bort så att dela träning och testning inte innehåller samma bilder. Tar bort dubbletter bilder kan uppnås med hjälp av en hash-baserade metod som fungerar i två steg: (i) först hash-sträng har beräknats för alla avbildningar. (ii) i ett andra steg över avbildningar behålls endast dessa bilder med en hash-sträng som ännu inte har visats. Alla avbildningar ignoreras. Påträffades i `dhash` metod i Python-bibliotek `imagehash` och som beskrivs i det här [blogg](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) att utföra, med parametern `hash_size` anges till 16. Det är OK att felaktigt tar bort vissa icke-dubblett-avbildningar, så länge som flesta verkliga dubbletter av tas bort.





## <a name="conclusion"></a>Slutsats

Vissa viktiga markeringar i det här exemplet är:
- Kod för att träna, utvärdera och distribuera avbildningen klassificering modeller.
- Demo-avbildningar som angavs, men enkelt anpassningsbar (en rad ändra) att använda egna avbildning dataset.
- Den senaste expert implementerade funktioner för att träna hög noggrannhet modeller baserat på Överför Learning.
- Interaktiva modellen utveckling med Azure Machine Learning arbetsstationen och Jupyter-anteckningsbok.


## <a name="references"></a>Referenser

[1] Alex Krizhevsky och Ilya Sutskever Geoffrey E. Hinton [ _ImageNet klassificering med Convolutional djupa Neurala nätverk_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming han, Xiangyu Madsen, Shaoqing Ren och Jian Sun, [ _djupt kvarvarande Learning för avbildningen Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
