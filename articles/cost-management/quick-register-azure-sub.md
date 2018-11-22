---
title: Registrera din Azure-prenumeration med Cloudyn | Microsoft Docs
description: Du registrerar dig för Cloudyn med din Azure-prenumeration.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 4b0c0a6fdf8d84b6519d1228f148342b8486c282
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276405"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrera en enskild Azure-prenumeration och visa kostnadsdata

Du registrerar dig för Cloudyn med din Azure-prenumeration. Registreringen ger dig åtkomst till Cloudyn-portalen. Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du kan börja visa kostnadsdata direkt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registrera dig för Cloudyn

1. Klicka på **Kostnadshantering + fakturering** i listan med tjänster i Azure Portal.
2. Klicka på **Cloudyn** under **Översikt**  
    ![Cloudyn-sida](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. På sidan **Kostnadshantering** klickar du på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-registreringssidan i ett nytt fönster.
4. På utvärderingsregistreringssidan på Cloudyn-portalen anger du namnet på ditt företag och väljer sedan **Azure Individual Subscription Owner** och klickar sedan på **Nästa**. Ditt kontonamn och klient-ID läggs automatiskt till i formuläret.  
    ![Utvärderingsregistrering](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Välj det **erbjudande-ID – namn** som är associerat med din prenumeration. Om du är osäker på vilket ID du har för din prenumeration kan du se **erbjudande-ID** på din Azure-faktura.
6. Godkänn användningsvillkoren och validera sedan dina uppgifter och klicka sedan på **Nästa**.
7. Klicka på **Nästa** på sidan **Gather additional data** (Samla in ytterligare data) för att tillåta att Cloudyn samlar in Azure-resursdata. Data som samlas in omfattar information om användning, prestanda, fakturering och taggar från dina prenumerationer.  
    ![Samla in ytterligare data](./media/quick-register-azure-sub/gather-additional.png)
8. Din webbläsare tar dig till inloggningssidan för Cloudyn. Logga in med dina Azure-autentiseringsuppgifter.
9. Klicka på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-portalen. Nu ska kontoinformationen för din Azure-prenumeration visas på sidan **Accounts Management** (Kontohantering).  
    ![Kontohantering](./media/quick-register-azure-sub/accounts-mgt.png)

Vill du se en video om hur du registrerar din Azure-prenumeration kan du titta på [Finding your Directory GUID and Rate ID for use in Cloudyn](https://youtu.be/PaRjnyaNGMI) (Hitta ditt unika katalog-ID och tariff-ID för användning i Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du din Azure-prenumerationsinformation för att göra en registrering med Cloudyn. Du loggade också in på Cloudyn-portalen och började visa kostnadsdata. Om du vill veta mer om Cloudyn kan du fortsätta till självstudien för Cloudyn.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
