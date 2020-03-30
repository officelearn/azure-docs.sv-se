---
title: Fråga en SQL-databas med frågeredigeraren i Azure-portalen
description: Lär dig hur du använder Frågeredigeraren för att köra Transact-SQL-frågor (T-SQL) mot en Azure SQL-databas.
keywords: ansluta till SQL-databas,fråga sql-databas, Azure Portal, portal, frågeredigerare
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 03/12/2020
ms.openlocfilehash: 5847ef3033d257faef4831785b8abd864d54e835
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79209586"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-a-sql-database"></a>Snabbstart: Använda Azure-portalens frågeredigerare för att fråga en SQL-databas

Frågeredigeraren är ett verktyg i Azure-portalen för att köra SQL-frågor mot din Azure SQL-databas eller Azure SQL Data Warehouse. 

I den här snabbstarten ska du använda frågeredigeraren för att köra Transact-SQL-frågor (T-SQL) mot en Azure SQL-databas.


## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten krävs exempeldatabasen AdventureWorksLT. Om du inte har en fungerande kopia av AdventureWorksLT SQL-databasen skapas en snabbstart genom följande snabbstart:

- [Snabbstart: Skapa en enda Azure SQL-databas med Azure-portalen, PowerShell eller Azure CLI](sql-database-single-database-get-started.md) 

### <a name="configure-network-settings"></a>Konfigurera nätverksinställningar

Om du får något av följande fel i frågeredigeraren: *Dina lokala nätverksinställningar kan hindra Frågeredigeraren från att utfärda frågor. Klicka här för instruktioner om hur du konfigurerar nätverksinställningarna*eller *så gick det inte att upprätta en anslutning till servern. Detta kan tyda på ett problem med den lokala brandväggskonfigurationen eller nätverksproxyinställningarna,* följande viktiga information bör hjälpa dig att lösa:

> [!IMPORTANT]
> Frågeredigeraren använder portarna 443 och 1443 för att kommunicera. Kontrollera att du har aktiverat utgående HTTPS-trafik på dessa portar. Du måste också lägga till [din utgående IP-adress i serverns tillåtna brandväggsregler](sql-database-server-level-firewall-rule.md) för att komma åt dina databaser och informationslager.


## <a name="open-the-sql-database-query-editor"></a>Öppna SQL Database Query Editor

1. Logga in på [Azure-portalen](https://portal.azure.com/) och välj den SQL-databas som du vill fråga.

2. På **SQL-databasmenyn** väljer du **Frågeredigeraren (förhandsgranskning)**.

    ![hitta frågeredigerare](./media/sql-database-connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Upprätta en anslutning till databasen

Även om du är inloggad på portalen måste du fortfarande ange autentiseringsuppgifter för att komma åt SQL-databasen. Du kan ansluta med SQL-autentisering eller Azure Active Directory för att ansluta till databasen.

### <a name="connect-using-sql-authentication"></a>Anslut med SQL-autentisering

1. Ange en **inloggning** och **ett lösenord** för en användare som har åtkomst till databasen under **SQL-serverautentisering**på inloggningssidan. **Login** Om du är osäker använder du inloggningen och lösenordet för serveradministratören på databasens server.

    ![logga in](./media/sql-database-connect-query-portal/login-menu.png)

2. Välj **OK**.


### <a name="connect-using-azure-active-directory"></a>Ansluta med Azure Active Directory

Genom att konfigurera en Azure Active Directory-administratör (Azure AD) kan du använda en enda identitet för att logga in på Azure-portalen och din SQL-databas. Om du vill ansluta till databasen med Azure AD följer du stegen nedan för att konfigurera en Azure AD-administratör för din SQL-server.

> [!NOTE]
> * E-postkonton (till exempel outlook.com, gmail.com, yahoo.com och så vidare) stöds ännu inte som Azure AD-administratörer. Se till att välja en användare som skapats internt i Azure AD eller som federerats till Azure AD.
> * Azure AD-administratörsinloggning fungerar inte med konton som har tvåfaktorautentisering aktiverat.

#### <a name="set-an-active-directory-admin-for-the-database-server"></a>Ange en Active Directory-administratör för databasservern

1. Välj din SQL-server i Azure-portalen.

2. Välj **Active Directory-administratör**på **SQL-servermenyn** .

3. I verktygsfältet **för administrationssidan för** SQL-servern väljer du Ange **administratör** och väljer användaren eller gruppen som Azure AD-administratör.

    ![Välj active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. På sidan Lägg till administratör anger du en användare eller grupp som ska hittas på sidan Lägg till **administratör,** väljer den som administratör och väljer sedan knappen **Välj.**

5. Välj **Spara**i verktygsfältet **Active Directory admin-administration** i SQL-servern.

### <a name="connect-to-the-database"></a>Ansluta till databasen

6. Välj **SQL-databaser**på **SQL-server-menyn** och välj sedan DIN SQL-databas.

7. På **SQL-databasmenyn** väljer du **Frågeredigeraren (förhandsgranskning)**. På **inloggningssidan,** under **Active Directory-autentiseringsetiketten,** visas ett meddelande som säger att du har loggat in om du är Azure AD-administratör. Välj sedan knappen **Fortsätt som** * \<användare eller grupp-ID>.* Om sidan visar att du inte har loggat in kan du behöva uppdatera sidan.

## <a name="query-a-sql-database"></a>Fråga en SQL-databas

Följande exempelfrågor bör köras mot exempeldatabasen AdventureWorksLT.

### <a name="run-a-select-query"></a>Köra en SELECT-fråga

1. Klistra in följande fråga i frågeredigeraren:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Välj **Kör** och granska sedan utdata i **resultatfönstret.**

   ![resultat från frågeredigeraren](./media/sql-database-connect-query-portal/query-editor-results.png)

3. Du kan också spara frågan som en SQL-fil eller exportera returnerade data som en .json-, CSV- eller .xml-fil.

### <a name="run-an-insert-query"></a>Köra en INSERT-fråga

Kör följande [INSERT](/sql/t-sql/statements/insert-transact-sql/) T-SQL-uttryck för att `SalesLT.Product` lägga till en ny produkt i tabellen.

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


2. Välj **Kör** för att infoga en ny rad i tabellen `Product`. **Meddelandefönstret** visar **Frågan lyckades: Berörda rader: 1**.


### <a name="run-an-update-query"></a>Köra en UPDATE-fråga

Kör följande [UPDATE](/sql/t-sql/queries/update-transact-sql/) T-SQL-uttryck för att ändra den nya produkten.

1. Ersätt den tidigare frågan med denna.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i tabellen `Product`. **Meddelandefönstret** visar **Frågan lyckades: Berörda rader: 1**.

### <a name="run-a-delete-query"></a>Köra en DELETE-fråga

Kör följande [DELETE](/sql/t-sql/statements/delete-transact-sql/) T-SQL-uttryck för att ta bort den nya produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att ta bort den angivna raden i tabellen `Product`. **Meddelandefönstret** visar **Frågan lyckades: Berörda rader: 1**.


## <a name="query-editor-considerations"></a>Överväganden för frågeredigeraren

Det finns några saker du behöver veta när du arbetar med frågeredigeraren.

* Frågeredigeraren använder portarna 443 och 1443 för att kommunicera. Kontrollera att du har aktiverat utgående HTTPS-trafik på dessa portar. Du måste också lägga till din utgående IP-adress på serverns tillåtna brandväggsregler för att komma åt dina databaser och informationslager.

* Frågeredigeraren fungerar med Privat länk utan att behöva lägga till klient-IP-adressen i SQL Database-brandväggen

* Om du trycker på **F5** uppdateras frågeredigerarens sida och alla frågor som bearbetas går förlorade.

* Frågeredigeraren stöder inte anslutning till databasen `master`.

* Det finns en 5 minuters timeout för frågekörning.

* Frågeredigeraren stöder endast cylindriska projektioner för geografidatatyper.

* Det finns inget stöd för IntelliSense för databastabeller och vyer, men redigeraren stöder automatisk komplettering av namn som redan har skrivits.




## <a name="next-steps"></a>Nästa steg

Mer information om Transact-SQL (T-SQL) som stöds i Azure SQL-databaser finns i [Lösa Transact-SQL-skillnader under migreringen till SQL Database](sql-database-transact-sql-information.md).
