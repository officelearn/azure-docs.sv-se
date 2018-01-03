---
title: Maskin learning algoritmen fusklapp | Microsoft Docs
description: "Utskrivbara machine learning-algoritmen fusklapp hjälper dig att välja rätt algoritmen för din förutsägelsemodell i Azure Machine Learning Studio."
keywords: "algoritmen fusklapp, fusklapp, maskininlärningsalgoritmen"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: garye
ms.openlocfilehash: 31251e039414ee6f268aeb54f3eef755fcde9cb3
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Facit för Machine Learning-algoritm i Microsoft Azure Machine Learning Studio
Den **Microsoft Azure Machine Learning algoritmen Cheat blad** hjälper dig att välja rätt algoritmen för en förutsägelseanalysmodell.

[Azure Machine Learning Studio](https://studio.azureml.net/) har stora biblioteket med algoritmer från den ***regression***, ***klassificering***, ***clustering***, och  ***avvikelseidentifiering*** familjer. Varje är utformat för att hantera en annan typ av machine learning problem.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Hämta: Algoritmen fusklapp för Maskininlärning
**Hämta fusklapp här: [Machine Learning algoritmen Cheat blad (11 x 17 tum)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Datorn Inlärningsalgoritmen fusklapp: Lär dig hur du väljer en Machine Learning-algoritm.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Hämta och skriva Machine Learning algoritmen Cheat ut i tabloidformat för att enkelt och få hjälp med att välja en algoritm.

> [!NOTE]
> Se artikeln [så väljer du algoritmer för Microsoft Azure Machine Learning](algorithm-choice.md) en detaljerad guide till med hjälp av den här fusklapp.
> 
> 

## <a name="more-help-with-algorithms"></a>Mer hjälp med algoritmer
* Hjälp med att använda den här fusklapp för att välja rätt algoritmen plus en ingående beskrivning av de olika typerna av maskininlärningsalgoritmer och hur de används finns [så väljer du algoritmer för Microsoft Azure Machine Learning](algorithm-choice.md).
* En nedladdningsbar infographic som beskriver algoritmer och exempel finns [nedladdningsbara Infographic: maskin lär du dig grunderna med algoritmen exempel](basics-infographic-with-algorithm-examples.md).
* En lista efter kategori av alla de tillgängliga maskininlärningsalgoritmer i Machine Learning Studio finns [initiera modell] [ initialize-model] i Machine Learning Studio algoritmen modulen hjälpen och.
* En fullständig alfabetisk lista över algoritmer och moduler i Machine Learning Studio finns [A-Ö-listan över Machine Learning Studio moduler] [ a-z-list] i Machine Learning Studio algoritmen och hjälpa till att modulen.
* Se [Översiktsdiagram över funktionerna i Azure Machine Learning Studio](studio-overview-diagram.md) om du vill ladda ned och skriva ut ett diagram med en översikt över funktionerna i Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Anteckningar och termer som för den maskininlärningsalgoritmen cheat blad

* Förslag som erbjuds i den här algoritmen fusklapp är ungefärliga regler för USB. Vissa kan böjas och vissa kan vara flagrantly bröt mot. Detta är avsett att föreslå en startpunkt. Är bara att köra en jämförelse konkurrensen mellan flera algoritmer på dina data. Det är bara ingen ersättning för att förstå principerna för varje algoritm och förstå system som genererats av dina data.

* Varje maskininlärningsalgoritmen har sin egen formatmall eller *induktiv bias*. Flera algoritmer kan vara lämpligt för ett specifikt problem och en algoritm kan vara en passar bättre än andra. Men det är inte alltid möjligt att i förväg veta vilka som är bäst. I sådana fall visas flera algoritmer tillsammans i fusklapp. En lämplig strategi för att en algoritm och om resultatet inte är tillräckliga, försök övriga. Här är ett exempel från den [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) av ett experiment som försöker flera algoritmer mot samma data och jämför resultaten: [jämför flera klassen klassificerare: enhetsbokstaven recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Det finns tre huvudsakliga kategorier av machine learning: **övervakad inlärning**, **oövervakad inlärning**, och **förstärkning learning**.

  * I **övervakad inlärning**, varje datapunkt etikett eller kopplas till en kategori eller ett värde av intresse.  Ett exempel på en kategoriska etikett tilldelar en avbildning som en katt eller en hund.  Ett exempel på en etikett för värdet är försäljningspriset som är associerade med en bil som används. Målet med övervakad inlärning är att studera många märkt exempel sådana, och sedan för att kunna göra förutsägelser om framtida datapunkter. Till exempel identifiera nya foton med rätt djuret eller tilldela andra används bilar korrekt försäljning priser. Detta är ett populärt och praktiskt typ av maskininlärning. Alla moduler i Azure Machine Learning är övervakad inlärning algoritmer utom för [K-Means Clustering][k-means-clustering].

  * I **oövervakad inlärning**, datapunkterna har inga etiketter. Målet med en algoritm för oövervakad inlärning är i stället att ordna data på något sätt eller för att beskriva strukturen. Detta kan innebära att gruppera den i kluster som K-means eller hitta olika sätt att granska komplexa data så att den visas enklare.

  * I **förstärkning learning**, algoritmen hämtar att välja en åtgärd som svar på varje datapunkt. Det är en vanlig metod i robotics, där en uppsättning sensoravläsningar vid en punkt i tiden är en datapunkt och algoritmen måste välja av roboten nästa åtgärd. Det är också en fysisk plats för Sakernas Internet-program. Inlärningsalgoritmen får också en ersättning signal en kort stund senare, som anger hur bra beslutet. Baserat på den här ändrar algoritmen sin strategi för att uppnå högsta ersättning. Det finns inga förstärkning learning i Azure ML-algoritmen moduler.

* **Bayesian metoder** förmodar statistiskt oberoende datapunkter. Det innebär att unmodeled variationer i en datapunkt är uncorrelated med andra, det vill säga den kan inte förutsägas. Om data som registreras är antalet minuter tills nästa språng tåget anländer, är till exempel två mätningar som utförs per dag isär statistiskt oberoende. Dock isär två mätningarna för en minut är inte statistiskt oberoende - värdet för en hög förutsägande av värdet för den andra.

* **Ökat beslut trädet regression** drar nytta av funktionen överlappar varandra eller interaktion mellan funktioner. Som innebär att, helst angivna värdet för en funktion något förutsägande för ett annat värde. Exempelvis dagliga hög/låg temperatur data kan veta låg temperaturen för dagen du se en rimlig gissning för stort. Informationen i de två funktionerna är något redundant.

* Klassificera data i mer än två kategorier kan göras genom att använda en kombination flera klassen klassificerare eller genom att kombinera en uppsättning tvåklass klassificerare i en **ensemble**. Det finns en separat tvåklass klassificerare för varje klass i ensemble-metoden – var och en separerar data i två kategorier: ”den här klassen” och ”inte den här klassen”. Sedan rösta dessa klassificerare på rätt tilldelningen för datapunkt. Detta är den operativa principen bakom [en eller alla Multiclass][one-vs-all-multiclass].

* Flera metoder, inklusive logistic regression och Bayes point-dator förutsätter **linjär klassgränser**. Det vill säga förutsätter de att gränserna mellan klasser cirka raka linjer (eller hyperplanes mer allmän om). Det här är ofta en egenskap för de data som du inte känner till när du har gjort att skilja den, men det är något som vanligtvis kan hämtas av visualisera i förväg. Om klassgränser ser mycket oregelbundna kan behålla med beslutsträd, beslut jungles, support vector datorer eller neurala nätverk.

* Neurala nätverk kan användas med kategoriska variabler genom att skapa en **dummy variabeln** för varje kategori kan du ange värdet till 1 i fall där kategorin gäller 0 där den inte.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
