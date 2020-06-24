---
title: 'Azure Portal: dynamisk data maskning'
description: Så här kommer du igång med Azure SQL Database dynamisk data maskning i Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 12a187b29737ba9388d9b33ac8bcfedf1339c059
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253791"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Kom igång med SQL Database dynamisk data maskning med Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln visar hur du implementerar [dynamisk data maskning](dynamic-data-masking-overview.md) med Azure Portal. Du kan också implementera dynamisk data maskning med hjälp av [Azure SQL Database-cmdletar](https://docs.microsoft.com/powershell/module/az.sql/) eller [REST API](https://docs.microsoft.com/rest/api/sql/).

> [!NOTE]
> Den här funktionen kan inte ställas in med Portal för Azure-Synapse (Använd PowerShell eller REST API) eller SQL-hanterad instans. Mer information finns i [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurera dynamisk data maskning för databasen med hjälp av Azure Portal

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com) .
2. Gå till sidan Inställningar i databasen som innehåller de känsliga data som du vill maskera.
3. Klicka på bladet **dynamisk data maskning** under **säkerhets** avsnittet i databasen.

   ![Navigeringsfönster](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. På konfigurations sidan för **dynamisk data maskning** kan du se vissa databas kolumner som rekommendationer-motorn har flaggat för maskering. För att godkänna rekommendationerna klickar du bara på **Lägg till mask** för en eller flera kolumner och en mask skapas baserat på standard typen för den här kolumnen. Du kan ändra Maskerings funktionen genom att klicka på masknings regeln och redigera maskens fält format till ett annat format. Se till att klicka på **Spara** för att spara inställningarna.

    ![Navigeringsfönster](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Om du vill lägga till en mask för en kolumn i databasen går du till början av sidan konfiguration av **dynamisk data maskning** och klickar på **Lägg till mask** för att öppna sidan **Lägg till masknings regel** konfiguration.

    ![Navigeringsfönster](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Välj **schemat**, **tabellen** och **kolumnen** för att definiera det angivna fältet för maskering.
7. **Välj hur du vill maskera** från listan över känsliga data masknings kategorier.

    ![Navigeringsfönster](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Klicka på **Lägg till** på sidan data masking-regel för att uppdatera uppsättningen masknings regler i den dynamiska data masking-principen.
9. Ange de SQL-användare eller Azure Active Directory (Azure AD)-identiteter som ska undantas från maskering och som har åtkomst till de avmaskerade känsliga data. Detta bör vara en semikolonavgränsad lista med användare. Användare med administratörs behörighet har alltid till gång till de ursprungliga avmaskerade data.

    ![Navigeringsfönster](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > För att göra det så att program lagret kan visa känsliga data för program privilegierade användare, lägger du till den SQL-användare eller Azure AD-identitet som programmet använder för att fråga databasen. Vi rekommenderar starkt att listan innehåller ett minimalt antal privilegierade användare för att minimera exponeringen av känsliga data.

10. Klicka på **Spara** på konfigurations sidan data maskning för att spara den nya eller uppdaterade masknings principen.

## <a name="next-steps"></a>Nästa steg

- En översikt över dynamisk data maskning finns i [dynamisk datamaskering](dynamic-data-masking-overview.md).
- Du kan också implementera dynamisk data maskning med hjälp av [Azure SQL Database-cmdletar](https://docs.microsoft.com/powershell/module/az.sql/) eller [REST API](https://docs.microsoft.com/rest/api/sql/).
