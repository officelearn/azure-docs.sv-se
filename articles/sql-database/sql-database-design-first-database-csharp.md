---
title: Utforma din första relationsdatabas – C# – Azure SQL Database | Microsoft Docs
description: Lär dig hur du utformar din första relationsdatabas i en enskild databas i Azure SQL Database med C# med ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 02/08/2019
ms.openlocfilehash: ce46a6b8d4e2bc57625f9202349718dfbaedc660
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995697"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Självstudier: Utforma en relationsdatabas i en enskild databas i Azure SQL Database C&#x23; och ADO.NET

Azure SQL Database är en relationsdatabas-som-tjänst (DBaaS) som bygger på Microsoft Cloud (Azure). I de här självstudierna får du lära dig att använda Azure-portalen och ADO.NET med Visual Studio för att:

> [!div class="checklist"]
> * Skapa en enskild databas med hjälp av Azure-portalen*
> * Ställa in en IP-brandväggsregel på servernivå med hjälp av Azure-portalen
> * Ansluta till databasen med ADO.NET och Visual Studio
> * Skapa tabeller med ADO.NET
> * Infoga, uppdatera och ta bort data med ADO.NET
> * Fråga efter data med ADO.NET

*Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Installation av [Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="create-a-blank-single-database"></a>Skapa en enda tom databas

En enskild databas i Azure SQL-databasen skapas med en definierad uppsättning beräknings- och lagringsresurser. Databasen skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och hanteras med hjälp av en [databasserver](sql-database-servers.md).

Följ dessa steg för att skapa en enskild tom databas.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Välj **Databaser** i avsnittet Azure Marketplace på sidan **Nytt** och klicka sedan på **SQL Database** i avsnittet **Aktuellt**.

   ![skapa tom databas](./media/sql-database-design-first-database/create-empty-database.png)

3. Fyll i följande information i **SQL Database**-formuläret (se föregående bild):

    | Inställning       | Föreslaget värde | Beskrivning |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Databasnamn** | *yourDatabase* | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
    | **Prenumeration** | *yourSubscription*  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
    | **Resursgrupp** | *yourResourceGroup* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/naming-conventions). |
    | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

4. Klicka på **Server** för att använda en befintlig databasserver eller skapa och konfigurera en ny databasserver. Välj en befintlig server eller klicka på **Skapa en ny server** och fyll i följande information i formuläret **Ny server**:

    | Inställning       | Föreslaget värde | Beskrivning |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/naming-conventions). |
    | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
    | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
    | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

    ![skapa databas-server](./media/sql-database-design-first-database/create-database-server.png)

5. Klicka på **Välj**.
6. Klicka på **Prisnivå** för att ange tjänstnivå, antalet DTU:er eller V-kärnor och mängden lagring. Du kan undersöka alternativen för antalet DTU:er/V-kärnor och lagringsutrymme som du har tillgång till på varje tjänstnivå.

    När du har valt tjänstenivå, antalet DTU:er eller virtuella kärnor och mängden lagring klickar du på **Använd**.

7. Ange en **sortering** för den tomma databasen (använd standardvärdet för de här självstudierna). Mer information om sorteringar finns i [Sorteringar](/sql/t-sql/statements/collations).

8. Nu när du har fyllt i **SQL Database**-formuläret klickar du på **Skapa** för att etablera den enskilda databasen. Det här steget kan ta några minuter.

9. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![avisering](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Skapa en IP-brandväggsregel på servernivå

SQL Database-tjänsten skapar en IP-brandvägg på servernivå. Den här brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern såvida inte en brandväggsregel tillåter deras IP-adresser genom brandväggen. För att tillåta externa anslutningar till din databas måste du först lägga till en IP-brandväggsregel för din IP-adress (eller ditt IP-adressintervall). Följ de här stegen för att skapa en [IP-brandväggsregel på SQL Database-servernivå](sql-database-firewall-configure.md).

> [!IMPORTANT]
> SQL Database-tjänsten kommunicerar via port 1433. Om du försöker ansluta till den här tjänsten från ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till din enskilda databas om inte administratören öppnar port 1433.

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och klickar sedan på *yourDatabase* på sidan **SQL-databaser**. Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade **servernamnet** (till exempel *yourserver.database.windows.net*) tillsammans med alternativ för ytterligare konfiguration.

2. Kopiera det här fullständigt kvalificerade servernamnet. Du behöver det när du ansluter till servern och dess databaser från SQL Server Management Studio.

   ![servernamn](./media/sql-database-design-first-database/server-name.png)

3. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

   ![IP-brandväggsregel på servernivå](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny IP-brandväggsregel. Med en IP-brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Klicka på **Spara**. En IP-brandväggsregel på servernivå för att öppna port 1433 på SQL Database-servern skapas för din aktuella IP-adress.

6. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Din IP-adress kan nu passera genom IP-brandväggen. Nu kan du ansluta till din databas med hjälp av SQL Server Management Studio eller ett annat verktyg. Se till att använda serveradmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom IP-brandväggen för SQL Database aktiverad för alla Azure-tjänster. Klicka på **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du lärt dig grundläggande databasuppgifter, till exempel att skapa en databas och tabeller, ansluta till databasen, läsa in data och köra frågor. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en databas
> * Konfigurera en brandväggsregel
> * Ansluta till databasen med [Visual Studio och C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Skapa tabeller
> * Infoga, uppdatera, ta bort och fråga efter data

Gå vidare till nästa självstudie om du vill lära dig mer om migrering av data.

> [!div class="nextstepaction"]
> [Migrera SQL Server till Azure SQL Database offline med DMS](../dms/tutorial-sql-server-to-azure-sql.md)
