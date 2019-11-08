---
title: Villkorlig åtkomst
description: Lär dig hur du konfigurerar villkorlig åtkomst för Azure SQL Database och informations lager.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827170"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Villkorlig åtkomst (MFA) med Azure SQL Database och informations lager  

Azure [SQL Database](sql-database-technical-overview.md), [hanterad instans](sql-database-managed-instance.md)och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stöd för villkorlig åtkomst från Microsoft. 

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

Följande steg visar hur du konfigurerar SQL Database att tillämpa en princip för villkorlig åtkomst.  

## <a name="prerequisites"></a>Nödvändiga komponenter  
- Du måste konfigurera SQL Database eller SQL Data Warehouse för att stödja Azure Active Directory autentisering. Vissa steg finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- När Multi-Factor Authentication har Aktiver ATS måste du ansluta till ett verktyg som stöds, till exempel den senaste SSMS. Mer information finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurera CA för Azure SQL DB/DW  
1. Logga in på portalen, Välj **Azure Active Directory**och välj sedan **villkorlig åtkomst**. Mer information finns i [teknisk referens för Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   blad](./media/sql-database-conditional-access/conditional-access-blade.png) för villkorlig åtkomst ![ 
     
2. I bladet **villkorlig åtkomst – principer** , klickar du på **ny princip**, anger ett namn och klickar sedan på **Konfigurera regler**.  
3. Under **tilldelningar**väljer **du användare och grupper**, markerar **Välj användare och grupper**och väljer sedan användaren eller gruppen för villkorlig åtkomst. Klicka på **Välj**och sedan på **Slutför** för att godkänna ditt val.  
   ![välja användare och grupper](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Välj **molnappar**och klicka på **Välj appar**. Du ser alla appar som är tillgängliga för villkorlig åtkomst. Välj **Azure SQL Database**, klicka på **Välj**längst ned på sidan och klicka sedan på **färdig**.  
   ![Välj SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Om du inte hittar **Azure SQL Database** som anges i följande tredje skärm bild, utför du följande steg:   
   - Logga in på din Azure SQL DB/DW-instans med hjälp av SSMS med ett AAD-administratörskonto.  
   - Kör `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Logga in på AAD och kontrol lera att Azure SQL Database och informations lagret visas i programmen i din AAD.  

5. Välj **åtkomst kontroller**, Välj **bevilja**och kontrol lera den princip som du vill använda. I det här exemplet väljer vi **Kräv Multi-Factor Authentication**.  
   ![väljer bevilja åtkomst](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Sammanfattning  
Det valda programmet (Azure SQL Database) som gör det möjligt att ansluta till Azure SQL DB/DW med hjälp av Azure AD Premium, tillämpar nu den valda principen för villkorlig åtkomst, **krävs Multi-Factor Authentication.**  
Kontakta MFAforSQLDB@microsoft.comom du har frågor om Azure SQL Database och informations lagret om multifaktorautentisering.  

## <a name="next-steps"></a>Nästa steg  

En själv studie kurs finns i [skydda din Azure SQL Database](sql-database-security-tutorial.md).
