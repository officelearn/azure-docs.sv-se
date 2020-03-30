---
title: En mot alla multiklass
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen One-vs-All Multiclass i Azure Machine Learning för att skapa en klassificeringsmodell med flera klasser från en ensemble med binära klassificeringsmodeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456088"
---
# <a name="one-vs-all-multiclass"></a>En mot alla multiklass

I den här artikeln beskrivs hur du använder modulen One-vs-All Multiclass i Azure Machine Learning designer (förhandsversion). Målet är att skapa en klassificeringsmodell som kan förutsäga flera klasser, med hjälp av *metoden en mot alla.*

Den här modulen är användbar för att skapa modeller som förutsäger tre eller fler möjliga resultat, när resultatet beror på kontinuerliga eller kategoriska prediktorvariabler. Med den här metoden kan du också använda binära klassificeringsmetoder för problem som kräver flera utdataklasser.

### <a name="more-about-one-versus-all-models"></a>Mer om modeller med en mot alla

Vissa klassificeringsalgoritmer tillåter användning av mer än två klasser avsiktligt. Andra begränsar möjliga resultat till ett av två värden (en binär eller tvåklassmodell). Men även binära klassificeringsalgoritmer kan anpassas för klassificeringsuppgifter av flera klasser genom en mängd olika strategier. 

Den här modulen implementerar metoden en mot alla, där en binär modell skapas för var och en av de flera utdataklasserna. Modulen bedömer var och en av dessa binära modeller för de enskilda klasserna mot dess komplement (alla andra klasser i modellen) som om det är en binär klassificering fråga. Modulen utför sedan förutsägelse genom att köra dessa binära klassificerare och välja förutsägelse med högsta konfidenspoäng.  

I huvudsak skapar modulen en ensemble av enskilda modeller och sedan slår samman resultaten, för att skapa en enda modell som förutsäger alla klasser. Alla binära klassificerare kan användas som grund för en modell som en mot alla.  

Anta till exempel att du konfigurerar en [Vector Machine-modell](two-class-support-vector-machine.md) i två klasser och anger den som indata till modulen One-vs-All Multiclass. Modulen skulle skapa tvåklassiga stöd vektormaskinmodeller för alla medlemmar i utdataklassen. Det skulle sedan tillämpa en-mot-alla-metoden för att kombinera resultaten för alla klasser.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Konfigurera klassificeraren För en mot alla-multiklassificerare  

Den här modulen skapar en ensemble av binära klassificeringsmodeller för att analysera flera klasser. Om du vill använda den här modulen måste du konfigurera och träna en *binär klassificeringsmodell* först. 

Du ansluter den binära modellen till modulen One-vs-All Multiclass. Du tränar sedan ensemblen av modeller med hjälp av [Train Model](train-model.md) med en märkt utbildningsdatauppsättning.

När du kombinerar modellerna skapar One-vs-All Multiclass flera binära klassificeringsmodeller, optimerar algoritmen för varje klass och sammanfogar sedan modellerna. Modulen utför dessa uppgifter även om träningsdatauppsättningen kan ha flera klassvärden.

1. Lägg till modulen One-vs-All Multiclass i pipelinen i designern. Du hittar den här modulen under **Machine Learning - Initialize**, i kategorin **Klassificering.**

   Klassificeraren One-vs-All Multiclass har inga egna konfigurerbara parametrar. Alla anpassningar måste göras i den binära klassificeringsmodellen som tillhandahålls som indata.

2. Lägg till en binär klassificeringsmodell i pipelinen och konfigurera den modellen. Du kan till exempel använda [tvåklassstödvektordatorn](two-class-support-vector-machine.md) eller [ett tvåklassigt förstärkt beslutsträd](two-class-boosted-decision-tree.md).

3. Lägg till [tågmodellmodulen](train-model.md) i pipelinen. Anslut den otränade klassificeraren som är utdata från One-vs-All Multiclass.

4. Anslut en märkt träningsdatauppsättning som har flera klassvärden på den andra indatavärdet [i Tågmodell.](train-model.md)

5. Skicka pipelinen.

## <a name="results"></a>Resultat

När träningen är klar kan du använda modellen för att göra förutsägelser med flera klasser.

Du kan också skicka den otränade klassificeraren till [Korsvalifierad modell](cross-validate-model.md) för korsvalidering mot en märkt valideringsdatauppsättning.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
