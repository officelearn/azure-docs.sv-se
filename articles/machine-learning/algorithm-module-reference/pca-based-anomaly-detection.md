---
title: 'PCA-baserad avvikelse identifiering: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder PCA-baserad avvikelse identifiering för att skapa en modell för avvikelse identifiering baserat på huvud komponent analys (PCA).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: f126eb347d11d3203ad3b97abe3606738e67c33c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925844"
---
# <a name="pca-based-anomaly-detection"></a>PCA-baserad avvikelse identifiering

Den här artikeln beskriver hur du använder den **PCA-baserade modulen för avvikelse identifiering** i Azure Machine Learning designer (för hands version) för att skapa en modell för avvikelse identifiering baserat på Principal Component Analysis (PCA).

Den här modulen hjälper dig att bygga en modell i scenarier där det är enkelt att hämta tränings data från en klass, till exempel giltiga transaktioner, men är svåra att få tillräckligt med exempel på mål avvikelser. 

För att till exempel identifiera bedrägliga transaktioner, är det mycket ofta att du inte har tillräckligt många exempel på bedrägerier att träna på, men har många exempel på bra transaktioner. Den **PCA-baserade modulen för avvikelse identifiering** löser problemet genom att analysera tillgängliga funktioner för att fastställa vad som utgör en "normal"-klass och tillämpa avstånds mått för att identifiera fall som representerar avvikelser. Detta gör att du kan träna en modell med hjälp av befintliga data som är förbalanserade.

## <a name="more-about-principal-component-analysis"></a>Mer om analys av huvud objekts komponenter

*Huvud komponent analys*, som ofta är förkortat till PCA, är en etablerad teknik i Machine Learning. PCA används ofta i analys av analys av data, eftersom det visar den inre strukturen i data och förklarar var Ian sen i data.

PCA fungerar genom att analysera data som innehåller flera variabler. Det söker efter korrelationer mellan variablerna och avgör den kombination av värden som bäst samlar in skillnader i resultat. Dessa kombinerade funktions värden används för att skapa ett mer kompakt funktions utrymme som kallas *huvud komponenter*.

Vid avvikelse identifiering analyseras varje ny indata och algoritmen för avvikelse identifiering beräknar projektionen på eigenvectors, tillsammans med ett normaliserat återuppbyggnads fel. Det normaliserade felet används som avvikelse poäng. Ju högre fel, desto mer avvikande är instansen.

Mer information om hur PCA fungerar och om implementeringen av avvikelse identifiering finns i följande dokument:

- [En slumpmässig algoritm för analys av huvud komponenter](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan och Tygert

- [Hitta strukturen med slumpmässig het: Probabilistic-algoritmer för att konstruera ungefärlig mat ris dekompositioner](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF-nedladdning). Halko, Martinsson och tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Konfigurera PCA-avvikelse identifiering

1. Lägg till modulen **PCA-baserad avvikelse identifiering** i din pipeline i designern. Du hittar den här modulen i kategorin för **avvikelse identifiering** .

2. I fönstret **Egenskaper** för modulen **PCA-baserad avvikelse identifiering** klickar du på alternativet **tränings läge** och anger om du vill träna modellen med en speciell uppsättning parametrar eller använder en parameter svep för att hitta de bästa parametrarna.

    - **Enskild parameter**: Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en viss uppsättning värden som argument.

3. **Antal komponenter som ska användas i PCA**: Ange antalet utmatnings funktioner eller komponenter som du vill mata ut.

    Beslutet om hur många komponenter som ska ingå är en viktig del av experiment design med hjälp av PCA. Allmän vägledning är att du inte bör inkludera samma antal PCA-komponenter eftersom det finns variabler. I stället bör du börja med ett mindre antal komponenter och öka dem tills vissa villkor är uppfyllda.

    Det bästa resultatet erhålls när antalet utgående komponenter är **mindre än** antalet funktions kolumner som är tillgängliga i data uppsättningen.

4. Ange mängden översampling som ska utföras under slumpmässig PCA-utbildning. Vid problem med avvikelse identifiering gör disbalanserade data det svårt att tillämpa standard PCA-tekniker. Genom att ange en mängd översampling kan du öka antalet mål instanser.

    Om du anger 1 utförs ingen översampling. Om du anger ett värde som är högre än 1 genereras ytterligare exempel för användning i träna modellen.

    Det finns två alternativ, beroende på om du använder en parameter Svep eller inte:

    - **Översamplings parameter för slumpmässig PCA**: Skriv ett enda heltal som representerar förhållandet för översampling av klassen minoritet över klassen normal. (Tillgängligt när du använder inlärnings metoden för **en enda parameter** .)

    > [!NOTE]
    > Det går inte att visa den översamplade data uppsättningen. Mer information om hur översampling används med PCA finns i [teknisk information](#technical-notes).

5. **Aktivera indatamängds funktion innebär normalisering**: Välj det här alternativet om du vill normalisera alla inmatade funktioner till ett medelvärde på noll. Normalisering eller skalning till noll rekommenderas vanligt vis för PCA, eftersom syftet med PCA är att maximera variansen mellan variabler.

     Det här alternativet är valt som standard. Avmarkera det här alternativet om värdena redan har normaliserats med en annan metod eller skala.

6. Anslut en data uppsättning för Taggad utbildning och en av inlärnings modulerna:

    - Om du ställer in alternativet **skapa utbildare läge** för **en enskild parameter**använder du modulen [träna avvikelse identifiering modell](train-anomaly-detection-model.md) .

7. Köra en pipeline.

## <a name="results"></a>Resultat

När inlärningen är klar kan du antingen spara den tränade modellen eller ansluta den till modulen [Poäng modell](score-model.md) för att förutsäga avvikande poäng.

För att utvärdera resultatet av en avvikelse identifierings modeller krävs vissa ytterligare steg:

1. Se till att en score-kolumn är tillgänglig i båda data uppsättningarna

    Om du försöker utvärdera en modell för avvikelse identifiering och får felet "det finns ingen Poäng kolumn i den resulterande data uppsättningen som ska jämföras", innebär det att du använder en typisk utvärderings uppsättning som innehåller en etikett kolumn men inga sannolikhets poäng. Du måste välja en data uppsättning som matchar schemats utdata för avvikelse identifierings modeller, som innehåller en **resultat etikett** och en **resultat** kolumn.

2. Se till att etikett kolumner är markerade

    Ibland tas de metadata som är kopplade till etikett-kolumnen bort i pipeline-diagrammet. Om detta inträffar kan du, när du använder modulen [utvärdera modell](evaluate-model.md) för att jämföra resultaten av två avvikelse identifierings modeller, få felet, "det finns ingen etikett kolumn i den returnerade data uppsättningen" eller "det finns ingen etikett kolumn i den returnerade data uppsättningen som ska jämföras".

    Du kan undvika det här felet genom att lägga till modulen [Redigera metadata](edit-metadata.md) innan du kan [utvärdera modell](evaluate-model.md) -modulen. Använd kolumn väljaren för att välja klass kolumnen och välj **etikett**i list rutan **fält** .

3. Använd [execute Python-skript](execute-python-script.md) för att justera etikett kolumn kategorier som 1 (positiv, normal) och 0 (negativt, onormalt).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Tekniska anteckningar

Den här algoritmen använder PCA för att approximera det under utrymme som innehåller klassen normal. Under utrymmet sträcks av eigenvectors som är associerat med den översta Eigenvalues i matrisen för data kovarians. För varje ny indata beräknar avvikelse detektorn först sin projektion i eigenvectors och beräknar sedan det normaliserade återuppbyggnads felet. Det här felet är avvikelse poängen. Ju högre fel desto mer avviker instansen. Mer information om hur det normala utrymmet beräknas finns i Wikipedia: [huvud komponent analys](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

Se [undantag och felkoder för designern (för hands version)](designer-error-codes.md) för en lista med fel som är speciella för designer-modulerna.