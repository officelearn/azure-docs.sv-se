---
title: Fråga en SQL Database med hjälp av Frågeredigeraren i Azure Portal
description: Lär dig hur du använder Frågeredigeraren för att köra Transact-SQL-frågor (T-SQL) mot en databas i Azure SQL Database.
titleSuffix: Azure SQL Database
keywords: ansluta till SQL Database, fråga SQL Database, Azure Portal, Portal, Frågeredigeraren
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 05/29/2020
ms.openlocfilehash: 2b6a54c25430cd2c1e68c42571f0f9039449ce96
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335100"
---
# <a name="quickstart-use-the-azure-portals-query-editor-to-query-an-azure-sql-database"></a>Snabb start: Använd Azure Portalens Frågeredigeraren för att fråga en Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Frågeredigeraren är ett verktyg i Azure Portal för att köra SQL-frågor mot databasen i Azure SQL Database eller informations lagret i Azure Synapse Analytics. 

I den här snabb starten använder du Frågeredigeraren för att köra Transact-SQL (T-SQL)-frågor mot en databas.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten krävs AdventureWorksLT-exempel databasen. Om du inte har en fungerande kopia av AdventureWorksLT-exempel databasen i SQL Database skapar följande snabb start snabbt en:

- [Snabb start: skapa en databas i Azure SQL Database med hjälp av Azure Portal, PowerShell eller Azure CLI](single-database-create-quickstart.md) 

### <a name="configure-network-settings"></a>Konfigurera nätverksinställningar

Om du får ett av följande fel i Frågeredigeraren: *dina lokala nätverks inställningar kanske hindrar Frågeredigeraren från att utfärda frågor. Klicka här för att få anvisningar om hur du konfigurerar nätverks inställningar*, eller *så gick det inte att upprätta en anslutning till servern. Detta kan tyda på ett problem med den lokala brand Väggs konfigurationen eller inställningarna för nätverks-proxyn*. följande viktiga information bör hjälpa dig att lösa problemet:

> [!IMPORTANT]
> Frågeredigeraren använder portarna 443 och 1443 för att kommunicera. Se till att du har aktiverat utgående HTTPS-trafik på dessa portar. Du måste också [lägga till din utgående IP-adress till serverns tillåtna brand Väggs regler](firewall-create-server-level-portal-quickstart.md) för att få åtkomst till dina databaser och informations lager.


## <a name="open-the-sql-database-query-editor"></a>Öppna Frågeredigeraren SQL Database

1. Logga in på [Azure Portal](https://portal.azure.com/) och välj den databas som du vill fråga.

2. I menyn **SQL-databas** väljer du **Frågeredigeraren (för hands version)**.

    ![hitta frågeredigerare](./media/connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Upprätta en anslutning till databasen

Även om du är inloggad på portalen måste du ändå ange autentiseringsuppgifter för att komma åt databasen. Du kan ansluta med SQL-autentisering eller Azure Active Directory för att ansluta till databasen.

### <a name="connect-using-sql-authentication"></a>Anslut med SQL-autentisering

1. På **inloggnings** sidan under **SQL Server-autentisering**anger du ett **inloggnings namn** och **lösen ord** för en användare som har åtkomst till databasen. Om du inte är säker använder du inloggning och lösen ord för Server administratören för databasens Server.

    ![logga in](./media/connect-query-portal/login-menu.png)

2. Välj **OK**.


### <a name="connect-using-azure-active-directory"></a>Ansluta med Azure Active Directory

Genom att konfigurera en Azure Active Directory (Azure AD)-administratör kan du använda en enda identitet för att logga in på Azure Portal och databasen. Om du vill ansluta till din databas med Azure AD följer du stegen nedan för att konfigurera en Azure AD-administratör för din SQL Server-instans.

> [!NOTE]
> * E-postkonton (till exempel outlook.com, gmail.com, yahoo.com och så vidare) stöds ännu inte som Azure AD-administratörer. Se till att välja en användare som skapats antingen internt i Azure AD eller federerade till Azure AD.
> * Azure AD-administratörsinloggning fungerar inte med konton som har tvåfaktorautentisering aktiverat.

#### <a name="set-an-active-directory-admin-for-the-server"></a>Ange en Active Directory administratör för servern

1. I Azure Portal väljer du SQL Server-instansen.

2. Välj **Active Directory administratör**på **SQL Server** -menyn.

3. I verktygsfältet SQL Server **Active Directory administrations** sida väljer du **Ange administratör** och väljer användaren eller gruppen som din Azure AD-administratör.

    ![Välj active directory](./media/connect-query-portal/select-active-directory.png)

4. På sidan **Lägg till administratör** , i sökrutan, anger du en användare eller grupp som du vill söka efter, väljer den som administratör och väljer sedan knappen **Välj** .

5. I verktygsfältet SQL Server **Active Directory administrations** sida väljer du **Spara**.

### <a name="connect-to-the-database"></a>Ansluta till databasen

6. Välj **SQL-databaser**på **SQL Server** -menyn och välj sedan din databas.

7. I menyn **SQL-databas** väljer du **Frågeredigeraren (för hands version)**. På **inloggnings** sidan, under etiketten **Active Directory autentisering** , visas ett meddelande om att du har loggat in om du är en Azure AD-administratör. Välj sedan knappen **Fortsätt som** *\<your user or group ID>* . Om sidan indikerar att du inte har loggat in, kan du behöva uppdatera sidan.

## <a name="query-a-database-in-sql-database"></a>Fråga en databas i SQL Database

Följande exempel frågor bör köras mot AdventureWorksLT-exempel databasen.

### <a name="run-a-select-query"></a>Kör en URVALs fråga

1. Klistra in följande fråga i Frågeredigeraren:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Välj **Kör** och granska sedan utdata i **resultat** fönstret.

   ![resultat från frågeredigeraren](./media/connect-query-portal/query-editor-results.png)

3. Alternativt kan du spara frågan som en. SQL-fil eller exportera returnerade data som en. JSON-,. csv-eller. XML-fil.

### <a name="run-an-insert-query"></a>Kör en INSERT-fråga

Kör följande [insert](/sql/t-sql/statements/insert-transact-sql/) T-SQL-uttryck för att lägga till en ny produkt i `SalesLT.Product` tabellen.

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


2. Välj **Kör** för att infoga en ny rad i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.


### <a name="run-an-update-query"></a>Kör en UPPDATERINGs fråga

Kör följande [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL-uttryck för att ändra din nya produkt.

1. Ersätt den tidigare frågan med denna.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.

### <a name="run-a-delete-query"></a>Kör en BORTTAGNINGs fråga

Kör följande T-SQL-uttryck för att [ta bort din](/sql/t-sql/statements/delete-transact-sql/) nya produkt.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att ta bort den angivna raden i tabellen `Product`. I fönstret **meddelanden** visas **frågan lyckades: rader som påverkas: 1**.


## <a name="query-editor-considerations"></a>Överväganden för frågeredigeraren

Det finns några saker du behöver veta när du arbetar med frågeredigeraren.

* Frågeredigeraren använder portarna 443 och 1443 för att kommunicera. Se till att du har aktiverat utgående HTTPS-trafik på dessa portar. Du måste också lägga till din utgående IP-adress till serverns tillåtna brand Väggs regler för att få åtkomst till dina databaser och informations lager.

* Om du har en anslutning till en privat länk fungerar Frågeredigeraren utan att du behöver lägga till klientens IP-adress i SQL Database brand väggen.

* Om du trycker på **F5** uppdateras Frågeredigeraren och alla frågor som arbetar på förloras.

* Frågeredigeraren stöder inte anslutning till databasen `master`.

* Det finns en 5 minuters timeout för frågekörning.

* Frågeredigeraren stöder endast cylindriska projektioner för geografidatatyper.

* Det finns inget stöd för IntelliSense för databas tabeller och vyer, men Redigeraren stöder komplettera automatiskt för namn som redan har skrivits.




## <a name="next-steps"></a>Nästa steg

Mer information om Transact-SQL (T-SQL) som stöds i Azure SQL Database finns i [lösa skillnader i Transact-SQL vid migrering till SQL Database](transact-sql-tsql-differences-sql-server.md).
