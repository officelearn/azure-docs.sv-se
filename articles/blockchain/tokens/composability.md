---
title: Azure Blockchain Tokens komposterbarhet
description: Azure Blockchain Tokens komposterbarhet ger flexibilitet för att skapa token för avancerade scenarier.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325123"
---
# <a name="azure-blockchain-tokens-composability"></a>Azure Blockchain Tokens komposterbarhet

[!INCLUDE [Preview note](./includes/preview.md)]

Tokenkomposterbarhet ger flexibilitet för att skapa token för avancerade scenarier. Du kan ha ett komplext scenario som inte kan implementeras med hjälp av de [fyra färdiga tokenmallarna](templates.md#base-token-types). Med tokenkomposterbarhet kan du utforma egna tokenmallar genom att lägga till eller ta bort definierade beteenden för att skapa din egen tokenmall. När du skapar en ny tokenmall verifierar Azure Blockchain-token alla token grammatikregler. Sammansättning mallar sparas i Azure Blockchain Tokens-tjänsten för utfärdande av anslutna blockchain-nätverk.

Du kan använda [tokenbeteendena](templates.md#token-behaviors) i följande avsnitt för att utforma tokenmallen.

## <a name="burnable-b"></a>Brännbar (b)

Möjlighet att ta bort token från leveransen.

När du till exempel löser in kreditkortspoäng online för ett presentkort bränns kreditkortspoängen.

## <a name="delegable-g"></a>Delegable (g)

Möjlighet att delegera de åtgärder som vidtagits på den token som du äger.

Ombudet kan utföra åtgärder som ägare av token. Du kan till exempel använda en delegable token för att genomföra en omröstning. En delegable token tillåter omröstningen token ägare att ha någon annan rösta på deras vägnar.

## <a name="logable-l"></a>Loggbar (l)

Möjlighet att logga.

Du kan till exempel utfärda en loggbar token för en filmdistribution till varje teater som visar en viss film. För att filmen ska spelas upp måste visningen logga en transaktion för varje visning eftersom royaltyutbetalningar visas per visning under filmens releasekörning. Skådespelarna bygga kan använda filmen tokens för att validera utbetalningar per film som visar per teater i distributionen.

## <a name="mint-able-m"></a>Mynta-kan (m)

Möjlighet att mynta ytterligare token för tokenklassen. Minter-rollen inkluderar mintable-beteendet.

Ett detaljhandelsföretag som vill implementera ett bonusprogram kan till exempel använda mintable-token för sitt bonusprogram. De kan prägla ytterligare lojalitetspoäng för sina kunder som deras kundbas växer.  

## <a name="non-subdividable-or-whole-d"></a>Ej subdividable eller hel (~d)

Begränsning för att förhindra att en token delas in i mindre delar.

En enda konstmålning kan till exempel inte delas in i flera mindre delar. 

## <a name="non-transferable-t"></a>Ej överförbar (~t)

Begränsning för att förhindra ägarbyte från den ursprungliga tokenägaren.

Ett examensbevis är till exempel en token som inte kan överlåtas. När ett examensbevis har lämnats till en examen kan det inte överföras från den utexaminerade till en annan person.

## <a name="roles-r"></a>Roller (r)

Möjlighet att definiera roller i tokenmallsklassen för specifika beteenden.

Du kan ange en lista över rollnamn som en token stöder vid tidpunkten för att skapa token. När roller anges kan användaren tilldela roller till dessa beteenden. För närvarande stöds endast minterrollen.

## <a name="singleton-s"></a>Singleton (s)

Begränsning för att tillåta en leverans av en token.

En museiartefakt är till exempel en singleton-token. Museiföremål är unika. En token som representerar en artefakt har bara ett enda objekt i leveransen.

## <a name="subdividable-d"></a>Dela upp (d)

Möjlighet att dela upp en token i mindre delar.

En dollar kan till exempel delas in i cent.

## <a name="transferable-t"></a>Överförbar (t)

Möjlighet att överföra äganderätten till token.

En egenskapstitel är till exempel en överförbar token som kan överföras från en person till en annan när egenskapen säljs.

## <a name="next-steps"></a>Nästa steg

Läs mer om [azure blockchain-tokens kontohantering](account-management.md).
