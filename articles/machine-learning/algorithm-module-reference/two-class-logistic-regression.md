---
title: 'Logistisk regression i två klasser: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Med tvåklassers logistisk regression i Azure Machine Learning för att skapa en logistisk regressionsmodell som kan användas för att förutsäga två (och endast två) resultat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455833"
---
# <a name="two-class-logistic-regression-module"></a>Modul för logistisk regressionsmodul i två klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en logistisk regressionsmodell som kan användas för att förutsäga två (och endast två) resultat. 

Logistisk regression är en välkänd statistisk teknik som används för modellering av många typer av problem. Denna algoritm är en *övervakad inlärningsmetod;*  Därför måste du tillhandahålla en datauppsättning som redan innehåller resultaten för att träna modellen.  

### <a name="about-logistic-regression"></a>Om logistisk regression  

Logistisk regression är en välkänd metod i statistik som används för att förutsäga sannolikheten för ett resultat, och är särskilt populär för klassificeringsuppgifter. Algoritmen förutsäger sannolikheten för förekomst av en händelse genom att anpassa data till en logistisk funktion.
  
I den här modulen är klassificeringsalgoritmen optimerad för dikotoma eller binära variabler. Om du behöver klassificera flera resultat använder du modulen [Multiclass Logistic Regression.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Konfigurerar du  

Om du vill träna den här modellen måste du ange en datauppsättning som innehåller en etikett eller klasskolumn. Eftersom den här modulen är avsedd för problem med två klasser måste etikett- eller klasskolumnen innehålla exakt två värden. 

Etikettkolumnen kan till exempel vara [Framröstad] med möjliga värden "Ja" eller "Nej". Eller så kan det vara [Kreditrisk], med möjliga värden "Hög" eller "Låg". 
  
1.  Lägg till modulen **För tvåklassers logistisk regression** i pipelinen.  
  
2.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument.  

    -   **Parameterintervall:** Om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Du anger ett visst värdeintervall och tränaren itererar över flera kombinationer av inställningarna för att bestämma vilken kombination av värden som ger bäst resultat.
  
3.  För **optimeringstolerans**anger du ett tröskelvärde som ska användas när modellen optimeras. Om förbättringen mellan iterationer sjunker under det angivna tröskelvärdet anses algoritmen ha konvergerat på en lösning och utbildning stoppas.  
  
4.  För **L1-regulariseringsvikt** och **L2-regulariseringsvikt**skriver du ett värde som ska användas för legaliseringsparametrarna L1 och L2. Ett värde som inte är noll rekommenderas för båda.  
     *Regularization* är en metod för att förhindra övermontering genom att straffa modeller med extrema koefficientvärden. Legalisering fungerar genom att lägga till den påföljd som är associerad med koefficientvärden till felet i hypotesen. Således skulle en korrekt modell med extrema koefficientvärden straffas mer, men en mindre exakt modell med mer konservativa värden skulle straffas mindre.  
  
     L1 och L2 regularization har olika effekter och användningsområden.  
  
    -   L1 kan tillämpas på glesa modeller, vilket är användbart när man arbetar med högdimensionella data.  
  
    -   Däremot är L2-legalisering att föredra för data som inte är glesa.  
  
     Den här algoritmen stöder en linjär kombination av L1- <code>x = L1</code> <code>y = L2</code>och <code>ax + by = c</code> L2-legaliseringsvärden: det vill än, om och , definierar sedan det linjära intervallet för regulariseringstermerna.  
  
    > [!NOTE]
    >  Vill du veta mer om L1 och L2 regularization? Följande artikel innehåller en diskussion om hur L1- och L2-legalisering skiljer sig åt och hur de påverkar modellmontering, med kodexempel för logistisk regression och neurala nätverksmodeller: [L1 och L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Olika linjära kombinationer av L1- och L2-termer har utformats för logistiska regressionsmodeller: till exempel [elastisk netto regularisering](https://wikipedia.org/wiki/Elastic_net_regularization). Vi föreslår att du refererar till dessa kombinationer för att definiera en linjär kombination som är effektiv i din modell.
      
5.  För **minnesstorlek för L-BFGS**anger du hur mycket minne som ska användas för *L-BFGS-optimering.*  
  
     L-BFGS står för "begränsat minne Broyden-Fletcher-Goldfarb-Shanno". Det är en optimeringsalgoritm som är populär för parameteruppskattning. Den här parametern anger antalet tidigare positioner och övertoningar som ska lagras för beräkningen av nästa steg.  
  
     Den här optimeringsparametern begränsar mängden minne som används för att beräkna nästa steg och riktning. När du anger mindre minne är träningen snabbare men mindre exakt.  
  
6.  För **slumptalsutsäde**skriver du ett heltalsvärde. Det är viktigt att definiera ett frövärde om du vill att resultaten ska reproduceras över flera körningar av samma pipeline.  
  
  
8. Lägg till en taggad datauppsättning i pipelinen och anslut en av [utbildningsmodulerna](module-reference.md).  
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](./train-model.md)  
  
9. Skicka pipelinen.  
  
## <a name="results"></a>Resultat

Efter träningen är klar:
 
  
+ Om du vill göra förutsägelser om nya data använder du den tränade modellen och nya data som indata till modulen [Poängmodell.](./score-model.md) 


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 