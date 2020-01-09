---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379307"
---
Granska `"confidence"` värden för varje nyckel/värde-resultat under noden `"pageResults"`. Du bör också titta på förtroende poängen i `"readResults"`-noden, som motsvarar Läs åtgärden text. Säkerheten för Läs resultaten påverkar inte tillförlitligheten för nyckel-/värde extraherings resultatet, så du bör kontrol lera båda.
* Om förtroende poängen för Läs åtgärden är låg, försöker du förbättra kvaliteten på dina inaktuella dokument (se [krav](../overview.md#input-requirements)för inläsning).
* Om förtroende poängen för extraherings åtgärden för nyckel/värde är låg, se till att dokumenten som analyseras är av samma typ som dokumenten som används i inlärnings uppsättningen. Om dokumenten i inlärnings uppsättningen har variationer i utseendet kan du överväga att dela upp dem i olika mappar och träna en modell för varje variation.

Förtroende poängen som du använder beror på ditt användnings fall, men vanligt vis är det en bra idé att rikta in dig på poängen på 80% eller högre. För mer känsliga fall, t. ex. läsning av medicinska poster eller fakturerings besked, rekommenderas resultatet 100%.