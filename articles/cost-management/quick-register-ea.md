---
title: Registrera ditt Azure Enterprise-avtal med Azure Cost Management | Microsoft Docs
description: Använd ditt Enterprise-avtal för att registrera dig för Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: 0a687d9f48a068260044a6ba91d5af20e61014b6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296774"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrera ett Azure Enterprise-avtal och visa kostnadsdata

Du kan använda ditt Azure Enterprise-avtal för att registrera dig för Azure Cost Management. Registreringen ger dig åtkomst till Cloudyn-portalen. Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du kan börja visa kostnadsdata direkt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="register-with-azure-cost-management"></a>Registrera dig för Azure Cost Management

1. Klicka på **Kostnadshantering + fakturering** i listan med tjänster i Azure Portal.
2. Klicka på **Kostnadshantering** under **Översikt**  
    ![Sidan Kostnadshantering](./media/quick-register-ea/cost-mgt-billing-service.png)
3. På sidan **Kostnadshantering** klickar du på **Gå till kostnadshantering** för att öppna Cloudyn-registreringssidan i ett nytt fönster.
4. På utvärderingsregistreringssidan på Cloudyn-portalen anger du namnet på ditt företag och väljer sedan **Azure Enterprise Enrollment Administrator**.  
    ![utvärderingsregistrering](./media/quick-register-ea/trial-reg.png)
5. Ange API-nyckeln för Enterprise Portal-registreringen. Om du inte har nyckeln tillgänglig kan du klicka på länken [Enterprise Portal](https://ea.azure.com) och göra så här:
  1. Logga in på webbplatsen för Azure Enterprise och klicka på **Rapporter**, klicka på **API-åtkomstnyckel** och kopiera sedan den primära nyckeln.  
    ![EA API-nyckel](./media/quick-register-ea/ea-key.png)
  3. Gå tillbaka till registreringssidan och klistra in API-nyckeln.
6. Godkänn användningsvillkoren och validera sedan nyckeln. Klicka på **Nästa** för att tillåta att Cloudyn samlar in Azure-resursdata. Data som samlas in omfattar information om användning, prestanda, fakturering och taggar från dina prenumerationer.  
    ![nyckelvalidering](./media/quick-register-ea/ea-key-validated.png)
7. Under **Invite other stakeholders** (Bjud in andra intressenter) kan du lägga till användare genom att ange deras e-postadresser. Klicka på **Nästa** när du är klar. Beroende på storleken på Azure-registreringen kan det ta upp till 24 timmar innan all faktureringsinformation läggs till i Cloudyn.
8. Klicka på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-portalen. Nu ska din registrerade EA-kontoinformation visas på sidan **Cloud Accounts Management** (Hantering av molnkonton).

Om du vill se en självstudievideo om registrering av Enterprise-avtalet kan du se [How to Find Your EA Enrollment ID and API Key for use in Azure Cost Management](https://youtu.be/u_phLs_udig) (Hitta ditt EA-registrerings-ID och din API-nyckel som används i Azure Cost Management).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du din Azure Enterprise-avtalsinformation för att göra en registrering med Cost Management. Du loggade också in på Cloudyn-portalen och började visa kostnadsdata. Om du vill veta mer om Azure Cost Management kan du fortsätta till självstudierna om Cost Management.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
