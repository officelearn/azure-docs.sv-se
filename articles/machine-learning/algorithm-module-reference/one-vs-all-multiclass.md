---
title: En-mot-alla – multiklass
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen En-mot-alla – multiklass i Azure Machine Learning-tjänsten för att skapa en klassificerings modell med multiklasser från en ensemble av binära klassificerings modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518169"
---
# <a name="one-vs-all-multiclass"></a>En-mot-alla – multiklass

I den här artikeln beskrivs hur du använder den **en-mot-alla – multiklass** modulen Azure Machine Learning designer (för hands version) för att skapa en klassificerings modell som kan förutsäga flera klasser med hjälp av metoden "en vs. all".

Den här modulen är användbar för att skapa modeller som förutsäger tre eller fler möjliga resultat, när resultatet beror på kontinuerliga eller kategoriska för förutsägande variabler. Med den här metoden kan du också använda binära klassificerings metoder för problem som kräver flera utgående klasser.

### <a name="more-about-one-vs-all-models"></a>Mer om en-vs-alla-modeller

Vissa klassificerings algoritmer tillåter användning av fler än två klasser efter design, men andra begränsar de möjliga resultat till ett av två värden (en binär eller en modell med två klasser). Men även binära klassificerings algoritmer kan anpassas för klassificerings aktiviteter med flera klasser med hjälp av olika strategier. 

I den här modulen implementeras en *vs. all-metod*i vilken en binär modell skapas för var och en av de olika utgående klasserna. Var och en av dessa binära modeller för enskilda klasser utvärderas mot komplementet (alla andra klasser i modellen) som om det vore ett binära klassificerings problem. Förutsägelsen utförs sedan genom att köra dessa binära klassificerare och välja förutsägelse med högsta möjliga resultat.  

I själva verket skapas en ensemble med enskilda modeller och resultaten sammanfogas för att skapa en enskild modell som förutsäger alla klasser. Därför kan en binär klassificerare användas som grund för en One-vs-all-modell.  

 Anta till exempel att du konfigurerar en [dubbelriktad Vector Machine-](two-class-support-vector-machine.md) modell och anger den som inmatad i **en-mot-alla – multiklass** -modulen. Modulen skapar dubbelriktade support vektor dator modeller för alla medlemmar i klassen output och använder sedan metoden One-vs-all för att kombinera resultaten för alla klasser.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Så här konfigurerar du en-vs-all-klassificerare  

Den här modulen skapar en ensemble med binära klassificerings modeller för att analysera flera klasser. Om du vill använda den här modulen måste du därför först konfigurera och träna en **binär klassificerings** modell. 

Sedan ansluter du den binära modellen till **en-mot-alla – multiklass** modul och tränar upp ensemblen för modeller med hjälp av [träna modell](train-model.md) med en etikettad utbildnings data uppsättning.

När du kombinerar modeller, även om inlärnings data uppsättningen kan ha flera klass värden, skapar **en-mot-alla – multiklass** flera binära klassificerings modeller, optimerar algoritmen för varje klass och sammanfogar sedan modellerna.

1. Lägg till **en-mot-alla – multiklass** i din pipeline i designern. Du hittar den här modulen under Machine Learning-Initialize i **klassificerings** kategorin.

    **En-mot-alla – multiklass** klassificeraren har inga parametrar som kan konfigureras. Eventuella anpassningar måste göras i den binära klassificerings modellen som anges som indata.

2. Lägg till en binär klassificerings modell i pipelinen och konfigurera modellen. Du kan till exempel använda en [dubbelriktad Vector-dator](two-class-support-vector-machine.md) eller två Klasss [öknings bara besluts träd](two-class-boosted-decision-tree.md).

3. Lägg till modulen [träna modell](train-model.md) i din pipeline och Anslut den intränade klassificeraren som är utdata från **en-mot-alla – multiklass**.

4. På den andra inmatningen av [träna modell](train-model.md)ansluter du en etikettad tränings data uppsättning som har flera klass värden.

5. Köra en pipeline.

## <a name="results"></a>Resultat

När utbildningen är klar kan du använda modellen för att göra förutsägelser i multiklass.

Alternativt kan du skicka den ej tränade klassificeraren till [kors](cross-validate-model.md) validerings modellen för kors validering mot en etikettad validerings data uppsättning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
