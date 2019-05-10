---
title: 'K-Means kluster: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen K-Means kluster i Azure Machine Learning-tjänsten kan skapa kluster modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e9b7c8f2cf86245322679198b84b50d2c5edce8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464675"
---
# <a name="module-k-means-clustering"></a>Modul: K-means-klustring

Den här artikeln beskriver hur du använder den *K-Means klustring* modul i Azure Machine Learning Studio för att skapa ett omdöme K-means klustringsmodell. 
 
K-means är en av de enklaste och mest känd *oövervakade* algoritmer. Du kan använda algoritmen för en mängd machine learning-aktiviteter, till exempel: 

* [Identifiering av onormal data](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Klustring textdokument.
* Analysera datauppsättningar innan du använder andra metoder för klassificerings- eller regressionsmodell. 

Skapa en klustringsmodell du:

* Lägg till den här modulen i experimentet.
* Ansluta en datauppsättning.
* Ange parametrar, till exempel antalet kluster som du förväntar dig avståndet mått som ska användas när kluster, och så vidare. 
  
När du har konfigurerat modulen hyperparametrar kan du ansluta omdöme modellen till den [klustring Träningsmodell](train-clustering-model.md). Eftersom K-means algoritmen är en metod för oövervakade learning, är en etikett kolumn valfritt. 

+ Om dina data innehåller en etikett, kan du använda etikettvärden att stödja valet av klustren och optimera modellen. 

+ Om dina data har ingen etikett, skapar algoritmen kluster som representerar möjliga grupper som endast baseras på data.  

##  <a name="understand-k-means-clustering"></a>Förstå K-means klustring
 
I allmänhet klustring för hjälp av iterativa tekniker att gruppen fall i en datauppsättning i kluster med liknande egenskaper. De här grupperna är användbara för att utforska data, identifiera avvikelser i data, och till slut för att göra förutsägelser. Klustring modeller kan också hjälpa dig att identifiera relationer i en datauppsättning som du inte kan logiskt härledd av Fjärrvisning av bläddrat eller ett enkelt. Därmed behöver används klustring ofta i de tidiga faserna av machine learning-aktiviteter, att utforska data och identifiera oväntade samband.  
  
 När du konfigurerar en klustringsmodell med hjälp av metoden K-means, måste du ange en målantalet *k* som anger antalet *centroids* du vill ha i modellen. Axelcentroid är en som är representativ för varje kluster. Algoritmen för K-means tilldelar varje inkommande data till ett kluster genom att minimera KVADRATSUMMA inom klustret. 
 
När den bearbetar träningsdata börjar algoritmen K-means med en grunduppsättning med slumpmässigt valda centroids. Centroids fungera som startpunkter för klustren och de tillämpas Lloyds algoritmen för att förfina iterativt deras platser. Algoritmen för K-means slutar att bygga och förfina kluster när det uppfyller en eller flera av dessa villkor:  
  
-   Centroids stabilisera, vilket innebär att du inte längre ändra på klustret tilldelningar för enskilda punkter och algoritmen har konvergerats på en lösning.  
  
-   Algoritmen har slutförts kör det angivna antalet iterationer.  
  
 När du har slutfört fasen utbildning kan du använda den [tilldela Data till ett kluster](assign-data-to-clusters.md) modulen ska tilldelas en av de som du har hittat med hjälp av algoritmen för K-means nya ärenden. Du kan utföra kluster tilldelning av computing avståndet mellan nytt ärende och axelcentroid för varje kluster. Varje nytt ärende tilldelas till klustret med den närmaste axelcentroid.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurera modulen K-Means klustring
  
1.  Lägg till den **K-Means klustring** modulen i experimentet.  
  
2.  Om du vill ange hur du vill att modellen få utbildning, Välj den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Om du vet de exakta parametrar som du vill använda i clustering-modell kan ange du en specifik uppsättning värden som argument.  
  
3.  För **nummer Centroids**, anger du antalet kluster som du vill börja med algoritmen.  
  
     Modellen är inte säkert att producera exakt det här antalet kluster. Algoritmen som börjar med det här antalet datapunkter och upprepas för att hitta den bästa konfigurationen.  
  
4.  Egenskaperna **initieringen** används för att ange den algoritm som används för att definiera den ursprungliga klusterkonfigurationen.  
  
    -   **Första N**: Vissa inledande antalet datapunkter är valt från datauppsättningen och används som första sätt. 
    
         Den här metoden anropas också den *Forgy metoden*.  
  
    -   **Slumpmässig**: Algoritmen placerar slumpmässigt en datapunkt i ett kluster och sedan beräknar första medelvärdet ska axelcentroid av klustrets slumpmässigt tilldelad punkter. 

         Den här metoden anropas också den *slumpmässiga partition* metod.  
  
    -   **K-innebär ++**: Det här är standardmetoden för initiering av kluster.  
  
         Den **K-innebär ++** algoritmen har föreslagits i 2007 av David Arthur och Sergei Vassilvitskii för att undvika dåliga klustring av algoritmen som standard K-means. **K-innebär ++** förbättras med standard K-means med hjälp av en annan metod för att välja de ursprungliga klustret datacenter.  
  
    
5.  För **nummer slumptal**kan du också ange ett värde som ska användas som startvärde för kluster-initieringen. Det här värdet kan ha stor påverkan på val av klustret.  
  
6.  För **mått**, välja funktionen som ska användas för att mäta avståndet mellan kluster vektorer eller mellan nya datapunkter och slumpmässigt valda axelcentroid. Azure Machine Learning har stöd för följande kluster avståndet mått:  
  
    -   **Euclidean**: Euclidean avståndet används ofta som ett mått på klustret punktdiagram för K-means klustring. Det här måttet är att föredra eftersom den minimerar mean avståndet mellan punkter och centroids.
  
7.  För **iterationer**, anger du antalet gånger som algoritmen ska iterera över träningsdata innan det Slutför val av centroids.  
  
     Du kan justera den här parametern för att belastningsutjämna Precision mot utbildningstid.  
  
8.  För **Tilldela etikett läge**, Välj ett alternativ som anger hur en etikett-kolumn, om den finns i datauppsättningen ska hanteras.  
  
     Eftersom K-means clustering är en oövervakad machine learning-metoden, är etiketter valfria. Om din datauppsättning redan har en etikett-kolumn, du kan använda dessa värden för att underlätta val av klustren, eller du kan ange att värdena kan ignoreras.  
  
    -   **Ignorera etikettkolumnen**: Värdena i etikettkolumnen ignoreras och används inte att skapa modellen.
  
    -   **Fyll i saknade värden**: Kolumnvärdena etiketten används som funktioner för att skapa klustren. Om några rader saknar en etikett, tillräknade värdet genom att använda andra funktioner.  
  
    -   **Skriv över från närmast center**: Kolumnvärdena etikett ersätts med förväntade etikettvärden, med hjälp av den punkt som är närmast den aktuella axelcentroid etiketten.  

8.  Välj den **normalisera funktioner** om du vill att normalisera funktionerna innan utbildning.
  
     Om du använder normalisering innan utbildning, datapunkter normaliseras till `[0,1]` av MinMaxNormalizer.

10. Träna modellen.  
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**lägger du till en datauppsättning som är taggade och träna modellen med hjälp av den [träna Clustering-modellen](train-clustering-model.md) modulen.  
  
### <a name="results"></a>Resultat

När du har slutfört konfigurera och träna modellen, har du en modell som du kan använda för att generera poäng. Men finns det flera sätt att träna modellen och flera olika sätt att visa och använda resultatet: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Samla in en ögonblicksbild av modellen i din arbetsyta

Om du har använt den [klustring Träningsmodell](train-clustering-model.md) modulen:

1. Högerklicka på den **klustring Träningsmodell** modulen.

2. Välj **Trained model**, och välj sedan **Spara som Trained Model**.

Sparade modellen representerar träningsdata när du har sparat modellen. Om du senare uppdaterar träningsdata som används i experimentet, uppdaterar den inte sparade modellen. 

#### <a name="see-the-clustering-result-dataset"></a>Klustring resultatet datauppsättning 

Om du har använt den [klustring Träningsmodell](train-clustering-model.md) modulen:

1. Högerklicka på den **klustring Träningsmodell** modulen.

2. Välj **resultat datauppsättning**, och välj sedan **visualisera**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tips för att generera det bästa klustermodellen  

Det är känt som den *seeding* process som används under klustringen kan avsevärt påverka modellen. Seeding innebär att inledande placeringen av punkter till potentiella centroids.
 
Till exempel om datauppsättningen innehåller många extremvärden och en avvikare väljs att seeda klustren, inga andra datapunkter skulle passa bra med klustret och klustret kan vara en singleton. Det vill säga kanske endast en punkt.  
  
Du kan undvika det här problemet på ett par olika sätt:  
  
-   Ändra antalet centroids och försök flera dirigerade värden.  
  
-   Skapa flera modeller, varierande måttet eller styra mer.  
  
I allmänhet med klustring modeller, är det möjligt att en viss konfiguration resulterar i ett lokalt optimerad uppsättning kluster. Med andra ord en uppsättning kluster som returneras av modellen passar bara för det aktuella datapunkter och är inte generaliserbara till andra data. Om du använder en annan inledande konfiguration, kanske en annan konfiguration för kanske överlägsen med K-means-metoden. 
