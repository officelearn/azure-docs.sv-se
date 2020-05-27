---
title: Konvertera ord till vektor
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder tre tillhandahållna Word2Vec-modeller för att extrahera en vokabulär och dess motsvarande Word-inbäddningar från en sökkorpus text.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853773"
---
# <a name="convert-word-to-vector"></a>Konvertera ord till vektor

Den här artikeln beskriver hur du använder modulen **Konvertera Word till Vector** i Azure Machine Learning designer (för hands version) för att tillämpa olika Word2Vec-modeller (Word2Vec, Fasttext, assisterad förtränad modell) på sökkorpus för den text som du har angett som indatamängd och generera en ord lista med ord inbäddningar.

I den här modulen används Gensim-biblioteket. Mer information om Gensim finns i den [officiella webbplatsen](https://radimrehurek.com/gensim/apiref.html) som innehåller självstudier och förklaringar av algoritmer.

### <a name="more-about-convert-word-to-vector"></a>Mer om att konvertera ord till vektor

I allmänhet är det en bearbetnings process för naturligt språk, som använder språk modeller eller tekniker för att mappa ord till vektor utrymme, det vill säga varje ord med en Vector med reella tal, och samtidigt, så att ord med liknande innebörd har liknande representationer.

Word-inbäddningar kan användas som inledande inmatade uppgifter för NLP efterföljande uppgifter som text klassificering, sentiment analys osv.

I den här modulen implementerade vi tre vanliga metoder, inklusive två utbildnings modeller, Word2Vec och FastText, och en förtränad modell, assisterad-wiki-gigaword-100. Online-tränings modeller är utbildade för indata, medan förtränade modeller är intränade i en större text sökkorpus, (till exempel Wikipedia, Google News) innehåller vanligt vis cirka 100 000 000 000 ord, och Word-inbäddning förblir konstant vid Word-vectorization. Förtränade Word-modeller ger fördelar som minskad inlärnings tid, bättre Word-vektorer kodade och förbättrade övergripande prestanda.

+ Word2Vec är en av de mest populära teknikerna för att lära sig Word-inbäddningar med hjälp av ett ytligt neurala nätverk, teori beskrivs i det här dokumentet, som är tillgängligt som en PDF-nedladdning: [effektiv uppskattning av ord representationer i Vector Space, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). Implementeringen i den här modulen baseras på [gensim-biblioteket för Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ FastText teori förklaras i det här dokumentet, som är tillgängligt som en PDF-nedladdning: att [utöka ord vektorer med subword-information, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). Implementeringen i den här modulen baseras på [gensim-biblioteket för fasttext](https://radimrehurek.com/gensim/models/fasttext.html).

+ Assisterad förtränad modell: assisterad-wiki-gigaword-100, är en samling förtränade vektorer baserade på Wikipedia-text sökkorpus, som innehåller 5,6 B-token och 400K, utan specialfall, PDF är tillgängligt: [assisterad: global Vectors för Word-representation](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Så här konfigurerar du konvertera ord till Vector

Den här modulen kräver en data uppsättning som innehåller en kolumn med text, Förbearbetad text är bättre.

1. Lägg till kommandot **konvertera ord till Vector** i din pipeline.

2. Som indata för modulen anger du en data uppsättning som innehåller en eller flera text kolumner.

3. För **mål kolumn**väljer du bara en kolumn som innehåller text att bearbeta.

    I allmänhet, eftersom den här modulen skapar en vokabulär från text, är innehållet i olika kolumner olika, vilket leder till olika vokabulär innehåll, och därför accepterar modulen bara en mål kolumn.

4. För **Word2Vec-strategin**väljer du mellan `GloVe pretrained English Model` , `Gensim Word2Vec` och `Gensim FastText` .

5. Om **Word2Vec-strategin** är `Gensim Word2Vec` eller `Gensim FastText` :

    + **Word2Vec**. Välj från `Skip_gram` och `CBOW` . Skillnaden presenteras i det ursprungliga [dokumentet](https://arxiv.org/pdf/1301.3781.pdf).

        Standard metoden är `Skip_gram` .

    + **Längden på ord inbäddning**. Ange dimensionalitet för ordet Vectors. Motsvarar `size` parametern i gensim.

        Standard embedding_size är 100.

    + **Kontext fönster storlek**. Ange det maximala avståndet mellan ordet som förutsägs och det aktuella ordet. Motsvarar `window` parametern i gensim.

        Standard fönster storleken är 5.

    + **Antal epoker**. Ange antalet epoker (iterationer) över sökkorpus. Motsvarar `iter` parametern i gensim.

        Standardvärdet för epoker är 5.

6. För **maximal vokabulär storlek**anger du det maximala antalet ord i den genererade ord listan.

    Om det finns fler unika ord än så kan du rensa de ovanliga dem.

    Standard storleken för ord listan är 10000.

7. För **minsta antal ord**anger du ett minsta antal ord, vilket gör att modulen ignorerar alla ord som har en lägre frekvens än det värdet.

    Standardvärdet är 5.

8. Skicka pipelinen.

## <a name="examples"></a>Exempel

Modulen har ett resultat:

+ **Vokabulär med inbäddningar**: innehåller den genererade ord listan, tillsammans med varje Word-inbäddning, en dimension upptar en kolumn.

### <a name="result-examples"></a>Resultat exempel

I följande exempel används den här modulen med standardinställningarna för den förbearbetade Wikipedia SP 500-datauppsättningen som anges i Azure Machine Learning (för hands version) för att illustrera hur **konverteringen av Word till Vector** fungerar.

#### <a name="source-dataset"></a>Källdatauppsättning

Data uppsättningen innehåller en kategori kolumn och den fullständiga texten som hämtats från wikipedia. Den här tabellen visar bara några exempel på några representativa exempel.

|text|
|----------|
|Nasdaq 100 Component s p 500 komponent Foundation grundare location ort Apple Campus 1 oändlig loop, gata oändlig loop Cupertino California Cupertino California ort land, USA...|
|br Nasdaq 100 Nasdaq 100 Component br s p 500 s p 500 komponent bransch Computer Software Foundation br grundare Charles Geschke br John Warnock location Adobe Systems...|
|s p 500 s p 500 komponent bransch bil bransch autofordons-föregångare allmänt motorer företag 1908 2009 efterföljande...|
|s p 500 s p 500-komponent bransch konglomerat företag konglomerat Foundation grundare plats stad Fairfield Connecticut Fairfield Connecticut location Country USA...|
|br s p 500 s p 500 komponent Foundation 1903 grundare William s Harley br Arthur Davidson Harley Davidson grundare Arthur Davidson br Walter Davidson br William en Davidson plats...|

#### <a name="output-vocabulary-with-embeddings"></a>Utmatnings ord lista med inbäddningar

Följande tabell innehåller utdata från den här modulen med Wikipedia SP 500 dataset som indata. Kolumnen längst till vänster visar vokabulären, dess inbäddnings vektor representeras av värden för återstående kolumner på samma rad.

|Vokabulär|Bädda in dim 0|Bädda in dim 1|Bädda in dim 2|Bädda in dim 3|Bädda in dim 4|Bäddar in dim 5|...|Bäddar in dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|– 0,375865|0,609234|0,812797|– 0,002236|0,319071|– 0,591986|...|0,364276
|-komponent|0,081302|0,40001|0,121803|0,108181|0,043651|– 0,091452|...|0,636587
|s|– 0,34355|– 0,037092|– 0,012167|0,151542|0,601019|0,084501|...|0,149419
|P|– 0,133407|0,073244|0,170396|0,326706|0,213463|– 0,700355|...|0,530901
Foundation|– 0,166819|0,10883|– 0,07933|– 0,073753|0,262137|0,045725|...|0,27487
grundare|– 0,297408|0,493067|0,316709|– 0,031651|0,455416|– 0,284208|...|0,22798
location|– 0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|– 0,460828|0,505516|– 0,074294|– 0,00639|0,116545|0,494368|...|– 0,2403
Apples|0,05779|0,672657|0,597267|– 0,898889|0,099901|0,11833|...|0,4636
kontor|– 0,281835|0,29312|0,106966|– 0,031385|0,100777|– 0,061452|...|0,05978
oändliga|– 0,263074|0,245753|0,07058|– 0,164666|0,162857|– 0,027345|...|– 0,0525
Peter|– 0,391421|0,52366|0,141503|– 0,105423|0,084503|– 0,018424|...|– 0,0521

I det här exemplet använde vi standardvärdet `Gensim Word2Vec` som **Word2Vec-strategin**, **utbildningen** är `Skip-gram` , **längden på ord inbäddningen** är 100 och vi har därför 100 inbäddning av kolumner.

## <a name="technical-notes"></a>Tekniska anteckningar

I det här avsnittet finns tips och svar på vanliga frågor.

+ Skillnad mellan online-träna och förtränad modell

    I detta **konverterings ord till Vector-modul**tillhandahöll vi tre olika strategier, två utbildnings modeller online och en förtränad modell. Online-tränings modell använder din indata-datauppsättning som tränings data, genererar vokabulär och Word-vektorer under utbildningen, medan förtränad modell redan har tränats av mycket större text sökkorpus, till exempel Wikipedia eller Twitter-text, och därför är förtränad modell i själva verket en samling (ord, inbäddning).  

    Om assisterad förtränad modell väljs som Word vectorization-strategi, sammanfattar den en vokabulär från indata-datauppsättningen och genererar inbäddnings funktionen för varje ord från den förtränade modellen, utan onlineutbildning, och användningen av förtränad modell kan spara inlärnings tiden och har bättre prestanda, särskilt när data uppsättningens storlek är relativt liten.

+ Inbäddnings storlek

    I allmänhet anges längden på ord inbäddning till några hundra (till exempel 100, 200, 300) för att uppnå bästa prestanda, eftersom små inbäddnings storlekar innebär små vektor utrymmen, vilket kan orsaka att ord bäddas in i Word.  

    För förtränade modeller är längd på ord inbäddningar fasta, i den här implementeringen är inbäddnings storleken på assisterad-wiki-gigaword-100 100.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

En lista med fel som är speciella för modulerna designer (för hands version) finns i [Machine Learning fel koder](designer-error-codes.md).
