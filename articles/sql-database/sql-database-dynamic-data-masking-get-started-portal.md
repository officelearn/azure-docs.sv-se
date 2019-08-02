---
title: 'Azure-portalen: SQL Database dynamisk data maskning | Microsoft Docs'
description: Så här kommer du igång med SQL Database dynamisk data maskning i Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: 1400a21c3fee51bb26a3271546a7553a3429b42d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568792"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Kom igång med SQL Database dynamisk data maskning med Azure Portal

Den här artikeln visar hur du implementerar [dynamisk data maskning](sql-database-dynamic-data-masking-get-started.md) med Azure Portal. Du kan också implementera dynamisk data maskning med hjälp av [Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) -cmdletar eller [REST API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurera dynamisk data maskning för databasen med hjälp av Azure Portal

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Gå till sidan Inställningar i databasen som innehåller de känsliga data som du vill maskera.
3. Klicka på panelen **dynamisk data maskning** som startar konfigurations sidan för **dynamisk data maskning** .

   * Alternativt kan du rulla ned till avsnittet **åtgärder** och klicka på **dynamisk data maskning**.

     ![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. På konfigurations sidan för **dynamisk data maskning** kan du se vissa databas kolumner som rekommendationer-motorn har flaggat för maskering. För att godkänna rekommendationerna klickar du bara på **Lägg till mask** för en eller flera kolumner och en mask skapas baserat på standard typen för den här kolumnen. Du kan ändra Maskerings funktionen genom att klicka på masknings regeln och redigera maskens fält format till ett annat format. Se till att klicka på **Spara** för att spara inställningarna.

    ![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Om du vill lägga till en mask för en kolumn i databasen går du till början av sidan konfiguration av **dynamisk data maskning** och klickar på **Lägg till mask** för att öppna sidan **Lägg till masknings regel** konfiguration.

    ![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Välj **schemat**, **tabellen** och **kolumnen** för att definiera det angivna fältet för maskering.
7. Välj ett **maskande fält format** i listan över känsliga data masknings kategorier.

    ![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Klicka på **Spara** på sidan data masking-regel för att uppdatera uppsättningen masknings regler i den dynamiska data masking-principen.
9. Ange de SQL-användare eller AAD-identiteter som ska undantas från maskering och som har åtkomst till de avmaskerade känsliga data. Detta bör vara en semikolonavgränsad lista med användare. Användare med administratörs behörighet har alltid till gång till de ursprungliga avmaskerade data.

    ![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > För att göra det så att program lagret kan visa känsliga data för program privilegierade användare, lägger du till den SQL-användare eller AAD-identitet som programmet använder för att fråga databasen. Vi rekommenderar starkt att listan innehåller ett minimalt antal privilegierade användare för att minimera exponeringen av känsliga data.

10. Klicka på **Spara** på konfigurations sidan data maskning för att spara den nya eller uppdaterade masknings principen.

## <a name="next-steps"></a>Nästa steg

* En översikt över dynamisk data maskning finns i [dynamisk](sql-database-dynamic-data-masking-get-started.md)datamaskering.
* Du kan också implementera dynamisk data maskning med hjälp av [Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) -cmdletar eller [REST API](https://docs.microsoft.com/rest/api/sql/).
