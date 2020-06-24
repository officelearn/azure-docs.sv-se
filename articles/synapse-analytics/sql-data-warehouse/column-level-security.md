---
title: Vad är säkerhet på kolumn nivå för Azure-Synapse?
description: Säkerhet på kolumn nivå gör det möjligt för kunder att styra åtkomsten till databas tabell kolumner baserat på användarens körnings kontext eller grupp medlemskap, förenkla utformningen och kodningen av säkerhet i ditt program och gör att du kan implementera begränsningar för kolumn åtkomst.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: a4da74c01f732f3a62d29847d5f61934dede9778
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208101"
---
# <a name="column-level-security"></a>Säkerhet på kolumn nivå

Säkerhet på kolumn nivå gör det möjligt för kunder att styra åtkomsten till tabell kolumner baserat på användarens körnings kontext eller grupp medlemskap.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Eftersom den här videon har publicerats [säkerhet på radnivå](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) blev tillgänglig för Azure Synapse.

Säkerhet på kolumn nivå fören klar utformningen och kodningen av säkerhet i ditt program, så att du kan begränsa kolumn åtkomsten för att skydda känsliga data. Till exempel ser du till att specifika användare bara har åtkomst till vissa kolumner i en tabell som är relevanta för deras avdelning. Logiken för åtkomst begränsning finns i databas nivån i stället för bort från data i en annan program nivå. Databasen tillämpar åtkomst begränsningar varje gång data åtkomsten görs från vilken nivå som helst. Den här begränsningen gör din säkerhet mer tillförlitlig och robust genom att minska det totala säkerhets systemets Area. Dessutom eliminerar säkerhet på kolumn nivå behovet av att introducera vyer för att filtrera bort kolumner för att få åtkomst begränsningar för användarna.

Du kan implementera säkerhet på kolumn nivå med instruktionen [bevilja](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL. Med den här mekanismen stöds både SQL-och Azure Active Directory-autentisering (AAD).

![CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```syntaxsql
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

I följande exempel visas hur du begränsar `TestUser` åtkomsten till `SSN` kolumnen i `Membership` tabellen:

Skapa `Membership` tabell med SSN-kolumn som används för att lagra person nummer:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Tillåt `TestUser` åtkomst till alla kolumner utom kolumnen SSN, som har känsliga data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Frågor som körs som `TestUser` inte fungerar om de innehåller kolumnen SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Användningsfall

Några exempel på hur säkerhet på kolumn nivå används idag:

- Ett finansiellt tjänste företag ger endast konto ansvariga åtkomst till kundens socialförsäkrings nummer (SSN), telefonnummer och annan personligt identifierbar information (PII).
- En hälso vårds leverantör tillåter endast att läkare och anställda har till gång till känsliga medicinska poster samtidigt som medlemmarna i fakturerings avdelningen inte kan visa dessa data.
