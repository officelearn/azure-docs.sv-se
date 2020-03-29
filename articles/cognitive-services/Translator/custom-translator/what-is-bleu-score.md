---
title: Vad är en BLEU-poäng? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: BLEU är ett mått på skillnaderna mellan maskinöversättning och mänskligt skapade referensöversättningar av samma käll mening.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647357"
---
# <a name="what-is-a-bleu-score"></a>Vad är en BLEU-poäng?

[BLEU (Tvåspråkig utvärdering Inhoppare)](https://en.wikipedia.org/wiki/BLEU) är ett mått på skillnaderna mellan en automatisk översättning och en eller flera människoskapade referensöversättningar av samma käll mening.

## <a name="scoring-process"></a>Bedömningsprocess

BLEU-algoritmen jämför på varandra följande fraser av den automatiska översättningen med de på varandra följande fraser som hittas i referensöversättningen, och räknar antalet matchningar, på ett viktat sätt. Dessa matcher är positionsoberoende. En högre matchningsgrad indikerar en högre grad av likhet med referensöversättningen och högre poäng. Begriplighet och grammatisk korrekthet beaktas inte.

## <a name="how-bleu-works"></a>Hur BLEU fungerar?

BLEU styrka är att det korrelerar väl med mänskligt omdöme genom att i genomsnitt ut enskilda fel meningen dom över en testkorpus, snarare än att försöka utforma den exakta mänskliga domen för varje mening.

En mer omfattande diskussion om BLEU poäng är [här](https://youtu.be/-UqDljMymMg).

BLEU-resultaten beror starkt på domänens bredd, testdatas konsekvens med tränings- och justeringsdata och hur mycket data du har tillgängliga för att träna. Om dina modeller har tränats på en smal domän och dina träningsdata överensstämmer med dina testdata kan du förvänta dig en hög BLEU-poäng.

>[!NOTE]
>En jämförelse mellan BLEU-poäng är endast motiverad när BLEU-resultat jämförs med samma testuppsättning, samma språkpar och samma MT-motor. En BLEU-poäng från en annan testuppsättning måste vara annorlunda.
