---
title: "Göra prognoser för utgifter med Azure kostnaden Management | Microsoft Docs"
description: "Göra prognos för utgifter med historisk användning och använder data."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: d8b0cd2a3e5f9829f0844783aad22d375eb9d7a8
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="forecast-future-spending"></a>Göra prognos för framtida utgifter

Azure kostnaden hanteringen av Cloudyn hjälper dig att göra prognoser för framtida utgifter med historisk användning och använder data. Du kan använda Cloudyn rapporter för att visa alla kostnaden projektion data. Exemplen i den här självstudiekursen vägleder dig genom att granska kostnaden projektioner med hjälp av rapporter. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Göra prognos för framtida utgifter

## <a name="forecast-future-spending"></a>Göra prognos för framtida utgifter

Cloudyn innehåller kostnaden projektion rapporter som hjälper dig att göra prognoser för utgifter baserat på förbrukningen över tid. Deras primära syfte är att hjälpa dig att tillse att kostnaden trenderna inte överstiger förväntningar för din organisation. Rapporter som du använder är aktuella månad beräknad kostnad och årliga beräknad kostnad. Både visa planerade framtida utgifter om din användning förblir relativt konsekventa med dina senaste 30 dagarna för användning.

Den aktuella månaden beräknad kostnad rapporten visar kostnaderna för dina tjänster. Kostnader från början av den och den föregående månaden används för att visa den planerade kostnaden. Klicka på menyn rapporter överst i portalen **kostnaden** > **projektion och Budget** > **aktuella månad beräknad kostnad**. Följande bild visar ett exempel.

![aktuell månad planerad kostnad](./media/tutorial-forecast-spending/project-month01.png)

I det här exemplet ser du vilka tjänster har använt den mest. Kostnader för Azure var lägre än AWS kostnader. Om du vill se detaljer för projektion av kostnaden för virtuella Azure-datorer i den **Filter** väljer **Virtuella och Azure**.

![Virtuella Azure-datorns aktuella månaden planerad kostnad](./media/tutorial-forecast-spending/project-month02.png)

Följ samma grundläggande föregående stegen för att titta på månatliga kostnaden projektioner för andra tjänster som du är intresserad av.

Årliga beräknad kostnad rapporten visar extrapolerade kostnaden för dina tjänster över nästa tolv månader.

Klicka på menyn rapporter överst i portalen **kostnaden** > **projektion och Budget** > **årliga beräknad kostnad**. Följande bild visar ett exempel.

![årlig planerad kostnad rapport](./media/tutorial-forecast-spending/project-annual01.png)

I det här exemplet ser du vilka tjänster har använt den mest. Som exempel månatliga var Azure kostnader lägre än AWS kostnader. Om du vill se detaljer för projektion av kostnaden för virtuella Azure-datorer i den **Filter** väljer **Virtuella och Azure**.

![årliga planerade kostnaden för virtuella datorer](./media/tutorial-forecast-spending/project-annual02.png)

I bilden ovan är årliga planerade kostnaden för virtuella datorer i Azure $28,374.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Göra prognos för framtida utgifter


Gå vidare till nästa kurs att lära dig att hantera kostnader med kostnaden tilldelning och showback-rapporter.

> [!div class="nextstepaction"]
> [Hantera kostnader med kostnaden tilldelning och showback-rapporter](tutorial-manage-costs.md)
