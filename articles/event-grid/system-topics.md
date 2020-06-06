---
title: System avsnitt i Azure Event Grid
description: Beskriver system avsnitt i Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c34ada2c7437d8415b52c68fb66103ec3aa81d95
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456937"
---
# <a name="system-topics-in-azure-event-grid"></a>System avsnitt i Azure Event Grid
System avsnitt är de avsnitt som skapats för Azure-tjänster som Azure Storage och Azure Event Hubs. Du kan skapa system ämnen med hjälp av mallen Azure Portal, PowerShell, CLI eller Azure Resource Manager.  

> [!NOTE]
> Den här funktionen är för närvarande inte aktive rad för Azure Government molnet. 

## <a name="create-system-topics"></a>Avsnitt om att skapa system
Du kan skapa ett system ämne på två sätt: 

- Skapa ett system ämne för en Azure-resurs och skapa sedan en händelse prenumeration för det system ämnet.
- Skapa en händelse prenumeration på en Azure-resurs, som internt skapar ett system ämne för dig.

När du använder den första metoden tas inte system avsnittet bort automatiskt när den sista händelse prenumerationen för det system avsnittet tas bort. När du använder den andra metoden tas system avsnittet bort automatiskt när den sista händelse prenumerationen tas bort. 

Detaljerade anvisningar om hur du skapar system ämnen med hjälp av Azure Portal, PowerShell eller CLI finns i följande artiklar:

- [Skapa, Visa och hantera system ämnen med hjälp av Azure Portal](create-view-manage-system-topics.md).
- [Skapa, Visa och hantera Event Grid system ämnen med hjälp av Azure CLI](create-view-manage-system-topics-cli.md)
- [Skapa Event Grid system ämnen med hjälp av Azure Resource Manager mallar](create-view-manage-system-topics-arm.md)

## <a name="system-topic-name"></a>Namn på system ämne
Event Grid inte att skapa system ämnen för Azure-källor (Azure Storage, Azure Event Hubs osv.) som skapades före Mar, 15, 2020. Om du har skapat en prenumeration för en händelse som aktive ras av Azure-källor mellan 3/15/2020 och 6/2/2020, skapade Event Grid tjänsten automatiskt ett system avsnitt med ett **slumpmässigt genererat namn**. Efter 6/2/2020 kan du ange ett namn för system-avsnittet när du skapar en händelse prenumeration för en Azure-källa. 

## <a name="location-and-resource-group"></a>Plats och resurs grupp
För Azure Event-källor som finns i en speciell region/plats skapas system ämne på samma plats som Azure-händelseloggen. Om du till exempel skapar en händelse prenumeration för en Azure Blob Storage i östra USA skapas avsnittet system i USA, östra. För globala Azure-händelseloggar som Azure-prenumerationer, resurs grupper eller Azure Maps, skapar Event Grid system-avsnittet i den **globala** platsen. 

I allmänhet skapas system ämne i samma resurs grupp som Azures händelse källa. För händelse prenumerationer som skapats i Azure-prenumerationens omfattning skapas system avsnittet under resurs gruppens **standard-EventGrid**. Om resurs gruppen inte finns skapar Azure Event Grid den innan du skapar system avsnittet. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Skapa, Visa och hantera system ämnen med hjälp av Azure Portal](create-view-manage-system-topics.md).
- [Skapa, Visa och hantera Event Grid system ämnen med hjälp av Azure CLI](create-view-manage-system-topics-cli.md)
- [Skapa Event Grid system ämnen med hjälp av Azure Resource Manager mallar](create-view-manage-system-topics-arm.md)
