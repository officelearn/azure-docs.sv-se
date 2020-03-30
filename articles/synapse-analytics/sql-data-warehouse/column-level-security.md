---
title: Vad är säkerhet på kolumnnivå för Azure Synapse?
description: Med säkerhet på kolumnnivå kan kunderna styra åtkomsten till databastabellkolumner baserat på användarens körningskontext eller gruppmedlemskap, förenkla designen och kodningen av säkerheten i ditt program och låta dig implementera begränsningar för kolumn Tillgång.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 24ead458232b096a5c69ffe8b45c6298a9da9f75
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349098"
---
# <a name="column-level-security"></a>Säkerhet på kolumnnivå

Med säkerhet på kolumnnivå kan kunder styra åtkomsten till tabellkolumner baserat på användarens körningskontext eller gruppmedlemskap.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Eftersom den här videon lades upp [blev Radnivå säkerhet](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) tillgänglig för Azure Synapse. 

Säkerhet på kolumnnivå förenklar utformningen och kodningen av säkerheten i ditt program, så att du kan begränsa kolumnåtkomsten för att skydda känsliga data. Se till exempel till att specifika användare bara kan komma åt vissa kolumner i en tabell som är relevant för deras avdelning. Logiken för åtkomstbegränsning finns på databasnivån i stället för bort från data på en annan programnivå. Databasen tillämpar åtkomstbegränsningarna varje gång dataåtkomst görs från valfri nivå. Den här begränsningen gör din säkerhet mer tillförlitlig och robust genom att minska ytan på ditt övergripande säkerhetssystem. Dessutom eliminerar säkerhet på kolumnnivå också behovet av att införa vyer för att filtrera bort kolumner för att införa åtkomstbegränsningar för användarna.

Du kan implementera säkerhet på kolumnnivå med [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL-uttrycket. Med den här mekanismen stöds både SQL- och Azure Active Directory-autentisering (AAD).

![Cls](./media/column-level-security/cls.png)

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
I följande exempel visas `TestUser` hur du `SSN` begränsar `Membership` från att komma åt kolumnen i tabellen:

Skapa `Membership` tabell med SSN-kolumn som används för att lagra personnummer:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Tillåt `TestUser` att komma åt alla kolumner utom SSN-kolumnen, som har känsliga data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Frågor som körs `TestUser` som misslyckas om de innehåller SSN-kolumnen:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Användningsfall

Några exempel på hur säkerhet på kolumnnivå används idag:

- Ett företag för finansiella tjänster tillåter endast kontoansvariga att ha tillgång till kundnummer (SSN), telefonnummer och annan personligt identifierbar information (PII).
- En vårdgivare tillåter endast läkare och sjuksköterskor att ha tillgång till känsliga journaler samtidigt som medlemmar av faktureringsavdelningen från att visa dessa uppgifter.
