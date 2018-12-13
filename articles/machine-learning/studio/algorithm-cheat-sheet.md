---
title: Machine learning-algoritmfacit – Azure Machine Learning Studio | Microsoft Docs
description: Utskrivbara machine learning-algoritmfacit hjälper dig att välja rätt algoritmen för din förutsägande modell i Azure Machine Learning Studio.
keywords: algoritmfacit, facit, machine learning-algoritm
services: machine-learning
author: ericlicoding
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 7ddc3a9829dc05ac1c1df450091789e4cad2b2f5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274521"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Machine learning-algoritmfacit för Azure Machine Learning Studio

Den **Azure Machine Learning-algoritm facit blad** hjälper dig att välja rätt algoritmen för en modell för förutsägelseanalys.

[Azure Machine Learning Studio](https://studio.azureml.net/) har ett stort bibliotek med algoritmer från den ***regression***, ***klassificering***, ***klustring***, och  ***avvikelseidentifiering*** familjer. Varje är utformad för att åtgärda en annan typ av problem med machine learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Ladda ned: Machine learning-algoritmfacit

**Hämta facit här: [Machine Learning-Algoritmfacit (11 x 17 tum)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Machine learning-algoritmfacit: Lär dig hur du väljer en Machine Learning-algoritm.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Ladda ned och skriva Machine Learning Studio algoritmen facit ut i tabloidformat för att enkelt och få hjälp med att välja en algoritm.

> [!NOTE]
> Om du vill ha hjälp med den här lathunden för att välja rätt algoritmen, plus en djupare beskrivning av de olika typerna av machine learning-algoritmer och hur de används finns i [så väljer du algoritmer för Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Viktig information och termer som är för Machine Learning Studio-algoritmen Lathund

* Förslag erbjuds i den här algoritmfacit är ungefärliga regler för USB. Vissa kan böjda och vissa kan ha brutits flagrantly. Detta är avsett att föreslå en startpunkt. Inte rädd för att köra en jämförelse konkurrensen mellan flera algoritmer för dina data. Det är bara ingen ersättning för att förstå principerna för varje algoritm och förstå system som genererats av dina data.

* Varje maskininlärningsalgoritmen har sin egen style eller *induktiv bias*. Flera algoritmer kan vara lämpligt för ett specifikt program och en algoritm kan vara ett bättre alternativ än andra. Men det går inte alltid på förhand känna till som passar bäst. I sådana fall visas flera algoritmer tillsammans i facit. En lämplig strategi är att prova en algoritm och om resultatet inte är ännu tillfredsställande, kan du prova de andra. Här är ett exempel från den [Azure AI-galleriet](http://gallery.azure.ai/) av ett experiment som försöker flera algoritmer mot samma data och jämför resultaten: [Jämför flera klassificerare: Enhetsbokstaven för](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

* Det finns tre huvudkategorier av machine learning: **övervakad inlärning**, **oövervakad inlärning**, och **förstärkande inlärning**.

  * I **övervakad inlärning**, varje datapunkt märkts eller som är associerade med en kategori eller ett värde av intresse.  Ett exempel på en kategoriska etikett tilldelar en avbildning som en katt eller en hund.  Ett exempel på en etikett är försäljningspriset som är associerade med en återförsäljaren. Målet med övervakad inlärning är att studera många taggade exemplen som dessa, och sedan för att kunna göra förutsägelser om framtida datapunkter. Till exempel identifierar nya foton med rätt djur eller tilldelas andra begagnade bilar korrekt försäljning priser. Det här är en populär och användbart typ av machine learning. Alla moduler i Azure Machine Learning är övervakade algoritmer undantag för [K-Means klustring][k-means-clustering].

  * I **oövervakad inlärning**, datapunkterna har inga etiketter. Målet med en oövervakad Inlärningsalgoritmen är istället att ordna data på något sätt eller för att beskriva dess struktur. Detta kan innebära att gruppera den i kluster, precis K-means eller hitta olika sätt att titta på komplexa data så att den visas enklare.

  * I **förstärkande inlärning**, algoritmen hämtar att välja en åtgärd som svar på varje datapunkt. Det är en vanlig metod i robotteknik, där uppsättningen sensoravläsningar vid en punkt i tiden är en datapunkt och algoritmen måste välja den robot nästa åtgärd. Det är också en fysisk för IOT-program. Inlärningsalgoritmen får också en utmärkelse signal en kort tid senare, som anger hur bra beslutet var. Baserat på den här ändrar algoritmen sin strategi för att uppnå högsta trafik. Det finns för närvarande inga förstärkt inlärningsmoduler algoritmen i Azure ML.

* **Bayesian metoder** förutsätter statistiskt oberoende datapunkter. Det innebär att unmodeled variationer i en datapunkt är uncorrelated med andra, dvs, det går inte att förutse. Om de data som registrerats är antalet minuter tills nästa subway tåget tas emot, är till exempel två mätningar som görs per dag från varandra statistiskt oberoende. Dock två mätningar som görs en minut ifrån varandra är inte statistiskt oberoende - värdet för ett är mycket förebyggande av värdet för den andra.

* **Förstärkta regression för beslut** drar nytta av funktionsöverlappning eller interaktion mellan funktioner. Det innebär att värdet för en funktion i en viss datapunkt är något förebyggande av värdet för en annan. Till exempel i den dagliga hög/låg temperaturdata kan att känna till låg temperatur för dagen du se en rimlig gissning för stort. Informationen i de två funktionerna är något redundant.

* Klassificera data i mer än två kategorier kan göras genom att antingen använda en klassificerare för sin natur flera klass eller genom att kombinera en uppsättning tvåklassförhöjt klassificerare i en **ensemble**. Det finns en separat tvåklassförhöjt klassificerare för varje klass i ensemble-metoden – var och en delar upp data i två kategorier: ”den här klassen” och ”inte den här klassen”. Sedan rösta dessa klassificerare på rätt tilldelning för datapunkt. Detta är operativa principen bakom [en eller alla Multiclass][one-vs-all-multiclass].

* Flera metoder, inklusive logistic regression och Bayes point-dator, förutsätter **linjär klassgränser**. Det vill säga antar de att gränserna mellan klasser är ungefär raka linjer (eller hyperplanes mer allmän om). Det här är ofta en egenskap för de data som du inte känner till när du har försökt att skilja den, men det är något som vanligtvis kan hämtas genom att visualisera i förväg. Om klassen gränserna tittar mycket oregelbunden, håll dig till beslutsträd, beslut jungles, stöd för vektor datorer eller neurala nätverk.

* Neurala nätverk kan användas med kategoriska variabler genom att skapa en **dummy variabeln** för varje kategori, ange värdet till 1 i fall där kategorin tillämpar 0 där den inte.

## <a name="next-steps"></a>Nästa steg

* En nedladdningsbar infografik som beskriver algoritmer och ger exempel, se [nedladdningsbar Infografik: Machine learning-grunder med algoritmexempel](basics-infographic-with-algorithm-examples.md).

* En lista efter kategori av alla de tillgängliga maskininlärningsalgoritmer i Machine Learning Studio finns i [initiera modell] [ initialize-model] i Machine Learning Studio-algoritmen och modulen hjälpa.

* En fullständig alfabetisk lista över algoritmer och moduler i Machine Learning Studio finns i [A-Z-lista över Machine Learning Studio-moduler] [ a-z-list] i modulen hjälpen och Machine Learning Studio-algoritm.

* Se [Översiktsdiagram över funktionerna i Azure Machine Learning Studio](studio-overview-diagram.md) om du vill ladda ned och skriva ut ett diagram med en översikt över funktionerna i Machine Learning Studio.



<!-- Module References -->
[a-z-list]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/one-vs-all-multiclass
