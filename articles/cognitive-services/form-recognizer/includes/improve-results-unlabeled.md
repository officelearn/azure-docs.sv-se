---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379307"
---
Undersök `"confidence"` värdena för varje nyckel/värderesultat under noden. `"pageResults"` Du bör också titta på `"readResults"` förtroendepoängen i noden, som motsvarar textläsningen. Konfiden hos läsresultaten påverkar inte förtroendet för resultaten för nyckel-/värdeutvinning, så du bör kontrollera båda.
* Om konfidenspoängen för läsåtgärden är låga kan du försöka förbättra kvaliteten på indatadokumenten (se [Indatakrav).](../overview.md#input-requirements)
* Om konfidenspoängen för nyckel-/värdeextraheringsåtgärden är låga, se till att de dokument som analyseras är av samma typ som dokument som används i utbildningsuppsättningen. Om dokumenten i utbildningsuppsättningen har variationer i utseende kan du överväga att dela upp dem i olika mappar och träna en modell för varje variant.

Det förtroende poäng du mål beror på din användning fall, men i allmänhet är det en god praxis att rikta en poäng på 80% eller högre. För mer känsliga fall, som att läsa journaler eller faktureringsutdrag, rekommenderas en poäng på 100 %.