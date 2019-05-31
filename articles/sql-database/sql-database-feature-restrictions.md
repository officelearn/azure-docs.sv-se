---
title: Begränsningar för Azure SQL Database-funktionen | Microsoft Docs
description: Begränsningar för Azure SQL Database-funktionen förbättrar din databassäkerhet genom att begränsa funktionerna i databasen som kan vara av angripare att få tillgång till informationen i dem.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259459"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database-funktionsbegränsningar

En gemensam källa för SQL Server-attacker är via webbprogram som använder databasen där olika former av SQL-inmatningsattacker används för att få djupare förståelse från information om databasen.  Vi rekommenderar utvecklas programkod så att den inte tillåter SQL-inmatning.  Men i stora-kodbaser som innehåller äldre och externa kod, vara ett aldrig säker på att alla fall har åtgärdat så SQL-inmatningar upp som vi har för att skydda mot.  Målet med funktionsbegränsningar är att förhindra vissa typer av SQL-inmatning från att läcka information om databasen, även när SQL-inmatning är klar.

## <a name="enabling-feature-restrictions"></a>Aktivera funktionsbegränsningar

Aktivera funktionsbegränsningar görs med hjälp av den `sp_add_feature_restriction` lagrade proceduren på följande sätt:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Följande funktioner kan vara begränsad:

| Funktion          | Beskrivning |
|------------------|-------------|
| N'ErrorMessages' | När begränsad, kommer att maskeras några användardata i felmeddelandet. Se [felmeddelanden funktionen begränsning](#error-messages-feature-restriction) |
| N'Waitfor'       | När begränsad, returnerar kommandot omedelbart utan fördröjning. Se [WAITFOR funktionen begränsning](#waitfor-feature-restriction) |

Värdet för `object_class` kan vara antingen `N'User'` eller `N'Role'` att ange om `object_name` är ett användarnamn eller ett rollnamn i databasen.

I följande exempel leder till alla felmeddelanden för användaren `MyUser` som ska maskeras:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Inaktivera funktionsbegränsningar

Inaktivera funktionsbegränsningar görs med hjälp av den `sp_drop_feature_restriction` lagrade proceduren på följande sätt:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

I följande exempel inaktiverar fel meddelande maskning för användaren `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Visa funktionsbegränsningar

Den `sys.sql_feature_restrictions` vyn visar alla nuvarande definierade funktionsbegränsningar för databasen. Den har följande kolumner:

| Kolumnnamn | Datatyp | Beskrivning |
|-------------|-----------|-------------|
| Klass       | nvarchar(128) | Objektklass som begränsningen gäller |
| objekt      | nvarchar(256) | Namnet på objektet som begränsningen gäller |
| Funktionen     | nvarchar(128) | Funktion som är begränsad |

## <a name="feature-restrictions"></a>Funktionsbegränsningar

### <a name="error-messages-feature-restriction"></a>Fel meddelanden funktionen begränsning

En vanlig SQL-inmatning attack metod är att injicera kod som orsakar ett fel.  Genom att undersöka felmeddelandet kan en angripare få information om systemet, att aktivera ytterligare mer riktade attacker.  Angreppet kan vara särskilt användbart om programmet inte visa resultatet av en fråga, men visas felmeddelanden.

Överväg ett webbprogram som har en begäran i form av:

```html
http://www.contoso.com/employee.php?id=1
```

Som kör följande databasfråga:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Om värdet skickas som den `id` parameter webbegäran för programmet kopieras för att ersätta $EmpId i databasfrågan, en angripare kan göra följande begäran:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

Och följande fel returneras, och ta reda på namnet på databasen:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

När du aktiverar felmeddelanden funktion begränsning för programanvändare i databasen, maskeras returneras följande felmeddelande så att inga intern information om databasen har läckts:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Angriparen kan se följande begäran:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

Och följande fel returneras, och Läs medarbetarens lön:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Med fel meddelanden funktionen begränsning kan returneras databasen:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR-funktionen begränsning

En hemlig SQL-inmatning är när ett program inte ger en angripare med resultatet från den inmatade SQL eller med ett felmeddelande, men angriparen kan hämta information från databasen genom att skapa en villkorlig fråga där två villkorlig grenar tar en annan tid att köra. Genom att jämföra svarstiden kan angriparen vet vilken gren som har körts, och därmed få information om systemet. Den enklaste varianten av angrepp använder den `WAITFOR` instruktionen att introducera fördröjningen.

Överväg ett webbprogram som har en begäran i form av:

```html
http://www.contoso.com/employee.php?id=1
```

som kör följande databasfråga:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Om värdet som skickas som ID-parametern till webbprogrambegäranden kopieras för att ersätta $EmpId i databasfrågan, kan en angripare kan göra följande begäran:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

Och frågan tar en ytterligare 5 sekunder om den `sa` kontot användes. Om `WAITFOR` funktionen begränsning har inaktiverats i databasen, den `WAITFOR` instruktionen ignoreras och inte information har läckts med angreppet.