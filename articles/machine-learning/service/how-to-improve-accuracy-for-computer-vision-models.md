---
title: Bättre noggrannhet syn & klassificering datormodeller i Azure Machine Learning
description: Lär dig att förbättra din dator vision avbildningen klassificering, objektet identifiering och avbildningen likhet modeller med hjälp av Azure Machine Learning-paketet för datorn Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Förbättra vision datormodeller

Med den **Azure Machine Learning-paketet för datorn Vision**, du kan skapa och distribuera avbildningen klassificering, objekt-identifiering och bild likhet modeller. Läs mer om det här paketet och hur du installerar den.

I den här artikeln får lära du att finjustera dessa modeller för att öka noggrannheten för deras. 

## <a name="accuracy-of-image-classification-models"></a>Riktighet avbildningen klassificering modeller

Datorn Vision paketet visas för att ge bra resultat för en mängd olika datauppsättningar. Som gäller för de flesta machine learning projekt, hämtar bäst möjliga resultat för en ny datamängd du kräver noggrann parametern justera samt utvärdera olika designbeslut. Följande avsnitt innehåller anvisningar om hur du förbättrar noggrannhet på den angivna datauppsättningen, det vill säga vilka parametrar är mest lovande att optimera först värdena för dessa parametrar som någon bör försöka och vilka fallgropar att undvika.

Generellt sett utbildning djup Learning-modeller levereras med en kompromiss mellan utbildningstid jämfört med modellen noggrannhet. Datorn Vision paketet har förinställda standardparametrar (se första raden i tabellen nedan) som fokusera på snabb utbildning hastighet vid normalt producerar hög noggrannhet modeller. Den här noggrannhet kan ofta förbättras ytterligare med, till exempel högre upplösning eller djupare modeller, men på bekostnad av utbildning time ökar med en faktor på 10 x eller mer.

Vi rekommenderar att du först fungerar med standardparametrarna som, tränar en modell, granska resultaten, korrigera grunden sanningen kommentarer om det behövs och endast försök parametrar som försämrar utbildning (se tabellen nedan föreslagna parametervärden). En förståelse av dessa parametrar när det inte är tekniskt nödvändigt rekommenderas dock.


### <a name="best-practices-and-tips"></a>Bästa praxis och tips

* DQS: uppsättningar för träning och testning ska vara av hög kvalitet. Det vill säga bilderna som har kommenterats korrekt tvetydig bilder tas bort (till exempel där det är oklart för mänskliga ögat om bilden visar en tennis kul- eller en Citron) och attribut är ömsesidigt uteslutande (det vill säga varje avbildning tillhör exakt ett attribut).

* Innan du förfina DNN bör SVM-klassificerare få utbildning med en före utbildade och fasta DNN som featurizer. Detta stöds i datorn Vision paketet och kräver inte lång tid att träna eftersom DNN själva inte ändras. Även den här enkla metoden uppnår ofta bra noggrannhet och därför representerar en stark baslinje. Nästa steg är sedan förfina DNN som ska ge bättre noggrannhet.

* Om objektet av intresse är liten bild, kända metoder för klassificering av bilden inte fungerar väl. Överväg att använda en metod för identifiering av objekt som datorn Vision paketet snabbare R-CNN baserat på Tensorflow i sådana fall.

* Flera träningsinformationen desto bättre. Som regel-för-par, bör ett ha minst 100 exempel för varje klass som är 100 bilder för ”hund”, 100 bilder för ”katt”, osv. En modell med färre bilder är möjligt, men inte kan ge bra resultat.

* Utbildning bilder måste finnas lokalt på datorn med GPU och på SSD-enhet (inte en Hårddisk). Annars kan svarstid från avbildningen läsning kraftigt minska utbildning hastighet (som även en faktor på 100 x).


### <a name="parameters-to-optimize"></a>Parametrar för att optimera

Hitta optimala värden för dessa parametrar är viktigt och ofta kan öka noggrannheten avsevärt:
* Learning hastighet (`lr_per_mb`): parametern tvekan viktigaste för att få. Om noggrannhet på utbildningen ange när DNN förfining är över ~ 5 procent troligen inlärningsfrekvensen är antingen för hög eller antalet utbildning epoker för lågt. DNN tenderar att åsidosätta passar för utbildning-data, men i praktiken detta leder till bra modeller på testet ange särskilt med små datauppsättningar. Vi använder vanligtvis 15 epoker där inledande inlärningsfrekvensen minskas två gånger. i vissa fall kan utbildning med flera epoker förbättra prestanda.

* Ange upplösning (`image_dims`): standard bildupplösningen är 224 x 224 bildpunkter. Med högre upplösning på exempelvis 500 x 500 bildpunkter eller 1000 x 1 000 bildpunkter förbättrar noggrannhet men långsammare DNN förfining. Datorn Vision paketet förväntar sig indata lösning ska vara en tuppel (färg kanaler-bredd, bildens höjd), till exempel (3, 224, 224), där antalet färgkanaler måste anges till 3 (röd grönt blå band).

* Modellen arkitektur (`base_model_name`): Använd djupare DNNs, till exempel ResNet 34 eller ResNet 50 i stället för standard ResNet-18-modellen. Resnet 50 modellen är inte bara djupare, men dess utdata från näst sista lagret är av storleken 2048 flyttal (vs. 512 flyttal ResNet 18 och ResNet 34 modeller). Den här ökade dimensionalitet kan vara särskilt bra när hålla DNN fast och i stället utbildning SVM-klassificerare.

* Minibatch storlek (`mb_size`): hög minibatch storlekar leder till snabbare utbildning men på bekostnad av en ökad DNN minnesförbrukning. Därför när du väljer djupare modeller (till exempel ResNet-50 jämfört med ResNet-18) och/eller högre upplösning (500\*500 bildpunkter jämfört med 224\*224 bildpunkter), en har vanligtvis mindre minibatch för att undvika fel i minnet är slut. När du ändrar minibatch storlek, måste ofta inlärningsfrekvensen också justeras som visas i tabellen nedan.
* Släpp ut hastighet (`dropout_rate`) och L2 regularizer (`l2_reg_weight`): DNN över passning kan reduceras med hjälp av en annullerad andel 0,5 (standardvärdet är 0,5 i datorn Vision paketet) eller flera och genom att öka vikten regularizer (standard är 0.0005 i datorn Vision Paketet). Observera dock att särskilt med små datauppsättningar DNN över passning är svår och ofta omöjligt att undvika.


### <a name="parameter-definitions"></a>Parameterdefinitioner

- **Learning hastighet**: steg storlek som används under toning härstammar learning. Om set för lågt och sedan modellen som har många epoker för att träna, om set för hög sedan modellen inte konvergerar till en bra lösning. Normalt används ett schema där inlärningsfrekvensen minskas efter ett visst antal epoker. E.For exempel på learning Betygsätt schema `[0.05]*7 + [0.005]*7 + [0.0005]` motsvarar med en inledande inlärningsfrekvensen på 0,05 för de första sju epoker, följt av en 10 x minskade inlärningsfrekvensen av 0,005 för en annan sju epoker och slutligen finjustera modellen för en enskild epok minska inlärningsfrekvensen av 0.0005 med 100 x.

- **Minibatch storlek**: GPU-kort kan bearbeta flera avbildningar parallellt påskynda beräkning. Parallell bearbetade avbildningarna kallas även en minibatch. Ju högre minibatch storlek snabbare utbildning kommer att vara, men på bekostnad av en ökad DNN minnesförbrukning.

### <a name="recommended-parameter-values"></a>Rekommenderade parametervärden

Tabellen nedan innehåller olika parameteruppsättningar som visades för att skapa hög noggrannhet modeller på en mängd olika uppgifter för klassificering av avbildningen. Parametrarna optimala beror på specifika datauppsättningen och exakt GPU används, därför tabellen bör ses som en riktlinje endast. När du har försökt parametrarna, bör du också upplösningar på mer än 500 x 500 bildpunkter eller djupare modeller, till exempel Resnet 101 eller Resnet 152.

Den första raden i tabellen motsvarar standardparametrar som anges i dator Vision paketet. Alla andra rader tar längre tid att träna (anges i den första kolumnen) men på fördelen med ökad precision (se den andra kolumnen genomsnittlig korrekta över tre interna datauppsättningar). Parametrarna i den sista raden ta till exempel 5-15 x längre tid att träna, men resulterade i ökad (Genomsnittlig) noggrannhet på tre interna testuppsättningar från 82.6% till 92.8%.

Djupare modeller och högre inkommande upplösning tar upp mer DNN minne och därför minibatch storlek måste minskas med ökad modellen komplexitet för att undvika out för minnet fel. Som visas i tabellen nedan, är det bra att minska inlärningsfrekvensen med en faktor på två när minskar minibatch storlek med samma multiplikatorn. Minibatch storlek kan behöva hämta minskas ytterligare på GPU-kort med mindre mängder minne.

| Utbildningstid (grov uppskattning) | Exempel noggrannhet | Minibatch storlek (*mb_size*) | Learning hastighet (*lr_per_mb*) | Bild-upplösning (*image_dims*) | DNN arkitektur (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (referens) | 82.6% | 32 | [0,05] \*7 + [0,005]\*7 + [0.0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2-5 x    | 90.2% | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2-5 x    | 87,5% | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5-15 x        | 92.8% |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Nästa steg

Information om Azure Machine Learning-paketet för datorn Vision:
+ Ta en titt i referensdokumentationen

+ Lär dig mer om [andra Python-paket för Azure Machine Learning](reference-python-package-overview.md)