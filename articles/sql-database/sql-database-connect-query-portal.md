---
title: 'Azure-portalen: Fråga Azure SQL Database med frågeredigeraren | Microsoft Docs'
description: Lär dig hur du ansluter till SQL Database i Azure Portal med hjälp av SQL-frågeredigeraren. Kör sedan Transact-SQL-uttryck (T-SQL) för att söka i och redigera data.
keywords: connect to sql database,azure portal, portal, query editor
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: carlrab
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: b2109afe6760eb87269a33ed74784149c2bf3e03
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105802"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Snabbstart: Använda SQL-frågeredigeraren på Azure-portalen för att ansluta och fråga efter data

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
> Kontrollera att alternativet **Tillåt åtkomst till Azure-tjänster** är inställt på **PÅ** i inställningarna för SQL Server-brandväggen. Det här alternativet ger SQL-frågeredigeraren åtkomst till dina databaser och datalager.

## <a name="sign-in-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="connect-using-sql-authentication"></a>Anslut med SQL-autentisering

1. Välj **SQL-databaser** på menyn till vänster och välj sedan **mySampleDatabase**.

2. På menyn till vänster hittar och väljer du **Frågeredigeraren (förhandsversion)**. Sidan **Inloggning** visas.

    ![hitta frågeredigerare](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. Välj **Auktoriseringstyp** på den nedrullningsbara menyn, välj **SQL Server-autentisering** och ange användar-ID och lösenord för det serveradministratörskonto som används för att skapa databasen.

    ![logga in](./media/sql-database-connect-query-portal/login-menu.png) 

4. Välj **OK**.


## <a name="connect-using-azure-active-directory"></a>Ansluta med Azure Active Directory

Genom att konfigurera en Active Directory-administratör (AD) kan du använda en enda identitet för att logga in på Azure-portalen och SQL-databasen. Konfigurera en AD-administratör för SQL-servern genom att följa stegen nedan.

> [!NOTE]
> * E-postkonton (t.ex. outlook.com, gmail.com och yahoo.com) stöds inte än som AD-administratörer. Se till att välja en användare som skapats internt i Azure AD eller som federerats till Azure AD.
> * Azure AD-administratörsinloggning fungerar inte med konton som har tvåfaktorautentisering aktiverat.

1. Välj **Alla resurser** på menyn till vänster och välj sedan SQL-servern.

2. På SQL-serverns meny **Inställningar** väljer du **Active Directory-administratör**.

3. I verktygsfältet för AD-administratörssidan väljer du **Konfigurera administratör** och väljer användarens eller gruppen som AD-administratör.

    ![Välj active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. Välj **Spara** på AD-administratörssidan.

5. Gå till databasen **mySampleDatabase** och välj **Frågeredigeraren (förhandsversion)** på menyn till vänster. Sidan **Inloggning** visas. Om du är AD-administratör visas ett meddelande till höger under **Active Directory – enkel inloggning** om att du har loggats in. 
   
6. Välj **OK**.


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

1. Ersätt den tidigare frågan med denna.

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


2. Välj **Kör** för att infoga en ny rad i tabellen `Product`. I fönstret **Meddelande** visas **Frågan slutförd: Berörda rader: 1**.


## <a name="update-data"></a>Uppdatera data

Kör följande [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL-instruktion för att ändra din nya produkt.

1. Ersätt den tidigare frågan med denna.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i tabellen `Product`. I fönstret **Meddelande** visas **Frågan slutförd: Berörda rader: 1**.

## <a name="delete-data"></a>Ta bort data

Kör följande [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL-instruktion för att ta bort den nya produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att ta bort den angivna raden i tabellen `Product`. I fönstret **Meddelande** visas **Frågan slutförd: Berörda rader: 1**.


## <a name="query-editor-considerations"></a>Överväganden för frågeredigeraren

Det finns några saker du behöver veta när du arbetar med frågeredigeraren.

* Du kan inte använda frågeredigeraren för att fråga SQL-serverdatabaser i ett virtuellt nätverk.

* Om du trycker på F5 uppdateras frågeredigerarens sida och frågan som bearbetas går förlorad.

* Frågeredigeraren stöder inte anslutning till databasen `master`.

* Det finns en 5 minuters timeout för frågekörning.

* Frågeredigeraren stöder endast cylindriska projektioner för geografidatatyper.

* Det finns inte stöd för IntelliSense för databastabeller och vyer. Men redigeraren har stöd för automatisk komplettering av namn som redan har skrivits.


## <a name="next-steps"></a>Nästa steg

Mer information om Transact-SQL som stöds i Azure SQL-databaser finns i [Lösa Transact-SQL-skillnader vid migrering till SQL Database](sql-database-transact-sql-information.md).
