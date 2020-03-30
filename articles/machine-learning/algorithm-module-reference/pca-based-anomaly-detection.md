---
title: 'PCA-baserad avvikelseidentifiering: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen FÖR PCA-baserad avvikelseidentifiering för att skapa en avvikelseidentifieringsmodell baserad på PCA (Principal Component Analysis).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502989"
---
# <a name="pca-based-anomaly-detection"></a>Identifiering av PCA-baserad avvikelse

I den här artikeln beskrivs hur du använder modulen **FÖR PCA-baserad avvikelseidentifiering** i Azure Machine Learning designer (förhandsversion) för att skapa en avvikelseidentifieringsmodell baserad på Principal Component Analysis (PCA).

Den här modulen hjälper dig att skapa en modell i scenarier där det är enkelt att hämta träningsdata från en klass, till exempel giltiga transaktioner, men svårt att få tillräckligt med exempel på riktade avvikelser. 

För att till exempel upptäcka bedrägliga transaktioner har du ofta inte tillräckligt med exempel på bedrägerier att träna på, men har många exempel på bra transaktioner. Den **PCA-baserade avvikelseidentifieringsmodulen** löser problemet genom att analysera tillgängliga funktioner för att avgöra vad som utgör en "normal" klass och tillämpa avståndsmått för att identifiera ärenden som representerar avvikelser. På så sätt kan du träna en modell med befintliga obalanserade data.

## <a name="more-about-principal-component-analysis"></a>Mer om Principal Component Analysis

*Principal Component Analysis*, som ofta förkortas till PCA, är en etablerad teknik inom maskininlärning. PCA används ofta i utforskande dataanalys eftersom det avslöjar den inre strukturen av data och förklarar variansen i data.

PCA fungerar genom att analysera data som innehåller flera variabler. Den letar efter korrelationer mellan variablerna och bestämmer kombinationen av värden som bäst fångar skillnader i resultat. Dessa kombinerade funktionsvärden används för att skapa ett mer kompakt funktionsutrymme som kallas *huvudkomponenterna*.

För avvikelseidentifiering analyseras varje ny indata och avvikelseidentifieringsalgoritmen beräknar sin projektion på eigenvectors, tillsammans med ett normaliserat rekonstruktionsfel. Det normaliserade felet används som avvikelsepoäng. Ju högre fel, desto mer avvikande instans är.

Mer information om hur PCA fungerar och om implementeringen för avvikelseidentifiering finns i följande dokument:

- [En randomiserad algoritm för huvudkomponentanalys](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan och Tygert

- [Hitta struktur med slumpmässighet: Probabilistiska algoritmer för att konstruera ungefärliga Matrix Sönderdelningar](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (PDF-nedladdning). Halko, Martinsson och Tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>KonfigureraR identifiering av PCA-avvikelse

1. Lägg till modulen **FÖR att upptäcka PCA-baserad avvikelse** i pipelinen i designern. Du hittar den här modulen i kategorin **Avvikelseidentifiering.**

2. På den högra panelen i modulen **för pca-baserad avvikelseidentifiering** klickar du på alternativet **Träningsläge** och anger om du vill träna modellen med hjälp av en viss uppsättning parametrar eller använda ett parametervep för att hitta de bästa parametrarna.

    - **Enkel parameter:** Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en specifik uppsättning värden som argument.

3. **Antal komponenter som ska användas i PCA:** Ange antalet utdatafunktioner, eller komponenter, som du vill mata ut.

    Beslutet om hur många komponenter att inkludera är en viktig del av experimentdesign med hjälp av PCA. Allmän vägledning är att du inte ska inkludera samma antal PCA-komponenter som det finns variabler. I stället bör du börja med några mindre antal komponenter och öka dem tills vissa kriterier är uppfyllda.

    Det bästa resultatet erhålls när antalet utdatakomponenter är **mindre än** antalet funktionskolumner som är tillgängliga i datauppsättningen.

4. Ange hur mycket översampling som ska utföras under randomiserad PCA-utbildning. I problem med avvikelseidentifiering gör obalanserade data det svårt att tillämpa vanliga PCA-tekniker. Genom att ange en viss mängd översampling kan du öka antalet målinstanser.

    Om du anger 1 utförs ingen översampling. Om du anger ett värde som är högre än 1 genereras ytterligare exempel som ska användas för att träna modellen.

    Det finns två alternativ, beroende på om du använder en parameter svep eller inte:

    - **Översamplingsparameter för randomiserad PCA**: Skriv ett enda heltal som representerar förhållandet mellan översampling av minoritetsklassen över den normala klassen. (Tillgänglig när du använder metoden **För** en parameterträning.)

    > [!NOTE]
    > Du kan inte visa den översamplad datauppsättningen. Mer information om hur översampling används med PCA finns i [Tekniska anteckningar](#technical-notes).

5. **Aktivera indatafunktion betyder normalisering:** Välj det här alternativet om du vill normalisera alla indatafunktioner till ett medelvärde på noll. Normalisering eller skalning till noll rekommenderas i allmänhet för PCA, eftersom målet med PCA är att maximera variansen mellan variabler.

     Det här alternativet är markerat som standard. Avmarkera det här alternativet om värden redan har normaliserats med en annan metod eller skala.

6. Anslut en taggad träningsdatauppsättning och en av utbildningsmodulerna:

    - Om du ställer in alternativet **Skapa trainer mode** till Single **Parameter**använder du modulen Identifieringsmodell för [tågavvikelse.](train-anomaly-detection-model.md)

7. Skicka pipelinen.

## <a name="results"></a>Resultat

När träningen är klar kan du antingen spara den tränade modellen eller ansluta den till modulen [Poängmodell](score-model.md) för att förutsäga avvikelsepoäng.

Utvärdera resultaten av en avvikelse identifiering modell kräver några ytterligare steg:

1. Kontrollera att en poängkolumn är tillgänglig i båda datauppsättningarna

    Om du försöker utvärdera en avvikelseidentifieringsmodell och får felmeddelandet "Det finns ingen poängkolumn i poängbeställningen att jämföra", betyder det att du använder en typisk utvärderingsdatauppsättning som innehåller en etikettkolumn men inga sannolikhetspoäng. Du måste välja en datauppsättning som matchar schemautdata för avvikelseidentifieringsmodeller, som innehåller kolumnen **Poängsatta etiketter** och **poängsatta sannolikheter.**

2. Kontrollera att etikettkolumnerna är markerade

    Ibland tas metadata som är associerade med etikettkolumnen bort i pipelinediagrammet. Om detta inträffar, när du använder modulen [Utvärdera modell](evaluate-model.md) för att jämföra resultaten av två avvikelseidentifieringsmodeller, kan du få felmeddelandet "Det finns ingen etikettkolumn i poängsatt datauppsättning" eller "Det finns ingen etikettkolumn i poängsatt datauppsättning att jämföra".

    Du kan undvika det här felet genom att lägga till modulen [Redigera metadata](edit-metadata.md) före modulen [Utvärdera modell.](evaluate-model.md) Använd kolumnväljaren för att välja klasskolumnen och välj **Etikett**i listrutan **Fält** .

3. Använd [Kör Python Script](execute-python-script.md) för att justera etikettkolumnkategorier som 1(positiva, normala) och 0(negativa, onormala).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Tekniska anmärkningar

Den här algoritmen använder PCA för att approximera underområdet som innehåller den normala klassen. Subrymden spänns över av eigenvectors associerade med de högsta egenvärdena i datakovariansmatrisen. För varje ny indata beräknar avvikelsedetektorn först sin projektion på egenvektaltectorerna och beräknar sedan det normaliserade rekonstruktionsfelet. Det här felet är avvikelsepoängen. Ju högre fel, desto mer avvikande instans. För detaljer om hur det normala utrymmet beräknas, se Wikipedia: [Principal Component Analysis](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

Se [Undantag och felkoder för designern (förhandsversion)](designer-error-codes.md) för en lista över fel som är specifika för designermodulerna.''