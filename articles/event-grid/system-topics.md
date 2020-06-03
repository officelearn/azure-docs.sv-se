---
title: System avsnitt i Azure Event Grid
description: Beskriver system avsnitt i Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: 67746ebd8a16eb02b8f02d238b0e3c0125989189
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308276"
---
# <a name="system-topics-in-azure-event-grid"></a>System avsnitt i Azure Event Grid
Azure Event Grids tjänsten skapar system ämnen när du skapar en första händelse prenumeration för en Azure-händelse källa. För närvarande skapar Event Grid inte system ämnen för ämnes källor som skapades före Mar, 15, 2020. För alla ämnes källor som du skapade på eller efter det här datumet skapas automatiskt system ämnen i Event Grid. I den här artikeln beskrivs **system ämnen** i Azure Event Grid.

> [!NOTE]
> Den här funktionen är för närvarande inte aktive rad för Azure Government molnet. 

## <a name="overview"></a>Översikt
När du skapar en första händelse prenumeration för en Azure-händelseloggen, till exempel Azure Storage konto, skapar etablerings processen för prenumerationen en ytterligare resurs av typen **Microsoft. EventGrid/systemTopics**. När den sista händelse prenumerationen på Azures händelse källa tas bort, tas system avsnittet bort automatiskt.

System avsnittet är inte tillämpligt för anpassade ämnes scenarier, det vill säga Event Grid ämnen och Event Grid domäner. 

## <a name="name"></a>Name 
När du tidigare skapade en prenumeration för en händelse som aktive ras av Azure-källor, skapade Event Grid tjänsten automatiskt ett system avsnitt med ett **slumpmässigt genererat namn**. Nu kan du ange ett namn för system avsnittet när du skapar avsnittet i Azure Portal. Du kan använda den här system ämnes resursen för att identifiera statistik och diagnostikloggar.

## <a name="location"></a>Location
För Azure Event-källor som finns i en speciell region/plats skapas system ämne på samma plats som Azure-händelseloggen. Om du till exempel skapar en händelse prenumeration för en Azure Blob Storage i östra USA skapas avsnittet system i USA, östra. För globala Azure-händelseloggar som Azure-prenumerationer, resurs grupper eller Azure Maps, skapar Event Grid system-avsnittet i den **globala** platsen. 

## <a name="resource-group"></a>Resursgrupp 
I allmänhet skapas system ämne i samma resurs grupp som Azures händelse källa. För händelse prenumerationer som skapats i Azure-prenumerationens omfattning skapas system avsnittet under resurs gruppens **standard-EventGrid**. Om resurs gruppen inte finns skapar Azure Event Grid den innan du skapar system avsnittet. 

När du försöker ta bort resurs gruppen med lagrings kontot visas avsnittet system i listan över resurser som påverkas.  

![Ta bort resursgrupp](./media/system-topics/delete-resource-group.png)


## <a name="next-steps"></a>Nästa steg
Se följande artikel: [skapa, Visa och hantera system ämnen](create-view-manage-system-topics.md).