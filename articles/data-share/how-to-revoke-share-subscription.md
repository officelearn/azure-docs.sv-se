---
title: Återkalla en resurs prenumeration i Azure Data Share
description: Lär dig hur du återkallar en resurs prenumeration från en mottagare med Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: c1c049d467cdf07962719f0dc257acbf47631aaf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119692"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Återkalla en konsuments resurs prenumeration i Azure Data Share

Den här artikeln förklarar hur du återkallar en resurs prenumeration från en eller flera av dina användare med hjälp av Azure Data Share. Detta förhindrar en konsument från att utlösa fler ögonblicks bilder. Om konsumenten ännu inte har utlöst en ögonblicks bild, kommer de aldrig att ta emot data när resurs prenumerationen har återkallats. Om de tidigare har utlöst en ögonblicks bild, kommer de senaste data som de har kvar i sitt konto.

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I Azure Data Share navigerar du till den skickade resursen och väljer fliken **dela prenumerationer** .

![Återkalla resurs prenumeration](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Markera kryss rutorna bredvid mottagarna vars resurs prenumerationer som du vill ta bort och klicka sedan på **återkalla**. Konsumenten får inte längre uppdateringar av sina data.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [övervakar dina data resurser](how-to-monitor.md).