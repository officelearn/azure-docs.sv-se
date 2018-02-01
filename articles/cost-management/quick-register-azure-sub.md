---
title: Registrera din Azure-prenumeration med Azure Cost Management | Microsoft Docs
description: "Använd din Azure-prenumeration för att registrera dig med Azure Cost Management från Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 666919e37ffa3bb039fc9387737b8e6c3789311a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrera en enskild Azure-prenumeration och visa kostnadsdata

Du använder din Azure-prenumeration för registreringen med Azure Cost Management från Cloudyn. Registreringen ger dig åtkomst till Cloudyn-portalen. Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du kan börja visa kostnadsdata direkt.

## <a name="log-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Skapa en utvärderingsregistrering

1. Klicka på **Kostnadshantering + fakturering** i listan med tjänster i Azure Portal.
2. Klicka på **Kostnadshantering** under **Översikt**  
    ![Kostnadshanteringssidan](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. På sidan **Kostnadshantering** klickar du på **Gå till kostnadshantering** för att öppna Cloudyn-registreringssidan i ett nytt fönster.
4. På utvärderingsregistreringssidan på Cloudyn-portalen anger du namnet på ditt företag och väljer sedan **Azure Individual Subscription Owner** och klickar sedan på **Nästa**. Ditt kontonamn och klient-ID läggs automatiskt till i formuläret.  
    ![utvärderingsregistrering](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Välj det **erbjudande-ID – namn** som är associerat med din prenumeration. Om du är osäker på vilket ID du har för din prenumeration kan du se **erbjudande-ID** på din Azure-faktura.
6. Godkänn användningsvillkoren och validera sedan dina uppgifter och klicka sedan på **Nästa**.
7. Klicka på **Nästa** på sidan **Gather additional data** (Samla in ytterligare data) för att tillåta att Cloudyn samlar in Azure-resursdata. Data som samlas in omfattar information om användning, prestanda, fakturering och taggar från dina prenumerationer.  
    ![samla in ytterligare data](./media/quick-register-azure-sub/gather-additional.png)
8. Din webbläsare tar dig till inloggningssidan för Cloudyn. Logga in med dina Azure-autentiseringsuppgifter.
9. Klicka på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-portalen. Nu ska kontoinformationen för din Azure-prenumeration visas på sidan **Accounts Management** (Kontohantering).  
    ![Kontohantering](./media/quick-register-azure-sub/accounts-mgt.png)

Om du vill titta på en video om hur du registrerar din Azure-prenumeration kan du se [Finding your Directory GUID and Rate ID for use in Azure Cost Management by Cloudyn](https://youtu.be/PaRjnyaNGMI) (Hitta ditt unika katalog-ID och tariff-ID för användning i Azure Cost Management från Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du din Azure-prenumerationsinformation för att göra en registrering med Cost Management. Du loggade också in på Cloudyn-portalen och började visa kostnadsdata. Om du vill veta mer om Azure Cost Management från Cloudyn kan du fortsätta till självstudierna om Cost Management.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
