---
title: 'Neurala nätverk i multiklass: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder neurala-modulen i multiklass i Azure Machine Learning-tjänsten för att skapa en neurala-nätverks modell som kan användas för att förutsäga ett mål som har flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 44d1e7606efd5bc6d2286254dc4863728e3edbfd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128603"
---
# <a name="multiclass-neural-network-module"></a>Neurala för multiklass

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en neurala-nätverks modell som kan användas för att förutsäga ett mål som har flera värden. 

Till exempel kan neurala-nätverk av den här typen användas i komplexa uppgifter för visuellt innehåll, t. ex. siffer-och bokstavs igenkänning, dokument klassificering och mönster igenkänning.

Klassificering med neurala-nätverk är en övervakad inlärnings metod och kräver därför en *Taggad data uppsättning* som innehåller en etikett kolumn.

Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  

## <a name="about-neural-networks"></a>Om neurala-nätverk

Ett neurala-nätverk är en uppsättning sammankopplade lager. Indata är det första lagret och är anslutna till ett utmatnings lager med ett acykliska diagram som består av viktade kanter och noder.

Mellan indata-och utdata-lagren kan du infoga flera dolda lager. De flesta förutsägande uppgifter kan enkelt utföras med bara ett eller flera dolda lager. Den senaste forskningen har dock visat att djup neurala-nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter som bild-eller tal igenkänning. Efterföljande lager används för att modellera ökande nivåer av semantiskt djup.

Relationen mellan indata och utdata har lärts från att träna neurala-nätverket på indatan. Riktningen på diagrammet fortsätter från indatan via det dolda lagret och till output-lagret. Alla noder i ett lager ansluts med de viktade kanterna till noderna i nästa lager.

För att beräkna nätverkets utdata för ett visst indata beräknas ett värde vid varje nod i de dolda lagren och i utmatnings lagret. Värdet anges genom att beräkna den viktade summan av värdena för noderna från föregående skikt. En aktiverings funktion används sedan för den viktade summan.

## <a name="configure-multiclass-neural-network"></a>Konfigurera neurala nätverk i multiklass

1. Lägg till **neurala** -modulen för multiklass i ditt experiment i gränssnittet. Du kan hitta den här modulen under **Machine Learning**, **initiera**i **klassificerings** kategorin.

2. **Skapa utbildare läge**: Använd det här alternativet för att ange hur du vill att modellen ska tränas:

    - **Enskild parameter**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    

3. **Specifikation för dolda lager**: Välj vilken typ av nätverks arkitektur som ska skapas.

    - **Fullständigt anslutet ärende**: Välj det här alternativet om du vill skapa en modell med standard nätverks arkitekturen neurala. För neurala nätverks modeller i multiklass är standardvärdena följande:

        - Ett dolt lager
        - Utmatnings lagret är fullständigt anslutet till det dolda lagret.
        - Det dolda lagret är fullständigt anslutet till indataströmmen.
        - Antalet noder i Indatakällan bestäms av antalet funktioner i tränings data.
        - Antalet noder i det dolda lagret kan ställas in av användaren. Standardvärdet är 100.
        - Antalet noder i utmatnings lagret beror på antalet klasser.
  
   

5. **Antal dolda noder**: Med det här alternativet kan du anpassa antalet dolda noder i standard arkitekturen. Ange antalet dolda noder. Standardvärdet är ett dolt lager med 100 noder.

6. **Inlärnings takten**: Definiera storleken på steget som tas vid varje iteration, före korrigeringen. Ett större värde för inlärnings frekvensen kan göra att modellen konvergerar snabbare, men den kan överskotta lokala minimi.

7. **Antal inlärnings iterationer**: Ange det maximala antalet gånger som algoritmen ska bearbeta inlärnings fallen.

8. **Diametern för inledande inlärnings vikt**: Ange nodens vikt i början av inlärnings processen.

9. **Ett moment**: Ange en vikt som ska användas vid inlärning till noder från föregående iterationer.
  
11. **Blanda exempel**: Välj det här alternativet om du vill blanda fall mellan iterationer.

    Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör experimentet.

12. **Värde för slumpmässig siffra**: Ange ett värde som ska användas som start, om du vill säkerställa repeterbarhet i flera körningar av samma experiment.

14. Anslut en data uppsättning för utbildning och en [](module-reference.md)av inlärnings modulerna: 

    - Om du ställer in **skapa** utbildare till **en parameter**använder du [träna modell](train-model.md).  
  

## <a name="results"></a>Resultat

När utbildningen är klar:

- Om du vill se en översikt över modellens parametrar, tillsammans med funktions vikterna från träningen och andra parametrar för neurala-nätverket, högerklickar du på utdata från [träna modell](./train-model.md) och väljer **visualisera**.  

- Om du vill spara en ögonblicks bild av den tränade modellen högerklickar du på den tränade modellens utdata och väljer **Spara som utbildad modell**. Den här modellen uppdateras inte vid efterföljande körningar av samma experiment.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 