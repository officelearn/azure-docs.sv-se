---
title: Lägga till datauppsättningar i en befintlig Azure-dataresurs
description: Lär dig hur du lägger till datauppsättningar i en befintlig dataresurs i Azure Data Share och delar med samma mottagare.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490543"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Så här lägger du till datauppsättningar i en befintlig resurs i Azure Data Share

I den här artikeln beskrivs hur du lägger till datauppsättningar i en befintlig dataresurs med Azure Data Share. På så sätt kan du dela mer data med samma mottagare utan att behöva skapa en ny resurs.

Information om hur du lägger till datauppsättningar när du skapar en resurs finns i självstudiekursen [Dela data.](share-your-data.md)

## <a name="navigate-to-a-sent-data-share"></a>Navigera till en skickad dataresurs

I Azure Data Share navigerar du till din skickade resurs och väljer fliken **Datauppsättningar.** Klicka **+ Lägg till datauppsättningar** för att lägga till fler datauppsättningar.

![Lägg till datauppsättningar](./media/how-to/how-to-add-datasets/add-datasets.png)

Markera den datauppsättningstyp som du vill lägga till på panelen till höger och klicka sedan på **Nästa**. Välj prenumerations- och resursgruppen för de data som du vill lägga till. Leta reda på och markera kryssrutan bredvid de data som ska läggas till med hjälp av listpilarna.

![Lägg till datauppsättningar](./media/how-to/how-to-add-datasets/add-datasets-side.png)

När du klickar på **Lägg till datauppsättningar**läggs datauppsättningarna till i din resurs. En ögonblicksbild måste utlösas av dina konsumenter för att de ska kunna se de nya datauppsättningarna. Om det finns anpassade inställningar för ögonblicksbilder ser konsumenterna de nya datauppsättningarna när nästa schemalagda ögonblicksbild har slutförts. Utan att bildskärmsinställningarna har konfigurerats måste konsumenten manuellt utlösa en fullständig eller inkrementell kopia av data för att ta emot uppdateringarna. Mer information om ögonblicksbilder finns i [Ögonblicksbilder](terminology.md).

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [lägger till mottagare i en befintlig dataresurs](how-to-add-recipients.md).