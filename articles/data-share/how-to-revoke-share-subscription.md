---
title: Återkalla en resurs prenumeration i för hands versionen av Azure Data reshare
description: Återkalla en delad prenumeration
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: t-maadam
ms.openlocfilehash: fb5572aee513172d06b5a75c2d3b11885081f792
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877212"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Återkalla en konsuments resurs prenumeration i Azure Data repreview

Den här artikeln förklarar hur du återkallar en resurs prenumeration från en eller flera av dina användare med hjälp av Azure Data Share Preview. Detta förhindrar en konsument från att utlösa fler ögonblicks bilder. Om konsumenten ännu inte har utlöst en ögonblicks bild, kommer de aldrig att ta emot data när resurs prenumerationen har återkallats. Om de tidigare har utlöst en ögonblicks bild, kommer de senaste data som de har kvar i sitt konto.

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I för hands versionen av Azure Data Share navigerar du till den skickade resursen och väljer fliken **dela prenumerationer** .

![Återkalla resurs prenumeration](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Markera kryss rutorna bredvid mottagarna vars resurs prenumerationer som du vill ta bort och klicka sedan på **återkalla**. Konsumenten får inte längre uppdateringar av sina data.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [övervakar dina data resurser](how-to-monitor.md).