---
title: "Aktivera avbildningsfunktionen för Azure Event Hubs via portalen | Microsoft Docs"
description: "Använd Azure Portal för att aktivera avbildningsfunktionen i Event Hubs."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017


---

# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Aktivera avbildningsfunktionen i Event Hubs med Azure Portal

Du kan konfigurera avbildningsfunktionen med [Azure Portal](https://portal.azure.com) när du skapar händelsehubben. Du aktiverar avbildningsfunktionen genom att klicka på knappen **På** i portalbladet **Skapa händelsehubb**. Sedan konfigurerar du ett lagringskonto och en behållare genom att klicka i avsnittet **Behållare** på bladet. Eftersom tjänst-till-tjänst-autentisering används för lagring i avbildningsfunktionen i Event Hubs behöver du inte ange någon anslutningssträng för lagringen. Resursväljaren väljer automatiskt rätt resurs-URI för ditt lagringskonto. Om du använder Azure Resource Manager måste du ange den här URI:n uttryckligen som en sträng.

Standardvärdet för Tidsperiod är 5 minuter. Lägsta värde är 1 och högsta är 15. Du kan ange intervallet 10–500 MB för **Storlek**.

![][1]

## <a name="adding-capture-to-an-existing-event-hub"></a>Lägga till avbildning för en befintlig händelsehubb

Du kan konfigurera avbildning för befintliga händelsehubbar i namnrymder för Event Hubs. Funktionen är inte tillgänglig för äldre namnrymder av typen **Meddelandefunktion** eller **Blandat**. Om du vill aktivera avbildning för en befintlig händelsehubb eller ändra dina avbildningsinställningar klickar du på namnrymden för inläsning av bladet **Essentials** och sedan på den händelsehubb som du vill aktivera eller ändra avbildningsinställningar för. Klicka slutligen i avsnittet **Egenskaper** på det öppna bladet. Se bilden nedan:

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

## <a name="next-steps"></a>Nästa steg

Du kan också konfigurera avbildningsfunktionen i Event Hubs med hjälp av Azure Resource Manager-mallar. Mer information finns i [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med hjälp av en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

