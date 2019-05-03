---
title: 'Partitionera och ta prover: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen partitionera och ta prover i Azure Machine Learning-tjänsten att utföra sampling på en datauppsättning eller att skapa partitioner från din datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029287"
---
# <a name="partition-and-sample-module"></a>Partitionera och ta prover modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen att utföra sampling på en datauppsättning eller att skapa partitioner från din datauppsättning.

Sampling är ett viktigt verktyg i maskininlärning eftersom du kan minska storleken på en datauppsättning samtidigt som du behåller samma förhållandet mellan värdena. Den här modulen stöder flera relaterade uppgifter som är viktiga i machine learning: 

- Dela upp dina data i flera underavsnitt av samma storlek. 

    Du kan använda partitionerna för korsvalidering eller för att tilldelas slumpmässigt grupper.

- Delar data i grupper och sedan arbeta med data från en viss grupp. 

    När du har tilldelat slumpmässigt fall till olika användargrupper, kan du behöva ändra funktionerna som är associerad med endast en grupp.

- Sampling. 

    Du kan extrahera en del av data, tillämpa stickprov eller välj en kolumn som ska användas för belastningsutjämning datauppsättningen och utföra stratified sampling på dess värden.

- Skapa en mindre datauppsättning för testning. 

    Om du har stora mängder data kan du kanske vill använda endast första *n* rader när konfigurationen av experiment och sedan växla till med hjälp av hela datauppsättningen när du skapar din modell. Du kan också använda sampling för att skapa s mindre datauppsättning för användning i utveckling.

## <a name="configure-partition-and-sample"></a>Konfigurera Partition och exempel

Den här modulen stöder flera metoder för att dela upp dina data i partitioner eller för linjer. Välj metoden först och sedan ange ytterligare alternativ som krävs av-metoden.

- Huvud
- Samling
- Tilldela till vikningar
- Välj vikning

### <a name="get-top-n-rows-from-a-dataset"></a>Hämta TOP N rader från en datauppsättning

Använd det här läget för att få endast första *n* rader. Det här alternativet är användbart om du vill testa ett experiment på ett litet antal rader och behöver inte data som ska vara belastningsutjämnade eller samplas på något sätt.

1. Lägg till den **partitionera och ta prover** modulen i experimentet i gränssnittet, och Anslut datauppsättningen.  

2. **Partitionen eller exempel läge**: Välja alternativet **Head**.

3. **Antalet rader att välja**: Ange antalet rader som ska returneras.

    Antalet rader som du anger måste vara ett icke-negativt heltal. Om antalet valda rader är större än antalet rader i datauppsättningen, returneras hela datamängden.

4. Kör experimentet.

Modulen matar ut en enskild datauppsättning som innehåller endast det angivna antalet rader. Rader läses alltid högst upp på datauppsättningen.

### <a name="create-a-sample-of-data"></a>Skapa ett exempel på data

Det här alternativet stöder enkel stickprov eller stratified stickprov. Detta är användbart om du vill skapa en mindre representativt urval datauppsättning för testning.

1. Lägg till den **partitionera och ta prover** modul till ditt experiment, och Anslut datauppsättningen.

2. **Partitionen eller exempel läge**: Ställ in på **Sampling**.

3. **Frekvensen för sampling**: Ange ett värde mellan 0 och 1. Det här värdet anger hur stor procentandel av rader från datauppsättningen för källan som ska ingå i datauppsättningen för utdata.

    Om du vill att endast hälften av den ursprungliga datauppsättningen, exempelvis `0.5` att indikera att samplingsfrekvensen ska vara 50%.

    Raderna i datauppsättningen för indata är blandad och placera selektivt i datauppsättningen för utdata enligt angivna förhållandet.

4. **Slumpmässig dirigering för provtagning**: Du kan också ange ett heltal som ska användas som ett startvärde.

    Det här alternativet är viktigt om du vill att raderna som ska delas upp på samma sätt varje gång. Standardvärdet är 0, vilket innebär att en från seed genereras baserat på systemklockan. Detta kan leda till lite olika resultat varje gång du kör experimentet.

5. **Stratified dela för provtagning**: Välj detta alternativ om det är viktigt att raderna i datauppsättningen jämt ska divideras med vissa nyckelkolumn innan sampling.

    För **stratifiering nyckelkolumn för provtagning**, Välj en enda *strata kolumnen* ska användas för att dela upp datauppsättningen. Raderna i datauppsättningen delas sedan enligt följande:

    1. Alla inkommande rader är grupperade (stratified) med värdena i den angivna strata-kolumnen.

    2. Rader blandad inom varje grupp.

    3. Varje grupp läggs selektivt till datauppsättningen för utdata som uppfyller angivna förhållandet.


6. Kör experimentet.

    Med det här alternativet visar modulen en enskild datauppsättning som innehåller ett representativt urval av data. Återstående, unsampled del av datauppsättningen är inte utdata. 

## <a name="split-data-into-partitions"></a>Dela data i partitioner

Använd det här alternativet när du vill dela upp datauppsättningen i delmängder av data. Det här alternativet är också användbart när du vill skapa en anpassad antalet vikningar för korsvalidering eller dela upp rader i flera grupper.

1. Lägg till den **partitionera och ta prover** modul till ditt experiment, och Anslut datauppsättningen.

2. För **Partition eller exempel läge**väljer **tilldelas Vikningar**.

3. **Använda ersättas i partitionering**: Välj det här alternativet om du vill provade raden som ska tas bakåt i poolen med rader för eventuell återanvändning. Därför kan kan samma rad tilldelas till flera gånger.

    Om du inte använder ersättning (standardalternativet), tas inte den valda raden tillbaka i poolen med rader för eventuell återanvändning. Därmed kan kan varje rad tilldelas till endast en vikningsantalet.

4. **Slumpmässig dela**:  Välj det här alternativet om du vill att rader som ska tilldelas slumpmässigt vikningar.

    Om du inte väljer det här alternativet tilldelas rader till vikningar med metoden resursallokering.

5. **Slumpmässig dirigering**: Du kan också ange ett heltal som ska användas som seed-värdet. Det här alternativet är viktigt om du vill att raderna som ska delas upp på samma sätt varje gång. I annat fall standardvärdet 0 innebär att ett slumpmässigt startar seed ska användas.

6. **Anger metoden som partitioner**: Ange hur du vill att data ska fördelas till varje partition med hjälp av dessa alternativ:

    - **Partitionera jämnt**: Använd det här alternativet för att placera en lika med antalet rader i varje partition. Om du vill ange antalet partitioner i utdata, ange ett heltal i den **anger antalet vikningar som att dela upp jämnt i** textrutan.

    - **Partitionen med anpassade proportioner**: Använd det här alternativet för att ange storleken på varje partition som en kommaavgränsad lista.

        Till exempel om du vill skapa tre partitioner med den första partitionen som innehåller 50% av data och de återstående två partitionerna varje som innehåller 25% av data, klickar du på den **lista över proportioner avgränsade med kommatecken** textrutan och Skriv dessa siffror: `.5, .25, .25`

        Summan av alla partitionsstorlekarna måste lägga till upp till exakt 1.

        - Om du anger siffror som lägger till **mindre än 1**, en extra partition skapas för att lagra de återstående raderna. Till exempel om du anger skapas värdena.2 och.3, tredje partition som innehåller de återstående 50 procent av alla rader.

        - Om du anger siffror som lägger till **mer än 1**, ett fel inträffar när du kör experimentet.

7. **Stratified dela**: Välj det här alternativet om du vill att raderna som ska vara stratified när dela och väljer sedan den _strata kolumnen_.

8. Kör experimentet.

    Med det här alternativet visar modulen flera datauppsättningar, partitionerad med hjälp av regler som du har angett.

### <a name="use-data-from-a-predefined-partition"></a>Användningsdata från en fördefinierad partition  

Det här alternativet används när du har delat en datauppsättning i flera partitioner och nu vill läsa in varje partition i sin tur för vidare analys eller bearbetning.

1. Lägg till den **partitionera och ta prover** modulen till arbetsytan för experimentet.

2. Koppla den till utdataporten för en föregående instans av **partitionera och ta prover**. Den instansen måste ha använt den **tilldelas Vikningar** alternativet för att generera ett antal partitioner.

3. **Partitionen eller exempel läge**: Välj **Välj vikning**.

4. **Ange vilket viker och samlas in från**: Välj en partition som ska användas genom att skriva dess index. Partitionsindex är 1-baserade. Om du har delat datauppsättningen i tre delar, skulle partitionerna har index 1, 2 och 3.

    Om du anger ett ogiltigt indexvärde inträffar ett fel i designläge: "Error 0018: Datauppsättningen innehåller ogiltiga data ”.

    Förutom att gruppera datauppsättningen efter vikningar, kan du avgränsa datauppsättningen i två grupper: en mål-vika och allt annat. Att göra detta index för en enda vikning och skriv sedan markerar du alternativet **Välj uppsättning valda vikning**, för att hämta allt utom data i den angivna vikningsantalet.

5. Om du arbetar med flera partitioner måste du lägga till ytterligare instanser av den **partitionera och ta prover** modul för att hantera varje partition.

    Exempel: Anta tidigare partitionerade patienter i fyra vikningar med ålder. Om du vill arbeta med varje enskild vikning, behöver du fyra kopior av den **partitionera och ta prover** modulen, och var och en, Välj en annan vikning som visas nedan. Det är inte rätt att använda den **tilldelas Vikningar** utdata direkt.  

    [![Partitionera och ta prover](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Kör experimentet.

    Med det här alternativet visar modulen en enskild datauppsättning som innehåller endast de rader som har tilldelats den vikningsantalet.

> [!NOTE]
>  Du kan inte visa vikning benämningar direkt; de finns bara i metadata.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 