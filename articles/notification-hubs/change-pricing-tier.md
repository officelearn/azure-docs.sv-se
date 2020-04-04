---
title: Ändra prisnivån för namnområdet Notification Hubs | Microsoft-dokument
description: Lär dig hur du ändrar prisnivån för ett namnområde för Azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656473"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Ändra prisnivån för ett namnområde för Azure-meddelandehubbar

Meddelandehubbar erbjuds i tre nivåer: **gratis,** **grundläggande**och **standard.** Den här artikeln visar hur du ändrar prisnivån för ett namnområde för Azure Notification Hubs.

## <a name="overview"></a>Översikt

I Azure Notification Hubs är ett nav den minsta resursen/entiteten. Det mappar i allmänhet till ett program och kan innehålla ett certifikat för varje plattformsmeddelandesystem (PNS) som vi stöder för appen. Programmet kan vara en hybrid, eller en inbyggd och en plattformsoberoende program.

Ett **namnområde** är en samling meddelandehubbar. Varje namnområde består vanligtvis av hubbar som är relaterade och används för ett visst ändamål. Du kan till exempel ha tre olika namnområden för utvecklings-, test- respektive produktionsändamål.

Du kan associera ett namnområde med de **kostnadsfria,** **grundläggande**eller **standardprisnivåerna.** Du kan använda nivån för ett namnområde som passar dina behov. I följande avsnitt visas hur du ändrar prisnivån för ett namnområde för meddelandehubbar.

## <a name="use-azure-portal"></a>Använda Azure-portalen

När du använder Azure-portalen kan du ändra prisnivån för ett namnområde på namnområdessidan eller på en hubbsida. När du ändrar det på en hubbsida ändrar du den faktiskt på namnområdesnivå. Prisnivån för namnområdet och alla hubbar i namnområdet ändras.

### <a name="change-tier-on-the-namespace-page"></a>Ändra nivå på namnområdessidan

Följande procedur visar hur du ändrar prisnivån för ett namnområde på namnområdessidan. När du ändrar nivån för ett namnområde gäller den för alla hubbar i namnområdet.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra menyn.
3. Välj Namnområden för **meddelandehubben** i avsnittet **Sakernas Internet.** Om du väljer asterisken (`*`) bredvid texten läggs den till i det vänstra **navigeringsfältet**under FAVORITER . Det hjälper dig att komma åt namnrymdssidan snabbare framöver. När du har lagt till den i FAVORITER väljer du **Namnområden för meddelandehubben**.

    ![Alla tjänster -> Namnområden för meddelandehubben](./media/change-pricing-tier/all-services-nhub.png)

4. På sidan Namnområden för **meddelandehubben** väljer du det namnområde som du vill ändra prisnivån för.
5. På sidan **Meddelandehubbnamnområde** för ditt namnområde kan du se den aktuella prisnivån för namnområdet i avsnittet **Essentials.** I följande bild kan du se att prisnivån för namnområdet är **ledig**.

    ![Aktuell prisnivå på namnområdessidan](./media/change-pricing-tier/pricing-tier-before.png)

6. På sidan Namnområde för **meddelandehubben** för namnområdet väljer du **Prisnivå** under **Hantera.**

    ![Välj prisnivå på namnområdessidan](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Ändra prisnivån och klicka sedan på knappen **Välj.**
8. Du kan se status för nivåändringsåtgärden i **aviseringarna**.
9. Växla till sidan **Översikt.** Bekräfta att den nya nivån visas för fältet **Prisnivå** i avsnittet **Essentials.**
10. Det här steget är valfritt. Markera ett nav i namnområdet. Bekräfta att du ser samma prisnivå i avsnittet **Essentials.** Du bör se samma prisnivå för alla hubbar i namnområdet.

### <a name="change-tier-on-the-hub-page"></a>Ändra nivå på hubbsidan

Följande procedur visar hur du ändrar prisnivån för ett namnområde på hubbsidan. Även om du gör dessa steg från hubbsidan ändrar du faktiskt prisnivån för namnområdet och alla hubbar i namnområdet.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** på den vänstra menyn.
3. Välj **Meddelandehubbar** i avsnittet **Sakernas Internet.**
4. Välj **meddelandehubben**.
5. Välj **Prisnivå** på den vänstra menyn.
6. Ändra prisnivån och klicka på knappen **Välj.** Den här åtgärden ändrar prisnivåinställningen för namnområdet som innehåller navet. Så ser du den nya prisnivån på namnområdessidan och alla hubbsidor.

> [!NOTE]
> Alla prisnivåändringar träder i kraft omedelbart.

## <a name="use-rest-api"></a>Använda REST-API

Du kan använda följande REST-API:er för resursprovider för att hämta den aktuella prisnivån och uppdatera den.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Hämta aktuell prisnivå för ett namnområde

Om du vill hämta den aktuella namnområdesnivån skickar du ett GET-kommando, som visas i följande exempel:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Uppdatera prisnivå för ett namnområde

Om du vill uppdatera namnområdesnivån skickar du ett PUT-kommando, som visas i följande exempel:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Nästa steg

Mer information om dessa nivåer och priser finns i [Priser för Meddelandehubbar](https://azure.microsoft.com/pricing/details/notification-hubs/).
