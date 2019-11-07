---
title: Säkerhet på kolumnnivå
description: Säkerhet på kolumn nivå (CLS) gör det möjligt för kunder att kontrol lera åtkomsten till databas tabell kolumner baserat på användarens körnings kontext eller grupp medlemskap. CLS fören klar utformningen och kodningen av säkerhet i ditt program. Med CLS kan du implementera begränsningar för kolumn åtkomst.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 605dfadaf4cd1686b124b120151e6a88a43f1a68
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693079"
---
# <a name="column-level-security"></a>Säkerhet på kolumn nivå
Säkerhet på kolumn nivå (CLS) gör det möjligt för kunder att kontrol lera åtkomsten till databas tabell kolumner baserat på användarens körnings kontext eller grupp medlemskap.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS fören klar utformningen och kodningen av säkerhet i ditt program. Med CLS kan du implementera begränsningar för kolumn åtkomst för att skydda känsliga data. Till exempel ser du till att specifika användare bara har åtkomst till vissa kolumner i en tabell som är relevanta för deras avdelning. Logiken för åtkomst begränsning finns i databas nivån i stället för bort från data i en annan program nivå. Databasen tillämpar åtkomst begränsningar varje gång som data åtkomsten görs från vilken nivå som helst. Den här begränsningen gör ditt säkerhets system mer tillförlitligt och stabilt genom att minska det övergripande säkerhets systemets Area. Dessutom eliminerar CLS även behovet av att introducera vyer för att filtrera bort kolumner för att få åtkomst begränsningar för användarna.

Du kan implementera CLS med [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) -T-SQL-instruktionen. Med den här mekanismen stöds både SQL-och Azure Active Directory-autentisering (AAD).

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
I följande exempel visas hur du begränsar "TestUser" från åtkomst till kolumnen "SSN" i tabellen "Membership":

Skapa medlems tabellen "medlemskap" med kolumnen SSN som används för att lagra socialförsäkrings nummer:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Tillåt ' TestUser ' för att få åtkomst till alla kolumner utom SSN-kolumnen som har känsliga data:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Frågor som har utförts som "TestUser" Miss söker om de innehåller kolumnen SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Användningsfall
Några exempel på hur CLS används idag:
- Ett finansiellt tjänste företag ger endast konto ansvariga åtkomst till kundens socialförsäkrings nummer (SSN), telefonnummer och annan personligt identifierbar information (PII).
- En hälso vårds leverantör tillåter endast att läkare och anställda har till gång till känsliga medicinska poster samtidigt som medlemmarna i fakturerings avdelningen inte kan visa dessa data.
