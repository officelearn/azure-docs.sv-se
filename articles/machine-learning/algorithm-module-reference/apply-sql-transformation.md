---
title: Använda SQL-transformering
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd SQL Transformation i Azure Machine Learning för att köra en SQLite-fråga på indatauppsättningar för att omvandla data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314546"
---
# <a name="apply-sql-transformation"></a>Använda SQL-transformering

I den här artikeln beskrivs en modul med Azure Machine Learning designer (förhandsversion).

Med modulen Använd SQL Transformation kan du:
  
-   Skapa tabeller för resultat och spara datauppsättningarna i en bärbar databas.  
  
-   Utför anpassade omvandlingar på datatyper eller skapa aggregat.  
  
-   Kör SQL-frågesatser för att filtrera eller ändra data och returnera frågeresultaten som en datatabell.  

> [!IMPORTANT]
> SQL-motorn som används i den här modulen är **SQLite**. Mer information om SQLite-syntax finns i [SQL som förstås av SQLite](https://www.sqlite.org/index.html) för mer information.  

## <a name="how-to-configure-apply-sql-transformation"></a>Konfigurera Tillämpa SQL-omvandling  

Modulen kan ta upp till tre datauppsättningar som indata. När du refererar till de datauppsättningar som är `t1` `t2`anslutna `t3`till varje indataport måste du använda namnen , och . Tabellnumret anger indexet för indataporten.  
  
Den återstående parametern är en SQL-fråga som använder syntaxen SQLite. När du skriver flera rader i textrutan **SQL Script** använder du ett semikolon för att avsluta varje sats. Annars konverteras radbrytningar till blanksteg.  

Den här modulen stöder alla standardsatser i syntaxen SQLite. En lista över program som inte stöds finns i avsnittet [Tekniska anteckningar.](#technical-notes)

##  <a name="technical-notes"></a>Tekniska anmärkningar  

Det här avsnittet innehåller implementeringsinformation, tips och svar på vanliga frågor.

-   En ingång krävs alltid på port 1.  
  
-   För kolumnidentifierare som innehåller ett blanksteg eller andra specialtecken, omsluter alltid kolumnidentifieraren inom hakparenteser eller dubbla citattecken när du refererar till kolumnen i `SELECT` eller-satserna. `WHERE`  
  
### <a name="unsupported-statements"></a>Uttalanden som inte stöds  

Även om SQLite stöder mycket av ANSI SQL-standarden, innehåller den inte många funktioner som stöds av kommersiella relationsdatabassystem. Mer information finns i [SQL som förstås av SQLite](http://www.sqlite.org/lang.html). Tänk också på följande begränsningar när du skapar SQL-uttryck:  
  
- SQLite använder dynamisk skrivning för värden, i stället för att tilldela en typ till en kolumn som i de flesta relationsdatabassystem. Den skrivs svagt och tillåter implicit typkonvertering.  
  
- `LEFT OUTER JOIN`genomförs, men inte `RIGHT OUTER JOIN` `FULL OUTER JOIN`eller .  

- Du kan `RENAME TABLE` `ADD COLUMN` använda och `ALTER TABLE` satser med kommandot, men `DROP COLUMN`andra `ALTER COLUMN`satser stöds inte, inklusive , och `ADD CONSTRAINT`.  
  
- Du kan skapa en VY inom SQLite, men därefter är vyer skrivskyddade. Du kan `DELETE`inte `INSERT`köra `UPDATE` en , eller sats i en vy. Du kan dock skapa en utlösare `DELETE`som `INSERT`utlöses vid ett försök att , eller `UPDATE` i en vy och utföra andra åtgärder i utlösarens brödtext.  
  

Förutom listan över funktioner som inte stöds på den officiella SQLite-webbplatsen innehåller följande wiki en lista över andra funktioner som inte stöds: [SQLite - SQL som inte stöds](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
