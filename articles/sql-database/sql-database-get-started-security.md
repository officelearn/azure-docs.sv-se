---
title: "SQL Database-självstudier: Komma igång med säkerhet"
description: "Lär dig skapa användarkonton för att få åtkomst till och hantera en databas."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: e846654b659c1240451e3ac26973fbfce206936d


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL Database-självstudier: Skapa användarkonton för en SQL-databas för att få åtkomst till och hantera en databas
> [!div class="op_single_selector"]
> * [Självstudier för att komma igång](sql-database-get-started-security.md)
> * [Bevilja åtkomst](sql-database-manage-logins.md)
> 
> 

I de här självstudierna får du lära dig hur du använder SQL Server Management Studio (SSMS) för att:

* Logga in till SQL Database med en huvudsaklig inloggning på servernivå.
* Skapa ett användarkonto för SQL Database.
* Ge den nya databasanvändaren [db_owner-behörigheter](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
* Anslut till en SQL-databas med ett användarkonto som inte är ett huvudsakligt användardkonto på servernivå.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört de här SQL Database-självstudierna och skapat ett användarkonto samt gett användarkontot dbo-behörigheter, är du redo att lära dig mer om [SQL Database-säkerhet](sql-database-manage-logins.md).




<!--HONumber=Dec16_HO1-->


