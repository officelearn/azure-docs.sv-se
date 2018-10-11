---
title: Registrera ditt Azure Enterprise-avtal med Cloudyn | Microsoft Docs
description: Använd ditt Enterprise-avtal för att registrera dig för Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/14/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: bed1ea3785c35d1053a0ff2147c3bdd797e28581
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996646"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrera ett Azure Enterprise-avtal och visa kostnadsdata

Du använder ditt Azure Enterprise-avtal för att registrera dig för Cloudyn. Registreringen ger dig åtkomst till Cloudyn-portalen. Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du kan börja visa kostnadsdata direkt.

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](overview-cost-mgt.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="register-with-cloudyn"></a>Registrera med Cloudyn

1. Klicka på **Kostnadshantering + fakturering** i listan med tjänster i Azure Portal.
2. Klicka på **Cloudyn** under **Översikt**  
    ![Cloudyn-sida](./media/quick-register-ea/cost-mgt-billing-service.png)
3. På sidan **Cloudyn** klickar du på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-registreringssidan i ett nytt fönster.
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

Om du vill se en självstudievideo om registrering av Enterprise-avtalet kan du titta på [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig) (Hitta ditt EA-registrerings-ID och din API-nyckel som används i Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du din Azure Enterprise-avtalsinformation för att göra en registrering med Cloudyn. Du loggade också in på Cloudyn-portalen och började visa kostnadsdata. Om du vill veta mer om Cloudyn kan du fortsätta till självstudien för Cloudyn.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
