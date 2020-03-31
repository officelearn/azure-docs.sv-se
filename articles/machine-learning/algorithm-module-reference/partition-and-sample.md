---
title: 'Partition och exempel: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder partitions- och exempelmodulen i Azure Machine Learning för att utföra sampling på en datauppsättning eller för att skapa partitioner från datauppsättningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477518"
---
# <a name="partition-and-sample-module"></a>Partitions- och exempelmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd partitions- och exempelmodulen för att utföra sampling på en datauppsättning eller för att skapa partitioner från datauppsättningen.

Sampling är ett viktigt verktyg för maskininlärning eftersom det låter dig minska storleken på en datauppsättning samtidigt som samma värdeförhållande bibehålls. Den här modulen stöder flera relaterade uppgifter som är viktiga i maskininlärning: 

- Dela upp dina data i flera underavsnitt av samma storlek. 

  Du kan använda partitionerna för korsvalidering eller för att tilldela ärenden till slumpmässiga grupper.

- Separera data i grupper och sedan arbeta med data från en viss grupp. 

  När du slumpmässigt har tilldelat ärenden till olika grupper kan du behöva ändra de funktioner som är associerade med endast en grupp.

- Provtagning. 

  Du kan extrahera en procentandel av data, använda slumpmässiga samplingar eller välja en kolumn som ska användas för att balansera datauppsättningen och utföra stratifierad sampling på dess värden.

- Skapa en mindre datauppsättning för testning. 

  Om du har mycket data kanske du bara *n* vill använda de första n-raderna när du konfigurerar pipelinen och sedan växla till att använda den fullständiga datauppsättningen när du skapar modellen. Du kan också använda sampling för att skapa en mindre datauppsättning som kan användas under utveckling.

## <a name="configure-the-module"></a>Konfigurera modulen

Den här modulen stöder följande metoder för att dela upp dina data i partitioner eller för sampling. Välj metoden först och ange sedan ytterligare alternativ som metoden kräver.

- Head
- Samling
- Tilldela till veck
- Plocka luckan

### <a name="get-top-n-rows-from-a-dataset"></a>Hämta TOP N-rader från en datauppsättning

Använd det här läget för att bara hämta de första *n* raderna. Det här alternativet är användbart om du vill testa en pipeline på ett litet antal rader och du inte behöver informationen balanseras eller samplas på något sätt.

1. Lägg till **partitions- och exempelmodulen** i pipelinen i gränssnittet och anslut datauppsättningen.  

1. **Partitions- eller exempelläge**: Ställ in det här alternativet på **Head**.

1. **Antal rader som ska väljas**: Ange antalet rader som ska returneras.

   Antalet rader måste vara ett icke-negativt heltal. Om antalet markerade rader är större än antalet rader i datauppsättningen returneras hela datauppsättningen.

1. Skicka pipelinen.

Modulen matar ut en enda datauppsättning som bara innehåller det angivna antalet rader. Raderna läss alltid uppifrån datauppsättningen.

### <a name="create-a-sample-of-data"></a>Skapa ett exempel på data

Det här alternativet stöder enkel slumpmässig sampling eller stratifierad slumpmässig provtagning. Det är användbart om du vill skapa en mindre representativ exempeldatauppsättning för testning.

1. Lägg till **partitions- och exempelmodulen** i pipelinen och anslut datauppsättningen.

1. **Partitions- eller exempelläge**: Ställ in det här alternativet på **Sampling**.

1. **Samplingshastighet**: Ange ett värde mellan 0 och 1. Det här värdet anger procentandelen rader från källdatauppsättningen som ska inkluderas i utdatauppsättningen.

   Om du till exempel bara vill ha hälften `0.5` av den ursprungliga datauppsättningen anger du att samplingsfrekvensen ska vara 50 procent.

   Raderna för indatauppsättningen blandas och placeras selektivt i utdatauppsättningen, enligt det angivna förhållandet.

1. **Slumpmässigt utsäde för provtagning:** Ange eventuellt ett heltal som ska användas som frövärde.

   Det här alternativet är viktigt om du vill att raderna ska delas på samma sätt varje gång. Standardvärdet är **0**, vilket innebär att ett startutsäde genereras baserat på systemklockan. Det här värdet kan leda till lite olika resultat varje gång du kör pipelinen.

1. **Stratifierad delning för sampling**: Välj det här alternativet om det är viktigt att raderna i datauppsättningen delas jämnt med någon nyckelkolumn före sampling.

   För **nyckelkolumn för stratifiering för sampling**väljer du en enskild *stratakolumn* som ska användas när datauppsättningen delas upp. Raderna i datauppsättningen delas sedan upp på följande sätt:

   1. Alla indatarader grupperas (stratifieras) efter värdena i den angivna stratakolumnen.

   1. Rader blandas inom varje grupp.

   1. Varje grupp läggs selektivt till i utdatauppsättningen för att uppfylla det angivna förhållandet.


1. Skicka pipelinen.

   Med det här alternativet matar modulen ut en enda datauppsättning som innehåller ett representativt urval av data. Den återstående, oförstärkt delen av datauppsättningen matas inte ut. 

## <a name="split-data-into-partitions"></a>Dela upp data i partitioner

Använd det här alternativet när du vill dela upp datauppsättningen i delmängder av data. Det här alternativet är också användbart när du vill skapa ett anpassat antal veck för korsvalidering eller dela upp rader i flera grupper.

1. Lägg till **partitions- och exempelmodulen** i pipelinen och anslut datauppsättningen.

1. För **partitions- eller exempelläge**väljer du **Tilldela till vickningar**.

1. **Använd ersättning i partitioneringen:** Välj det här alternativet om du vill att den samplade raden ska återföras till raden för potentiell återanvändning. Därför kan samma rad tilldelas flera veck.

   Om du inte använder ersättning (standardalternativet) läggs den samplade raden inte tillbaka till raden för potentiell återanvändning. Därför kan varje rad tilldelas endast en vik.

1. **Randomiserad split:** Välj det här alternativet om du vill att rader ska tilldelas slumpmässigt till veck.

   Om du inte väljer det här alternativet tilldelas rader att vikas genom round-robin-metoden.

1. **Slumpmässigt frö:** Ange ett heltal som ska användas som frövärde. Det här alternativet är viktigt om du vill att raderna ska delas på samma sätt varje gång. Annars innebär standardvärdet **0** att ett slumpmässigt startfrö används.

1. **Ange partitionermetoden**: Ange hur du vill att data ska fördelas till varje partition med hjälp av följande alternativ:

   - **Partition jämnt:** Använd det här alternativet för att placera lika många rader i varje partition. Om du vill ange antalet utdatapartitioner anger du ett heltal i rutan **Ange antal veck som ska delas upp jämnt i.**

   - **Partition med anpassade proportioner**: Använd det här alternativet för att ange storleken på varje partition som en kommaavgränsad lista.

     Anta till exempel att du vill skapa tre partitioner. Den första partitionen innehåller 50 procent av data. De återstående två partitionerna innehåller vardera 25 procent av data. I rutan **Lista över proportioner avgränsade med kommatecken** anger du följande siffror: **0,5, 0,25, .25**.

     Summan av alla partitionsstorlekar måste lägga till upp till exakt 1.

     Om du anger tal som lägger till upp till *mindre än 1*skapas en extra partition för att hålla de återstående raderna. Om du till exempel anger värdena **0,2** och **0,3**skapas en tredje partition för att rymma de återstående 50 procenten av alla rader.
     
     Om du anger tal som lägger till upp till *fler än 1*utlöses ett fel när du kör pipelinen.

1. **Stratifierad split:** Välj det här alternativet om du vill att raderna ska stratifieras när de delas och väljer sedan _stratakolumnen_.

1. Skicka pipelinen.

   Med det här alternativet matar modulen ut flera datauppsättningar. Datauppsättningarna partitioneras enligt de regler som du har angett.

### <a name="use-data-from-a-predefined-partition"></a>Använda data från en fördefinierad partition  

Använd det här alternativet när du har delat upp en datauppsättning i flera partitioner och nu vill läsa in varje partition i sin tur för ytterligare analys eller bearbetning.

1. Lägg till **partitions- och exempelmodulen** i pipelinen.

1. Anslut modulen till utdata från en tidigare instans av **Partition och Exempel**. Den instansen måste ha använt alternativet **Tilldela till vicks** för att generera ett visst antal partitioner.

1. **Partitions- eller exempelläge**: Välj **Välj plockvikning**.

1. **Ange vilken vikning som ska samplas från**: Välj en partition som ska användas genom att ange dess index. Partitionsindex är 1-baserade. Om du till exempel delade upp datauppsättningen i tre delar skulle partitionerna ha indexen 1, 2 och 3.

   Om du anger ett ogiltigt indexvärde utlöses ett designtidsfel: "Fel 0018: Datauppsättningen innehåller ogiltiga data."

   Förutom att gruppera datauppsättningen efter veck kan du separera datauppsättningen i två grupper: en målvikning och allt annat. För att göra detta anger du indexet för en enda vikning och väljer sedan alternativet **Välj komplement till den valda luckan** för att få allt utom data i den angivna vecket.

1. Om du arbetar med flera partitioner måste du lägga till fler instanser av **partitions- och exempelmodulen** för att hantera varje partition.

   Partitions- **och exempelmodulen** på den andra raden är till exempel inställd **på Tilldela till vicks**och modulen i den tredje raden är inställd på **Plocka vikning**.   

   ![Partition och exempel](./media/module/partition-and-sample.png)

1. Skicka pipelinen.

   Med det här alternativet matar modulen ut en enda datauppsättning som bara innehåller de rader som tilldelats den vikningen.

> [!NOTE]
>  Du kan inte visa vikbeteckningarna direkt. De finns bara i metadata.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 