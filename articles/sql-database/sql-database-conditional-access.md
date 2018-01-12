---
title: "Villkorlig åtkomst - Azure SQL Database och Data Warehouse | Microsoft-dokument"
description: "Lär dig hur du konfigurerar villkorlig åtkomst för Azure SQL Database och datalagret."
services: sql-database
author: GithubMirek
manager: johammer
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 06/07/2017
ms.author: mireks
ms.workload: Inactive
ms.openlocfilehash: b1c569e414de292b59e0915483d45bf011edb1a6
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Villkorlig åtkomst (MFA) med Azure SQL-databas och informationslager  

Både SQL Database och SQL Data Warehouse stöder Microsoft villkorlig åtkomst. Följande steg visar hur du konfigurerar SQL-databas för att tillämpa en princip för villkorlig åtkomst.  

## <a name="prerequisites"></a>Förutsättningar  
- Du måste konfigurera SQL Database eller SQL Data Warehouse för att stödja Azure Active Directory-autentisering. Specifika anvisningar finns [konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- När multifaktorautentisering har aktiverats måste du ansluta med vid stöds verktyg, till exempel senaste SSMS. Mer information finns i [konfigurera Azure SQL Database Multi-Factor authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurera Certifikatutfärdaren för Azure SQL DB/DW  
1.  Logga in på portalen, Välj **Azure Active Directory**, och välj sedan **villkorlig åtkomst**. Mer information finns i [Teknisk referens för Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![bladet för villkorlig åtkomst](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  I den **villkorliga åtkomstprinciper** bladet, klickar du på **ny princip**, ange ett namn och klicka sedan på **konfigurera regler**.  
3.  Under **tilldelningar**väljer **användare och grupper**, kontrollera **Välj användare och grupper**, och välj sedan användare eller grupp för villkorlig åtkomst. Klicka på **Välj**, och klicka sedan på **klar** att acceptera valet.  
  ![Välj användare och grupper](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Välj **Molnappar**, klickar du på **Välj appar**. Du kan se alla appar som är tillgängliga för villkorlig åtkomst. Välj **Azure SQL Database**, klicka på **Välj**, och klicka sedan på **klar**.  
  ![Välj SQL-databas](./media/sql-database-conditional-access/select-sql-database.png)  
  Om du inte hittar **Azure SQL Database** som anges i följande tredje skärmdump, gör du följande:   
  - Logga in till din Azure SQL DB/DW-instans med hjälp av SSMS med ett administratörskonto för AAD.  
  - Köra `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Logga in på AAD och kontrollera att Azure SQL Database och datalagret finns i program i din AAD.  

5.  Välj **åtkomstkontroller**väljer **bevilja**, och kontrollera sedan principen som du vill använda. Det här exemplet väljer vi **kräver Multi-Factor authentication**.  
  ![Välj bevilja åtkomst](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Sammanfattning  
Det valda programmet (Azure SQL Database) så att ansluta till Azure SQL DB/DW med hjälp av Azure AD Premium, tillämpar nu valda principen för villkorlig åtkomst **krävs multifaktorautentisering.**  
Frågor om Azure SQL Database och datalagret för multifaktorautentisering Kontakta MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Nästa steg  

En självstudiekurs finns [skydda din Azure SQL Database](sql-database-security-tutorial.md).
