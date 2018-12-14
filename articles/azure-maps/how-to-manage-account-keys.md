---
title: Så här hanterar du ditt Azure Maps-konto och dina nycklar | Microsoft Docs
description: Du kan använda Azure-portalen för att hantera ditt Azure Maps-konto och hantera dina åtkomstnycklar.
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 351bc26d6c5b3dcfa9479138f2bd523f64a2912a
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335820"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Så här hanterar du ditt Azure Maps-konto och dina nycklar

Du kan hantera ditt Azure Maps-konto och dina nycklar via Azure portal. När du har ett konto och en nyckel kan implementera du de API: er i din webbplats eller mobila program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-new-account"></a>Skapa ett nytt konto

1. Logga in på [Azure Portal](http://portal.azure.com).

2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

3. Sök efter och välj **Maps**, klicka sedan på **skapa**.

4. Ange information för ditt nya konto.

![Ange information i portalen](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Hantera nycklar på sidan

När du skapar ett konto kan få du två slumpmässigt genererat nycklar. Du kan använda nycklarna för att autentisera mot Maps-API: er när du vill hämta kartdata eller skapa en ny JavaScript map-instans.

Du hittar nycklarna i Azure-portalen. Gå till ditt konto och välj sedan **nycklar** på menyn.

![Hantera nycklar i portalen](./media/how-to-manage-account-keys/account-keys-portal.png)

Från den här sidan kan du kopiera dina nycklar eller skapa nya.

## <a name="delete-an-account"></a>Ta bort ett konto

Du kan ta bort ett konto från Azure-portalen. Gå till översiktssidan för kontot och välj **ta bort**.

![Ta bort ditt konto i portalen](./media/how-to-manage-account-keys/account-delete-portal.png)

Därefter visas en bekräftelsesida. Du kan bekräfta borttagningen av ditt konto genom att skriva dess namn.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar prisnivå för Azure Maps-konto:
> [!div class="nextstepaction"]
> [Hantera prisnivå](./how-to-manage-pricing-tier.md)

Lär dig hur du visar användningsstatistik för API för Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](./how-to-view-api-usage.md)
