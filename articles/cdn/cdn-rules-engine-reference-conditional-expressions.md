---
title: Azure CDN regler motorn villkorsuttryck | Microsoft Docs
description: "I referensdokumentationen för Azure CDN regler motorn matchar villkoren och funktioner."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Azure CDN regler motorn villkorsuttryck
Det här avsnittet innehåller detaljerade beskrivningar av villkorliga uttryck för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Den första delen av en regel är villkorsuttrycket.

Villkorsuttryck | Beskrivning
-----------------------|-------------
OM | Ett IF-uttryck är alltid en del av den första satsen i en regel. Precis som andra villkorsuttryck måste om-satsen vara kopplad till en matchning. Om inga ytterligare villkorsuttryck definieras anger matchningen villkor som måste uppfyllas innan en uppsättning funktioner som kan tillämpas på en begäran.
OCH OM | Ett uttryck och om kan endast läggas till efter följande typer av villkorliga uttryck: om, och om. Anger att det finns ytterligare ett villkor som måste uppfyllas för den första om-satsen.
ANNARS OM| ANNARS om uttryck anger ett alternativt villkor som måste uppfyllas innan en uppsättning funktioner som är specifika för den här ANNARS om instruktionen äger rum. Förekomsten av en annan om instruktionen anger slutet av den föregående instruktionen. Endast villkorsuttrycket som kan vara placerad efter uttrycket ANNARS om är en annan ELSE IF-sats. Det innebär ett ELSE IF-uttryck får endast användas för att ange ett enda ytterligare villkor som måste uppfyllas.

**Exempel**: ![CDN matchar villkoret](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel. Exempel: En fångar in alla regel skyddar alla begäranden via Token-baserad autentisering. En annan regel som kan skapas direkt under den så att ett undantag för vissa typer av begäranden.

### <a name="next-steps"></a>Nästa steg
* [Azure CDN-översikt](cdn-overview.md)
* [Regler modulreferens](cdn-rules-engine-reference.md)
* [Regler motorn matchar villkor](cdn-rules-engine-reference-match-conditions.md)
* [Regler motorn funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)
