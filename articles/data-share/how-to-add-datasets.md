---
title: Lägga till data uppsättningar i en befintlig Azure Data repreview-förhandsgranskning
description: Lägga till data uppsättningar till en befintlig data resurs
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: bd8cd7af72c349060eb035dc32e9ddd1a7f9920e
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327513"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Så här lägger du till data uppsättningar till en befintlig resurs i för hands versionen av Azure Data Share

Den här artikeln beskriver hur du lägger till data uppsättningar till en befintlig data resurs med hjälp av för hands versionen av Azure Data Share. På så sätt kan du dela mer data med samma mottagare utan att behöva skapa en ny resurs.

Information om hur du lägger till data uppsättningar när du skapar en resurs finns i själv studie kursen [dela data](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I för hands versionen av Azure Data Share navigerar du till den skickade resursen och väljer fliken **data uppsättningar** . Lägg till fler data uppsättningar genom att klicka på **+ Lägg till data uppsättningar** .

![Lägg till datamängder](./media/how-to/how-to-add-datasets/add-datasets.png)

I panelen till höger väljer du den data uppsättnings typ som du vill lägga till och klickar sedan på **Nästa**. Välj prenumerationen och resurs gruppen för de data som du vill lägga till. Använd de nedrullningsbara pilarna och leta upp och markera kryss rutan bredvid de data som ska läggas till.

![Lägg till datamängder](./media/how-to/how-to-add-datasets/add-datasets-side.png)

När du klickar på **Lägg till data uppsättningar**kommer data uppsättningarna att läggas till i din resurs. Obs! En ögonblicks bild måste utlösas av dina konsumenter för att de ska kunna se de nya data uppsättningarna. Om inställningarna för ögonblicks bilder har kon figurer ATS ser användarna de nya data uppsättningarna när nästa schemalagda ögonblicks bild har slutförts. Om inte inställningarna för ögonblicks bilder har kon figurer ATS måste konsumenten manuellt utlösa en fullständig eller stegvis kopia av data för att ta emot uppdateringarna. Mer information om ögonblicks bilder finns i [ögonblicks bilder](terminology.md).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du [lägger till mottagare i en befintlig data resurs](how-to-add-recipients.md).