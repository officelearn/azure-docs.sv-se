---
title: 'Linjär Regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen linjär Regression i Azure Machine Learning-tjänsten för att skapa en linjär regressionsmodell för användning i ett experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029722"
---
# <a name="linear-regression-module"></a>Modul för linjär Regression
Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en linjär regressionsmodell för användning i ett experiment.  Linjär regression försöker upprätta en linjär relation mellan en eller flera oberoende variabler och ett numeriskt resultat eller beroende variabel. 

Du använder den här modulen för att definiera en linjär regression metod och sedan tränar en modell som använder en taggade datauppsättning. Den tränade modellen kan sedan användas för att göra förutsägelser.

## <a name="about-linear-regression"></a>Om linjär regression

Linjär regression är en vanlig statistisk metod som har antagit i machine learning och förbättrad med många nya metoder för att passa raden och mäta fel. I den mest grundläggande meningen avser regression förutsägelse av ett numeriskt mål. Linjär regression är fortfarande ett bra val när du vill ha en enkel modell för en förutsägande standardaktivitet. Linjär regression tenderar också att fungera på hög-dimensionell, null-optimerade datauppsättningar som inte har stöd för komplexitet.

Azure Machine Learning stöder en mängd olika regressionsmodeller, förutom linjär regression. Dock termen ”regression” kan tolkas löst och vissa typer av regression i andra verktyg stöds inte.

+ Den klassiska regressionsproblem innebär att en enda oberoende och en beroende-variabel. Detta kallas *enkel regression*.  Den här modulen stöder enkel regression.

+ *Flera linjär regression* innebär att två eller flera oberoende variabler som bidrar till en enda beroende-variabel. Problem där flera inmatningar som används för att förutsäga ett enda numeriskt resultat kallas också *multivarierad linjär regression*.

    Den **linjär Regression** modulen kan lösa dessa problem som kan göra de flesta andra regression moduler.

+ *Flera etikett regression* är uppgiften att förutsäga flera beroende variabler inom en enda modell. Flera etikett logistic regression, till exempel tilldelas ett exempel till flera olika etiketter. (Detta skiljer sig från aktiviteten för att förutsäga flera nivåer i en enskild klass-variabel.)

    Den här typen av regression stöds inte i Azure Machine Learning. Skapa en separat learner för varje utdata som du vill förutsäga för att förutsäga flera variabler.

Statistiker har utvecklat allt mer avancerade metoder för regression för i år. Detta gäller även för linjär regression. Den här modulen stöder två metoder för att mäta fel och anpassa regressionslinjen: vanlig minsta kvadratmetoden och brantaste lutningsmetoden.

- **Brantaste lutningsmetoden** är en metod som minimerar mängden fel vid varje steg i träningsprocess modellen. Det finns många varianter på brantaste lutningsmetoden och dess optimering för olika learning problem har undersökts i stor utsträckning. Om du väljer det här alternativet för **lösning metoden**, du kan ange olika parametrar för att styra Stegstorlek learning hastighet och så vidare. Det här alternativet stöder också användningen av en integrerad parameterrensning.

- **Vanlig minsta kvadrat** är en av de vanligaste teknikerna i linjär regression. Till exempel minsta kvadrat är den metod som används i analysverktygens för Microsoft Excel.

    Vanlig minsta kvadrat refererar till funktionen förlust som beräknar fel som summan av kvadraten av avståndet från det faktiska värdet till den förväntade raden och passar modellen genom att minimera kvadratfel. Den här metoden förutsätter en stark linjär relation mellan indata och den beroende variabeln.

## <a name="configure-linear-regression"></a>Konfigurera linjär Regression

Den här modulen stöder två metoder för att passa en regressionsmodell med olika alternativ:

+ [Skapa en regressionsmodell med hjälp av online brantaste lutningsmetoden](#bkmk_GradientDescent)

    Brantaste lutningsmetoden är en bättre förlust funktion för modeller som är mer komplexa eller som har för lite träningsdata givet antalet variabler.



+ [Anpassa en regressionsmodell som med vanlig minsta kvadrat](#bkmk_OrdinaryLeastSquares)

    Det är bäst att välja vanliga minsta kvadrat för små datauppsättningar. Detta bör ge liknande resultat till Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Skapa en regressionsmodell som med vanlig minsta kvadrat

1. Lägg till den **linjär regressionsmodell** modulen i experimentet i gränssnittet.

    Du hittar den här modulen i den **Machine Learning** kategori. Expandera **initiera modell**, expandera **Regression**, och dra sedan den **linjär regressionsmodell** modulen i experimentet.

2. I den **egenskaper** fönstret i den **lösning metoden** listrutan, väljer **vanlig minsta kvadrat**. Det här alternativet anger beräkningen-metoden som används för att hitta regressionslinjen.

3. I **L2 regularisering vikt**, skriver du värdet som ska användas som vikten för L2 regulariseringshastigheter. Vi rekommenderar att du använder ett annat värde än noll för att undvika overfitting.

     Mer information om hur regularisering påverkar modellen montering finns i den här artikeln: [L1 och L2 Regulariseringshastighet för Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Välj alternativet **inkludera skärningspunkt för termen**, om du vill visa giltighetstiden för skärningspunkten.

    Avmarkera det här alternativet om du inte behöver att granska regressionsformeln.

5. För **nummer slumptal**, kan du ange ett värde för att dirigera slumpmässiga talgeneratorns startvärden som används av modellen.

    Med hjälp av en seed-värdet är användbart om du vill behålla samma resultat från olika körningarna av samma experimentet. I annat fall är standard att använda ett värde från systemklockan.


7. Lägg till den [Träningsmodell](./train-model.md) modul till ditt experiment, och ansluter en datauppsättning med märkta.

8. Kör experimentet.

## <a name="results-for-ordinary-least-squares-model"></a>Resultat för vanlig minsta kvadrat modell

När utbildning har slutförts:

+ Om du vill visa modellens parametrar, högerklicka på trainer utdata och välj **visualisera**.

+ För att göra förutsägelser, ansluta den tränade modellen till den [Poängmodell](./score-model.md) modulen tillsammans med en datauppsättning med nya värden. 


## <a name="bkmk_GradientDescent"></a> Skapa en regressionsmodell med hjälp av online brantaste lutningsmetoden

1. Lägg till den **linjär regressionsmodell** modulen i experimentet i gränssnittet.

    Du hittar den här modulen i den **Machine Learning** kategori. Expandera **initiera modell**, expandera **Regression**, och dra den **linjär regressionsmodell** modulen i experimentet

2. I den **egenskaper** fönstret i den **lösning metoden** listrutan väljer du **Online brantaste Lutningsmetoden** som metod för beräkning som används för att hitta regressionslinjen.

3. För **skapande trainer läge**, ange om du vill att träna modellen med en fördefinierad uppsättning parametrar eller om du vill optimera modellen med hjälp av en parameterrensning.

    + **Enkel parametern**: Om du vet hur du vill konfigurera nätverket linjär regression, kan du ange en specifik uppsättning värden som argument.

   
4. För **Learning rate**, ange inledande inlärningsfrekvensen för stokastisk brantaste lutningsmetoden optimering.

5. För **antalet utbildning epoker**, Skriv ett värde som anger hur många gånger algoritmen som ska gå igenom exemplen. Det här värdet ska vara stor för att nå konvergens för datauppsättningar med ett litet antal exempel.

6. **Normalisera funktioner**: Om du har redan normalized numeriska data som används för att träna modellen, kan du avmarkera det här alternativet. Som standard normaliserar modulen alla numeriska indata till ett intervall mellan 0 och 1.

    > [!NOTE]
    > 
    > Kom ihåg att använda metoden samma normalisering nya data som används för bedömning.

7. I **L2 regularisering vikt**, skriver du värdet som ska användas som vikten för L2 regulariseringshastigheter. Vi rekommenderar att du använder ett annat värde än noll för att undvika overfitting.

    Mer information om hur regularisering påverkar modellen montering finns i den här artikeln: [L1 och L2 Regulariseringshastighet för Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Välj alternativet **minska inlärningsfrekvensen**om du vill inlärningsfrekvensen att minska som iterationer arbete.  

10. För **nummer slumptal**, kan du ange ett värde för att dirigera slumpmässiga talgeneratorns startvärden som används av modellen. Med hjälp av en seed-värdet är användbart om du vill behålla samma resultat från olika körningarna av samma experimentet.


12. Lägga till en datauppsättning som är märkta och en av modulerna som utbildning.

    Om du inte använder en parameterrensning kan använda den [Träningsmodell](train-model.md) modulen.

13. Kör experimentet.

## <a name="results-for-online-gradient-descent"></a>Resultat för online brantaste lutningsmetoden

När utbildning har slutförts:

+ För att göra förutsägelser, ansluta den tränade modellen till den [Poängmodell](./score-model.md) modulen tillsammans med nya indata.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 