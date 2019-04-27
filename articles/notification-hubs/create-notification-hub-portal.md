---
title: Skapa en Azure-meddelandehubb med hjälp av Azure-portalen | Microsoft Docs
description: I den här självstudien lär du dig att skapa en Azure SQL-meddelandehubb med hjälp av Azure-portalen.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 62e72f27e48f7bf220901f4eb36090f926724a2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682039"
---
# <a name="create-an-azure-notification-hub-in-the-azure-portal"></a>Skapa en Azure-meddelandehubb i Azure-portalen 
Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten skapar du en meddelandehubb i Azure-portalen. Det första avsnittet innehåller steg för att skapa en Notification Hubs-namnrymd och en hubb i den namnrymden. Det andra avsnittet innehåller steg för att skapa en meddelandehubb i en befintlig Notification Hubs-namnrymd. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Skapa en namnrymd och en meddelandehubb
I det här avsnittet skapar du en namnrymd och en hubb i namnrymden. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Skapa en meddelandehubb i en befintlig namnrymd
I det här avsnittet skapar du en meddelandehubb i en befintlig namnrymd. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra menyn, sök efter **Notification Hub** (Meddelandehubb), välj **stjärnan** (`*`) intill **Notification Hub Namespaces** (Namnrymder för meddelandehubb) för att lägga till den i avsnittet **FAVORITES** (Favoriter) på den vänstra menyn. Välj **Notification Hub Namespaces** (Namnrymder för meddelandehubb). 

      ![Azure-portalen – välj Namnrymder för meddelandehubb](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. På sidan **Notification Hub Namespaces** (Namnrymder för meddelandehubb) väljer du din namnrymd i listan. 

      ![Välj namnrymd i listan](./media/create-notification-hub-portal/select-namespace.png)
1. På sidan **Notification Hub Namespaces** (Namnrymder för meddelandehubb) väljer du **Add Hub** (Lägg till hubb) i verktygsfältet. 

      ![Namnrymder för meddelandehubb – knappen Lägg till hubb](./media/create-notification-hub-portal/add-hub-button.png)
4. På sidan **New Notification Hub** (Ny meddelandehubb) anger du ett namn på meddelandehubben och väljer **OK**.

      ![Sidan Ny meddelandehubb -> ange ett namn på hubben](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Välj **Meddelanden** (klockikonen) längst upp för att se status för distributionen av den nya hubben. Välj **X** i det högra hörnet för att stänga meddelandefönstret. 

      ![Distributionsmeddelande](./media/create-notification-hub-portal/deployment-notification.png)
5. Uppdatera webbplatsen **Notification Hub Namespaces** (Namnrymder för meddelandehubb) för att se den nya hubben i listan. 

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Välj din **meddelandehubb** för att se startsidan för meddelandehubben. 

      ![Azure Portal – Meddelanden -> Gå till resurser](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat en meddelandehubb. Om du vill lära dig att konfigurera hubben med inställningar för plattformsspecifikt meddelandesystem (PNS) läser du avsnittet om att [konfigurera en meddelandehubb med PNS-inställningar](configure-notification-hub-portal-pns-settings.md). 