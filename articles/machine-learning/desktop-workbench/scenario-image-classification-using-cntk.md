---
title: Bildklassificering med hjälp av CNTK i Azure Machine Learning Workbench | Microsoft Docs
description: Träna, utvärdera och distribuera en modell för klassificering av anpassad avbildning med hjälp av Azure ML Workbench.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: a8c258a9bc8e006d5bbae4b7bbb61c7ef6e95412
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605954"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Klassificering av avbildning med hjälp av Azure Machine Learning Workbench

Metoder för klassificering av avbildning kan användas för att lösa många problem för visuellt innehåll.
Dessa omfattar att skapa modeller som svar på frågor som: *är ett objekt som finns i bild?* om OBJEKTET till exempel skulle kunna vara *hund*, *bil*, eller  *leverera*. Eller mer komplexa frågor som: *vilken klass av öga sjukdomar allvarlighetsgrad evinced av den här patientens retinal genomsökning?*.

Den här självstudiekursen adresser lösa dessa problem. Visar vi hur du tränar, utvärdera och distribuera dina egna avbildningen klassificering modellen med hjälp av den [Microsoft Cognitive Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) för djupinlärning.
Exempel-avbildningar tillhandahålls, men läsaren kan också ta med sina egna datauppsättningen och skapa egna anpassade modeller.

Visuellt lösningar krävs traditionellt goda kunskaper för att identifiera och implementera s.k. manuellt *funktioner*, vilket Markera önskad information i bilder.
Den här manuella metoden ändras i 2012 med det berömda [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] Deep Learning papper, och för närvarande, djupa Neurala nätverk (DNN) används för att automatiskt hitta de här funktionerna.
Dnn: er fick en enorm förbättring i fältet inte bara för klassificering av avbildning, men också för andra problem med visuellt innehåll, till exempel objektidentifiering och avbildning likhet.


## <a name="link-to-the-gallery-github-repository"></a>Länka till GitHub-lagringsplatsen galleriet
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Översikt

Den här självstudien är uppdelat i tre delar:

- Del 1 visar hur du tränar, utvärdera och distribuera ett bildklassificeringssystem med ett förtränade DNN som upplärda och utbildning en SVM på dess utdata.
- Del 2 visar sedan hur du kan förbättra noggrannheten av, till exempel förfina DNN i stället för att använda det som en fast upplärda.
- Del 3 beskriver hur du använder en egen datauppsättning i stället för de angivna exempel avbildningarna och om det behövs, hur du skapar en egen datauppsättning genom skrapning bilder från net.

Det inte krävs tidigare erfarenhet av maskininlärning och CNTK, är det användbart för att förstå de underliggande principerna. Precision siffror, utbildning tid, etc. som rapporteras i självstudien finns endast som referens de faktiska värdena när du kör koden skiljer sig sannolikt.


## <a name="prerequisites"></a>Förutsättningar

Förutsättningar för att kunna köra det här exemplet är följande:

1. En [Azure-konto](https://azure.microsoft.com/free/) (kostnadsfria utvärderingsversioner är tillgängliga).
2. Den [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) följande den [installation snabbstartsguiden](../service/quickstart-installation.md) att installera programmet och skapa en arbetsyta.  
3. En Windows-dator. Windows OS är nödvändigt eftersom Workbench stöder endast Windows och MacOS när Microsofts Cognitive Toolkit (som vi använder som deep learning-biblioteket) stöder endast Windows och Linux.
4. En dedikerad GPU krävs inte att köra SVM utbildning i del 1, men det behövs för att förfina av DNN som beskrivs i del 2. Om du saknar en stark GPU, vill utbilda i flera GPU: er eller inte har en Windows-dator, bör du använda Azures Deep Learning Virtual Machine med Windows-operativsystem. Se [här](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) för en 1-Klicka Distributionsguide. När distribuerats ansluter till den virtuella datorn via en anslutning till fjärrskrivbord, installera Workbench det och kör kod lokalt från den virtuella datorn.
5. Olika Python-biblioteken, till exempel OpenCV måste vara installerade. Klicka på *öppna Kommandotolken* från den *filen* menyn i Workbench och kör följande kommandon för att installera dessa beroenden:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` När du hämtat OpenCV snurra från http://www.lfd.uci.edu/~gohlke/pythonlibs/ (det exakta filnamnet och den version kan ändra)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Felsökning / kända buggar
- En GPU krävs för en del 2 och annars felet ”Batch normalisering utbildning på processor inte har implementerats ännu” genereras vid försök att förfina DNN.
- Minnet är slut fel under DNN utbildning kan undvikas genom att minska storleken minibatch (variabeln `cntk_mb_size` i `PARAMETERS.py`).
- Koden har testats med CNTK 2.2 och bör också köras på äldre (upp till v2.0) och senare versioner utan någon eller bara mindre ändringar.
- Vid tidpunkten för skrivning gick Azure Machine Learning Workbench inte att visa anteckningsböcker som är större än 5 MB. Anteckningsböcker för den här stora storleken kan inträffa om anteckningsboken sparas med alla celler utdata visas. Om du får det här felet och sedan öppna Kommandotolken från menyn Arkiv i Workbench, köra `jupyter notebook`, öppna anteckningsboken, rensa alla utdata och spara den bärbara datorn. När du har utfört dessa steg, öppnas anteckningsboken korrekt i Azure Machine Learning Workbench igen.
- Alla skript som angetts i det här exemplet måste köras lokalt, och inte på t.ex. en docker remote-miljö. Alla bärbara datorer måste köras med kernel som har angetts till lokala projektet kernel med namnet ”PROJECTNAME lokala” (t.ex. ”myImgClassUsingCNTK lokala”).

    
## <a name="create-a-new-workbench-project"></a>Skapa ett nytt workbench-projekt

Skapa ett nytt projekt med det här exemplet som en mall:
1.  Öppna Azure Machine Learning Workbench.
2.  På den **projekt** klickar du på den **+** och markera **nytt projekt**.
3.  I den **Skapa nytt projekt** rutan, fyller du i informationen för det nya projektet.
4.  I den **Sök efter projektmallar** sökrutan skriver du ”Bildklassificering” och väljer mallen.
5.  Klicka på **Skapa**.

Utför de här stegen skapar projektstruktur som visas nedan. Projektkatalogen är begränsad till att vara mindre än 25 MB eftersom Azure Machine Learning Workbench skapar en kopia av den här mappen efter varje körning (för att aktivera körningshistoriken). Därför alla avbildningen och temporära filer sparas till och från katalogen *~/Desktop/imgClassificationUsingCntk_data* (kallas *DATA_DIR* i det här dokumentet).

  Mapp| Beskrivning
  ---|---
  aml_config /|                           Katalogen som innehåller konfigurationsfilerna för Azure Machine Learning Workbench
  bibliotek /|                              Katalogen som innehåller alla Python och Jupyter hjälpfunktioner
  anteckningsböcker /|                              Katalogen som innehåller alla anteckningsböcker
  resurser /|                              Katalogen som innehåller alla resurser (till exempel URL: en för dirigering bilder)
  skript /|                              Katalogen som innehåller alla skript
  PARAMETERS.py|                       Python-skriptet att ange alla parametrar
  Readme.md|                           Det här readme-dokumentet


## <a name="data-description"></a>Databeskrivning

Den här självstudien använder som kör exempel en övre brödtext kläder struktur datauppsättning som består av upp till 428 avbildningar. Varje avbildning kommenteras som en av tre olika strukturer (prickad, stripe, leopard). Vi behöll antalet bilder små så att den här självstudien kan utföras snabbt. Koden är väl testad och fungerar med tiotusentals bilder eller mer. Alla avbildningar har hand kommenteras som förklaras i [del 3](#using-a-custom-dataset). Bilden visas URL: er med deras respektive attribut i den */resources/fashionTextureUrls.tsv* fil.

Skriptet `0_downloadData.py` laddar ned alla avbildningar till den *DATA_DIR/bilder/fashionTexture/* directory. Vissa av de 428 URL: er är sannolikt bruten. Detta är inte ett problem och innebär bara att vi har något lägre avbildningar för träning och testning. Alla skript som angetts i det här exemplet måste köras lokalt, och inte på t.ex. en docker remote-miljö.

Följande bild visar exempel på attributen prickade (vänster), stripe (mitten) och leopard (höger). Anteckningar har utförts enligt övre brödtext kläder objektet.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Del 1 – utforma träning och utvärdering av

I den första delen av den här självstudiekursen vi utbildning ett system som använder, men inte ändra ett förtränade djupa neurala nätverk. Den här förtränade DNN används som en upplärda och en linjär SVM har tränats att förutsäga attributet (prickade, stripe-volymer eller leopard) för en viss avbildning.

Vi nu beskrivs den här metoden i detalj, steg för steg och visa vilka skript måste köras. Vi rekommenderar efter varje steg att inspektera vilka filer skrivs och där de skrivs till.

Alla viktiga parametrar har angetts och en kort beskrivning anges i en och samma plats: den `PARAMETERS.py` filen.




### <a name="step-1-data-preparation"></a>Steg 1: Förberedelse av Data
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Anteckningsboken `showImages.ipynb` kan användas för att visualisera bilderna och korrigera sina anteckning efter behov. Om du vill köra anteckningsboken, öppna den i Azure Machine Learning Workbench, klicka på ”Starta Notebook Server” om det här alternativet visas ändrar till kernel lokalt projekt med namnet ”PROJECTNAME lokala” (t.ex. ”myImgClassUsingCNTK lokal”), och sedan köra alla celler i den bärbar dator. Se avsnittet om felsökning i det här dokumentet om du får ett felmeddelande som klagar att anteckningsboken är för stor som ska visas.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Nu kör skriptet med namnet `1_prepareData.py`, vilket tilldelar alla avbildningar till antingen utbildning inställt eller testet inställt. Den här tilldelningen är ömsesidigt uteslutande – ingen utbildning bild används också för att testa eller vice versa. Som standard en slumpmässig 75% av avbildningar från varje attributklassen tilldelas till utbildning och resterande 25% är tilldelade till testning. Alla data som genereras av skriptet sparas i den *DATA_DIR/proc/fashionTexture/* mapp.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Steg 2: Förfina djupa Neurala nätverk
`Script: 2_refineDNN.py`

Som vi förklaras i del 1 av självstudien förtränade DNN sparas fast (dvs, det är inte förfinade). Dock skriptet med namnet `2_refineDNN.py` körs fortfarande i del 1, som laddas en förtränade [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] modell och ändrar det, till exempel om du vill tillåta för högre bildupplösningen som indata. Det här steget är (sekunder) för snabb och kräver inte en GPU.

I del 2 av självstudiekursen en ändring av PARAMETERS.py filen orsaker i `2_refineDNN.py` skript för att också förfina förtränade DNN. Som standard kör vi 45 utbildning epoker under förfining.

I båda fallen skrivs sedan den slutliga modellen till filen *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Steg 3: Utvärdera DNN för alla avbildningar
`Script: 3_runDNN.py`

Vi kan nu använda (eventuellt förfinade) DNN från det sista steget att förtränade våra avbildningar. En bild som indata för DNN får är utdata 512 flyttal vektor från näst sista lagret av modellen. Den här vektor är mycket mindre dimensionell än avbildningen som själva. Dock måste det innehålla (och även markera) all information i avbildningen som är relevanta för att identifiera avbildningens attribut, som, om kläder objektet har en prickad fördelas, eller leopard struktur.

Alla garantier för DNN-avbildningen sparas i filen *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Steg 4: Dator för Vektorstöd utbildning
`Script: 4_trainSVM.py`

512-flyttal garantier beräknade det sista steget används nu för att träna en SVM klassificerare: en bild som indata får SVM matar ut en poäng för varje attribut måste finnas. I vårt exempel datauppsättning innebär en poäng för 'stripe', 'prickad ”, och” leopard'.

Skriptet `4_trainSVM.py` läser in bilder för utbildning, träna en SVM för olika värden för parametern regularisering (slack) C och håller SVM med bästa noggrannheten. Klassificering noggrannheten skrivs ut på konsolen och ritas i Workbench. För de angivna struktur data bör värdena vara cirka 100% och 88% respektive. Slutligen tränade SVM skrivs till filen *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Steg 5: Utvärdering och visualisering
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Riktighet tränade bild klassificeraren kan mätas med hjälp av skriptet `5_evaluate.py`. Skriptet poängen alla test-avbildningar med hjälp av den tränade SVM klassificeraren, tilldelar varje bild attributet med högst poäng och jämför de förväntade attribut med grunden sanningen anteckningar.

Utdata från skriptet `5_evaluate.py` visas nedan. Noggrannheten klassificering för varje enskild klass beräknas samt precisionen för fullständig testning set (övergripande precision) och medelvärdet över enskilda noggrannhet (”totala klass i genomsnitt noggrannheten”). 100% motsvarar den bästa möjliga precision och 0% till värst. Slumpmässig gissa i genomsnitt resulterar i en klass i genomsnitt riktighet 1 över antalet attribut: i vårt fall noggrannheten skulle vara 33,33%. De här resultaten returneras avsevärt förbättra när du använder en högre indataupplösningen som `rf_inputResoluton = 1000`, men på bekostnad av längre DNN beräkning gånger.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Förutom noggrannhet ritas ROC-kurvan med respektive området-under-kurva (vänster). och felmatrisen visas (höger):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Slutligen anteckningsboken `showResults.py` tillhandahålls för att bläddra igenom avbildningar för testning och visualisera sina respektive klassificering poäng. Enligt beskrivningen i steg 1, måste alla datorer i det här exemplet använder kerneln lokalt projekt med namnet ”PROJECTNAME lokala”:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Steg 6: distribution
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Tränade systemet kan nu publiceras som en REST-API. Distribution förklaras i anteckningsboken `deploy.ipynb`, och baseras på funktionen i Azure Machine Learning Workbench (Kom ihåg att ange som kernel kernel lokalt projekt med namnet ”lokal PROJECTNAME”). Se även avsnittet utmärkt distributionen i den [IRIS, självstudie](tutorial-classifying-iris-part-3.md) för distribution av mer relaterad information.

När distribuerats webbtjänsten kan anropas med hjälp av skriptet `6_callWebservice.py`. Observera att IP-adressen (lokalt eller i molnet) för webbtjänsten måste anges först i skriptet. Anteckningsboken `deploy.ipynb` förklarar hur du hittar den här IP-adressen.








## <a name="part-2---accuracy-improvements"></a>Del 2 - förbättringar av Precision

I del 1 visade vi hur du klassificerar en avbildning genom att träna en linjär dator för Vektorstöd på 512 flyttal resultatet av ett djupt Neuralt nätverk. Den här DNN var förtränade miljontals avbildningar och näst sista lagret returneras som funktionen vektor. Den här metoden är snabb eftersom DNN används som – är, men ändå ofta ger bra resultat.

Nu presenterar vi flera olika sätt att förbättra modellen från del 1. Vi förfina framför DNN i stället för att hålla det åtgärdat.

### <a name="dnn-refinement"></a>DNN förfining

I stället för en SVM göra en klassificeringen direkt i det neurala nätverket. Detta uppnås genom att lägga till ett nytt senaste lager förtränade DNN, som tar 512-flyttal från näst sista lagret som indata. Fördelen med att göra klassificeringen i DNN har som nu kan modellkomponenten hela nätverket med hjälp av backpropagation. Den här metoden leder ofta till mycket bättre klassificering noggrannhet jämfört med användningen av förtränade DNN som – är, men på bekostnad av mycket längre tid för träning (även med GPU).

Tränar det Neurala nätverket i stället för en SVM görs genom att ändra variabeln `classifier` i `PARAMETERS.py` från `svm` till `dnn`. Sedan, enligt beskrivningen i del 1, alla skript förutom förberedelse av data (steg 1) och SVM utbildning (steg 4) måste köras igen. DNN förfining kräver en GPU. Om inga GPU hittades eller om GPU är låst (till exempel av en tidigare körning CNTK) sedan skriptet `2_refineDNN.py` genererar ett fel. DNN utbildning orsaka fel om minnet är slut på vissa GPU: er, som kan undvikas genom att minska storleken minibatch (variabeln `cntk_mb_size` i `PARAMETERS.py`).

När det är klart att utbildning, förfinade modellen har sparats till *DATA_DIR/proc/fashionTexture/cntk_refined.model*, och en rityta ritas som visar hur träning och testning klassificering felen ändras under utbildningen. Observera att i den diagram som felet för träningsmängden är mycket mindre än i test-grupp. Problemet så kallade över passningsåtgärderna kan minskas, till exempel med hjälp av ett högre värde för det annullerad priset `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Som kan ses i området nedan, är det arbete du utfört med hjälp av DNN förfining på den angivna datauppsättningen 92.35% rabatt jämfört med 88.92% innan (del 1). I synnerhet förbättra 'prickad ”avbildningar mycket, med en ROC området under kurva för 0,98 med förfining vs. 0.94 innan. Vi använder en liten datamängd och kan därför faktiska noggrannhet Kör koden är olika. Skillnaden är på grund av stokastisk effekter, till exempel slumpmässiga delningen av avbildningarna i utbildning och testningsuppsättningar.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Kör spårning av logg

Azure Machine Learning Workbench butiker historiken för var och en körs på Azure för att tillåta jämförelse av två eller flera körningar som är även veckor ifrån varandra. Detta förklaras i detalj i de [Iris, självstudie](tutorial-classifying-iris-part-2.md). Den också illustreras i följande skärmbilder där vi jämföra två körningar av skriptet `5_evaluate.py`, med hjälp av antingen DNN förfining det vill säga `classifier = "dnn"`(körningsnumret 148) eller SVM utbildning det vill säga `classifier = "svm"` (körningsnumret 150).

I den första skärmbilden leder DNN förfining till bättre noggrannhet än SVM utbildning för alla klasser. Andra skärmbilden visar alla mått som spåras, inklusive klassificeraren har. Den här spårning görs i skriptet `5_evaluate.py` genom att anropa Azure Machine Learning Workbench loggaren. Dessutom skriptet sparar ROC-kurvan och förvirring matris att även den *matar ut* mapp. Detta *matar ut* mappen är speciell eftersom dess innehåll också spåras av funktionen Workbench historik och kan därför utdatafilerna kan nås när som helst, oavsett om det har skrivits över lokala kopior.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Parametern justering

Som gäller för de flesta machine learning-projekt, kräver få bra resultat för en ny datauppsättning noggrann parametern justering som utvärderar olika designbeslut. För att hjälpa till med dessa uppgifter, alla viktiga parametrar har angetts och en kort beskrivning anges i en och samma plats: den `PARAMETERS.py` filen.

Några av de mest lovande vägar för förbättringar är:

- DQS: se till att uppsättningar för träning och testning har hög kvalitet. Det vill säga bilderna är korrekt kommenterad, tvetydig avbildningar tas bort (till exempel kläder objekt med både stripe och punkter) och attribut kan inte användas samtidigt (det vill säga att du valt så att varje avbildning som hör till exakt ett attribut).

- Om objekt av intresse är liten bild sedan Bildklassificering metoder är känt att inte fungera. I sådana fall att överväga att använda en metod för identifiering av objekt som beskrivs i det här [självstudien](https://github.com/Azure/ObjectDetectionUsingCntk).
- DNN förfining: parametern är utan tvekan viktigast att få rätt är inlärningsfrekvensen `rf_lrPerMb`. Om det arbete du utfört på utbildningen ange (första bilden i del 2) inte är nära 0 och 5%, mest sannolikt det är på grund av ett fel inlärningsfrekvensen. De andra parametrarna från och med `rf_` är mindre viktiga. Utbildning fel bör normalt minska exponentiellt och vara nära 0% efter utbildning.

- Ange upplösning: standard bildupplösningen är 224 x 224 bildpunkter. Med högre upplösning (parameter: `rf_inputResoluton`), till exempel 448 x 448 eller 896 x 896 pixlar ofta betydande förbättrar precisionen men saktar ned DNN förfining. **Med högre upplösning är nästan är gratis och nästan alltid ökar noggrannheten**.

- DNN över montering: undvika ett stort mellanrum mellan träning och testning noggrannheten under DNN förfining (först figur i del 2). Luckan kan minskas annullerad enligt taxan för `rf_dropoutRate` minst 0,5 och genom att öka vikten regularizer `rf_l2RegWeight`. Med en hög annullerad frekvens kan vara särskilt användbart om DNN inkommande bildupplösningen är hög.

- Försök med djupare dnn: er genom att ändra `rf_pretrainedModelFilename` från `ResNet_18.model` antingen `ResNet_34.model` eller `ResNet_50.model`. Resnet-50 modellen är inte bara bättre, men dess utdata från det näst sista lagret tillhör storleken 2048 flyttal (jämfört med 512 flyttal med ResNet-18 och ResNet-34 modeller). Den här ökade dimensionen kan vara särskilt bra när utbildning en SVM klassificerare.

## <a name="part-3---custom-dataset"></a>Del 3 – anpassad datauppsättning

I del 1 och 2 får vi tränas och utvärderas en modell för klassificering av avbildning med hjälp av angiven text för övre kläder strukturer avbildningar. Nu visar vi hur du använder en anpassad datauppsättning för användaren i stället. 

### <a name="using-a-custom-dataset"></a>Med hjälp av en anpassad datauppsättning

Först ska vi ta en titt på mappstrukturen för kläder struktur data. Observera hur alla avbildningar för olika attribut är i respektive undermappar *prickad*, *leopard*, och *stripe* på *DATA_DIR/bilder / fashionTexture /*. Observera också hur mappen Avbildningsnamnet också sker i den `PARAMETERS.py` fil:
```python
datasetName = "fashionTexture"
```

Med hjälp av en anpassad datauppsättning är lika enkelt som att återge den här mappstrukturen där alla avbildningar finns i undermappar enligt deras attribut och kopiera dessa undermappar till en ny katalog för användardefinierade *DATA_DIR/bilder/newDataSetName/*. Den enda måste koden ändras är att ange den `datasetName` variabeln *newDataSetName*. Skript 1-5 kan sedan köras i ordning och alla mellanliggande filer skrivs till *DATA_DIR/proc/newDataSetName/*. Det krävs inga kodändringar alls.

Det är viktigt att varje avbildning kan tilldelas till exakt ett attribut. Till exempel vore det fel ha attribut för 'djur' och 'leopard', eftersom en ”leopard' avbildning skulle också tillhör” djur ”. Dessutom är det bäst att ta bort avbildningar som är tvetydig och därför svåra att kommentera.



### <a name="image-scraping-and-annotation"></a>Bild skrapning och anteckning

Kan vara svårt att samla in tillräckligt många kommenterade avbildningar träning och testning. Ett sätt att lösa problemet är att skrapa bilder från Internet.

> [!IMPORTANT] 
> Kontrollera att du inte bryter mot en bilds copyright och licensiering för alla avbildningar som du använder. 

<!--
For example, see below the Bing Image Search results for the query *t-shirt striped*. As expected, most images indeed are striped t-shirts. The few incorrect or ambiguous images (such as column 1, row 1; or column 3, row 2) can be identified and removed easily:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>
-->

Flera frågor ska användas för att generera en stor och skilda datauppsättning. Till exempel 7\*3 = 21 frågor kan syntetiseras automatiskt med alla kombinationer av kläder {BLUS, hoodie, tröja, tröja, skjorta, t-shirt, innehav} och {stripe, prickad, leopard}-attribut. Sedan hämtar upp 50 bilder per fråga skulle leda till högst 21 * 50 = 1050 avbildningar.

<!--
Rather than manually downloading images from Bing Image Search, it is much easier to instead use the [Cognitive Services Bing Image Search API](https://www.microsoft.com/cognitive-services/bing-image-search-api) which returns a set of image URLs given a query string.
-->

Vissa av de hämta avbildningarna är exakt eller nära dubbletter (till exempel skiljer sig bara bild lösning eller jpg artefakter). Dessa dubbletter ska tas bort så att träning och testning delningen inte innehåller samma avbildningar. Tar bort duplicerade bilder kan uppnås med hjälp av en hash-baserade metod, som fungerar i två steg: (i) först hash-sträng beräknas för alla avbildningar. (ii) i ett andra steg över avbildningarna hålls endast dessa avbildningar med en hash-sträng som ännu inte har påträffats. Alla andra avbildningar tas bort. Vi hittade den `dhash` metoden i Python-bibliotek `imagehash` och beskrivs i det här [blogg](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) att utföra, med parametern `hash_size` inställd på 16. Det är OK att felaktigt ta bort vissa icke-dubblett-avbildningar, så länge som flesta verkliga dubbletterna komma bort.





## <a name="conclusion"></a>Sammanfattning

Vissa viktiga höjdpunkter i det här exemplet är:
- Kod för att träna, utvärdera och distribuera avbildningsklassificeringsmodeller.
- Demo-avbildningarna som tillhandahålls, men enkelt anpassningsbar (en rad ändra) att använda egna bilden datauppsättning.
- Den senaste experten funktioner implementeras för att skapa modeller för hög precision baserat på överföra Learning.
- Interaktiv modellen utveckling med Azure Machine Learning Workbench och Jupyter-anteckningsboken.


## <a name="references"></a>Referenser

[1] Alex Krizhevsky och Ilya Sutskever Geoffrey E. Hinton [ _ImageNet klassificering med djupgående Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Madsen, Shaoqing Ren och Jian Sun, [ _resterande djupgående utbildning för Bildigenkänning_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
