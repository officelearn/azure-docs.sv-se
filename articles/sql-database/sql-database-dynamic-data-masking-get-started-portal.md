---
title: 'Azure-portal: Dynamisk datamaskering'
description: Komma igång med dynamisk datamaskering i SQL Database i Azure-portalen
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722126"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Komma igång med dynamisk datamaskering i SQL Database med Azure-portalen

Den här artikeln visar hur du implementerar [dynamisk datamaskering](sql-database-dynamic-data-masking-get-started.md) med Azure-portalen. Du kan också implementera dynamisk datamaskering med [Azure SQL Database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) eller [REST API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurera dynamisk datamaskering för databasen med Hjälp av Azure-portalen

1. Starta Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Navigera till inställningssidan i databasen som innehåller de känsliga data som du vill maskera.
3. Klicka på panelen **Dynamisk datamaskering** som startar konfigurationssidan för dynamisk **datamaskering.**

   * Du kan också bläddra ned till avsnittet **Operationer** och klicka på **Dynamisk datamaskering**.

     ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. På konfigurationssidan **för dynamisk datamaskering** kan du se några databaskolumner som rekommendationermotorn har flaggat för maskering. Om du vill acceptera rekommendationerna klickar du bara på **Lägg till mask** för en eller flera kolumner och en mask skapas baserat på standardtypen för den här kolumnen. Du kan ändra maskeringsfunktionen genom att klicka på maskeringsregeln och redigera maskeringsfältformatet till ett annat format som du väljer. Var noga med att klicka på **Spara** för att spara dina inställningar.

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Om du vill lägga till en mask för en kolumn i databasen klickar du på **Lägg till mask** högst upp på konfigurationssidan för dynamisk **datamaskning** för att öppna konfigurationssidan **Lägg till maskningsregel.**

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Markera **fältet Schema,** **Tabell** och **Kolumn** för att definiera det angivna fältet för maskering.
7. Välj ett **maskeringsfältformat** i listan över känsliga datamaskeringskategorier.

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Klicka på **Spara** på sidan datamaskeringsregel om du vill uppdatera uppsättningen maskeringsregler i principen för dynamisk datamaskering.
9. Skriv de SQL-användare eller AAD-identiteter som ska uteslutas från maskering och ha åtkomst till de omaskerade känsliga data. Detta bör vara en semikolonavskiljande lista över användare. Användare med administratörsbehörighet har alltid åtkomst till de ursprungliga omaskerade data.

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Om du vill göra det så att programlagret kan visa känsliga data för programprivilegierat användare lägger du till SQL-användaren eller AAD-identiteten som programmet använder för att fråga databasen. Vi rekommenderar starkt att den här listan innehåller ett minimalt antal privilegierade användare för att minimera exponeringen av känsliga data.

10. Klicka på **Spara** på konfigurationssidan för datamaskering om du vill spara den nya eller uppdaterade maskeringsprincipen.

## <a name="next-steps"></a>Nästa steg

* En översikt över dynamisk datamaskering finns i [dynamisk datamaskering](sql-database-dynamic-data-masking-get-started.md).
* Du kan också implementera dynamisk datamaskering med [Azure SQL Database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) eller [REST API](https://docs.microsoft.com/rest/api/sql/).
