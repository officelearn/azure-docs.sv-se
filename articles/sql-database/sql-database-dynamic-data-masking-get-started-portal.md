---
title: 'Azure-portalen: SQL Database dynamisk datamaskning | Microsoft Docs'
description: Hur du kommer igång med SQL Database dynamisk datamaskning i Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2018
ms.openlocfilehash: 3d5ab203268ced1951d2ba9c852ece5bd5467c68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077841"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Kom igång med SQL Database dynamisk datamaskning med Azure portal

Den här artikeln visar hur du implementerar [dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md) med Azure-portalen. Du kan också implementera dynamisk data maskning med [Azure SQL Database-cmdletar](https://docs.microsoft.com/powershell/module/az.sql/) eller [REST API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurera dynamisk datamaskering för databasen med hjälp av Azure portal

1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till inställningssidan för den databas som innehåller känsliga data som du vill att maskera.
3. Klicka på den **dynamisk Datamaskning** panel som startar den **dynamisk Datamaskning** konfigurationssidan.

   * Du kan också bläddra ned till den **Operations** och klicka **dynamisk Datamaskning**.

     ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. I den **dynamisk Datamaskning** konfigurationssidan, som du kan se vissa databaskolumner som rekommendationsmotorn har flaggats för Maskning. För att kunna acceptera rekommendationer, klicka bara på **Lägg till Mask** för en eller flera kolumner och en mask skapas baserat på typ av för den här kolumnen. Du kan ändra maskningsfunktion genom att klicka på maskningsregel och redigera maskeringen fältformat till ett annat format du väljer. Se till att klicka på **spara** att spara dina inställningar.

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Att lägga till en mask för alla kolumner i databasen, överst på den **dynamisk Datamaskning** konfigurationssidan, klickar du på **Lägg till Mask** att öppna den **Lägg till regel för maskera** konfigurationssidan .

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Välj den **schemat**, **tabell** och **kolumnen** att definiera det angivna fältet för Maskning.
7. Välj en **maskera fältformat** från listan över känsliga data masking kategorier.

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Klicka på **spara** i regeln sidan för att uppdatera uppsättningen maskera regler i principen för dynamisk datamaskning för datamaskning.
9. Ange SQL-användare eller AAD-identiteter som ska uteslutas från Maskning och har åtkomst till omaskerat känsliga data. Detta bör vara en semikolonavgränsad lista med användare. Användare med administratörsbehörighet har alltid åtkomst till den ursprungliga omaskerat informationen.

    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Om du vill göra det så programlagret kan visa känsliga data för som privilegierad programanvändare, lägger du till SQL-användaren eller AAD-identitet som programmet använder för att fråga databasen. Vi rekommenderar starkt att den här listan innehåller ett minimalt antal Privilegierade användare att minimera exponering av känsliga data.

10. Klicka på **spara** i datamaskning konfigurationssidan för att spara den nya eller uppdaterade maskering av principen.

## <a name="next-steps"></a>Nästa steg

* En översikt över dynamisk datamaskning, se [dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md).
* Du kan också implementera dynamisk data maskning med [Azure SQL Database-cmdletar](https://docs.microsoft.com/powershell/module/az.sql/) eller [REST API](https://docs.microsoft.com/rest/api/sql/).
