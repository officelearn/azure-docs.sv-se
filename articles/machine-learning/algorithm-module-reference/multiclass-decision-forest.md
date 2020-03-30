---
title: 'Multiclass Beslut Forest: Modul Referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Multiclass Decision Forest i Azure Machine Learning för att skapa en maskininlärningsmodell baserat på *beslutsskogsalgoritmen.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477552"
---
# <a name="multiclass-decision-forest-module"></a>Moduler för Skogsmodul för fleraklasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en maskininlärningsmodell baserat på *beslutsskogsalgoritmen.* En beslutsskog är en ensemblemodell som snabbt bygger en rad beslutsträd, samtidigt som man lär sig av taggade data.

## <a name="more-about-decision-forests"></a>Mer om beslutsskogar

Beslutsalgoritmen är en ensembleinlärningsmetod för klassificering. Algoritmen fungerar genom att bygga flera beslutsträd och sedan *rösta* på den mest populära utdataklassen. Röstning är en form av aggregering, där varje träd i ett klassificeringsbeslut skog ut ur en icke-normaliserad frekvens histogram av etiketter. Aggregeringsprocessen summerar dessa histogram och normaliserar resultatet för att få "sannolikheter" för varje etikett. De träd som har hög förutsägelse förtroende har en större betydelse i det slutliga beslutet av ensemblen.

Beslutsträd i allmänhet är icke-parametriska modeller, vilket innebär att de stöder data med olika distributioner. I varje träd körs en sekvens av enkla tester för varje klass, vilket ökar nivåerna för en trädstruktur tills en lövnod (beslut) har nåtts.

Beslutsträd har många fördelar:

+ De kan representera icke-linjära beslutsgränser.
+ De är effektiva i beräkning och minnesanvändning under träning och förutsägelse.
+ De utför integrerat funktionsval och klassificering.
+ De är motståndskraftiga i närvaro av bullriga funktioner.

Beslutsskogsklassificeraren i Azure Machine Learning består av en ensemble av beslutsträd. Generellt ensemble modeller ger bättre täckning och noggrannhet än enda beslut träd. Mer information finns i [Beslutsträd](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Konfigurerar Beslutsskog för fleraklasser

1. Lägg till modulen **Multiclass Decision Forest** i pipelinen i designern. Du hittar den här modulen under **Maskininlärning,** **initiera modell**och **klassificering**.

2. Dubbelklicka på modulen för att öppna fönstret **Egenskaper.**

3. För **omsamplingsmetod**väljer du den metod som används för att skapa de enskilda träden.  Du kan välja mellan uppsamlare eller replikering.

    + **Uppsamlare:** Uppsamlare kallas också *bootstrap aggregating*. I den här metoden odlas varje träd på ett nytt urval, skapat genom att slumpmässigt sampling av den ursprungliga datauppsättningen med ersättning tills du har en datauppsättning storleken på originalet. Modellernas resultat kombineras med *omröstning*, vilket är en form av aggregering. Mer information finns i Wikipedia-posten för Bootstrap-sammanställning.

    + **Replikera**: I replikering tränas varje träd på exakt samma indata. Bestämningen av vilken delad predikat används för varje trädnod förblir slumpmässig, vilket skapar olika träd.

   

4. Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**

    + **Enkel parameter:** Välj det här alternativet om du vet hur du vill konfigurera modellen och ange en uppsättning värden som argument.

    + **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.   

5. **Antal beslutsträd**: Skriv det maximala antalet beslutsträd som kan skapas i ensemblen. Genom att skapa fler beslutsträd kan du eventuellt få bättre täckning, men träningstiden kan öka.

    Det här värdet styr också antalet träd som visas i resultaten när den tränade modellen visualiseras. Om du vill visa eller skriva ut ett enskilt träd kan du ange värdet till 1. Detta innebär dock att endast ett träd kan produceras (trädet med den ursprungliga uppsättningen parametrar), och inga ytterligare iterationer utförs.

6. **Beslutsträdens maximala djup**: Skriv ett tal för att begränsa det maximala djupet för ett beslutsträd. Öka djupet av trädet kan öka precisionen, med risk för viss övermontering och ökad träningstid.

7. **Antal slumpmässiga delningar per nod**: Ange antalet delningar som ska användas när varje nod i trädet skapas. En *delning* innebär att funktioner i varje nivå i trädet (noden) delas upp slumpmässigt.

8. **Minsta antal prover per bladnod**: Ange det minsta antal fall som krävs för att skapa en terminalnod (löv) i ett träd. Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler.

    Till exempel med standardvärdet 1 kan även ett enskilt ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste utbildningsdata innehålla minst fem fall som uppfyller samma villkor.



10. Anslut en märkt datauppsättning och en av utbildningsmodulerna:

    + Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](./train-model.md)

11. Skicka pipelinen.



## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 