---
title: Återkalla en resurs prenumeration i för hands versionen av Azure Data reshare
description: Lär dig hur du återkallar en resurs prenumeration från en mottagare med hjälp av Azure Data Share Preview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: bd8ef23ead500acb9e403b38fd52a2d980d12bf4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169061"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Återkalla en konsuments resurs prenumeration i Azure Data repreview

Den här artikeln förklarar hur du återkallar en resurs prenumeration från en eller flera av dina användare med hjälp av Azure Data Share Preview. Detta förhindrar en konsument från att utlösa fler ögonblicks bilder. Om konsumenten ännu inte har utlöst en ögonblicks bild, kommer de aldrig att ta emot data när resurs prenumerationen har återkallats. Om de tidigare har utlöst en ögonblicks bild, kommer de senaste data som de har kvar i sitt konto.

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I för hands versionen av Azure Data Share navigerar du till den skickade resursen och väljer fliken **dela prenumerationer** .

![Återkalla resurs prenumeration](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Markera kryss rutorna bredvid mottagarna vars resurs prenumerationer som du vill ta bort och klicka sedan på **återkalla**. Konsumenten får inte längre uppdateringar av sina data.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [övervakar dina data resurser](how-to-monitor.md).