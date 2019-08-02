---
title: Azure SQL Database funktions begränsningar | Microsoft Docs
description: Azure SQL Database funktions begränsningar förbättrar databas säkerheten genom att begränsa funktionerna i databasen som kan vara av angripare för att få till gång till information i dem.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: 5f5123624b5b9388baf799b48127b5b796eec21b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568209"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database funktions begränsningar

En gemensam källa för SQL Server-attacker är via webb program som har åtkomst till databasen där olika typer av SQL-injektering-attacker används för att få information om databasen.  Vi rekommenderar att program koden utvecklas så att den inte tillåter SQL-inmatning.  I stora kod baser som innehåller äldre och extern kod kan det dock aldrig vara säkert att alla ärenden har åtgärd ATS, så SQL-injektioner är ett visst liv som vi måste skydda mot.  Syftet med funktions begränsningar är att förhindra att vissa typer av SQL-injektering läcker information om databasen, även när SQL-injekteringen har slutförts.

## <a name="enabling-feature-restrictions"></a>Aktivera funktions begränsningar

Att aktivera funktions begränsningar görs med den `sp_add_feature_restriction` lagrade proceduren enligt följande:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Följande funktioner kan begränsas:

| Funktion          | Beskrivning |
|------------------|-------------|
| N'ErrorMessages' | Om detta är begränsat maskeras alla användar data i fel meddelandet. Se [begränsningen för fel meddelanden](#error-messages-feature-restriction) |
| N'Waitfor'       | Om detta är begränsat returneras kommandot omedelbart utan fördröjning. Se [begränsningar för waitfor-funktionen](#waitfor-feature-restriction) |

Värdet för `object_class` kan vara antingen `N'User'` eller `N'Role'` för att ange om `object_name` är ett användar namn eller ett rollnamn i databasen.

I följande exempel kommer alla fel meddelanden för användaren `MyUser` att maskeras:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Inaktiverar funktions begränsningar

Att inaktivera funktions begränsningar görs med den `sp_drop_feature_restriction` lagrade proceduren enligt följande:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

I följande exempel inaktive ras fel meddelande maskering för `MyUser`användaren:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Visa funktions begränsningar

`sys.sql_feature_restrictions` Vyn visar alla nyligen definierade funktions begränsningar för databasen. Den innehåller följande kolumner:

| Kolumnnamn | Datatyp | Beskrivning |
|-------------|-----------|-------------|
| Klass       | nvarchar (128) | Objekt klass som begränsningen gäller |
| object      | nvarchar (256) | Namn på objektet som begränsningen gäller |
| funktion     | nvarchar (128) | Funktion som är begränsad |

## <a name="feature-restrictions"></a>Funktions begränsningar

### <a name="error-messages-feature-restriction"></a>Funktions begränsning för fel meddelanden

En vanlig attack metod för SQL-inmatning är att mata in kod som orsakar ett fel.  Genom att undersöka fel meddelandet kan en angripare lära sig mer om systemet, vilket möjliggör ytterligare mer riktade attacker.  Det här angreppet kan vara särskilt användbart när programmet inte visar resultatet av en fråga, men visar fel meddelanden.

Överväg ett webb program som har en begäran i form av:

```html
http://www.contoso.com/employee.php?id=1
```

Som kör följande databas fråga:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Om det värde som skickas som `id` parameter till begäran om webb programmet kopieras för att ersätta $EmpId i databas frågan, kan en angripare göra följande begäran:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

Och följande fel returneras, så att angriparen kan lära sig namnet på databasen:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

När funktionen för fel meddelanden har Aktiver ATS för program användaren i databasen, maskeras det fel meddelande som returnerades så att ingen intern information om databasen läcker:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

På samma sätt kan angriparen göra följande begäran:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

Och följande fel returneras, så att angriparen kan lära sig den anställdas lön:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Om du använder begränsningen för fel meddelanden returnerar databasen:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Begränsningar för WAITFOR-funktionen

En blind SQL-inmatning är när ett program inte tillhandahåller en angripare med resultaten från den inmatade SQL-filen eller med ett fel meddelande, men angriparen kan härleda information från databasen genom att skapa en villkors fråga i vilken de två villkorliga grenarna ta en annan tid att köra. Genom att jämföra svars tiden vet angriparen vilken gren som kördes och kan därmed lära sig mer om systemet. Den enklaste varianten av det här angreppet använder `WAITFOR` instruktionen för att introducera fördröjningen.

Överväg ett webb program som har en begäran i form av:

```html
http://www.contoso.com/employee.php?id=1
```

som kör följande databas fråga:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Om värdet som skickas som ID-parameter till webb program begär Anden kopieras för att ersätta $EmpId i databas frågan, kan en angripare göra följande begäran:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

Och frågan skulle ta ytterligare 5 sekunder om `sa` kontot användes. Om `WAITFOR` funktions begränsningen är inaktive rad i `WAITFOR` databasen ignoreras instruktionen och ingen information läcker med den här angreppet.