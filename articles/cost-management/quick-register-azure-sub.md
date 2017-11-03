---
title: Registrera din Azure-prenumeration med Azure kostnaden Management | Microsoft Docs
description: "Använd din Azure-prenumeration för att registrera med Azure kostnaden Management med Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 4fed181a004ba6a5ea35dcbe1366e7d48342c96d
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrera ett enskilda Azure-prenumeration och visa data

Du kan använda din Azure-prenumeration för att registrera med Azure kostnaden Management med Cloudyn. Registreringen ger åtkomst till Cloudyn-portalen. Den här snabbstartsguide information registreringsprocessen behövs för att skapa en utvärderingsprenumeration Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du startar visa kostnadsdata direkt.

## <a name="log-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Skapa en utvärderingsregistrering

1. I Azure-portalen klickar du på **kostnaden Management + fakturering** i listan över tjänster.
2. Under **översikt**, klickar du på **kostnaden Management**  
    ![Kostnad sidan för hantering](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. På den **kostnaden Management** klickar du på **går du till kostnaden Management** att öppna sidan Cloudyn registrering i ett nytt fönster.
4. På sidan Cloudyn portal utvärderingsregistrering Ange företagets namn och välj sedan **Azure enskilda Prenumerationsägaren** och klicka sedan på **nästa**. Ditt kontonamn och klient-ID läggs automatiskt till formuläret.  
    ![utvärderingsregistrering](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Välj din **erbjuder ID - namnet** associerad med din prenumeration. Om du är osäker på vilken hastighet-ID är för din prenumeration kan du visa fakturan Azure och leta efter **erbjuder ID**.
6. Godkänner du användningsvillkoren och sedan validera dina uppgifter och klickar sedan på **nästa**.
7. I den **samla in ytterligare data** klickar du på **nästa** att auktorisera Cloudyn att samla in data i Azure-resurs. Data som samlas in innehåller användning, prestanda, fakturering och taggdata från dina prenumerationer.  
    ![samla in ytterligare data](./media/quick-register-azure-sub/gather-additional.png)
8. Din webbläsare går du till inloggningssidan för Cloudyn. Logga in med dina Azure-prenumeration.
9. Klicka på **går du till Cloudyn** öppna Cloudyn portal och klicka sedan på den **kontohantering** sidan du bör se din kontoinformation för Azure-prenumeration.  
    ![Kontohantering](./media/quick-register-azure-sub/accounts-mgt.png)

Om du vill se en video som självstudier om hur du registrerar din Azure-prenumeration, se [hitta ditt Directory GUID och hastighet-ID för användning i Azure kostnaden Management med Cloudyn](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten används du din Azure-prenumerationsinformation för att registrera med hantering av kostnaden. Du loggat in på portalen Cloudyn och igång visa kostnadsdata. Om du vill veta mer om Azure kostnaden Management med Cloudyn kan fortsätta att kursen för hantering av kostnaden.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
