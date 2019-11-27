---
title: Sammanställnings bar Azure blockchain-token
description: Blockchain för Azure DataStore-token ger flexibilitet för att skapa token för avancerade scenarier.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325123"
---
# <a name="azure-blockchain-tokens-composability"></a>Sammanställnings bar Azure blockchain-token

[!INCLUDE [Preview note](./includes/preview.md)]

Det finns flexibilitet i att skapa token för avancerade scenarier. Du kan ha ett komplext scenario som inte kan implementeras med de [fyra förbyggda token-mallarna](templates.md#base-token-types). Med datafiler för token kan du skapa egna tokens genom att lägga till eller ta bort definierade beteenden för att skapa en egen token-mall. När du skapar en ny token-mall, verifierar Azure blockchain-token alla grammatiska regler för tokensignering. Sammansatta mallar sparas i Azure blockchain-token-tjänsten för utfärdande av anslutna blockchain-nätverk.

Du kan använda [token-beteenden](templates.md#token-behaviors) i följande avsnitt för att utforma din token-mall.

## <a name="burnable-b"></a>Brännings bara (b)

Möjlighet att ta bort token från aggregatet.

När du t. ex. löser in kredit korts platser online för ett presentkort, bränns kredit korts platserna.

## <a name="delegable-g"></a>Kan delegeras (g)

Möjlighet att delegera de åtgärder som vidtas för den token som du äger.

Ombudet kan utföra åtgärder som ägare av token. Du kan till exempel använda en kan delegeras-token för att implementera en röst. Med en kan delegeras-token kan en röst-token-ägare ha någon annan röst för deras räkning.

## <a name="logable-l"></a>Logable (l)

Möjlighet att logga.

Du kan till exempel utfärda en logable-token för en film distribution till varje teater som visar en speciell film. För att filmen ska kunna spelas upp måste visningen logga en transaktion för varje visning eftersom royalty-utbetalningar per visning visas under filmens versions körning. Aktörerna som skapar kan använda film-token för att verifiera utbetalningar per film som visas per teater i distributionen.

## <a name="mint-able-m"></a>Mintgrön – kan (m)

Möjlighet att mintgrön ytterligare token för klassen token. Minter-rollen innehåller beteendet mintable.

Till exempel kan ett detalj handels företag, som vill implementera ett förmåns program, använda mintable-token för sina lojalitets program. De kan mintgrön ytterligare förmåns Poäng för kunderna när deras kund bas växer.  

## <a name="non-subdividable-or-whole-d"></a>Icke-underdelad eller helhet (~ d)

Begränsning för att förhindra att en token delas upp i mindre delar.

En enda konst målning kan till exempel inte delas upp i flera mindre delar. 

## <a name="non-transferable-t"></a>Det går inte att överföra (~ t)

Begränsning för att förhindra en ändring av ägarskap från den inledande token-ägaren.

Ett universitets diplom är till exempel en icke-överförbar token. När ett diplom har fått en examen kan det inte överföras från en annan persons examen.

## <a name="roles-r"></a>Roller (r)

Möjlighet att definiera roller i klassen token för vissa beteenden.

Du kan ange en lista över roll namn som en token stöder vid skapande av token. När roller anges kan användaren tilldela roller till dessa beteenden. För närvarande stöds endast Minter-rollen.

## <a name="singleton-s"></a>Singleton (s)

Begränsning för att tillåta en till gång till en token.

Till exempel är en Museum-artefakt en singleton-token. Museum-artefakter är unika. En token som representerar en artefakt har bara ett enda objekt i aggregatet.

## <a name="subdividable-d"></a>Subdivisionable (d)

Möjlighet att dela upp en token i mindre delar.

Till exempel kan en dollar delas upp i cent.

## <a name="transferable-t"></a>Överförings bara (t)

Möjlighet att överföra ägarskapet för token.

En egenskaps rubrik är till exempel en överförings bara token som kan överföras från en person till en annan när egenskapen säljs.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [konto hantering för Azure blockchain-token](account-management.md).
