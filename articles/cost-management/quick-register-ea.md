---
title: Registrera Azure Enterprise-avtal med hantering av Azure kostnad | Microsoft Docs
description: "Använda Enterprise-avtal för att registrera med Azure kostnaden Management med Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 41a9df712b07253d9f5f9db8542fb9917592320f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrera en Azure Enterprise-avtal och visa data

Du kan använda Azure Enterprise-avtal för att registrera med Azure kostnaden Management med Cloudyn. Registreringen ger åtkomst till Cloudyn-portalen. Den här snabbstartsguide information registreringsprocessen behövs för att skapa en utvärderingsprenumeration Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du startar visa kostnadsdata direkt.

## <a name="log-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Skapa en utvärderingsregistrering

1. I Azure-portalen klickar du på **kostnaden Management + fakturering** i listan över tjänster.
2. Under **översikt**, klickar du på **kostnaden Management**  
    ![Kostnad sidan för hantering](./media/quick-register-ea/cost-mgt-billing-service.png)
3. På den **kostnaden Management** sidan **går du till kostnaden Management** att öppna sidan Cloudyn registrering i ett nytt fönster.
4. På sidan Cloudyn portal utvärderingsregistrering Ange företagets namn och välj sedan **Azure registrering företagsadministratör**.  
    ![utvärderingsregistrering](./media/quick-register-ea/trial-reg.png)
5. Ange Enterprise Portal registrering API-nyckel. Om du inte har nyckeln praktiska, klickar du på den [Enterprise Portal](https://ea.azure.com) länka och utför följande steg:
  1. Logga in på Azure Enterprise-webbplats och klickar på **rapporter**, klickar du på **API åtkomstnyckel** och sedan kopiera primärnyckel.  
    ![EA API-nyckel](./media/quick-register-ea/ea-key.png)
  3. Gå tillbaka till registreringssidan för och klistra in i din API-nyckel.
6. Godkänner du användningsvillkoren och sedan validera din nyckel. Klicka på **nästa** att auktorisera Cloudyn att samla in data i Azure-resurs. Data som samlas in innehåller användning, prestanda, fakturering och taggdata från dina prenumerationer.  
    ![nyckel-verifiering](./media/quick-register-ea/ea-key-validated.png)
7. Under **Bjud in andra berörda parter**, du kan lägga till användare genom att skriva sina e-postadresser. När du är klar klickar du på **nästa**. Det tar ungefär två timmar för alla dina faktureringsinformation att läggs till Cloudyn.
8. Klickar du på **går du till Cloudyn** att öppna Cloudyn portal och klicka sedan på den **konton Molnhantering** sidan du bör se din registrerade EA-kontoinformation.

Om du vill se en video som självstudier om hur du registrerar Enterprise-avtal, se [hitta din EA registrering ID och API-nyckeln för användning i Azure kostnaden Management med Cloudyn](https://youtu.be/u_phLs_udig).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten används du din Azure-Företagsavtal information för att registrera med hantering av kostnaden. Du loggat in på portalen Cloudyn och igång visa kostnadsdata. Om du vill veta mer om Azure kostnaden Management med Cloudyn kan fortsätta att kursen för hantering av kostnaden.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
