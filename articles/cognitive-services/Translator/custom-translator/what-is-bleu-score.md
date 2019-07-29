---
title: Vad är en BLEU Poäng? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: BLEU är ett mått på skillnaderna mellan en automatisk översättning och en eller flera referens översättningar som skapats av en person som skapats av samma käll mening. BLEU-algoritmen jämför upprepade fraser i den automatiska översättningen med de efterföljande fraserna som hittas i referens översättningen och räknar antalet matchningar i viktat mode.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 1993819ad227d7e9aa5ef899045e00447a6740b8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595411"
---
# <a name="what-is-a-bleu-score"></a>Vad är en BLEU Poäng?

[Bleu (tvåspråkig Evaluation destudie)](https://en.wikipedia.org/wiki/BLEU) är ett mått på skillnaderna mellan en automatisk översättning och en eller flera referens översättningar som skapats av en person som skapats av samma käll mening.

## <a name="scoring-process"></a>Bedömnings process

BLEU-algoritmen jämför upprepade fraser i den automatiska översättningen med de efterföljande fraserna som hittas i referens översättningen och räknar antalet matchningar i viktat mode. Dessa matchningar är placerade oberoende av varandra. En högre matchnings grad visar en högre grad av likhet med referens översättningen och högre poäng. Intelligibility och grammatiskhet har inte rätt att ta hänsyn till.

## <a name="how-bleu-works"></a>Hur fungerar BLEU?

BLEU styrkan är att den korreleras bra med mänsklig bedömning genom att medelvärdet av enskilda menings fel överskrids med en test-sökkorpus i stället för att försöka utforma den exakta mänsklig domen för varje mening.

En mer omfattande diskussion om BLEU resultat finns [här](https://youtu.be/-UqDljMymMg).

BLEU-resultaten är beroende av din domän, konsekvensen av test data med inlärnings-och justerings data och hur mycket data som är tillgängliga för att träna. Om dina modeller har tränats på en smal domän och dina utbildnings data är konsekventa med dina test data, kan du vänta en hög BLEU poäng.

>[!NOTE]
>En jämförelse mellan BLEU-poängen är bara motiverad när BLEU resultat jämförs med samma test uppsättning, samma språk par och samma MT-motor. En BLEU-Poäng från en annan test uppsättning är kopplad till en annan.
