---
title: Skapa en brandväggsregel på servernivå – Azure SQL Database| Microsoft Docs
description: Skapa en brandväggsregel på SQL Database-servernivå för enkla databaser eller pooldatabaser
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: f708e5a3cd5bc0f11f8b0cfe79a791347c7a7a2b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108967"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Snabbstart: Skapa en brandväggsregel på servernivå för enkla databaser eller pooldatabaser med hjälp av Azure-portalen

Den här snabbstarten visar hur du skapar en [brandväggsregel på servernivå](sql-database-firewall-configure.md) för enkla databaser eller pooldatabaser i Azure SQL Database med hjälp av Azure-portalen, så att du kan ansluta till databasservrar, enkla databaser, samt elastiska pooler och deras databaser. Du måste ha en brandväggsregel för att kunna ansluta från andra Azure-resurser och lokala resurser.

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här snabbstarten används de resurser som skapades i avsnittet [Skapa en enskild databas med Azure-portalen](sql-database-single-database-get-started.md) som startpunkt.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Skapa en IP-brandväggsregel på servernivå

SQL Database-tjänsten skapar en brandvägg på databasservernivå för enskilda och poolindelade databaser. Den här brandväggen förhindrar att klientprogram ansluter till servern eller någon av dess enkla eller poolindelade databaser såvida du inte skapar en IP-brandväggsregel för att öppna brandväggen. För anslutningar från en IP-adress utanför Azure kan du skapa en brandväggsregel för en viss IP-adress eller ett adressintervall som du vill kunna ansluta. Mer information om IP-brandväggsregler på servernivå och databasnivå finns i avsnittet om [IP-brandväggsregler på SQL Database-servernivå och databasnivå](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kan det hända att utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server såvida inte IT-avdelningen öppnar port 1433.
> [!IMPORTANT]
> En brandväggsregel på 0.0.0.0 gör det möjligt för alla Azure-tjänster att passera brandväggsregeln på servernivå och försöka ansluta till en enkel eller en poolindelad databas via servern. Läs om hur du använder regler för virtuellt nätverk i [Virtual network rules as alternatives to IP rules](sql-database-firewall-configure.md#virtual-network-rules-as-alternatives-to-ip-rules) (Regler för virtuellt nätverk som alternativ till IP-regler).

Följ de här stegen om du vill skapa en IP-brandväggsregel på servernivå för din klients IP-adress och aktivera extern anslutning genom SQL Database-brandväggen endast för din IP-adress.

1. När distributionen av [den Azure SQL-databas som krävs](#prerequisites) är klar väljer du **SQL-databaser** på den vänstra menyn och sedan **mySampleDatabase** på sidan **SQL-databaser**. Översiktssidan för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver-20170824.database.windows.net**) och alternativ för ytterligare konfiguration.

2. Kopiera det här fullständigt kvalificerade servernamnet för användning vid anslutning till servern och dess databaser i andra snabbstarter.

   ![servernamn](./media/sql-database-get-started-portal/server-name.png)

3. Välj **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för databasservern öppnas.

   ![IP-brandväggsregel på servernivå](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Välj **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny IP-brandväggsregel på servernivå. Med en brandväggsregel på servernivå kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

   > [!IMPORTANT]
   > Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Välj **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.
   >

5. Välj **Spara**. En IP-brandväggsregel på servernivå för att öppna port 1433 på SQL Database-servern skapas för din aktuella IP-adress.

6. Stäng sidan **Brandväggsinställningar**.

Med hjälp av SQL Server Management Studio eller annat valfritt verktyg kan du nu ansluta till SQL Database-servern och dess databaser från den här IP-adressen med det serveradministratörskonto som skapades tidigare.

## <a name="clean-up-resources"></a>Rensa resurser

Spara de här resurserna om du vill gå till [nästa steg](#next-steps) och lära dig hur du ansluter och skickar frågor till din databas med ett antal olika metoder. Men om du vill ta bort de resurser som du skapade i den här snabbstarten utför du följande steg.

1. På menyn till vänster i Azure-portalen klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På sidan med resursgrupper väljer du **Ta bort**, skriver **myResourceGroup** i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Nu när du har en databas kan du [ansluta och köra frågor](sql-database-connect-query.md) med de verktyg och språk du föredrar, till exempel
  - [Ansluta och köra frågor med SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- I följande självstudier lär du dig hur du utformar din första databas, skapar tabeller och infogar data:
  - [Skapa din första enkla databas i Azure SQL Database med hjälp av SSMS](sql-database-design-first-database.md)
  - [Utforma en enskild databas i Azure SQL Database och ansluta med C# och ADO.NET](sql-database-design-first-database-csharp.md)
