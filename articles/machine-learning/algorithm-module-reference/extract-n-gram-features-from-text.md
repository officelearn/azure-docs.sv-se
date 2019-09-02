---
title: 'Extrahera N-gram: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen extrahera N-g i Azure Machine Learning-tjänsten för att funktionalisera text data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 450c205f92fc65cad4e552aef3a1f28157d25ab6
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210876"
---
# <a name="extract-n-gram-features-from-text"></a>Extrahera N-gram-funktioner från text

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd **extrahera N-g-funktioner från text** -modulen för att *funktionalisera* ostrukturerade text data.

Den här modulen stöder följande scenarier för användning av en n-gram-ordlista:

* [Skapa en ny n-gram-ordlista](#create-a-new-n-gram-dictionary) från en kolumn med fritext.

* [Använd en befintlig uppsättning text funktioner](#use-an-existing-n-gram-dictionary) för att funktionalisera en kostnads fri text kolumn.

* [Poäng eller publicera en modell](#score-or-publish-a-model-that-uses-n-grams) som använder n-gram.

## <a name="configure-extract-n-gram-features-from-text"></a>Konfigurera extrahera N-gram-funktioner från text

### <a name="create-a-new-n-gram-dictionary"></a>Skapa en ny ord lista för n-gram

1.  Lägg till **funktionerna extrahera N-g från text** -modulen till experimentet och Anslut den data uppsättning som innehåller den text som du vill bearbeta.

1.  Använd **text kolumn** för att välja en kolumn av typen **sträng** som innehåller den text som du vill extrahera. Eftersom resultaten är utförliga kan du bara bearbeta en kolumn i taget.

1. Ange det **vokabulär läge** som ska **skapas** för att visa att du skapar en ny lista över n-gram-funktioner. 

1. Ange **n-gram-storlek** för att ange den *maximala* storleken på N-g som ska extraheras och lagras. 

    Om du till exempel anger 3, unigrams, 2 g och trigrams kommer att skapas.

1. **Viktnings funktionen** anger hur du skapar dokument funktions vektorn och hur du extraherar vokabulär från dokument.

    * **Binär vikt**: Tilldelar ett binärt närvaro värde till den extraherade n-gram. Värdet för varje n-gram är 1 när det finns i det aktuella dokumentet och 0 annars.

    * **TF vikt**: Tilldelar en villkors frekvens (**TF**) till den extraherade n-gram. Värdet för varje n-gram är dess förekomst frekvens i det aktuella dokumentet.

    * **IDF-vikt**: Tilldelar en inverterad dokument frekvens Poäng (**IDF**) till den extraherade n-gram. Värdet för varje n-gram är loggen för sökkorpus storlek dividerat med dess förekomst frekvens i hela sökkorpus. `IDF = log of corpus_size / document_frequency`
 
    *  **TF – IDF-vikt**: Tilldelar en term frekvens/inverterad dokument frekvens Poäng (**TF/IDF**) till den extraherade n-gram. Värdet för varje n-gram är dess TF-poäng multiplicerat med dess IDF-poäng.

1. Ange **minsta längd på ord** till det minsta antal bokstäver som kan användas i ett *enskilt ord* i en n-gram.

1. Använd **maximal ord längd** för att ange det maximala antalet bokstäver som kan användas i ett *enskilt ord* i en n-gram.

    Som standard är upp till 25 tecken per ord eller token tillåtna.

1. Använd **minimalt antal n-gram dokument absolut frekvens**för att ange de minsta förekomster som krävs för att n-gram ska ingå i ord listan för n-gram. 

    Om du till exempel använder standardvärdet 5 måste alla n-gram visas minst fem gånger i sökkorpus för att inkluderas i n-gram-ordlistan. 

1.  Ange **högsta dokument kvot för n-gram** till det maximala förhållandet för antalet rader som innehåller en viss n-gram, över antalet rader i den övergripande sökkorpus.

    Till exempel betyder förhållandet 1 att även om det finns ett särskilt n-gram på varje rad, kan du lägga till n-gram i ord listan n-gram. Vanligt vis anses ett ord som förekommer i varje rad vara ett brus ord och tas bort. Om du vill filtrera bort domän beroende skräpord kan du försöka minska förhållandet.

    > [!IMPORTANT]
    > Antalet förekomster av vissa ord är inte enhetligt, men det varierar från dokument till dokument. Om du till exempel analyserar kund kommentarer om en specifik produkt kan produkt namnet vara mycket högt och nära ett brus ord, men det är en viktig term i andra sammanhang.

1. Välj alternativet **normalisera n-gram-funktions vektorer** för att normalisera funktions vektorerna. Om den är aktive rad delas varje n-gram-funktions vektor dividerat med dess L2-norm.

1. Kör experimentet.

### <a name="use-an-existing-n-gram-dictionary"></a>Använd en befintlig n-gram-ordlista

1.  Lägg till **funktionerna extrahera N-g från text** -modulen till experimentet och Anslut data uppsättningen som innehåller den text som du vill bearbeta till **data uppsättnings** porten.

1.  Använd **text kolumnen** för att välja den text kolumn som innehåller den text som du vill funktionalisera. Som standard markeras alla kolumner av typen sträng i modulen. För bästa resultat ska du bearbeta en enskild kolumn i taget.

1. Lägg till den sparade data uppsättningen som innehåller en tidigare skapad n-gram-ordlista och Anslut den till porten för indata- **ordlista** . Du kan också ansluta **resultatet** av en överordnad instans av en överordnad instans av **funktionerna extrahera N-g från text** -modulen.

1. För **ord listans läge**väljer du följande uppdaterings alternativ i list rutan:

   * **ReadOnly**: Representerar sökkorpus för indatamängden. I stället för att använda term frekvenser från den nya text data uppsättningen (till vänster), tillämpas vikterna i n-gram från indata-vokabuläret som de är.

    > [!TIP]
    > Använd det här alternativet när du vill värdera en text klassificerare.

1.  För alla andra alternativ, se egenskaps beskrivningarna i [föregående avsnitt](#create-a-new-n-gram-dictionary).

1.  Kör experimentet.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Poäng eller publicera en modell som använder n-gram

1.  Kopiera **funktionerna extrahera N-g från text** -modulen från Training-dataflödet till Poäng data flödet.

1.  Anslut utdata från inlärnings data flödet till indatamängden för ord listan i resultat **data** flödet.

1.  I bedömnings arbets flödet ändrar du **extrahera N-g-funktioner från** textmodulen och gör dessa ändringar och lämnar alla andra samma:

    - Ange parametern för **ord listans läge** som **skrivskyddad**.

1.  Spara **frågeresultatet** som data uppsättning för att publicera experimentet.

1.  Anslut sedan den sparade data uppsättningen till funktionen **extrahera N-g från text** -modulen i poängsättnings diagrammet.

## <a name="results"></a>Resultat

Utdraget av **N-gram-funktioner från en text** -modul skapar två typer av utdata: 

* **Data uppsättning för resultat**: En sammanfattning av den analyserade texten kombinerat med det n-gram som extraherades. Kolumner som du inte har valt i **text kolumns** alternativet skickas till utdata. För varje kolumn med text som du analyserar genererar modulen dessa kolumner:

  * **Matris för n-gram-förekomster**: Modulen genererar en kolumn för varje n-gram som finns i den totala sökkorpus och lägger till en poäng i varje kolumn för att ange vikten för n-gram för raden. 

* **Terminologi för resultat**: Ord listan innehåller den faktiska n-gram-ordlistan, tillsammans med villkors poängen som genereras som en del av analysen. Du kan spara data uppsättningen för åter användning med en annan uppsättning indata eller för en senare uppdatering. Du kan också återanvända ord listan för modellering och bedömning.

### <a name="result-vocabulary"></a>Resultat vokabulär

Ord listan innehåller en n-gram-ordlista med termen frekvens Poäng som genereras som en del av analysen. Poängen **DF** och **IDF** genereras oavsett andra alternativ.

+ **ID**: En identifierare som genereras för varje unik n-gram.
+ **NGram**: N-gram. Blank steg eller andra ord separatorer ersätts av under streck.
+ **DF**: Term frekvens poängen för n-gram i den ursprungliga sökkorpus.
+ **IDF**: Resultatet av den inverterade dokument frekvensen för n-gram i den ursprungliga sökkorpus.

Du kan uppdatera den här data uppsättningen manuellt. Du kan dock införa fel. Exempel:

* Ett fel uppstår om modulen hittar dubblettrader med samma nyckel i den angivna ord listan. Se till att inga två rader i ord listan har samma ord.
* Schemat för vokabulärens data uppsättningar måste matcha exakt, inklusive kolumn namn och kolumn typer. 
* Kolumnen **ID** och **DF** -poängen måste vara av heltals typ. 
* **IDF** -kolumnen måste vara av typen float.

> [!Note]
> Anslut inte data utdata till modulen **träna modell** direkt. Kostnads fria text kolumner ska tas bort före utfodring i **träna modell**, annars kommer tomma text kolumner att behandlas som kategoriska funktioner.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
