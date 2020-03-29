---
title: 'K-Medel Klustring: Modul referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen K-Means Clustering i Azure Machine Learning för att träna klustermodeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921172"
---
# <a name="module-k-means-clustering"></a>Modul: K-Medel Klustring

I den här artikeln beskrivs hur du använder *modulen K-Means Clustering* i Azure Machine Learning designer (förhandsversion) för att skapa en otränad K-medelsklustermodell. 
 
K-means är en av de enklaste och mest kända *oövervakade inlärningsalgoritmerna.* Du kan använda algoritmen för en mängd olika maskininlärningsuppgifter, till exempel: 

* [Upptäcka onormala data](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Gruppera textdokument.
* Analysera datauppsättningar innan du använder andra klassificerings- eller regressionsmetoder. 

Om du vill skapa en klustermodell:

* Lägg till den här modulen i pipelinen.
* Anslut en datauppsättning.
* Ange parametrar, till exempel antalet kluster du förväntar dig, avståndet mått som ska användas för att skapa kluster, och så vidare. 
  
När du har konfigurerat modulen hyperparametrar ansluter du den otränade modellen till [train clustering-modellen](train-clustering-model.md). Eftersom K-betyder-algoritmen är en oövervakad inlärningsmetod är en etikettkolumn valfri. 

+ Om dina data innehåller en etikett kan du använda etikettvärdena för att styra valet av kluster och optimera modellen. 

+ Om dina data inte har någon etikett skapar algoritmen kluster som representerar möjliga kategorier, baserat enbart på data.  

##  <a name="understand-k-means-clustering"></a>Förstå K-medel klustring
 
I allmänhet använder klustring iterativa tekniker för att gruppera ärenden i en datauppsättning i kluster som har liknande egenskaper. Dessa grupperingar är användbara för att utforska data, identifiera avvikelser i data och så småningom för att göra förutsägelser. Klustermodeller kan också hjälpa dig att identifiera relationer i en datauppsättning som du kanske inte logiskt härleda genom att bläddra eller enkelt observera. Av dessa skäl används klustring ofta i de tidiga faserna av maskininlärningsuppgifter, för att utforska data och upptäcka oväntade korrelationer.  
  
 När du konfigurerar en klustermodell med hjälp av metoden K-means måste du ange ett målnummer *k* som anger antalet *centroider* som du vill ha i modellen. Centroiden är en punkt som är representativ för varje kluster. K-menar-algoritmen tilldelar varje inkommande datapunkt till ett av klustren genom att minimera inom-kluster summan av rutor. 
 
När den bearbetar träningsdata börjar K-means-algoritmen med en första uppsättning slumpmässigt valda centroider. Centroids fungerar som utgångspunkter för kluster, och de tillämpar Lloyds algoritm för att iterativt förfina sina platser. K-betyder-algoritmen slutar bygga och förfina kluster när den uppfyller ett eller flera av dessa villkor:  
  
-   Centroiderna stabiliseras, vilket innebär att klustertilldelningarna för enskilda punkter inte längre ändras och algoritmen har konvergerat på en lösning.  
  
-   Algoritmen slutförde körningen av det angivna antalet iterationer.  
  
 När du har slutfört utbildningsfasen använder du modulen [Tilldela data till kluster](assign-data-to-clusters.md) för att tilldela nya ärenden till ett av de kluster som du hittade med hjälp av K-medelalgoritmen. Du utför klustertilldelning genom att beräkna avståndet mellan det nya ärendet och centroiden för varje kluster. Varje nytt ärende tilldelas till klustret med närmaste centroid.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurera K-Means-klustermodulen
  
1.  Lägg till **K-Medel-klustermodulen** i pipelinen.  
  
2.  Om du vill ange hur du vill att modellen ska tränas väljer du alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Om du känner till de exakta parametrar som du vill använda i klustermodellen kan du ange en specifik uppsättning värden som argument.  
  
3.  För **Antal centroider**anger du antalet kluster som du vill att algoritmen ska börja med.  
  
     Modellen är inte garanterad att producera exakt detta antal kluster. Algoritmen börjar med det här antalet datapunkter och itererar för att hitta den optimala konfigurationen.  
  
4.  Egenskaperna **Initiering** används för att ange den algoritm som används för att definiera den ursprungliga klusterkonfigurationen.  
  
    -   **Första N:** Ett initialt antal datapunkter väljs från datauppsättningen och används som det ursprungliga sättet. 
    
         Denna metod kallas också *Forgy-metoden*.  
  
    -   **Slumpmässigt**: Algoritmen placerar slumpmässigt en datapunkt i ett kluster och beräknar sedan det ursprungliga medelvärdet som centroid för klustrets slumpmässigt tilldelade poäng. 

         Den här metoden kallas också *slumpmässig partitionsmetod.*  
  
    -   **K-Means++**: Detta är standardmetoden för att initiera kluster.  
  
         **K-means++-algoritmen** föreslogs 2007 av David Arthur och Sergei Vassilvitskii för att undvika dålig klustring med standardalgoritmen för K-medel. **K-means++** förbättrar standard-K-medel genom att använda en annan metod för att välja de första klustercentren.  
  
    
5.  För **slumptalsutsäde**anger du eventuellt ett värde som ska användas som utkomst för klusteriniteringen. Det här värdet kan ha en betydande effekt på klusterval.  
  
6.  För **Metric**väljer du den funktion som ska användas för att mäta avståndet mellan klustervektorer eller mellan nya datapunkter och den slumpmässigt valda centroiden. Azure Machine Learning stöder följande mätvärden för klusteravstånd:  
  
    -   **Euklidiska**: Det euklidiska avståndet används ofta som ett mått på klusterspridning för K-medels klustring. Det här måttet är att föredra eftersom det minimerar medelavståndet mellan punkter och centroider.
  
7.  För **iterationer**anger du hur många gånger algoritmen ska iterera över träningsdata innan den slutför valet av centroider.  
  
     Du kan justera den här parametern för att balansera noggrannheten mot träningstiden.  
  
8.  För **Tilldela etikettläge**väljer du ett alternativ som anger hur en etikettkolumn, om den finns i datauppsättningen, ska hanteras.  
  
     Eftersom K-betyder klustring är en oövervakad maskininlärningsmetod är etiketter valfria. Om datauppsättningen redan har en etikettkolumn kan du använda dessa värden för att styra valet av kluster, eller så kan du ange att värdena ska ignoreras.  
  
    -   **Ignorera etikettkolumn**: Värdena i etikettkolumnen ignoreras och används inte för att skapa modellen.
  
    -   **Fyll saknade värden:** Etikettkolumnvärdena används som funktioner för att skapa kluster. Om några rader saknar en etikett imputeras värdet med hjälp av andra funktioner.  
  
    -   **Skriv över från närmaste till mitten**: Etikettkolumnvärdena ersätts med förväntade etikettvärden med hjälp av etiketten för den punkt som ligger närmast den aktuella centroiden.  

8.  Välj alternativet **Normalisera funktioner** om du vill normalisera funktioner före träningen.
  
     Om du tillämpar normalisering, före träning, är `[0,1]` datapunkterna normaliserade till av MinMaxNormalizer.

10. Träna modellen.  
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**lägger du till en taggad datauppsättning och tränar modellen med hjälp av modulen [Träna klustermodell.](train-clustering-model.md)  
  
## <a name="results"></a>Resultat

När du har konfigurerat och tränat modellen har du en modell som du kan använda för att generera poäng. Det finns dock flera sätt att träna modellen och flera sätt att visa och använda resultaten: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Ta en ögonblicksbild av modellen på arbetsytan

Om du använde modulen [Train Clustering Model:](train-clustering-model.md)

1. Välj modulen **Träna klustermodell** och öppna den högra panelen.

2. Välj fliken **Utdata.** Välj ikonen **Registrera datauppsättning** för att spara en kopia av den tränade modellen.

Den sparade modellen representerar träningsdata när du sparade modellen. Om du senare uppdaterar utbildningsdata som används i pipelinen uppdateras inte den sparade modellen. 

### <a name="see-the-clustering-result-dataset"></a>Se klusterresultatdatauppsättningen 

Om du använde modulen [Train Clustering Model:](train-clustering-model.md)

1. Högerklicka på modulen **Träna klustermodell.**

2. Välj **Visualisera**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tips för att generera den bästa klustermodellen  

Det är känt att *seedningsprocessen* som används under klustring kan påverka modellen avsevärt. Seedning innebär den inledande placeringen av poäng i potentiella centroider.
 
Om datauppsättningen till exempel innehåller många extremvärden och en avvikare väljs för att dirigera klustren, passar inga andra datapunkter väl med det klustret och klustret kan vara ett singleton. Det vill säga, det kanske bara har en poäng.  
  
Du kan undvika det här problemet på ett par olika sätt:  
  
-   Ändra antalet centroider och prova flera frövärden.  
  
-   Skapa flera modeller, variera måttet eller iterera mer.  
  
I allmänhet, med klustermodeller, är det möjligt att en viss konfiguration resulterar i en lokalt optimerad uppsättning kluster. Med andra ord passar uppsättningen kluster som returneras av modellen endast de aktuella datapunkterna och är inte generaliserbar för andra data. Om du använder en annan inledande konfiguration kan K-metoden hitta en annan, överlägsen konfiguration. 

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
