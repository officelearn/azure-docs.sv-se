---
title: 'Azure-portalen: SQL-databas dynamisk datamaskning | Microsoft Docs'
description: Hur du kommer igång med SQL Database dynamisk datamaskning i Azure-portalen
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: 9b47345278bd74449c16e4e17ed1748dba489f82
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645522"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Kom igång med SQL Database dynamisk datamaskering med Azure-portalen

Den här artikeln visar hur du implementerar [dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md) med Azure-portalen. Du kan också implementeras dynamiska datamaskering med [Azure SQL Database-cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx) eller [REST API](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Konfigurera dynamisk datamaskering för databasen med hjälp av Azure portal
1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com).
2. Gå till inställningssidan i databasen som innehåller känsliga data som du vill att maskera.
3. Klicka på den **dynamisk Datamaskering** panelen som startar den **dynamisk Datamaskering** konfigurationssidan.
   
   * Du kan också bläddra ned till den **Operations** avsnittet och klicka på **dynamisk Datamaskering**.
     
     ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. I den **dynamisk Datamaskering** konfigurationssidan kan du se vissa databaskolumner som rekommendationer-motorn har flaggats för maskering. För att kunna acceptera rekommendationerna, klicka bara på **Lägg till Mask** för en eller flera kolumner och en mask skapas baserat på standardtypen för den här kolumnen. Du kan ändra maskningsfunktion genom att klicka på maskningsregel och redigera maskeringen fältformat till ett annat format du föredrar. Klicka på **spara** att spara dina inställningar.
   
    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Att lägga till en mask för alla kolumner i databasen, överst på den **dynamisk Datamaskering** konfigurationssidan, klickar du på **Lägg till Mask** att öppna den **Lägg till regel för maskering** konfigurationssidan .
   
    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Välj den **schemat**, **tabell** och **kolumnen** att definiera fältet för maskering.
7. Välj en **maskering cellformatet** från listan över känsliga data maskering kategorier.
   
    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Klicka på **spara** i regeln sidan om du vill uppdatera en uppsättning av maskering regler i principen för dynamisk datamaskering för datamaskning.
9. Ange SQL-användare eller AAD identiteter som ska uteslutas från maskering och har åtkomst till masken känsliga data. Detta bör vara en semikolonavgränsad lista med användare. Användare med administratörsbehörighet alltid ska ha åtkomst till den ursprungliga omaskerat data.
   
    ![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Lägg till SQL-användaren för att göra det så att Applikationsnivån kan visa känsliga data för som privilegierad programanvändare, eller AAD-identitet programmet använder för att ställa frågor till databasen. Vi rekommenderar starkt att den här listan innehåller ett minimalt antal Privilegierade användare för att minimera exponering av känsliga data.
   > 
   > 
10. Klicka på **spara** i datamaskning konfigurationssidan om du vill spara den nya eller uppdaterade maskering av principen.


## <a name="next-steps"></a>Nästa steg

* En översikt över dynamisk datamaskning finns [dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md).
* Du kan också implementeras dynamiska datamaskering med [Azure SQL Database-cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx) eller [REST API](https://msdn.microsoft.com/library/dn505719.aspx).
