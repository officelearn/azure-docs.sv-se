---
title: 'Partition och exempel: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder partition och exempel modul i Azure Machine Learning för att utföra sampling på en data uppsättning eller skapa partitioner från din data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 659dcae0bee0fe1015ba6225797c31bad438013f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497710"
---
# <a name="partition-and-sample-module"></a>Partition och exempel modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att utföra sampling på en data uppsättning eller för att skapa partitioner från din data uppsättning.

Sampling är ett viktigt verktyg i Machine Learning eftersom det gör att du kan minska storleken på en data uppsättning samtidigt som du behåller samma förhållande mellan värden. Den här modulen stöder flera relaterade uppgifter som är viktiga i Machine Learning: 

- Dela upp data i flera underavsnitt med samma storlek. 

    Du kan använda partitionerna för kors validering eller tilldela ärenden till slumpmässiga grupper.

- Separera data i grupper och arbeta med data från en speciell grupp. 

    När du slumpmässigt har tilldelat ärenden till olika grupper kan du behöva ändra de funktioner som är associerade med endast en grupp.

- Ta. 

    Du kan extrahera en procent andel av data, tillämpa slumpmässig sampling eller välja en kolumn som ska användas för att balansera data uppsättningen och utföra Stratified-sampling på dess värden.

- Skapa en mindre data uppsättning för testning. 

    Om du har många data kanske du bara vill använda de första *n* raderna när du konfigurerar pipelinen och sedan växlar till att använda den fullständiga data uppsättningen när du skapar din modell. Du kan också använda sampling för att skapa en mindre data uppsättning för användning under utveckling.

## <a name="configure-partition-and-sample"></a>Konfigurera partition och exempel

Den här modulen stöder flera metoder för att dela data i partitioner eller för att sampla. Välj först metoden och ange sedan ytterligare alternativ som krävs av metoden.

- Head
- Samling
- Tilldela till vikning
- Välj vikning

### <a name="get-top-n-rows-from-a-dataset"></a>Hämta de översta N raderna från en data uppsättning

Använd det här läget för att bara hämta de första *n* raderna. Det här alternativet är användbart om du vill testa en pipeline på ett litet antal rader och inte behöver de data som ska bal anse ras eller samplas på något sätt.

1. Lägg till **partition och exempel** modul i din pipeline i gränssnittet och Anslut data uppsättningen.  

2. **Partition eller exempel läge**: Ange det här alternativet till **Head**.

3. **Antal rader att välja**: Ange antalet rader som ska returneras.

    Antalet rader som du anger måste vara ett heltal som inte är negativt. Om antalet markerade rader är större än antalet rader i data uppsättningen returneras hela data uppsättningen.

4. Köra en pipeline.

Modulen matar ut en enskild data uppsättning som bara innehåller det angivna antalet rader. Raderna läses alltid in från data uppsättningens överkant.

### <a name="create-a-sample-of-data"></a>Skapa ett exempel på data

Det här alternativet stöder enkel slumpmässig provtagning eller Stratified slumpmässig sampling. Detta är användbart om du vill skapa en mindre representativ exempel data uppsättning för testning.

1. Lägg till **partition och exempel** modul i din pipeline och Anslut data uppsättningen.

2. **Partition eller exempel läge**: Ange det här för att **Sampla**.

3. **Samplings frekvens**: Ange ett värde mellan 0 och 1. Det här värdet anger procent andelen av rader från käll data uppsättningen som ska inkluderas i data uppsättningen för utdata.

    Om du till exempel bara vill ha hälften av den ursprungliga data uppsättningen skriver du `0.5` för att indikera att samplings frekvensen ska vara 50%.

    Raderna i indata-datauppsättningen är blandade och placeras selektivt i den utgående data uppsättningen enligt det angivna förhållandet.

4. **Slumpmässigt utsäde för sampling: om**du vill kan du ange ett heltal som ska användas som ett Seed-värde.

    Det här alternativet är viktigt om du vill att raderna ska delas upp på samma sätt varje gång. Standardvärdet är 0, vilket innebär att ett startutsäde genereras baserat på system klockan. Detta kan leda till något annorlunda resultat varje gången du kör pipelinen.

5. **Stratified Split för sampling**: Välj det här alternativet om det är viktigt att raderna i data uppsättningen ska delas jämnt av en nyckel kolumn före sampling.

    För **Stratification nyckel kolumn för sampling**väljer du en enda *Strata-kolumn* som ska användas vid division av data uppsättningen. Raderna i data uppsättningen delas sedan upp på följande sätt:

    1. Alla ingående rader grupperas (Stratified) med värdena i den angivna Strata-kolumnen.

    2. Rader flyttas i varje grupp.

    3. Varje grupp läggs selektivt till i data uppsättningen för utdata för att uppfylla det angivna förhållandet.


6. Köra en pipeline.

    Med det här alternativet matar modulen ut en enda data uppsättning som innehåller ett representativt urval av data. Den återstående, insamplade delen av data uppsättningen är inte utdata. 

## <a name="split-data-into-partitions"></a>Dela upp data i partitioner

Använd det här alternativet om du vill dela upp data uppsättningen i del mängder av data. Det här alternativet är också användbart när du vill skapa ett anpassat antal vikningar för kors validering eller dela rader i flera grupper.

1. Lägg till **partition och exempel** modul i din pipeline och Anslut data uppsättningen.

2. För **partition eller exempel läge**väljer **du tilldela till vikning**.

3. **Använd ersättnings i partitionering**: Välj det här alternativet om du vill att den exempel rad som ska läggas till i poolen med rader för eventuell åter användning. Därför kan samma rad tilldelas till flera vikningar.

    Om du inte använder Replacement (standard alternativet) tas inte den exempel raden tillbaka till poolen med rader för eventuell åter användning. Därför kan varje rad endast tilldelas till en vikning.

4. **Slumpmässig delning**: Välj det här alternativet om du vill att rader ska tilldelas slumpmässigt till vikning.

    Om du inte väljer det här alternativet tilldelas rader till vikning med hjälp av metoden Round-Robin.

5. **Slumpmässigt utsäde**: om du vill kan du ange ett heltal som ska användas som startvärdet. Det här alternativet är viktigt om du vill att raderna ska delas upp på samma sätt varje gång. Annars innebär standardvärdet 0 att ett slumpmässigt start värde används.

6. **Ange partitionerings metod**: Ange hur du vill att data ska fördelas till varje partition med hjälp av följande alternativ:

    - **Jämn partition**: Använd det här alternativet om du vill placera ett lika antal rader i varje partition. Ange antalet utgående partitioner genom att skriva ett heltal i rutan **Ange antal kurvor för att dela jämnt i** text rutan.

    - **Partition med anpassade proportioner**: Använd det här alternativet för att ange storleken på varje partition som en kommaavgränsad lista.

        Om du till exempel vill skapa tre partitioner, med den första partitionen som innehåller 50% av data, och de återstående två partitionerna som innehåller 25% av data, klickar du på **listan med proportioner avgränsade med kommatecken** och skriver följande tal: @no_ _t_1_`.5, .25, .25`

        Summan av alla partition storlekar måste vara upp till exakt 1.

        - Om du anger siffror som lägger till upp till **mindre än 1**, skapas en extra partition som innehåller de återstående raderna. Om du till exempel skriver värdena 2 och .3 skapas en tredje partition som innehåller de återstående 50 procenten av alla rader.

        - Om du anger siffror som lägger till upp till **fler än 1**uppstår ett fel när du kör pipelinen.

7. **Stratified Split**: Välj det här alternativet om du vill att raderna ska Stratified vid delning, och sedan välja _kolumnen Strata_.

8. Köra en pipeline.

    Med det här alternativet kommer modulen att mata ut flera data uppsättningar, partitionerade med de regler som du har angett.

### <a name="use-data-from-a-predefined-partition"></a>Använda data från en fördefinierad partition  

Det här alternativet används när du har delat upp en data uppsättning i flera partitioner och nu vill läsa in varje partition i tur och svar för vidare analys eller bearbetning.

1. Lägg till **partitionen och modulen exempel** i pipelinen.

2. Anslut den till utdata från en tidigare instans av **partition och exempel**. Den instansen måste ha använt alternativet **tilldela till vikning** för att generera ett antal partitioner.

3. **Partition eller exempel läge**: Välj **Välj vikning**.

4. **Ange vilken vikning som ska samplas från**: Välj en partition som ska användas genom att skriva dess index. Partition index är 1-baserade. Om du till exempel har delat upp data uppsättningen i tre delar, skulle partitionerna ha index 1, 2 och 3.

    Om du anger ett ogiltigt index värde genereras ett design tids fel: "Error 0018: dataset innehåller ogiltiga data".

    Förutom att gruppera data uppsättningen efter vikning kan du separera data uppsättningen i två grupper: en mål vikning och allt annat. Det gör du genom att skriva in indexet för en enstaka vikning och sedan välja alternativet, **välja komplementet till den valda vikningen**för att hämta allt, men data i den angivna vikningen.

5. Om du arbetar med flera partitioner måste du lägga till ytterligare instanser av **partition och exempel** -modulen för att hantera varje partition.

    Anta till exempel att du har partitionerat patienter i fyra vikningar med hjälp av ålder. Om du vill arbeta med varje enskild vikning behöver du fyra kopior av **partitionen och** modulen, och i varje väljer du en annan vikning, som du ser nedan. Det är inte rätt att använda alternativet **tilldela för att vika** utdata direkt.  

    [![partition och exempel](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Köra en pipeline.

    Med det här alternativet matar modulen ut en enda data uppsättning som innehåller endast de rader som tilldelats den vikningen.

> [!NOTE]
>  Du kan inte Visa vikt designerna direkt; de finns bara i metadata.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 