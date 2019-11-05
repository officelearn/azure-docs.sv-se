---
title: Finjustera modellens egenskaper
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen finjustera Modellets standardparametrar i Azure Machine Learning-tjänsten för att utföra en parameter rensning på en modell för att fastställa de optimala parameter inställningarna.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515660"
---
# <a name="tune-model-hyperparameters"></a>Finjustera modellens egenskaper

Den här artikeln beskriver hur du använder modulen [finjustera modells](tune-model-hyperparameters.md) för hands parametrar i Azure Machine Learning designer (för hands version) för att fastställa de optimala egenskaperna för en specifik maskin inlärnings modell. Modulen bygger och testar flera modeller, med olika kombinationer av inställningar och jämför mått över alla modeller för att få en kombination av inställningar. 

Villkors *parametern* och den andra *parametern* kan vara förvirrande. Modellens *parametrar* är det du anger i rutan Egenskaper. I princip utför den här modulen en *parameter rensning* över de angivna parameter inställningarna och lär sig en optimal uppsättning med _grundparametrar_som kan vara olika för varje specifik besluts träd, data uppsättning eller Regressions metod. Processen för att hitta den optimala konfigurationen kallas ibland *justering*. 

Modulen stöder följande metod för att hitta de optimala inställningarna för en modell 

**Integrerad utbildning och justering**: du konfigurerar en uppsättning parametrar som ska användas och sedan itererar modulen över flera kombinationer, vilket mäter noggrannhet tills den hittar en "bästa" modell. Med de flesta lärare-moduler kan du välja vilka parametrar som ska ändras under inlärnings processen och vilka som ska förbli fasta.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Den här metoden skapar en utbildad modell som du kan spara för åter användning.  

### <a name="related-tasks"></a>Relaterade uppgifter

+ Innan du justerar ska du använda val av funktioner för att fastställa vilka kolumner eller variabler som har det högsta värdet information.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Så här konfigurerar du justerings modellens egenskaper  

I allmänhet är det optimalt att lära sig de optimala egenskaperna för en specifik maskin inlärnings modell som kräver stora pipelineation.

### <a name="train-a-model-using-a-parameter-sweep"></a>Träna en modell med en parameter svepning  

I det här avsnittet beskrivs hur du utför en grundläggande parameter rensning, som tågen en modell med hjälp av modulen för att [finjustera modellens standardparametrar](tune-model-hyperparameters.md) .

1.  Lägg till modulen för att [finjustera modellens standardparametrar](tune-model-hyperparameters.md) till din pipeline i designern.

2.  Anslut en modell som inte är tränad till intill vänster. 

3. Ställ in alternativet **skapa utbildare** för **parameter intervall** och Använd **intervall Builder** för att ange ett värde intervall som ska användas i parametern svep.  

    Nästan alla moduler för klassificering och regression stöder en integrerad parameter rensning. För de som inte har stöd för att konfigurera ett parameter intervall kan bara de tillgängliga parametervärdena användas.

    Du kan ange värdet manuellt för en eller flera parametrar och sedan svepa över de återstående parametrarna. Det kan spara en stund.

4.  Lägg till den data uppsättning som du vill använda för utbildning och Anslut den till den mittersta indatan för [justerings modellens egenskaper](tune-model-hyperparameters.md).  

    Om du har en taggad data uppsättning kan du ansluta den till porten längst till höger (**valfri verifierings data uppsättning**). På så sätt kan du mäta noggrannhet medan du tränar och justerar.

5.  Välj ett värde för **parameter rensnings läge**i fönstret Egenskaper för att [Justera modellens](tune-model-hyperparameters.md) **Egenskaper** . Det här alternativet styr hur parametrarna väljs.

    - **Hela rutnätet**: när du väljer det här alternativet upprepas modulen över ett rutnät som är fördefinierat i systemet, för att testa olika kombinationer och identifiera den bästa bättre eleven. Det här alternativet är användbart i fall där du inte vet vad de bästa parameter inställningarna kan vara och vill testa alla möjliga kombinationer av värden.

    - **Slumpmässig svep**: när du väljer det här alternativet kommer modulen slumpmässigt att välja parameter värden över ett Systemdefinierat intervall. Du måste ange det maximala antalet körningar som du vill att modulen ska köra. Det här alternativet är användbart för de fall där du vill öka modell prestandan med hjälp av de mått som du väljer, men ändå spara dator resurser.

    Om du väljer en slumpmässig svep kan du ange det **maximala antalet körningar på slumpmässig svep**, vilket innebär hur många gånger modellen ska tränas med hjälp av en slumpmässig kombination av parameter värden.

6.  För **kolumnen etikett**startar du kolumn väljaren för att välja en enskild etikett kolumn.

7.  **Maximalt antal körningar vid slumpmässig svep**: om du väljer en slumpmässig svep kan du ange hur många gånger modellen ska tränas med hjälp av en slumpmässig kombination av parameter värden.

    **Maximalt antal körningar i slumpmässiga rutnät**: det här alternativet styr också antalet iterationer över en slumpmässig sampling av parameter värden, men värdena genereras inte slumpmässigt från det angivna intervallet. i stället skapas en matris av alla möjliga kombinationer av parameter värden och en slumpmässig sampling tas över matrisen. Den här metoden är mer effektiv och mindre känslig för regional översampling eller undertagning.

8.  Välj ett mått som ska användas när du **rankar** modeller.

    När du kör en parameter rensning beräknas alla tillämpliga mått för modell typen och returneras i rapporten **svep resultat** . Separata mått används för Regressions-och klassificerings modeller.

    Måttet du väljer avgör dock hur modellerna rangordnas. Endast den översta modellen, som rangordnas av det valda måttet, är utdata som en utbildad modell som kan användas för poängsättning.

9.  För **slumpmässigt utsäde**anger du ett tal som ska användas vid initiering av parameter svep. 

10. Köra en pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Resultat av inställning av min parameter

När inlärningen är klar:

+ Om du vill visa en uppsättning noggrannhets mått för den bästa modellen högerklickar du på modulen, väljer **svep resultat**och väljer sedan **visualisera**.

    Alla noggrannhets mått som är tillämpliga för modell typen är utdata, men måttet som du har valt för rangordning avgör vilken modell som betraktas som "bästa".

+ Om du vill använda modellen för poängsättning i andra pipeliner, utan att behöva upprepa justerings processen, högerklickar du på modellens utdata och väljer **Spara som utbildad modell**. 


## <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

### <a name="how-a-parameter-sweep-works"></a>Så här fungerar en parameter Svep

I det här avsnittet beskrivs hur parameter rensning fungerar i allmänhet och hur alternativen i den här modulen interagerar.

När du ställer in en parameter rensning definierar du omfånget för din sökning, om du vill använda antingen ett begränsat antal parametrar som marker ATS slumpmässigt eller en uttömmande sökning över ett parameter utrymme som du definierar.

+ **Slumpmässig svep**: det här alternativet tågen en modell som använder ett angivet antal iterationer. 

     Du anger ett intervall med värden att iterera över och modulen använder en slumpmässigt vald delmängd av dessa värden.  Värdena väljs med ersättning, vilket innebär att de siffror som tidigare valdes slumpmässigt inte tas bort från poolen med tillgängliga nummer. Det innebär att risken för alla värden som väljs förblir samma för alla pass.  

+ **Hela rutnätet**: alternativet för att använda hela rutnätet innebär bara att: var och en av kombinationerna testas. Det här alternativet kan ses som mest grundligt, men det kräver mest tid. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Kontrol lera längden och komplexiteten för utbildningen

Det kan vara tids krävande att iterera över många kombinationer av inställningar, så att modulen ger flera olika sätt att begränsa processen:

+ Begränsa antalet iterationer som används för att testa en modell
+ Begränsa parameter utrymmet
+ Begränsa både antalet iterationer och parameter utrymmet

Vi rekommenderar att du förloppet med inställningarna för att fastställa den mest effektiva metoden för utbildning på en viss data uppsättning och modell.

### <a name="choosing-an-evaluation-metric"></a>Välja ett utvärderings mått

En rapport som innehåller noggrannheten för varje modell visas i slutet så att du kan granska mått resultatet. En enhetlig uppsättning mått används för alla binära klassificerings modeller, precision används för alla klassificerings modeller i flera klasser och en annan uppsättning mått används för Regressions modeller. Under utbildningen måste du dock välja ett **enda** mått som ska användas för att rangordna de modeller som genereras under justerings processen. Du kanske upptäcker att det bästa måttet varierar, beroende på ditt företags problem och kostnaden för falska positiva identifieringar och falskt negativ.

#### <a name="metrics-used-for-binary-classification"></a>Mått som används för binär klassificering

-   **Noggrannhet** Proportionen av sanna resultat till totalt antal fall.  

-   **Precision** Proportionen av sant resultat till positiva resultat.  

-   **Återkalla** Bråk delen av alla korrekta resultat över alla resultat.  

-   **F-Poäng** Ett mått som balanserar precision och återkallande.  

-   **AUC** Ett värde som representerar ytan under kurvan när falska positiva identifieringar ritas på x-axeln och sanna positiva resultat ritas på y-axeln.  

-   **Genomsnittlig förlust av loggar** Skillnaden mellan två sannolikhets fördelningar: den sanna och den som finns i modellen.  

#### <a name="metrics-used-for-regression"></a>Mått som används för regression

-   Medelvärde för medelvärdet av **absoluta fel** beräknar alla fel i modellen, där fel innebär avståndet från det förväntade värdet från det sanna värdet. Ofta förkortat som **Mae**.  

-   **Roten av medelvärdet i ett kvadratvärde** mäter medelvärdet av fel rutornas kvadrat och använder sedan roten för det värdet. Ofta förkortat som **rmse**  

-   Ett **relativt absolut fel** representerar felet som en procent andel av det sanna värdet.  

-   **Ett relativt kvadratvärde** normaliserar det totala antalet fel i en fyrkant, dividerat med det totala antalet fel i det förväntade värdet.  

-   **Effektiv bestämning** Ett enda tal som anger hur bra data passar en modell. Värdet ett innebär att modellen exakt matchar data. värdet noll innebär att data är slumpmässiga eller på annat sätt inte får plats i modellen. Kallas ofta **r<sup>2</sup>** , **r<sup>2</sup>** eller **r-kvadratvärdet**.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Moduler som inte stöder en parameter-Svep

Nästan alla lär i Azure Machine Learning stöd för kors validering med en integrerad parameter rensning, vilket gör att du kan välja vilka parametrar som ska användas för pipeline. Om eleven inte har stöd för att ange ett värde intervall kan du fortfarande använda det vid kors validering. I det här fallet väljs en rad tillåtna värden för svepet. 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 

