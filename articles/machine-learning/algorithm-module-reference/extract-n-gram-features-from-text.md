---
title: Extrahera N-Gram-funktioner från textmodulreferens
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Extrahera N-Gram i Azure Machine Learning för att visa textdata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477620"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrahera N-Gram-funktioner från textmodulreferens

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion). Använd modulen Extrahera N-Gram-funktioner från text för att *visa* ostrukturerade textdata. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Konfiguration av extrahera N-Gram-funktioner från textmodulen

Modulen stöder följande scenarier för att använda en n-gram ordlista:

* [Skapa en ny n-gram-ordlista](#create-a-new-n-gram-dictionary) från en kolumn med fritext.

* [Använd en befintlig uppsättning textfunktioner](#use-an-existing-n-gram-dictionary) för att visa en fritextkolumn.

* [Betygsätta eller publicera en modell](#score-or-publish-a-model-that-uses-n-grams) som använder n-gram.

### <a name="create-a-new-n-gram-dictionary"></a>Skapa en ny n-gramordlista

1.  Lägg till modulen Extrahera N-Gram-funktioner från text till pipelinen och anslut den datauppsättning som har den text du vill bearbeta.

1.  Använd **kolumnen Text** för att välja en kolumn med strängtyp som innehåller den text som du vill extrahera. Eftersom resultaten är utförliga kan du bara bearbeta en enskild kolumn åt gången.

1. Ange **ordförrådsläge** **för** att skapa för att ange att du skapar en ny lista med n-gram-funktioner. 

1. Ställ **N-Gram storlek** för att ange den *maximala* storleken på n-gram att extrahera och lagra. 

    Om du till exempel anger 3 skapas unigram, bigrams och trigrams.

1. **Viktningsfunktionen** anger hur du skapar dokumentfunktionens vektor och hur du extraherar ordförråd från dokument.

    * **Binärvikt**: Tilldelar det extraherade n-gramvärdet ett binärt närvarovärde. Värdet för varje n-gram är 1 när det finns i dokumentet och 0 annars.

    * **TF Vikt:** Tilldelar en term frekvens (TF) poäng till extraherade n-gram. Värdet för varje n-gram är dess förekomstfrekvens i dokumentet.

    * **IDF-vikt:** Tilldelar en omvänd dokumentfrekvens (IDF) poäng till de extraherade n-gram. Värdet för varje n-gram är stocken av korpusstorlek dividerat med dess förekomstfrekvens i hela corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF-vikt**: Tilldelar en termfrekvens/omvänd dokumentfrekvens (TF/IDF) till de extraherade n-gram. Värdet för varje n-gram är dess TF-poäng multiplicerat med dess IDF-poäng.

1. Ange **Minsta ordlängd** till det minsta antalet bokstäver som kan användas i ett *enda ord* i ett n-gram.

1. Använd **Maximal ordlängd** för att ange det maximala antalet bokstäver som kan användas i ett *enda ord* i ett n-gram.

    Som standard tillåts upp till 25 tecken per ord eller token.

1. Använd **Absolut lägsta n-gramdokumentfrekvens** för att ange de minsta förekomster som krävs för att n-gram ska ingå i ordlistan för n-gram. 

    Om du till exempel använder standardvärdet 5 måste alla n-gram visas minst fem gånger i corpus som ska ingå i n-gram-ordlistan. 

1.  Ange **Maximalt n-gramdokumentförhållande** till det maximala förhållandet för antalet rader som innehåller ett visst n-gram över antalet rader i den totala corpus.

    Ett förhållande på 1 anger till exempel att även om det finns ett visst n-gram på varje rad kan n-gram läggas till i ordlistan n-gram. Mer typiskt, ett ord som förekommer i varje rad skulle betraktas som ett brus ord och skulle tas bort. Om du vill filtrera bort domänberoende brusord kan du försöka minska det här förhållandet.

    > [!IMPORTANT]
    > Förekomsten av vissa ord är inte enhetlig. Det varierar från dokument till dokument. Om du till exempel analyserar kundkommentarer om en viss produkt kan produktnamnet vara mycket högfrekvent och nära ett brusord, men vara en viktig term i andra sammanhang.

1. Välj alternativet **Normalisera n-gram-funktionsvektorer** för att normalisera funktionens vektorer. Om det här alternativet är aktiverat divideras varje n-gram-funktionsvektor med l2-normen.

1. Skicka pipelinen.

### <a name="use-an-existing-n-gram-dictionary"></a>Använda en befintlig n-gram-ordlista

1.  Lägg till modulen Extrahera N-Gram-funktioner från text till pipelinen och anslut den datauppsättning som har den text som du vill bearbeta till **Dataset-porten.**

1.  Använd **kolumnen Text** för att markera den textkolumn som innehåller den text som du vill visa. Som standard väljer modulen alla kolumner av typen **sträng**. För bästa resultat, bearbeta en enskild kolumn i taget.

1. Lägg till den sparade datauppsättningen som innehåller en tidigare genererad n-gram-ordlista och anslut den till **indataordlistan.** Du kan också ansluta **resultatet ordförråd** utdata från en uppströmsinstans av extrahera N-Gram funktioner från text modulen.

1. För **ordförrådsläge**väljer du alternativet **Uppdatera ReadOnly** i listrutan.

   Alternativet **ReadOnly** representerar indatakorsen för indataordlistan. I stället för att beräkna termfrekvenser från den nya textdatauppsättningen (till vänster indata) tillämpas n-gramvikterna från indataordlistan som de är.

   > [!TIP]
   > Använd det här alternativet när du gör ett textklassificeringsdokumentant.

1.  Alla andra alternativ finns i egenskapsbeskrivningarna i [föregående avsnitt](#create-a-new-n-gram-dictionary).

1.  Skicka pipelinen.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Betygsätta eller publicera en modell som använder n-gram

1.  Kopiera **extrahera N-Gram-funktioner från textmodulen** från träningsdataflödet till bedömningsdataflödet.

1.  Anslut **resultatet Ordförråd** utdata från utbildningsdataflödet till **Indataordlista** på bedömningsdataflödet.

1.  I bedömningsarbetsflödet ändrar du modulen Extrahera N-Gram-funktioner från text och ställer in parametern **Ordförrådsläge** till **ReadOnly**. Lämna allt annat.

1.  Om du vill publicera pipelinen sparar **du Resultatordlista** som en datauppsättning.

1.  Anslut den sparade datauppsättningen till extrahera N-Gram-funktioner från textmodulen i bedömningsdiagrammet.

## <a name="results"></a>Resultat

Modulen Extrahera N-Gram-funktioner från text skapar två typer av utdata: 

* **Resultatdatauppsättning:** Den här utdata är en sammanfattning av den analyserade texten i kombination med n-gram som extraherades. Kolumner som du inte har markerat i alternativet **Textkolumn** skickas vidare till utdata. För varje kolumn med text som du analyserar genererar modulen följande kolumner:

  * **Matris av n-gram förekomster**: Modulen genererar en kolumn för varje n-gram som finns i den totala corpus och lägger till en poäng i varje kolumn för att ange vikten av n-gram för den raden. 

* **Resultatordlista**: Ordförrådet innehåller den faktiska n-gramordlistan, tillsammans med termen frekvenspoäng som genereras som en del av analysen. Du kan spara datauppsättningen för återanvändning med en annan uppsättning indata eller för en senare uppdatering. Du kan också återanvända ordförrådet för modellering och bedömning.

### <a name="result-vocabulary"></a>Ordförråd för resultat

Ordförrådet innehåller n-gram ordlistan med termen frekvenspoäng som genereras som en del av analysen. DF- och IDF-poängen genereras oavsett andra alternativ.

+ **ID:** En identifierare som genereras för varje unik n-gram.
+ **NGram**: N-gram. Blanksteg eller andra ordavgränsare ersätts med understrecket.
+ **DF**: Benämnafrekvenspoängen för n-gramn i den original- corpusen.
+ **IDF**: Den omvända dokumentfrekvenspoängen för n-gram i den ursprungliga corpus.

Du kan uppdatera den här datauppsättningen manuellt, men du kan införa fel. Ett exempel:

* Ett fel utlöses om modulen hittar dubblettrader med samma nyckel i indataordlistan. Se till att inga två rader i ordförrådet har samma ord.
* Indataschemat för ordförrådets datauppsättningar måste matcha exakt, inklusive kolumnnamn och kolumntyper. 
* **Kolumnen ID** och **DF** måste vara av heltalstypen. 
* **IDF-kolumnen** måste vara av flottörtyp.

> [!Note]
> Anslut inte datautdata till modulen Tågmodell direkt. Du bör ta bort fritextkolumner innan de matas in i tågmodellen. Annars behandlas fritextkolumnerna som kategoriska funktioner.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.
