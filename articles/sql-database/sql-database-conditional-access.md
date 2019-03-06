---
title: Villkorlig åtkomst – Azure SQL Database och Data Warehouse | Microsoft-dokument
description: Lär dig hur du konfigurerar villkorlig åtkomst för Azure SQL Database och Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 8e7e72236abdaeb77a6bfe3dd85c9ab0c8fbe40e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442707"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Villkorlig åtkomst (MFA) med Azure SQL Database och Data Warehouse  

Både Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stöd för villkorlig åtkomst för Microsoft. 

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

Följande steg visar hur du konfigurerar SQL-databas om du vill framtvinga principer för villkorlig åtkomst.  

## <a name="prerequisites"></a>Förutsättningar  
- Du måste konfigurera SQL Database eller SQL Data Warehouse för att stödja Azure Active Directory-autentisering. Specifika anvisningar finns i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- När multifaktorautentisering har aktiverats måste du ansluter till vid stöds verktyg, till exempel den senaste SSMS. Mer information finns i [konfigurera Azure SQL Database Multi-Factor authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurera Certifikatutfärdaren för Azure SQL DB/DW  
1.  Logga in på portalen, väljer **Azure Active Directory**, och välj sedan **villkorlig åtkomst**. Mer information finns i [Teknisk referens för Azure Active Directory villkorsstyrd åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![bladet för villkorlig åtkomst](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  I den **villkorliga åtkomstprinciper** bladet klickar du på **ny princip**, ange ett namn och klicka sedan på **konfigurera regler för**.  
3.  Under **tilldelningar**väljer **användare och grupper**, kontrollera **Välj användare och grupper**, och välj sedan användare eller grupp för villkorlig åtkomst. Klicka på **Välj**, och klicka sedan på **klar** att godkänna ditt val.  
  ![Välj användare och grupper](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Välj **Molnappar**, klickar du på **Välj appar**. Du kan se alla appar som är tillgängliga för villkorlig åtkomst. Välj **Azure SQL Database**, längst ned på sidan klickar du på **Välj**, och klicka sedan på **klar**.  
  ![select SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
  Om du inte hittar **Azure SQL Database** visas i skärmbilden tredje, gör du följande:   
  - Logga in på din Azure SQL DB/DW-instans med hjälp av SSMS med en AAD-administratörskonto.  
  - Köra `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Logga in på AAD och kontrollera att Azure SQL Database och Data Warehouse finns med i programmen i din AAD.  

5.  Välj **åtkomstkontroller**väljer **bevilja**, och markerar sedan den princip som du vill använda. I det här exemplet väljer vi **kräva multifaktorautentisering**.  
  ![Välj för att bevilja åtkomst](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Sammanfattning  
Det valda programmet (Azure SQL Database) så att du kan ansluta till Azure SQL DB/DW med hjälp av Azure AD Premium, tillämpar nu valda principen för villkorlig åtkomst, **krävs multifaktorautentisering.**  
Frågor om Azure SQL Database och Data Warehouse om multifaktorautentisering, kontakta MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Nästa steg  

En självstudiekurs finns i [skydda din Azure SQL Database](sql-database-security-tutorial.md).
