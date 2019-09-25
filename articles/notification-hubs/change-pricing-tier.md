---
title: Ändra pris nivå för Notification Hubs namnrymd | Microsoft Docs
description: Lär dig hur du ändrar pris nivån för ett Azure Notification Hubs-namnområde.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 679e8b58e5876d5460b426e694df85ded992a36d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212559"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Ändra pris nivå för ett namn område för Azure Notification Hub
Notification Hubs erbjuds på tre nivåer: **kostnads fri**, **Basic**och **standard**. Den här artikeln visar hur du ändrar pris nivån för ett Azure Notification Hubs-namnområde. 

## <a name="overview"></a>Översikt
I Azure Notification Hubs är en **hubb** den minsta resursen/entiteten. Den mappar vanligt vis till ett program och kan innehålla ett certifikat för varje Plattformsspecifikt meddelandesystem vi stöder för appen. Programmet kan vara en hybrid eller ett inbyggt program och ett plattforms oberoende program.

Ett **namn område** är en samling Notification Hub. Varje namnrymd består vanligt vis av hubbar som är relaterade till och används för ett specifikt syfte. Du kan till exempel ha tre olika namn rymder för utvecklings-, test-respektive produktions syfte. 

Du kan associera en pris nivå på namn områdes nivån. Notification Hubs stöder tre nivåer: **kostnads fri**, **Basic**och **standard**. Du kan använda nivån för ett namn område som passar dina krav. I följande avsnitt visas hur du ändrar pris nivån för ett Notification Hubs namn område. 

## <a name="use-azure-portal"></a>Använda Azure-portalen 
När du använder Azure Portal kan du ändra pris nivån för ett namn område på namn områdes sidan eller en nav sida.  När du ändrar den på en nav-sida, ändrar du faktiskt den på namn områdes nivån. Den ändrar pris nivån för namn området och alla hubbar i namn området. 

### <a name="change-tier-on-the-namespace-page"></a>Ändra nivå på namn områdes Sidan
Följande procedur visar hur du ändrar pris nivån för ett namn område på namn områdes sidan. När du ändrar nivån för en namnrymd, gäller den för alla hubbar i namn området.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** på den vänstra menyn. 
3. Välj **namn rymder för Notification Hub** i avsnittet **Sakernas Internet** . Om du väljer Star (`*`) bredvid texten läggs den till i det vänstra navigerings fältet under **Favoriter**. Det hjälper dig att komma åt sidan namn områden snabbare nästa gång. När du har lagt till den i favoriter väljer du **namn områden för Notification Hub**. 

    ![Alla tjänster-> Notification Hub-namnrymder](./media/change-pricing-tier/all-services-nhub.png)
1. På sidan **namn områden för Notification Hub** väljer du det namn område som du vill ändra pris nivån för. 
2. På sidan **namn område för meddelande hubb** för ditt namn område kan du se den aktuella pris nivån för namn området i avsnittet **Essentials** . I följande bild kan du se att pris nivån för namn området är **kostnads fri**. 

    ![Aktuell pris nivå på namn områdes Sidan](./media/change-pricing-tier/pricing-tier-before.png)
1. På namn områdes sidan för **Notification Hub** i namn området väljer du **pris nivå** under avsnittet **Hantera** . 

    ![Välj pris nivå på namn områdes Sidan](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Ändra din pris nivå och klicka på knappen **Välj** .    
7. Du ser status för nivå ändrings åtgärden i **aviseringarna**. 
8. Växla till **översikts** sidan. Bekräfta att den nya nivån visas för fältet **pris nivå** i avsnittet **Essentials** .     
1. Det här steget är valfritt. Välj valfri hubb i namn området. Bekräfta att du ser samma pris nivå i avsnittet **Essentials** . Du bör se samma pris nivå för alla hubbar i namn området. 

### <a name="change-tier-on-the-hub-page"></a>Ändra nivå på NAV Sidan
Följande procedur ger dig anvisningar om hur du ändrar pris nivån för ett namn område på hubb sidan. Även om du utför de här stegen från sidan hubb, ändrar du faktiskt pris nivå för namn området och alla hubbar i namn området. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** på den vänstra menyn.
3. Välj **Notification Hubs** i avsnittet **Sakernas Internet** . 
4. Välj Notification **Hub**. 
5. Välj **pris nivå** på den vänstra menyn. 
6. Ändra pris nivån och klicka på knappen **Välj** . Den här åtgärden ändrar pris nivå inställningen för det namn område som innehåller hubben. Så visas den nya pris nivån på namn områdes sidan och alla nav sidor. 

## <a name="use-rest-api"></a>Använda REST-API
Du kan använda följande resurs leverantör REST-API: er för att hämta den aktuella pris nivån och uppdatera den. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Hämta aktuell pris nivå för ett namn område
Om du vill hämta den **aktuella namn områdes nivån**skickar du ett get-kommando som visas i följande exempel: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Uppdatera pris nivån för ett namn område
Du **uppdaterar namn områdes nivån**genom att skicka ett placera-kommando som visas i följande exempel: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Nästa steg
Mer information om dessa nivåer och priser finns [Notification Hubs prissättning](https://azure.microsoft.com/pricing/details/notification-hubs/).
