---
title: Bättre noggrannhet vision och klassificering datormodeller i Azure Machine Learning
description: Lär dig att förbättra din dator bildklassificering för visuellt innehåll, objektidentifiering och avbildning likheter modeller med hjälp av Azure Machine Learning-paketet för visuellt innehåll.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6d6c540cd8711ae89784cdc797ca56d312522a83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989319"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Förbättra visuellt innehåll

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Med den **Azure Machine Learning-paket för visuellt innehåll**, du kan skapa och distribuera klassificering av avbildning, objekt-identifiering och avbildning likheter modeller. Läs mer om det här paketet och hur du installerar den.

I den här artikeln får lära du att finjustera dessa modeller för att öka deras precision. 

## <a name="accuracy-of-image-classification-models"></a>Riktighet avbildningsklassificeringsmodeller

Datorn Vision paketet visas att ge bra resultat för en mängd olika datauppsättningar. Som gäller för de flesta machine learning-projekt, hämtar bäst möjliga resultat för en ny datauppsättning du kräver noggrann parametern justering samt utvärdera olika designbeslut. Följande avsnitt innehåller anvisningar om hur du förbättrar noggrannhet på den angivna datauppsättningen, det vill säga vilka parametrar som är mest lovande att optimera först värdena för dessa parametrar som en bör försöka och vilka man att undvika.

Generellt sett training djupinlärningsmodeller levereras med en kompromiss mellan utbildningstid, med modellens precision. Datorn Vision paketet har förinställda standardparametrar (se första raden i tabellen nedan) som fokuserar på att snabb utbildning hastighet samtidigt producera normalt hög Precision modeller. Den här noggrannhet kan ofta förbättras ytterligare med hjälp av, till exempel högre bildupplösningen eller djupare modeller, men på bekostnad av ökar utbildning med en faktor på 10 x eller mer.

Vi rekommenderar att du först arbeta med standardparametrarna, tränar en modell, granska resultaten, korrigera grunden sanningen kommentarer om det behövs, och endast försök parametrar som försämrar utbildning (se tabellen nedan föreslagna parametervärden). En förståelse för dessa parametrar när det är tekniskt sett inte nödvändigt rekommenderas men.


### <a name="best-practices-and-tips"></a>Bästa praxis och tips

* DQS: uppsättningar för träning och testning bör vara av hög kvalitet. Det innebär bilderna är kommenterade korrekt tvetydig avbildningar tas bort (till exempel där det är oklart för mänskliga ögat om bilden visar en tennis boll eller en matt) och attribut kan inte användas samtidigt (dvs, varje avbildning hör till exakt ett attribut).

* Innan du förfina DNN bör en SVM klassificerare utbildas med en förtränade och fasta DNN som upplärda. Detta stöds i datorn Vision paket och kräver inte lång tid att träna eftersom DNN själva inte ändras. Även den här enkla metoden uppnår ofta bra noggrannhet och därmed representerar en stark baslinje. Nästa steg är sedan att förfina DNN som ska ge bättre noggrannhet.

* Om objekt av intresse är liten bild, kända metoder för klassificering av bild att inte fungera. Överväg att använda en metod för identifiering av objekt som datorn Vision paketet snabbare R-CNN baserat på Tensorflow i sådana fall.

* Mer träningsdata desto bättre. Som regel-för-par, bör ett ha minst 100 exempel för varje klass, det vill säga 100 bilder för ”hund”, 100 bilder för ”cat” osv. Träna en modell med färre bilder är möjlig, men kan inte ge bra resultat.

* Inlärningsbilder måste finnas lokalt på datorn med GPU och finnas på en SSD-enhet (inte en Hårddisk). I annat fall kan svarstiden från avbildningen läsning drastiskt minska utbildning hastighet (som även en faktor på 100 gånger).


### <a name="parameters-to-optimize"></a>Parametrar för att optimera

Hitta optimala värden för dessa parametrar är viktigt och ofta förbättra noggrannheten avsevärt:
* Learning hastighet (`lr_per_mb`): parametern utan tvekan den viktigaste för att få rätt. Om det arbete du utfört på utbildningen inställt när DNN förfining är högre än ~ 5% troligen inlärningsfrekvensen är antingen för högt eller antalet utbildning epoker för lågt. DNN tenderar att över plats på träningsdata, men i praktiken detta leder till bra modeller på testet ange särskilt i små datauppsättningar. Vi använder vanligtvis 15 epoker där inledande inlärningsfrekvensen minskar två gånger. i vissa fall kan utbildning med flera epoker förbättra prestanda.

* Ange upplösning (`image_dims`): bildupplösningen som standard är 224 x 224 bildpunkter. Med högre upplösning på exempelvis 500 x 500 bildpunkter eller 1 000 x 1 000 bildpunkter kan avsevärt förbättra noggrannheten men saktar ned DNN förfining. Datorn Vision paketet förväntar sig indataupplösningen ska vara en tuppel (färg-kanaler, bildens bredd, bildens höjd), till exempel (3, 224, 224), där antalet färgkanaler måste anges till 3 (röd-grön-blå-band).

* Modellera arkitektur (`base_model_name`): Använd djupare dnn: er, till exempel ResNet-34 eller ResNet-50 i stället för standard ResNet-18-modellen. Resnet-50 modellen är inte bara bättre, men dess utdata från det näst sista lagret tillhör storleken 2048 flyttal (jämfört med 512 flyttal med ResNet-18 och ResNet-34 modeller). Den här ökade dimensionalitet kan vara särskilt bra när att hålla DNN fast och utbildning i stället en SVM klassificerare.

* Minibatch storlek (`mb_size`): hög minibatch storlekar leder till utbildning snabbare men på bekostnad av en ökad DNN minnesförbrukning. Därför när du väljer djupare modeller (till exempel ResNet-50 jämfört med ResNet-18) och/eller högre upplösning (500\*500 bildpunkter jämfört med 224\*224 bildpunkter), en har vanligtvis att minska storleken minibatch för att undvika att minnet är slut fel. När du ändrar storlek på minibatch, måste ofta inlärningsfrekvensen också justeras efter kan ses i tabellen nedan.
* Släpp ut hastighet (`dropout_rate`) och L2 regularizer (`l2_reg_weight`): DNN över montering kan minskas genom att använda en annullerad andel 0,5 (standardvärdet är 0,5 i datorn Vision paket) eller flera och genom att öka regularizer vikt (standard är 0.0005 i visuellt innehåll Paket). Observera att särskilt i små datauppsättningar DNN över montering är svår och ofta omöjligt att undvika.


### <a name="parameter-definitions"></a>Principparameterns definitioner

- **Learning rate**: steg storleken som används under brantaste lutningsmetoden learning. Om set för lågt och sedan modellen som har många epoker att träna, om set för hög och sedan modellen inte konvergerar till en mycket bra lösning. Normalt används ett schema där inlärningsfrekvensen minskar efter ett visst antal epoker. E.For exempel utbildningsresurser Betygsätt schema `[0.05]*7 + [0.005]*7 + [0.0005]` motsvarar med en inledande inlärningsfrekvensen på 0,05 för sju första epoker, följt av en 10 gånger lägre inlärningsfrekvensen av 0,005 för en annan sju epoker och slutligen finjustera modellen för en enda epoch minskar inlärningsfrekvensen av 0.0005 med 100 gånger.

- **Minibatch storlek**: GPU: er kan bearbeta flera avbildningar parallellt för att påskynda beräkning. Dessa parallella bearbetade avbildningar kallas även en minibatch. Ju högre minibatch storleken snabbare utbildningen kommer att vara, men på bekostnad av en ökad DNN minnesförbrukning.

### <a name="recommended-parameter-values"></a>Rekommenderade parametervärden

Tabellen nedan innehåller olika parameteruppsättningar som visades för att producera hög Precision modeller på en mängd olika aktiviteter för klassificering av avbildning. Optimala parametrar beror på specifik datauppsättning och exakta GPU används, därför tabellen bör ses som en riktlinje endast. När du har försökt dessa parametrar, Tänk också bildupplösningar på högst 500 x 500 bildpunkter eller djupare modeller som till exempel Resnet 101 eller Resnet-152.

Den första raden i tabellen motsvarar standardparametrar som anges i dator Vision paketet. Alla andra rader som tar längre tid att träna (anges i den första kolumnen) men på fördelen med att ökad precision (se den andra kolumnen för den genomsnittliga precisionen över tre interna datauppsättningar). Till exempel ta parametrarna i den sista raden 5 till 15 x längre tid att träna, men resulterade i ökad (Genomsnittlig) noggrannhet på tre interna testuppsättningar från 82.6% till 92.8%.

Djupare modeller och högre indataupplösningen ta upp mer DNN-minne och kan därför minibatch storlek måste minskas med ökad modellen komplexiteten för att undvika ut av minne fel. Som framgår i tabellen nedan, är det bra att minska inlärningsfrekvensen med en faktor på två när minskar minibatch storlek genom att samma multiplikatorn. Minibatch storlek kan behöva hämta minskas ytterligare på GPU: er med mindre mängder minne.

| Utbildningstid (grov uppskattning) | Exempel noggrannhet | Minibatch storlek (*mb_size*) | Learning hastighet (*lr_per_mb*) | Bild-lösning (*image_dims*) | DNN-arkitektur (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (referens) | 82.6% | 32 | [0,05] \*7 + [0,005]\*7 + [0.0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 90.2% | 16 | [0.025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 87,5% | 16 | [0.025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 till 15 x        | 92.8% |  8 | [0.01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Nästa steg

Information om Azure Machine Learning-paket för visuellt innehåll:
+ Kolla in referensdokumentationen

+ Lär dig mer om [andra Python-paket för Azure Machine Learning](reference-python-package-overview.md)