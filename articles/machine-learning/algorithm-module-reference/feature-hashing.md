---
title: Funktionshagemodulreferens
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Funktionsh hashing i Azure Machine Learning för att visa textdata.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456258"
---
# <a name="feature-hashing-module-reference"></a>Funktionshagemodulreferens

I den här artikeln beskrivs en modul som ingår i Azure Machine Learning designer (förhandsversion).

Använd modulen Funktionshånning för att omvandla en ström med engelsk text till en uppsättning heltalsfunktioner. Du kan sedan skicka den här hash-funktionen till en maskininlärningsalgoritm för att träna en textanalysmodell.

Funktionen hash-funktioner som tillhandahålls i den här modulen är baserad på nimbusml-ramverket. Mer information finns i [klassen NgramHash](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Vad är funktionshage?

Funktionsh hash-funktioner fungerar genom att konvertera unika token till heltal. Den fungerar på de exakta strängar som du anger som indata och utför inte någon språklig analys eller förbehandling. 

Ta till exempel en uppsättning enkla meningar som dessa, följt av en sentimentpoäng. Anta att du vill använda den här texten för att skapa en modell.

|Användartext|Sentiment|
|--------------|---------------|
|Jag älskade den här boken|3|
|Jag hatade den här boken|1|
|Denna bok var stor|3|
|Jag älskar böcker|2|

Internt skapar funktionshagemodulen en ordlista med n-gram. Till exempel skulle listan över bigrams för denna datauppsättning vara ungefär så här:

|Term (bigrams)|Frequency|
|------------|---------------|
|Denna bok|3|
|Jag älskade|1|
|Jag hatade|1|
|Jag älskar|1|

Du kan styra storleken på n-gram med hjälp av **N-gram** egenskapen. Om du väljer bigrams beräknas även unigram. Ordboken skulle också innehålla enstaka termer som dessa:

|Term (unigram)|Frequency|
|------------|---------------|
|bok|3|
|I|3|
|böcker|1|
|Var|1|

När ordlistan har skapats konverteras ordlistningsmodulen ordlistetermerna till hash-värden. Det beräknar sedan om en funktion har använts i varje enskilt fall. För varje rad med textdata matar modulen ut en uppsättning kolumner, en kolumn för varje hash-funktion.

Efter hash-hasch kan till exempel funktionskolumnerna se ut ungefär så här:

|Omdöme|Hashing-funktion 1|Hashing-funktion 2|Hashing-funktion 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Om värdet i kolumnen är 0 innehöll inte raden funktionen hash.
* Om värdet är 1 innehöll raden funktionen.

Med funktionshökning kan du representera textdokument med varierande längd som numeriska funktionsvektorer med samma längd för att minska dimensionaliteten. Om du försökte använda textkolumnen för utbildning som den är, skulle den behandlas som en kategorisk funktionskolumn med många olika värden.

Numeriska utdata gör det också möjligt att använda vanliga machine learning-metoder, inklusive klassificering, klustring och informationshämtning. Eftersom uppslagsåtgärder kan använda heltalsscagerar i stället för strängjämförelser är det också mycket snabbare att få funktionsvikterna.

## <a name="configure-the-feature-hashing-module"></a>Konfigurera funktionshyrmodulen

1.  Lägg till modulen Funktionshhing i pipelinen i designern.

1. Anslut datauppsättningen som innehåller den text som du vill analysera.

    > [!TIP]
    > Eftersom funktionshämtning inte utför lexikala åtgärder som att hejda eller trunkera kan du ibland få bättre resultat genom att förbearbetning av text innan du tillämpar funktionshämtning. 

1. Ange **målkolumner** till de textkolumner som du vill konvertera till hash-funktioner. Tänk på att:

    * Kolumnerna måste vara strängdatatypen.
    
    * Om du väljer flera textkolumner kan det ha en betydande inverkan på funktionsdimensionaliteten. Antalet kolumner för en 10-bitars hash går till exempel från 1 024 för en enskild kolumn till 2 048 för två kolumner.

1. Använd **Hashing-bitstorlek** för att ange hur många bitar som ska användas när du skapar hash-tabellen.
    
    Standardbitstorleken är 10. För många problem är detta värde tillräckligt. Du kan behöva mer utrymme för att undvika kollisioner, beroende på storleken på n-gram ordförråd i utbildningen texten.
    
1. För **N-gram**anger du ett tal som definierar den maximala längden på n-gram som ska läggas till i träningsordlistan. Ett n-gram är en sekvens av *n-ord* som behandlas som en unik enhet.

    Om du till exempel anger 3 skapas unigram, bigrams och trigrams.

1. Skicka pipelinen.

## <a name="results"></a>Resultat

När bearbetningen är klar matar modulen ut en transformerad datauppsättning där den ursprungliga textkolumnen har konverterats till flera kolumner. Varje kolumn representerar en funktion i texten. Beroende på hur viktig ordlistan är kan den resulterande datauppsättningen vara stor:

|Kolumnnamn 1|Kolumntyp 2|
|-------------------|-------------------|
|ANVÄNDARTEXT|Kolumnen Ursprunglig data|
|Känsla|Kolumnen Ursprunglig data|
|USERTEXT - Hashing-funktion 1|Kolumnen Hashed-funktion|
|USERTEXT - Hashing-funktion 2|Kolumnen Hashed-funktion|
|USERTEXT - Hashing-funktion n|Kolumnen Hashed-funktion|
|USERTEXT - Hashing-funktion 1024|Kolumnen Hashed-funktion|

När du har skapat den transformerade datauppsättningen kan du använda den som indata till modulen Tågmodell.
 
## <a name="best-practices"></a>Bästa praxis

Följande metodtips kan hjälpa dig att få ut så mycket som möjligt av funktions hash-modulen:

* Lägg till en förprocesstextmodul innan du använder Funktionshämtning för att förbehandla indatatexten. 

* Lägg till en modul för select columns efter funktionshyrmodulen om du vill ta bort textkolumnerna från utdatauppsättningen. Du behöver inte textkolumnerna när hash-funktionerna har genererats.
    
* Överväg att använda dessa alternativ för förbehandling av text för att förenkla resultaten och förbättra noggrannheten:

    * Ord bryta
    * Stoppa borttagning av ord
    * Normalisering av skiftläge
    * Borttagning av skiljetecken och specialtecken
    * Avledning  

Den optimala uppsättningen förbearbetningsmetoder som ska tillämpas i alla lösningar beror på domän-, ordförråd och affärsbehov. pipeline med dina data för att se vilka textbehandlingsmetoder som är mest effektiva.

## <a name="next-steps"></a>Nästa steg
            
Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning 
