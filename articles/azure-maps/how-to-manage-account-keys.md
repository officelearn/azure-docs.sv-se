---
title: Så här hanterar du ditt Azure Maps-konto och nycklar | Microsoft Docs
description: Du kan använda Azure-portalen för att hantera Azure Maps-konto och hantera dina åtkomstnycklar.
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 091b91b51f7c880a1edbf65a0e2c83560d4bc38b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Så här hanterar du ditt Azure Maps-konto och nycklar

Du kan hantera dina Azure Maps-konto och nycklar via Azure-portalen. När du har ett konto och en nyckel kan implementera du API: er i din webbplats eller mobila program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-new-account"></a>Skapa ett nytt konto

1. Logga in på [Azure-portalen](http://portal.azure.com).

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

2. Söka efter och välja **Maps**, klicka på **skapa**.

3. Ange information för ditt nya konto. 

![Ange information i portalen](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Hantera nycklar på kontosidan

När du skapar ett konto kan hämta du två slumpmässigt genererat nycklar. Du kan använda nycklarna för att autentisera mot Maps-API: er när du vill hämta kartdata eller skapa en ny instans av JavaScript kartan. 

Du hittar dina nycklar i Azure-portalen. Navigera till ditt konto och välj sedan **nycklar** på menyn.

![Hantera nycklar i portalen](./media/how-to-manage-account-keys/account-keys-portal.png)

Från den här sidan kan du kopiera dina nycklar eller skapa nya. 

## <a name="delete-an-account"></a>Ta bort ett konto

Du kan ta bort ett konto i Azure Portal. Navigera till översiktssidan för kontot och markera **ta bort**.

![Ta bort ditt konto i portalen](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder den [mappar hanterings-API](https://docs.microsoft.com/rest/api/maps-management/accounts) för att skapa, uppdatera och ta bort Maps-konton. 