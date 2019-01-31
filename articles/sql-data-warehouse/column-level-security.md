---
title: Azure SQL Data Warehouse på kolumnnivå-säkerhet | Microsoft Docs
description: På kolumnnivå säkerhet (CLS) ger kunder möjlighet att styra åtkomsten till databasen tabellkolumner baserat på användarens körningskontexten skapades eller deras gruppmedlemskap. CLS förenklar design och kodning av säkerheten i ditt program. CLS kan du implementera begränsningar för kolumnen åtkomst.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: 323879fff90fa478797f85415faae9ae02ea5bcd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461516"
---
# <a name="column-level-security"></a>Säkerhet på radnivå kolumn 
På kolumnnivå säkerhet (CLS) ger kunder möjlighet att styra åtkomsten till databasen tabellkolumner baserat på användarens körningskontexten skapades eller deras gruppmedlemskap.  

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS förenklar design och kodning av säkerheten i ditt program. CLS kan du implementera begränsningar för kolumnen åtkomst till att skydda känsliga data. Till exempel se till att vissa användare har åtkomst till vissa av kolumnerna i en tabell som är relevanta för deras avdelning. Logiken för begränsning av är placerad på databasnivån i stället för in från data i en annan programnivå. Databasen gäller åtkomstbegränsningarna varje gång den dataåtkomsten görs från valfri nivå. Den här begränsningen gör din säkerhetssystem mer tillförlitlig och stabil genom att minska ytan på din övergripande säkerhetssystem. Dessutom eliminerar CLS även behovet introduktion till vyer att filtrera bort kolumner för aktiviteternas åtkomstbegränsningarna för användarna. 

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
I följande exempel visas hur du begränsar 'TestUser' från att komma åt 'SSN' kolumnen 'Medlemskap ”-tabellen: 

Skapa 'Medlemskap ”tabell med SSN kolumn som används för att lagra personnummer:

```sql
CREATE TABLE Membership   
  (MemberID int IDENTITY,   
   FirstName varchar(100) NULL,   
   SSN char(9) NOT NULL, 
   LastName varchar(100) NOT NULL,   
   Phone varchar(12) NULL,   
   Email varchar(100) NULL);  
```

Tillåt 'TestUser} att få åtkomst till alla kolumner utom SSN som innehåller känsliga data: 

```sql  
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;   
``` 

Frågor som ”TestUser' misslyckas om de innehåller kolumnen SSN:

```sql  
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'. 
``` 

## <a name="use-cases"></a>Användningsfall
Några exempel på hur CLS används idag: 
- Ett företag för finansiella tjänster kan endast konto hanterare kan ha åtkomst till kunden personnummer (SSN), telefonnummer och annan personligt identifierbar information (PII).
- En hälsovård provider kan endast läkare och sjuksköterskor ska ha åtkomst till känsliga medicinska journaler samtidigt som medlemmar i avdelningen fakturering inte att visa dessa data.
