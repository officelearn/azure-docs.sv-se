---
title: Hantera ditt Azure Maps-konto och dina nycklar | Microsoft Docs
description: Du kan använda Azure-portalen för att hantera ditt Azure Maps-konto och hantera dina åtkomstnycklar.
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 81746279e935f9fa5b6ef00bc8d31e75f4d3bd20
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103052"
---
# <a name="manage-your-azure-maps-account-and-keys"></a>Hantera dina Azure Maps-konto och nycklar

Du kan hantera ditt Azure Maps-konto och dina nycklar via Azure portal. När du har ett konto och en nyckel kan implementera du de API: er i din webbplats eller mobila program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-new-account"></a>Skapa ett nytt konto

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.

3. Sök efter och välj **Maps**. Välj sedan **Skapa**.

4. Ange information för ditt nya konto.

![Ange information i portalen](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Hantera nycklar på sidan

När du skapar ett konto kan få du två slumpmässigt genererat nycklar. Använd för att autentisera mot Azure Maps-API: er för att hämta kartdata eller skapa en ny JavaScript map-instans.

Du hittar nycklarna i Azure-portalen. Navigera till ditt konto. Välj sedan **nycklar** på menyn.

![Hantera nycklar i portalen](./media/how-to-manage-account-keys/account-keys-portal.png)

Från den här sidan kan du kopiera dina nycklar eller skapa nya.

## <a name="delete-an-account"></a>Ta bort ett konto

Du kan ta bort ett konto från Azure-portalen. Gå till översiktssidan för kontot och välj **ta bort**.

![Ta bort ditt konto i portalen](./media/how-to-manage-account-keys/account-delete-portal.png)

Då visas en bekräftelsesida. Du kan bekräfta borttagningen av ditt konto genom att skriva dess namn.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att hantera ett Azure Maps-konto som prisnivå:
    > [!div class="nextstepaction"] 
    > [Hantera en prisnivå](./how-to-manage-pricing-tier.md)

* Lär dig hur du visar användningsstatistik för API för Azure Maps-konto:
    > [!div class="nextstepaction"] 
    > [Visa användningsstatistik](./how-to-view-api-usage.md)
