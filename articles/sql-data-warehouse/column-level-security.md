---
title: Azure SQL Data Warehouse kolumnnivå säkerhet | Microsoft Docs
description: Kolumnnivå säkerhet (CLS) ger kunder möjlighet att styra åtkomsten till databasen tabellkolumner baserat på användarens körningskontexten eller deras gruppmedlemskap. CLS förenklar design och kodning av säkerhet i ditt program. CLS kan du implementera begränsningar för kolumnen.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 5a916132f705f3c517ee6789b61a3972b2445b62
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938518"
---
# <a name="column-level-security"></a>Kolumnen säkerhet 
Kolumnnivå säkerhet (CLS) ger kunder möjlighet att styra åtkomsten till databasen tabellkolumner baserat på användarens körningskontexten eller deras gruppmedlemskap.  

CLS förenklar design och kodning av säkerhet i ditt program. CLS kan du implementera begränsningar på kolumnen åtkomst för att skydda känsliga data. Till exempel säkerställer att vissa användare har åtkomst till vissa kolumner i en tabell som är relevanta för deras avdelning. Logik för åtkomst-begränsning är finns i databasnivån i stället för avstånd från data i ett annat program skikt. Databasen gäller åtkomstbegränsningarna varje gång ett försök gjordes att dataåtkomst från alla skikt. Detta gör ditt säkerhetssystem mer tillförlitlig och stabil genom att minska ytan på din övergripande säkerhetssystem. Dessutom kan eliminerar detta även behovet av introduktion till vyer att filtrera bort kolumner för åtkomst begränsningar för användarna. 

Du kan implementera CLS med den [BEVILJA](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL-instruktionen. Med den här mekanismen stöds både SQL och Azure Active Directory (AAD)-autentisering.

![CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax 

```sql
GRANT <permission> [ ,...n ] ON    
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]   
    TO <database_principal> [ ,...n ]    
    [ WITH GRANT OPTION ]   
    [ AS <database_principal> ]   
<permission> ::=   
    SELECT 
  | UPDATE  
<database_principal> ::=    
      Database_user    
    | Database_role    
    | Database_user_mapped_to_Windows_User    
    | Database_user_mapped_to_Windows_Group    
```

## <a name="example"></a>Exempel 
I följande exempel visas hur du begränsar 'TestUser' från att komma åt 'SSN' kolumn 'medlemskap, tabell: 

Skapa 'Medlemskap' tabell med SSN kolumn som används för att lagra personnummer:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Tillåt 'TestUser' åtkomst till alla kolumner utom SSN som innehåller känsliga data: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Frågor som körs som 'TestUser' misslyckas om de innehåller kolumnen SSN:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Användningsfall
Några exempel på hur CLS används idag: 
- Ett företag för finansiella tjänster kan endast konto chefer ska ha åtkomst till kunden personnummer (SSN), telefonnummer och annan personligt identifierbar information (PII).
- Hälsovård providern tillåter endast läkare och sjuksköterskor ska ha åtkomst till känsliga medicinska poster samtidigt som inte medlemmar i avdelningen faktureringsinformation att visa dessa data.
