---
title: Latent Dirichlet Tilldelning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Latent Dirichlet Allocation för att gruppera annars oklassificerad text i ett antal kategorier.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109230"
---
# <a name="latent-dirichlet-allocation"></a>Latent Dirichlet Tilldelning

I den här artikeln beskrivs hur du använder modulen **Latent Dirichlet Allocation** i Azure Machine Learning designer (förhandsversion) för att gruppera annars oklassificerad text i ett antal kategorier. 

Latent Dirichlet Allocation (LDA) används ofta i bearbetning av naturligt språk (NLP) för att hitta liknande texter. En annan vanlig term är *ämnesmodellering*.

Den här modulen tar en kolumn med text och genererar dessa utdata:

+ Källtexten, tillsammans med en poäng för varje kategori

+ En funktionsmatris som innehåller extraherade termer och koefficienter för varje kategori

+ En omvandling som du kan spara och återanvända till ny text som används som indata

Den här modulen använder biblioteket scikit-learn. Mer information om scikit-learn finns i [GitHub-databasen, som innehåller självstudier och en förklaring av algoritmen.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Mer om Latent Dirichlet Allocation (LDA)

Generellt sett är LDA inte en metod för klassificering i sig, men använder en generativ metod. Vad detta innebär är att du inte behöver ange kända klassetiketter och sedan dra slutsatsen att mönstren.  Istället genererar algoritmen en probabilistisk modell som används för att identifiera grupper av ämnen. Du kan använda den probabilistiska modellen för att klassificera antingen befintliga utbildningsärenden eller nya ärenden som du tillhandahåller modellen som indata.

En generativ modell kan vara att föredra eftersom den undviker att göra några starka antaganden om förhållandet mellan text och kategorier, och använder endast fördelningen av ord till matematiskt modellämnen.

+ Teorin diskuteras i detta dokument, tillgänglig som en PDF-nedladdning: [Latent Dirichlet Allocation: Blei, Ng och Jordanien](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ Implementeringen i denna modul är baserad på [scikit-learn biblioteket](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) för LDA.

Mer information finns i avsnittet [Tekniska anteckningar.](#technical-notes)

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Så här konfigurerar du Latent Dirichlet Allocation

Den här modulen kräver en datauppsättning som innehåller en textkolumn, antingen rå eller förbearbetad.

1. Lägg till modulen **Latent Dirichlet Allocation** i pipelinen.

2. Som indata för modulen anger du en datauppsättning som innehåller en eller flera textkolumner.

3. För **Målkolumner**väljer du en eller flera kolumner som innehåller text att analysera.

    Du kan välja flera kolumner, men de måste vara av strängdatatypen.

    Eftersom LDA skapar en stor funktionsmatris från texten analyserar du i allmänhet vanligtvis en enda textkolumn.

4. För **Antal ämnen som ska modelleras**skriver du ett heltal mellan 1 och 1000 som anger hur många kategorier eller ämnen du vill härleda från indatatexten.

    Som standard skapas 5 ämnen.

5. För **N-gram**anger du den maximala längden på N-gram som genereras under hashningen.

    Standard är 2, vilket innebär att både bigrams och unigrams genereras.

6. Välj alternativet **Normalisera** om du vill konvertera utdatavärden till sannolikheter. I stället för att representera de transformerade värdena som heltal omvandlas därför värden i utdata- och funktionsdatauppsättningen på följande sätt:

    + Värden i datauppsättningen representeras som en `P(topic|document)`sannolikhet där .

    + Värden i funktionsämnesmatrisen representeras `P(word|topic)`som en sannolikhet där .

    > [!NOTE] 
    > I Azure Machine Learning designer (förhandsversion), eftersom biblioteket som vi baserade, scikit-learn, inte längre stöder onormaliserad *doc_topic_distr* utdata från version 0.19, därför kan **normalisera** parametern normalisera i den här modulen endast tillämpas på matrisutdata för **funktionsämne,** **Transformerad datauppsättningsutdata normaliseras** alltid.

7. Välj alternativet, **Visa alla alternativ**och ställ sedan in det på SANT om du vill visa och ange sedan ytterligare avancerade parametrar.

    Dessa parametrar är specifika för scikit-learn genomförandet av LDA. Det finns några bra tutorials om LDA i scikit-lära, liksom den officiella [scikit-learn dokument](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho parameter**. Ge en tidigare sannolikhet för sparsamhet ämnesfördelningar. Motsvarar sklearns `topic_word_prior` parameter. Du skulle använda värdet 1 om du förväntar dig att ordfördelningen är platt. dvs alla ord antas utrustas. Om du tror att de flesta ord ska se glest ut kan du ställa in det till ett mycket lägre värde.

    + **Alfaparameter**. Ange en tidigare sannolikhet för sparsiteten för ämnesvikter per dokument.  Motsvarar sklearns `doc_topic_prior` parameter.

    + **Uppskattat antal dokument**. Skriv ett tal som representerar den bästa uppskattningen av antalet dokument (rader) som ska bearbetas. På så sätt kan modulen allokera en hash-tabell av tillräcklig storlek.  Motsvarar parametern `total_samples` i scikit-learn.

    + **Partiets storlek**. Skriv ett tal som anger hur många rader som ska tas med i varje grupp med text som skickas till LDA-modellen. Motsvarar parametern `batch_size` i scikit-learn.

    + **Det första värdet av iteration som används i uppdateringsschemat**för inlärning . Ange startvärdet som minskar inlärningsfrekvensen för tidiga iterationer i onlineinlärning. Motsvarar parametern `learning_offset` i scikit-learn.

    + **Ström som tillämpas på iterationen under uppdateringar**. Ange vilken effektnivå som tillämpas på iterationsantalet för att kontrollera inlärningsfrekvensen under onlineuppdateringar. Motsvarar parametern `learning_decay` i scikit-learn.

    + **Antal passerar över data**. Ange det maximala antalet gånger som algoritmen ska cykla över data. Motsvarar parametern `max_iter` i scikit-learn.

8. Välj alternativet Skapa **ordlista med ngrams** eller **Skapa ordlista med ngram före LDA**, om du vill skapa n-gram-listan i ett första pass, innan du klassificerar text.

    Om du skapar den första ordlistan i förväg kan du senare använda ordlistan när du granskar modellen. Att kunna mappa resultat till text snarare än numeriska index är i allmänhet lättare för tolkning. Det tar dock längre tid att spara ordlistan och använda ytterligare lagringsutrymme.

9. För **Maximal storlek på ngram-ordlista**anger du det totala antalet rader som kan skapas i ordlistan n-gram.

    Det här alternativet är användbart för att kontrollera storleken på ordlistan. Om antalet ngram i indata överstiger denna storlek kan kollisioner dock inträffa.

10. Skicka pipelinen. LDA-modulen använder Bayes sats för att avgöra vilka ämnen som kan associeras med enskilda ord. Ord är inte uteslutande associerade med några ämnen eller grupper. I stället har varje n-gram en inlärd sannolikhet att associeras med någon av de upptäckta klasserna.

## <a name="results"></a>Resultat

Modulen har två utgångar:

+ **Transformerad datauppsättning**: Innehåller indatatexten och ett angivet antal identifierade kategorier, tillsammans med poängen för varje textexempel för varje kategori.

+ **Funktionsämnesmatris:** Kolumnen längst till vänster innehåller den extraherade textfunktionen och det finns en kolumn för varje kategori som innehåller poängen för den funktionen i den kategorin.


### <a name="lda-transformation"></a>LDA-omvandling

Den här modulen matar också ut *LDA-omvandlingen* som tillämpar LDA på datauppsättningen.

Du kan spara den här omvandlingen genom att registrera datauppsättning under fliken **Utdata+loggar** i den högra rutan i modulen och återanvända den för andra datauppsättningar. Detta kan vara användbart om du har tränat på en stor korpus och vill återanvända koefficienterna eller kategorierna.

### <a name="refining-an-lda-model-or-results"></a>Förfina en LDA-modell eller -resultat

Vanligtvis kan du inte skapa en enda LDA-modell som uppfyller alla behov, och även en modell som utformats för en uppgift kan kräva många iterationer för att förbättra noggrannheten. Vi rekommenderar att du provar alla dessa metoder för att förbättra din modell:

+ Ändra modellparametrar
+ Använda visualisering för att förstå resultaten
+ Få feedback från ämnesexperter för att ta reda på om de genererade ämnena är användbara.

Kvalitativa åtgärder kan också vara användbara för att bedöma resultaten. Om du vill utvärdera ämnesmodelleringsresultat bör du tänka på följande:

+ Noggrannhet - Är liknande objekt verkligen liknande?
+ Mångfald - Kan modellen diskriminera mellan liknande objekt när det behövs för affärsproblemet?
+ Skalbarhet - Fungerar det på ett brett spektrum av textkategorier eller bara på en smal måldomän?

Noggrannheten i modeller baserade på LDA kan ofta förbättras genom att använda bearbetning av naturligt språk för att rengöra, sammanfatta och förenkla eller kategorisera text. Följande tekniker, alla som stöds i Azure Machine Learning, kan till exempel förbättra klassificeringsnoggrannheten:

+ Stoppa borttagning av ord

+ Normalisering av skiftläge

+ Lemmatisering eller avledning

+ Igenkänning av namngiven enhet

Mer information finns i [Förbehandlad text](preprocess-text.md).

I designern kan du också använda R- eller Python-bibliotek för textbearbetning: [Kör R Script](execute-r-script.md), Kör Python [Script](execute-python-script.md)



## <a name="technical-notes"></a>Tekniska anmärkningar

Det här avsnittet innehåller implementeringsinformation, tips och svar på vanliga frågor.

### <a name="implementation-details"></a>Information om genomförandet

Som standard normaliseras distributionerna av utdata för transformerade datamängder och funktionsämnesmatris som sannolikheter.

+ Den transformerade datauppsättningen normaliseras som den villkorliga sannolikheten för ämnen som får ett dokument. I det här fallet är summan av varje rad lika med 1.

+ Matrisen för funktionsämne normaliseras som den villkorliga sannolikheten för ord som får ett ämne. I det här fallet är summan av varje kolumn lika med 1.

> [!TIP]
> Ibland kan modulen returnera ett tomt ämne, vilket oftast orsakas av pseudo-slumpmässig initiering av algoritmen.  Om detta inträffar kan du prova att ändra relaterade parametrar, till exempel den maximala storleken på N-grams ordlistan eller antalet bitar som ska användas för funktionshage.

### <a name="lda-and-topic-modeling"></a>LDA och ämnesmodellering

Latent Dirichlet Allocation (LDA) används ofta för *innehållsbaserad ämnesmodellering*, vilket i princip innebär att lära sig kategorier från oklassificerad text. I innehållsbaserad ämnesmodellering är ett ämne en distribution över ord.

Anta till exempel att du har tillhandahållit en korpus av kundrecensioner som innehåller många, många produkter. Den recensioner som har lämnats in av många kunder över tiden skulle innehålla många termer, varav några används i flera ämnen.

Ett **ämne** som identifieras av LDA-processen kan representera recensioner för en enskild produkt A, eller det kan representera en grupp av produktrecensioner. För LDA är ämnet i sig bara en sannolikhetsfördelning över tiden för en uppsättning ord.

Villkor är sällan exklusiva för någon produkt, men kan hänvisa till andra produkter, eller vara allmänna villkor som gäller för allt ("bra", "hemskt"). Andra termer kan vara ljud ord.  Det är dock viktigt att förstå att LDA-metoden inte utger sig för att fånga alla ord i universum, eller att förstå hur ord är relaterade, bortsett från sannolikheter för samförekomst. Det kan bara gruppera ord som användes i måldomänen.

När termen index har beräknats jämförs enskilda rader med text med hjälp av ett avståndsbaserat likhetsmått för att avgöra om två textstycken är som varandra.  Du kanske till exempel upptäcker att produkten har flera namn som är starkt korrelerade. Eller så kanske du upptäcker att starkt negativa termer vanligtvis är associerade med en viss produkt. Du kan använda likhetsmåttet både för att identifiera relaterade termer och för att skapa rekommendationer.

###  <a name="module-parameters"></a>Modulparametrar

|Namn|Typ|Intervall|Valfri|Default|Beskrivning|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Målkolumnerna|Kolumnmarkering||Krävs|StringFeature (Stråk)|Namn eller index för målkolumn|  
|Antal ämnen att modellera|Integer|[1;1000]|Krävs|5|Modellera dokumentfördelningen mot N-ämnen|  
|N-gram|Integer|[1;10]|Krävs|2|Beställning av N-gram som genereras under hashing|  
|Normalisera|Boolean|Sant eller falskt|Krävs|true|Normalisera produktionen till sannolikheter.  Den transformerade datauppsättningen blir P(ämne&#124;dokument) och funktionsämnesmatrisen blir P(word&#124;ämne)|  
|Visa alla alternativ|Boolean|Sant eller falskt|Krävs|False|Presenterar ytterligare parametrar som är specifika för scikit-learn online LDA|  
|Rho parameter|Float (Flyttal)|[0.00001;1.0]|Används när kryssrutan **Visa alla alternativ** är markerad|0,01|Föregående ämnesfördelning|  
|Alfaparameter|Float (Flyttal)|[0.00001;1.0]|Används när kryssrutan **Visa alla alternativ** är markerad|0,01|Föregående distribution av dokumentämne|  
|Uppskattat antal dokument|Integer|[1;int. MaxValue)|Används när kryssrutan **Visa alla alternativ** är markerad|1000|Uppskattat antal dokument (motsvarar total_samples parameter)|  
|Partiets storlek|Integer|[1;1024]|Används när kryssrutan **Visa alla alternativ** är markerad|32|Partiets storlek|  
|Det första värdet av iteration som används i uppdateringsschemat för inlärningshastighet|Integer|[0;int. MaxValue)|Används när kryssrutan **Visa alla alternativ** är markerad|0|Initialt värde som downweights inlärningshastighet för tidiga iterationer. Motsvarar parametern learning_offset|  
|Ström som tillämpas på iterationen under uppdateringar|Float (Flyttal)|[0.0;1.0]|Används när kryssrutan **Visa alla alternativ** är markerad|0,5|Effekt som tillämpas på iterationsantalet för att kontrollera inlärningshastigheten. Motsvarar parametern learning_decay |  
|Antal träningsiterationer|Integer|[1;1024]|Används när kryssrutan **Visa alla alternativ** är markerad|25|Antal träningsiterationer|  
|Skapa ordlista med ngrams|Boolean|Sant eller falskt|Används när kryssrutan **Visa alla alternativ** *inte* är markerad|True|Bygger en ordbok med ngrams innan du beräknar LDA. Användbart för modellinspektion och tolkning|  
|Maximal storlek på ngram-ordlista|Integer|[1;int. MaxValue)|Gäller när alternativet **Skapa ordlista för ngram är** Sant|20000|Maximal storlek på ngrams-ordlistan. Om antalet token i indata överstiger denna storlek kan kollisioner inträffa|  
|Antal bitar som ska användas för funktionshage|Integer|[1;31]|Används när kryssrutan **Visa alla alternativ** *inte* är markerad och **Skapa ordlista för ngrams** är Falskt|12|Antal bitar som ska användas för funktionshage| 
|Skapa ordlista med ngram före LDA|Boolean|Sant eller falskt|Används när kryssrutan **Visa alla alternativ** är markerad|True|Skapar en ordlista med ngram före LDA. Användbart för modellinspektion och tolkning|  
|Maximalt antal ngram i ordlistan|Integer|[1;int. MaxValue)|Används när kryssrutan **Visa alla alternativ** är markerad och alternativet Skapa **ordlista för ngram är** Sant|20000|Maximal storlek på ordlistan. Om antalet token i indata överstiger denna storlek kan kollisioner inträffa|  
|Antal hash-bitar|Integer|[1;31]|Används när kryssrutan **Visa alla alternativ** är markerad och alternativet Skapa **ordlista för ngram är** Falskt|12|Antal bitar som ska användas under funktionshage|   


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.   
En lista över fel som är specifika för modulerna finns i [Undantag och felkoder för designern](designer-error-codes.md).
