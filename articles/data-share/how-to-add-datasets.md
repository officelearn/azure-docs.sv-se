---
title: Lägga till data uppsättningar till en befintlig Azure-Dataresurs
description: Lär dig hur du lägger till data uppsättningar till en befintlig data resurs i Azure Data Share och delar med samma mottagare.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73490543"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Så här lägger du till data uppsättningar till en befintlig resurs i Azure Data Share

Den här artikeln beskriver hur du lägger till data uppsättningar till en befintlig data resurs med hjälp av Azure Data Share. På så sätt kan du dela mer data med samma mottagare utan att behöva skapa en ny resurs.

Information om hur du lägger till data uppsättningar när du skapar en resurs finns i själv studie kursen [dela data](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en data resurs som skickats

I Azure Data Share navigerar du till den skickade resursen och väljer fliken **data uppsättningar** . Klicka på **+ Lägg till data uppsättningar** om du vill lägga till fler data uppsättningar.

![Lägg till data uppsättningar](./media/how-to/how-to-add-datasets/add-datasets.png)

I panelen till höger väljer du den data uppsättnings typ som du vill lägga till och klickar sedan på **Nästa**. Välj prenumerationen och resurs gruppen för de data som du vill lägga till. Använd de nedrullningsbara pilarna och leta upp och markera kryss rutan bredvid de data som ska läggas till.

![Lägg till data uppsättningar](./media/how-to/how-to-add-datasets/add-datasets-side.png)

När du klickar på **Lägg till data uppsättningar**kommer data uppsättningarna att läggas till i din resurs. Obs! en ögonblicks bild måste utlösas av dina användare för att de ska kunna se de nya data uppsättningarna. Om inställningarna för ögonblicks bilder har kon figurer ATS ser användarna de nya data uppsättningarna när nästa schemalagda ögonblicks bild har slutförts. Om inte inställningarna för ögonblicks bilder har kon figurer ATS måste konsumenten manuellt utlösa en fullständig eller stegvis kopia av data för att ta emot uppdateringarna. Mer information om ögonblicks bilder finns i [ögonblicks bilder](terminology.md).

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur du [lägger till mottagare i en befintlig data resurs](how-to-add-recipients.md).