---
title: Ändra prisnivån för Notification Hubs-namnområdet | Microsoft Docs
description: Lär dig hur du ändrar prisnivån för ett namnområde för Azure Notification Hubs.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: c572f64bdcb8846b5f73a0fee34f11c9729ee45d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885063"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Ändra prisnivån för ett namnområde med Azure notification hubs
Finns tre olika nivåer av Meddelandehubbar: **kostnadsfria**, **grundläggande**, och **standard**. Den här artikeln visar hur du ändrar prisnivån för ett Azure Notification Hubs-namnområde. 

## <a name="overview"></a>Översikt
I Azure Notification Hubs, en **hub** är den minsta resurs/enheten. Den allmänt mappas till ett program och kan innehålla ett certifikat för varje plattformsspecifika meddelandesystem som vi har stöd för appen. Programmet kan vara en hybrid eller ett ursprungligt och ett plattformsoberoende program.

En **namnområde** är en samling av meddelandehubbar. Varje namnområde består vanligtvis av hubbar som är relaterade och används för ett visst syfte. Du kan till exempel ha tre olika namnområden för utveckling, testning och produktion respektive. 

Du kan associera en prisnivå på namnområdesnivå. Notification Hubs stöder tre nivåer: **kostnadsfria**, **grundläggande**, och **standard**. Du kan använda på nivån för ett namnområde som passar dina behov. I följande avsnitt visas hur du ändrar prisnivån för ett Notification Hubs-namnområde. 

## <a name="use-azure-portal"></a>Använda Azure-portalen 
När du använder Azure-portalen kan ändra du prisnivån för ett namnområde på sidan namnområde eller en hubbsida.  När du ändrar den på en hubbsida kan ändra du faktiskt det på namnområdesnivå. Ändras prisnivån för namnområdet och alla hubs i namnområdet. 

### <a name="change-tier-on-the-namespace-page"></a>Ändra nivån på sidan namnområde
Följande procedur ger dig steg för att ändra prisnivån för ett namnområde på sidan namnområde. När du ändrar nivån för ett namnområde, tillämpas för alla hubs i namnområdet.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** på den vänstra menyn. 
3. Välj **namnområden för Meddelandehubb** i den **Internet of Things** avsnittet. Om du väljer star (`*`) bredvid texten, läggs den till det vänstra navigeringsfältet under **Favoriter**. Det hjälper dig med att komma åt sidan namnområden snabbare nästa gång och senare. När du lägger till den Favoriter, väljer **namnområden för Meddelandehubb**. 

    ![Alla tjänster -> namnområden för Meddelandehubb](./media/change-pricing-tier/all-services-nhub.png)
1. På den **namnområden för Meddelandehubb** väljer du det namnområde som du vill ändra prisnivån. 
2. På den **Notification Hub Namespace** sidan för ditt namnområde kan du se aktuell prisnivå för namnområde i den **Essentials** avsnittet. I följande bild, ser du att prisnivån för namnområdet är **kostnadsfri**. 

    ![Aktuell prisnivå på sidan namnområde](./media/change-pricing-tier/pricing-tier-before.png)
1. På den **Notification Hub Namespace** sidan för ditt namnområde väljer **prisnivå** under **hantera** avsnittet. 

    ![Välj prisnivå på sidan namnområde](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Ändra din prisnivå och klickar på den **Välj** knappen.    
7. Du ser status för nivån ändra åtgärden i den **aviseringar**. 
8. Växla till den **översikt** sidan. Bekräfta att den nya nivån visas för den **prisnivå** i den **Essentials** avsnittet.     
1. Det här steget är valfritt. Välj en hubb i namnområdet. Kontrollera att du ser samma prisnivå nivå i den **Essentials** avsnittet. Du bör se samma prisnivå för alla Hub i namnområdet. 

### <a name="change-tier-on-the-hub-page"></a>Ändra nivån på hubbsidan
Följande procedur ger dig steg för att ändra prisnivån för ett namnområde på hubbsidan. Även om du gör dessa från hubbsidan ändra du faktiskt prisnivån för namnområdet och alla hubs i namnområdet. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** på den vänstra menyn.
3. Välj **Meddelandehubbar** i den **Internet of Things** avsnittet. 
4. Välj ditt meddelande **hub**. 
5. Välj **prisnivå** på den vänstra menyn. 
6. Ändra prisnivån klicka sedan på den **Välj** knappen. Den här åtgärden ändras prissättningen Nivåinställning för det namnområde som innehåller hubben. Därför kan du se den nya prisnivån på sidan namnområde och alla sidor i hubben. 

## <a name="use-rest-api"></a>Använda REST-API
Du kan använda följande Resource Provider REST API: erna för att hämta aktuell prisnivå och uppdatera den. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Hämta aktuell prisnivå för ett namnområde
Att hämta den **nuvarande namnområdet nivån**, skicka en GET-kommando som du ser i följande exempel: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Uppdatera prisnivån för ett namnområde
Att **uppdatera namnområde nivån**, skicka ett PUT-kommando som du ser i följande exempel: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Nästa steg
Mer information om de här nivåerna och priser, finns i [prissättningen för Meddelandehubbar](https://azure.microsoft.com/pricing/details/notification-hubs/).
