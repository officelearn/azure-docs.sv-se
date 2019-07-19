---
title: Lägga till data uppsättningar till en befintlig data resurs i Azure Data Share Preview
description: Lägga till data uppsättningar till en befintlig data resurs
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: t-maadam
ms.openlocfilehash: 01197d91277c59b58c5ab841dfc2abfb78be71de
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877290"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Så här lägger du till data uppsättningar till en befintlig resurs i för hands versionen av Azure Data Share

Den här artikeln beskriver hur du lägger till data uppsättningar till en befintlig data resurs med hjälp av för hands versionen av Azure Data Share. På så sätt kan du dela mer data med samma mottagare utan att behöva skapa en ny resurs.

Information om hur du lägger till data uppsättningar när du skapar en resurs finns i själv studie kursen [dela data](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I för hands versionen av Azure Data Share navigerar du till den skickade resursen och väljer fliken **data uppsättningar** . Lägg till fler data uppsättningar genom att klicka på **+ Lägg till data uppsättningar** .

![Lägg till data uppsättningar](./media/how-to/how-to-add-datasets/add-datasets.png)

I panelen till höger väljer du den data uppsättnings typ som du vill lägga till och klickar sedan på **Nästa**. Välj prenumerationen och resurs gruppen för de data som du vill lägga till. Använd de nedrullningsbara pilarna och leta upp och markera kryss rutan bredvid de data som ska läggas till.

![Lägg till data uppsättningar](./media/how-to/how-to-add-datasets/add-datasets-side.png)

När du klickar på **Lägg till data uppsättningar**kommer data uppsättningarna att läggas till i din resurs. Obs! En ögonblicks bild måste utlösas av dina konsumenter för att de ska kunna se de nya data uppsättningarna. Om inställningarna för ögonblicks bilder har kon figurer ATS ser användarna de nya data uppsättningarna när nästa schemalagda ögonblicks bild har slutförts. Om inte inställningarna för ögonblicks bilder har kon figurer ATS måste konsumenten manuellt utlösa en fullständig eller stegvis kopia av data för att ta emot uppdateringarna. Mer information om ögonblicks bilder finns i [ögonblicks bilder](terminology.md).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du [lägger till mottagare i en befintlig data resurs](how-to-add-recipients.md).