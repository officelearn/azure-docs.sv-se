---
title: 'Hashing av funktioner: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen för funktions-hash i Azure Machine Learning-tjänsten för att funktionalisera text data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 78d01cf071faed312773ebf12c75e7e6e5596e71
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210850"
---
# <a name="feature-hashing"></a>Hashing av funktioner

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd modulen **funktion-hash** för att transformera en strömmande engelsk text till en uppsättning med heltals funktioner. Du kan sedan skicka den här hash-funktionen till en Machine Learning-algoritm för att träna en text analys modell.

Funktionerna i funktionen hashing som tillhandahålls i den här modulen baseras på nimbusml-ramverket. Mer information finns i [NgramHash-klass](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Vad är funktionen hashing

Funktionen hashing fungerar genom att konvertera unika tokens till heltal. Den fungerar på de exakta strängarna som du anger som inmatade och utför inte någon språkanalys eller för bearbetning. 

Ta till exempel en uppsättning enkla meningar som dessa, följt av en sentiment poäng. Anta att du vill använda den här texten för att bygga en modell.

|USERTEXT|SENTIMENT|
|--------------|---------------|
|Jag gillat den här boken|3|
|Jag Hated den här boken|1|
|Den här boken var fantastisk|3|
|Jag älskar böcker|2|

Internt skapar modulen för **funktions-hash** en ord lista med n-gram. Listan över för den här data uppsättningen skulle till exempel vara ungefär så här:

|TERM (bigram)|FREKVENS|
|------------|---------------|
|Den här boken|3|
|Jag gillat|1|
|Jag Hated|1|
|Jag älskar|1|

Du kan kontrol lera storleken på n-gram med hjälp av egenskapen **n-g** . Om du väljer unigrams beräknas även. Ord listan innehåller även enkla termer som dessa:

|Term (unigrams)|FREKVENS|
|------------|---------------|
|bokfilen|3|
|I|3|
|böcker|1|
|upptäckt|1|

När ord listan har skapats konverterar modulens **hashing** villkoren till hash-värden. Sedan beräknar den om en funktion har använts i varje fall. För varje rad med text data visar modulen en uppsättning kolumner, en kolumn för varje hashad funktion.

Till exempel kan funktions kolumnerna se ut ungefär så här efter hashing:

|Klassificering|Hash-funktion 1|Hash-funktion 2|Hash-funktion 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Om värdet i kolumnen är 0, innehåller raden inte den hashade funktionen.
* Om värdet är 1 innehåller raden funktionen.

Med funktionen hashing kan du representera text dokument med variabel längd som numeriska funktions vektorer med samma längd för att uppnå en minskning av dimensionalitet. Om du försökte använda text kolumnen för utbildning som är, behandlas den som en kategoriska funktions kolumn med många distinkta värden.

Numeriska utdata gör det också möjligt att använda vanliga Machine Learning-metoder, inklusive klassificering, klustring och informations hämtning. Eftersom Lookup-åtgärder kan använda heltals-hashs i stället för sträng jämförelser är det också mycket snabbare att få funktions vikter.

## <a name="configure-feature-hashing"></a>Konfigurera funktionens hashing

1.  Lägg till modulen för **funktions-hash** i experimentet i Visual Interface.

1. Anslut data uppsättningen som innehåller den text som du vill analysera.

    > [!TIP]
    > Eftersom funktionens hashing inte utför lexikala åtgärder som till exempel för identifiering eller trunkering kan du ibland få bättre resultat genom att utföra text förbearbetning innan du tillämpar funktionen hashing. 

1. Ange **mål kolumner** för de text kolumner som du vill konvertera till hashade funktioner. 

    * Kolumnerna måste vara av data typen sträng.
    
    * Att välja flera text kolumner kan ha en betydande inverkan på funktionens dimensionalitet. Antalet kolumner för en 10-bitars hash går till exempel från 1024 för en enda kolumn till 2048 för två kolumner.

1. Använd **hashing-bitsize** för att ange antalet bitar som ska användas när du skapar hash-tabellen.
    
    Standard bit storleken är 10. För många problem är detta värde tillräckligt. Du kan behöva mer utrymme för att undvika kollisioner beroende på storleken på inlärnings textens n-gram-vokabulär.
    
1. För **n-gram**anger du ett tal som definierar den maximala längden för N-g som ska läggas till i övnings ord listan. En n-gram är en sekvens av *n* ord som behandlas som en unik enhet.

    Om du till exempel anger 3, unigrams, 2 g och trigrams kommer att skapas.

1. Kör experimentet.

## <a name="results"></a>Resultat

När bearbetningen är klar matar modulen ut en transformerad data uppsättning i vilken den ursprungliga text kolumnen har konverterats till flera kolumner, var och en som representerar en funktion i texten. Beroende på hur stor ord listan är kan den resulterande data uppsättningen vara stor:

|Kolumn namn 1|Kolumn typ 2|
|-------------------|-------------------|
|USERTEXT|Ursprunglig data kolumn|
|SENTIMENT|Ursprunglig data kolumn|
|USERTEXT – hashing funktion 1|Kolumnen hashed Feature|
|USERTEXT-hashing funktion 2|Kolumnen hashed Feature|
|USERTEXT – hashing funktion n|Kolumnen hashed Feature|
|USERTEXT-hashing funktion 1024|Kolumnen hashed Feature|

När du har skapat den transformerade data uppsättningen kan du använda den som indata för modulen träna modell.
 
### <a name="best-practices"></a>Bästa praxis

Följande metod tips kan hjälpa dig att få ut mesta möjliga av modulen för **funktions-hash** :

* Lägg till en modul för Förbearbetad **text** innan du använder **funktionen hashing** för att Förbearbeta indatamängden. 

* Lägg till modulen **Välj kolumner** efter modulen för **funktions-hash** för att ta bort text kolumnerna från den utgående data uppsättningen. Du behöver inte text kolumnerna när hash-funktionerna har genererats.
    
* Överväg att använda dessa alternativ för förbehandling av text, för att förenkla resultaten och förbättra precisionen:

    * Ordnedbrytning
    * Stoppa borttagning av ord
    * Fall normalisering
    * Borttagning av interpunktion och specialtecken
    * Följd  

Den optimala uppsättningen för bearbetnings metoder som ska användas i en enskild lösning beror på domän, vokabulär och affärs behov. Experimentera med dina data för att se vilka text bearbetnings metoder som är mest effektiva.

## <a name="next-steps"></a>Nästa steg
            
Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
