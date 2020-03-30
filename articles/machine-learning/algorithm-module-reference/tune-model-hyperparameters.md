---
title: Finjustera hyperparametrar för modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Tune Model Hyperparameters i Azure Machine Learning för att utföra ett parametersvep på en modell för att avgöra de optimala parameterinställningarna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064165"
---
# <a name="tune-model-hyperparameters"></a>Finjustera hyperparametrar för modell

I den här artikeln beskrivs hur du använder modulen Tune Model Hyperparameters i Azure Machine Learning designer (förhandsversion). Målet är att bestämma de optimala hyperparametrarna för en maskininlärningsmodell. Modulen bygger och testar flera modeller med hjälp av olika kombinationer av inställningar. Den jämför mått över alla modeller för att få kombinationer av inställningar. 

*Termparametern* och *hyperparametern* kan vara förvirrande. Modellens *parametrar* är vad du anger i modulens högra fönsterruta. I grund och botten utför den här modulen en *parameter svepning* över de angivna parameterinställningarna. Den lär sig en optimal uppsättning _hyperparametrar_, som kan vara olika för varje specifikt beslutsträd, datauppsättning eller regressionsmetod. Processen att hitta den optimala konfigurationen kallas ibland *tuning*. 

Modulen stöder följande metod för att hitta de optimala inställningarna för en modell: *integrerat tåg och melodi.* I den här metoden konfigurerar du en uppsättning parametrar som ska användas. Du låter sedan modulen iterera över flera kombinationer. Modulen mäter noggrannheten tills den hittar en "bästa" modell. Med de flesta elevmoduler kan du välja vilka parametrar som ska ändras under utbildningsprocessen och vilka som ska vara fasta.

Beroende på hur länge du vill att justeringsprocessen ska köras kan du bestämma dig för att testa alla kombinationer på ett uttömmande sätt. Du kan också förkorta processen genom att upprätta ett rutnät med parameterkombinationer och testa en randomiserad delmängd av parameterrutnätet.

Den här metoden genererar en tränad modell som du kan spara för återanvändning.  

> [!TIP] 
> Du kan utföra en relaterad uppgift. Innan du börjar justera använder du funktionsval för att bestämma vilka kolumner eller variabler som har det högsta informationsvärdet.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Konfigurera Tune Model Hyperparameters  

Att lära sig de optimala hyperparametrarna för en maskininlärningsmodell kräver stor användning av rörledningar.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Träna en modell med hjälp av ett parametervep  

I det här avsnittet beskrivs hur du utför ett grundläggande parametervep, som tränar en modell med hjälp av modulen Tune Model Hyperparameters.

1.  Lägg till modulen Tune Model Hyperparameters i pipelinen i designern.

2.  Anslut en otränad modell till den vänstra ingången. 



4.  Lägg till den datauppsättning som du vill använda för utbildning och anslut den till den mellersta inmatningen av Tune Model Hyperparametrar.  

    Om du har en taggad datauppsättning kan du ansluta den till den högra indataporten (**Valfri valideringsdatauppsättning**). På så sätt kan du mäta noggrannheten under träning och justering.

5.  På den högra panelen i Tune Model Hyperparametrar väljer du ett värde för **parametersvepningsläge**. Det här alternativet styr hur parametrarna är markerade.

    - **Hela rutnätet**: När du väljer det här alternativet loopar modulen över ett rutnät som är fördefinierat av systemet, för att prova olika kombinationer och identifiera den bästa eleven. Det här alternativet är användbart när du inte vet vad de bästa parameterinställningarna kan vara och vill prova alla möjliga kombinationer av värden.

    - **Slumpmässigt svep:** När du väljer det här alternativet väljer modulen slumpmässigt parametervärden över ett systemdefinierat område. Du måste ange det maximala antalet körningar som du vill att modulen ska köra. Det här alternativet är användbart när du vill öka modellens prestanda genom att använda de mått som du väljer men ändå spara datorresurser.    

6.  För **kolumnen Etikett**öppnar du kolumnväljaren för att välja en kolumn med en etikett.

7.  Välj antal körningar:

    1. **Maximalt antal körningar på slumpmässigt svep:** Om du väljer ett slumpmässigt svep kan du ange hur många gånger modellen ska tränas genom att använda en slumpmässig kombination av parametervärden.

8.  För **Rankning**väljer du ett enda mått som ska användas för rangordning av modellerna.

    När du kör ett parametervep beräknar modulen alla tillämpliga mått för modelltypen och returnerar dem i rapporten **Rensa resultat.** Modulen använder separata mått för regressions- och klassificeringsmodeller.

    Det mått som du väljer avgör dock hur modellerna rangordnas. Endast den översta modellen, som rangordnas av det valda måttet, är utdata som en tränad modell som ska användas för bedömning.

9.  För **Slumputsäde**anger du ett tal som ska användas för att starta parametervepningen. 

10. Skicka pipelinen.

## <a name="results-of-hyperparameter-tuning"></a>Resultat av hyperparameterjustering

När utbildningen är klar:

+ Om du vill visa en uppsättning noggrannhetsmått för den bästa modellen högerklickar du på modulen och väljer sedan **Visualisera**.

    Utdata innehåller alla noggrannhetsmått som gäller för modelltypen, men måttet som du valde för rangordning avgör vilken modell som anses vara "bäst".

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.


## <a name="technical-notes"></a>Tekniska anmärkningar

Det här avsnittet innehåller information om implementering och tips.

### <a name="how-a-parameter-sweep-works"></a>Så här fungerar ett parametersvep

När du ställer in ett parametervep definierar du sökningens omfattning. Sökningen kan använda ett begränsat antal parametrar som valts slumpmässigt. Eller så kan det vara en uttömmande sökning över ett parameterutrymme som du definierar.

+ **Slumpmässigt svep:** Det här alternativet tränar en modell med hjälp av ett fast antal iterationer. 

  Du anger ett värdeintervall som du vill iterera över och modulen använder en slumpmässigt vald delmängd av dessa värden. Värden väljs med ersättning, vilket innebär att tal som tidigare valts slumpmässigt inte tas bort från poolen med tillgängliga nummer. Så chansen att något värde väljs förblir densamma över alla pass.  

+ **Hela rutnätet**: Alternativet att använda hela rutnätet innebär att varje kombination testas. Det här alternativet är det mest grundliga, men det tar mest tid. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Kontrollera utbildningens längd och komplexitet

Det kan vara tidskrävande att iterera över många kombinationer av inställningar, så modulen innehåller flera sätt att begränsa processen:

+ Begränsa antalet iterationer som används för att testa en modell.
+ Begränsa parameterutrymmet.
+ Begränsa både antalet iterationer och parameterutrymmet.

Vi rekommenderar att du pipeline med inställningarna för att bestämma den mest effektiva metoden för utbildning på en viss datauppsättning och modell.

### <a name="choosing-an-evaluation-metric"></a>Välja ett utvärderingsmått

I slutet av testningen presenterar modellen en rapport som innehåller noggrannheten för varje modell så att du kan granska mätresultaten:

- En enhetlig uppsättning mått används för alla binära klassificeringsmodeller.
- Noggrannhet används för alla klassificeringsmodeller i flera klasser.
- En annan uppsättning mått används för regressionsmodeller. 

Under träningen måste du dock välja ett *enda* mått som ska användas i rangordningen av de modeller som genereras under justeringsprocessen. Du kanske upptäcker att det bästa måttet varierar beroende på ditt affärsproblem och kostnaden för falska positiva och falska negativ.

#### <a name="metrics-used-for-binary-classification"></a>Mått som används för binär klassificering

-   **Noggrannhet** är andelen verkliga resultat till totala fall.  

-   **Precision** är andelen verkliga resultat till positiva resultat.  

-   **Recall** är fraktionen av alla korrekta resultat över alla resultat.  

-   **F-poäng** är ett mått som balanserar precision och återkallande.  

-   **AUC** är ett värde som representerar området under kurvan när falska positiva identifieringar ritas på x-axeln och sanna positiva ritas på y-axeln.  

-   **Genomsnittlig loggförlust** är skillnaden mellan två sannolikhetsfördelningar: den sanna och den i modellen.  

#### <a name="metrics-used-for-regression"></a>Mått som används för regression

-   **Genomsnittligt absolut fel** genomsnitt alla fel i modellen, där *fel* innebär avståndet för det förväntade värdet från det sanna värdet. Det är ofta förkortat *MAE*.  

-   **Roten till medelvärdet för kvadratfel** mäter medelvärdet av felens rutor och tar sedan roten till det värdet. Det är ofta förkortat *RMSE*.  

-   **Relativt absolut fel** representerar felet som en procentandel av det verkliga värdet.  

-   **Relativt fyrkantigt fel** normaliserar det totala kvadratfelet genom att dividera med det totala kvadratfelet för de förväntade värdena.  

-   **Bestämningskoefficienten** är ett enda tal som anger hur väl data passar en modell. Ett värde på en innebär att modellen exakt matchar data. Värdet noll innebär att data är slumpmässiga eller på annat sätt inte kan passa modellen. Det kallas ofta *r<sup>2,</sup>* *R<sup>2</sup>*, eller *r-kvadrat*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduler som inte stöder ett parametersvep

Nästan alla elever i Azure Machine Learning stöder korsvalidering med ett integrerat parametervep, vilket gör att du kan välja parametrarna att pipeline med. Om eleven inte stöder att ange ett värdeintervall kan du fortfarande använda det i korsvalidering. I det här fallet väljs ett intervall med tillåtna värden för svepet. 


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

