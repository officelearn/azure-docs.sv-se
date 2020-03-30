---
title: Återkalla en delningsprenumeration i Azure Data Share
description: Läs om hur du återkallar en aktieprenumeration från en mottagare med Hjälp av Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476387"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Så här återkallar du en konsuments delningsprenumeration i Azure Data Share

I den här artikeln beskrivs hur du återkallar en delningsprenumeration från en eller flera av dina konsumenter med Hjälp av Azure Data Share. Detta förhindrar att en konsument utlöser fler ögonblicksbilder. Om konsumenten ännu inte har utlöst en ögonblicksbild får de aldrig data när aktieprenumerationen har återkallats. Om de tidigare har utlöst en ögonblicksbild kommer de senaste data som de har att finnas kvar i kontot.

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en skickad dataresurs

I Azure Data Share navigerar du till din skickade resurs och väljer fliken **Dela prenumerationer.**

![Återkalla aktieprenumeration](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Markera rutorna bredvid mottagarna vars aktieprenumerationer som du vill ta bort och klicka sedan på **Återkalla**. Konsumenten kommer inte längre att få uppdateringar av sina data.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [övervakar dina dataresurser](how-to-monitor.md).