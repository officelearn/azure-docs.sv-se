---
title: Vad är säkerhet på kolumnnivå för Azure Synapse?
description: Med kolumnnivåsäkerhet kan kunder styra åtkomsten till databastabellkolumner baserat på användarens körningskontext eller gruppmedlemskap, förenkla designen och kodningen av säkerheten i ditt program och låta dig implementera begränsningar för kolumnåtkomst.
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
ms.openlocfilehash: 61a3e2eadaf79cdb30a931b31cff709298d0a22c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631303"
---
# <a name="column-level-security"></a>Säkerhet på kolumnnivå

Med säkerhet på kolumnnivå kan kunder styra åtkomsten till tabellkolumner baserat på användarens körningskontext eller gruppmedlemskap.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Eftersom den här videon lades upp [blev Radnivå säkerhet](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) tillgänglig för Azure Synapse.

Säkerhet på kolumnnivå förenklar utformningen och kodningen av säkerheten i ditt program, så att du kan begränsa kolumnåtkomsten för att skydda känsliga data. Se till exempel till att specifika användare bara kan komma åt vissa kolumner i en tabell som är relevant för deras avdelning. Logiken för åtkomstbegränsning finns på databasnivån i stället för bort från data på en annan programnivå. Databasen tillämpar åtkomstbegränsningarna varje gång dataåtkomst görs från valfri nivå. Den här begränsningen gör din säkerhet mer tillförlitlig och robust genom att minska ytan på ditt övergripande säkerhetssystem. Dessutom eliminerar säkerhet på kolumnnivå också behovet av att införa vyer för att filtrera bort kolumner för att införa åtkomstbegränsningar för användarna.

Du kan implementera säkerhet på kolumnnivå med [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-uttrycket. Med den här mekanismen stöds både SQL- och Azure Active Directory-autentisering (AAD).

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
