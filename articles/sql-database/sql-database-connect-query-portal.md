---
title: 'Azure Portal: fråga med Frågeredigeraren'
description: Lär dig hur du ansluter till SQL Database i Azure Portal med hjälp av SQL-frågeredigeraren. Kör sedan Transact-SQL-uttryck (T-SQL) för att söka i och redigera data.
keywords: connect to sql database,azure portal, portal, query editor
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: b3ccc2a5343cf02127990dca80a1300959fa06a3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087184"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Snabb start: Använd Azure Portal SQL-Frågeredigeraren för att ansluta och fråga efter data

SQL-frågeredigeraren är ett webbläsarverktyg på Azure-portalen som ger ett enkelt sätt att köra SQL-frågor på Azure SQL Database eller Azure SQL Data Warehouse. I den här snabbstarten kommer du att använda frågeredigeraren för att ansluta till en SQL-databas och sedan köra Transact-SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- En Azure SQL-databas. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

  || Enskild databas |
  |:--- |:--- |
  | Skapa| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurera | [IP-brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> Frågeredigeraren använder portarna 443 och 1443 för att kommunicera.  Kontrol lera att du har aktiverat utgående HTTPS-trafik på dessa portar. Du måste också lägga till din utgående IP-adress till serverns tillåtna brand Väggs regler för att få åtkomst till dina databaser och informations lager.

## <a name="sign-in-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Anslut med SQL-autentisering

1. Gå till Azure Portal för att ansluta till en SQL-databas. Sök efter och välj **SQL-databaser**.

    ![Gå till listan SQL Database, Azure Portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. Välj din SQL-databas.

    ![Välj en SQL-databas Azure Portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. I menyn **SQL-databas** väljer du **Frågeredigeraren (för hands version)** .

    ![hitta frågeredigerare](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. På **inloggnings** sidan under etiketten **SQL Server-autentisering** anger du **inloggnings** -ID och **lösen ord** för det Server administratörs konto som användes för att skapa databasen. Välj sedan **OK**.

    ![logga in](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Ansluta med Azure Active Directory

Genom att konfigurera en Azure Active Directory (Azure AD)-administratör kan du använda en enda identitet för att logga in på Azure Portal och SQL-databasen. Följ stegen nedan om du vill konfigurera en Azure AD-administratör för din SQL Server.

> [!NOTE]
> * E-postkonton (till exempel outlook.com, gmail.com, yahoo.com och så vidare) stöds ännu inte som Azure AD-administratörer. Se till att välja en användare som skapats internt i Azure AD eller som federerats till Azure AD.
> * Azure AD-administratörsinloggning fungerar inte med konton som har tvåfaktorautentisering aktiverat.

1. På Azure Portal-menyn eller på **Start** sidan väljer du **alla resurser**.

2. Välj din SQL Server.

3. Välj **Active Directory administratör**på **SQL Server** -menyn under **Inställningar**.

4. I verktygsfältet för SQL Server **Active Directory admin** väljer du **Ange administratör** och väljer användaren eller gruppen som din Azure AD-administratör.

    ![Välj active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

5. På sidan **Lägg till administratör** , i sökrutan, anger du en användare eller grupp som du vill söka efter, väljer den som administratör och väljer sedan knappen **Välj** .

6. Gå tillbaka till verktygsfältet för SQL Server **Active Directory admin** och välj **Spara**.

7. Välj **SQL-databaser**på **SQL Server** -menyn och välj sedan din SQL-databas.

8. I menyn **SQL-databas** väljer du **Frågeredigeraren (för hands version)** . På **inloggnings** sidan, under etiketten **Active Directory autentisering** , visas ett meddelande om att du har loggat in om du är en Azure AD-administratör. Välj sedan knappen **Fortsätt som** *\<ditt användar-eller grupp-ID >* .

## <a name="view-data"></a>Visa data

1. När du har autentiserats klistrar du in följande SQL i frågeredigeraren för att hämta de 20 främsta produkterna efter kategori.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Välj **Kör** i verktygsfältet och granska utdata i fönstret **Resultat**.

   ![resultat från frågeredigeraren](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>Infoga data

Kör följande [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL-instruktion för att lägga till en ny produkt i tabellen `SalesLT.Product`.

1. Ersätt den föregående frågan med denna.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Välj **Kör** för att infoga en ny rad i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.


## <a name="update-data"></a>Uppdatera data

Kör följande [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-instruktion för att ändra din nya produkt.

1. Ersätt den föregående frågan med denna.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.

## <a name="delete-data"></a>Ta bort data

Kör följande [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-instruktion för att ta bort den nya produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att ta bort den angivna raden i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.


## <a name="query-editor-considerations"></a>Överväganden för frågeredigeraren

Det finns några saker du behöver veta när du arbetar med frågeredigeraren.

* Frågeredigeraren använder portarna 443 och 1443 för att kommunicera.  Kontrol lera att du har aktiverat utgående HTTPS-trafik på dessa portar. Du måste också lägga till din utgående IP-adress till serverns tillåtna brand Väggs regler för att få åtkomst till dina databaser och informations lager.

* Frågeredigeraren fungerar med en privat länk utan att behöva lägga till klientens IP-adress i SQL Database brand väggen

* Om du trycker på F5 uppdateras frågeredigerarens sida och frågan som bearbetas går förlorad.

* Frågeredigeraren stöder inte anslutning till databasen `master`.

* Det finns en 5 minuters timeout för frågekörning.

* Frågeredigeraren stöder endast cylindriska projektioner för geografidatatyper.

* Det finns inte stöd för IntelliSense för databastabeller och vyer. Men redigeraren har stöd för automatisk komplettering av namn som redan har skrivits.




## <a name="next-steps"></a>Nästa steg

Mer information om Transact-SQL som stöds i Azure SQL-databaser finns i [Lösa Transact-SQL-skillnader vid migrering till SQL Database](sql-database-transact-sql-information.md).
