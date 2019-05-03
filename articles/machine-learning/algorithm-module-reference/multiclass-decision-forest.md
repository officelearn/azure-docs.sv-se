---
title: 'Beslutsskog med multiclass: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Multiclass Beslutsskog-modulen i Azure Machine Learning-tjänsten för att skapa en machine learning-modell som baseras på den *beslut skog* algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d5ff1809886198f7f1e4f4b9c36f877ce61d512e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029437"
---
# <a name="multiclass-decision-forest-module"></a>Beslutsskog med multiclass modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en machine learning-modell som baseras på den *beslut skog* algoritmen. En beslutsskog är en ensemble-modell som snabbt bygger en serie beslutsträd vid learning från taggade data.

## <a name="more-about-decision-forests"></a>Mer om beslutet skogar

Beslutet skog algoritmen är en ensemble learning metod för klassificering. Algoritmen fungerar genom att skapa flera beslutsträd och sedan *röstande* om klassen populäraste utdata. Rösta är en typ av aggregering, i vilken varje trädet i ett klassificeringsbeslut skog matar ut ett icke-normaliserat frekvens histogram för etiketter. Processen för aggregering summerar dessa histogram och normaliserar resultatet för att få ”sannolikhet” för varje etikett. Träd som har hög förutsägelsen har en högre vikt i det slutliga beslutet i ensemble.

Beslutsträd är i allmänhet icke-parametrisk modeller, vilket innebär att de har stöd för data med olika distributioner. I varje trädet körs en sekvens med enkla tester för varje klass, vilket ökar nivåer av en trädstruktur tills en lövnod (beslut) har uppnåtts.

Beslutsträd har många fördelar:

+ De kan representera icke-linjära beslut gränser.
+ De är effektiva i beräknings- och minnesanvändning under träning och förutsägelser.
+ De utför val av integrerade funktioner och klassificering.
+ De är flexibel när det finns störningar funktioner.

Beslutet skog Klassificeraren i Azure Machine Learning består av en ensemble för beslutsträd. I allmänhet ger ensemble modeller bättre täckning och noggrannhet än enkel beslutsträd. Mer information finns i [Decision trees](http://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Så här konfigurerar du Multiclass Beslutsskog



1. Lägg till den **Multiclass Beslutsskog** modulen i experimentet i gränssnittet. Du hittar den här modulen under **Maskininlärning**, **initiera modell**, och **klassificering**.

2. Dubbelklicka på modulen att öppna den **egenskaper** fönstret.

3. För **sampla metoden**, välja den metod som används för att skapa enskilda träd.  Du kan välja från förpackningskapacitet eller replikering.

    + **Bagging**: Kallas även för förpackningskapacitet *bootstrap sammanställa*. I den här metoden är varje träd odlas på ett nytt stickprov som skapats av slumpmässigt sampling den ursprungliga datauppsättningen med ersättning förrän du har en datauppsättning storleken på ursprungligt. Utdata för modellerna kombineras med *röstande*, vilket är en typ av aggregering. Mer information finns i posten för sammanställning av Bootstrap Wikipedia.

    + **Replikera**: I replikering tränas varje träd på exakt samma inkommande data. Bestämning av vilka dela predikatet används för varje trädnod förblir slumpmässiga skapar olika träd.

   

4. Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.

    + **Enkel parametern**: Välj det här alternativet om du vet hur du vill konfigurera modellen och tillhandahåller en uppsättning värden som argument.


5. **Antal beslutsträd**: Ange det maximala antalet beslutsträd som kan skapas i ensemble. Du kan eventuellt få bättre täckning genom att skapa mer beslutsträd, men utbildning kan öka.

    Det här värdet styr också antalet träd som visas i resultaten när visualisera den tränade modellen. Om du vill visa eller skriva ut ett enda träd, kan du ange värdet 1. Detta innebär dock bara en trädet kan vara produceras (trädet med den första uppsättningen parametrar) och inga ytterligare iterationer utförs.

6. **Maximalt djup för beslutsträd**: Ange ett tal som begränsar det maximala djupet på alla beslutsträdet. Öka djupet i trädet kan öka noggrannhet på millisekunder, dess risk utbildningstid overfitting och ökad.

7. **Antal slumpmässiga delningar per nod**: Ange antalet delningar ska användas för att skapa varje nod i trädet. En *dela* innebär att funktioner i varje nivå i trädet (node) delas slumpmässigt.

8. **Minsta antal prover per lövnod**: Ange det minsta antalet fall som krävs för att skapa en terminal nod (löv-) i ett träd. Genom att öka det här värdet kan du öka tröskelvärdet för att skapa nya regler.

    Med standardvärdet 1 kan till exempel även ett enda fall orsaka en ny regel som ska skapas. Om du ökar värdet till 5, skulle träningsdata behöva innehålla minst fem ärenden som uppfyller villkor som är samma.



10. Anslut en taggade datauppsättning och en av modulerna som utbildning:

    + Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](./train-model.md) modulen.

11. Kör experimentet.

## <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill se i trädet som har skapats för varje iteration, högerklickar du på utdata från den [Träningsmodell](./train-model.md) modul och välj **visualisera**.
+ Om du vill visa reglerna för varje nod, klickar du på varje trädet om du vill granska nedåt i delningen.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 