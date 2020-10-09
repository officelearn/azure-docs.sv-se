---
title: Använda SQL-transformering
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd SQL-omvandling i Azure Machine Learning för att köra en SQLite-fråga på indata-datauppsättningar för att transformera data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 9a195497b4376633bd3c767d7d0ea029109fdf9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "76314546"
---
# <a name="apply-sql-transformation"></a>Använda SQL-transformering

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Med hjälp av modulen Använd SQL-omvandling kan du:
  
-   Skapa tabeller för resultat och spara data uppsättningarna i en portabel databas.  
  
-   Utföra anpassade omvandlingar av data typer eller skapa agg regeringar.  
  
-   Kör SQL-frågeuttryck för att filtrera eller ändra data och returnera frågeresultaten som en data tabell.  

> [!IMPORTANT]
> SQL-motorn som används i den här modulen är **sqlite**. Mer information om SQLite-syntax finns i [SQL som förstås av sqlite](https://www.sqlite.org/index.html) för mer information.  

## <a name="how-to-configure-apply-sql-transformation"></a>Konfigurera tillämpa SQL-omvandling  

Modulen kan ta upp till tre data uppsättningar som indata. När du refererar till data uppsättningarna som är anslutna till varje indataport måste du använda namnen `t1` , `t2` och `t3` . Tabell numret anger indataportens index.  
  
Den återstående parametern är en SQL-fråga som använder SQLite-syntaxen. När du skriver flera rader i text rutan **SQL-skript** , använder du ett semikolon för att avsluta varje instruktion. Annars konverteras rad brytningar till blank steg.  

Den här modulen stöder alla standard-instruktioner för SQLite-syntaxen. En lista över instruktioner som inte stöds finns i avsnittet [tekniska anteckningar](#technical-notes) .

##  <a name="technical-notes"></a>Tekniska anteckningar  

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

-   Det krävs alltid indatatyper på port 1.  
  
-   För kolumn identifierare som innehåller ett blank steg eller andra specialtecken, omger du alltid kolumnens identifierare inom hakparenteser eller dubbla citat tecken när du refererar till kolumnen i or- `SELECT` `WHERE` satserna.  
  
### <a name="unsupported-statements"></a>Instruktioner som inte stöds  

Även om SQLite stöder mycket av ANSI SQL standard innehåller det inte många funktioner som stöds av kommersiella Relations databas system. Mer information finns i [SQL som förstås av sqlite](http://www.sqlite.org/lang.html). Tänk också på följande begränsningar när du skapar SQL-uttryck:  
  
- SQLite använder dynamisk inmatning för värden i stället för att tilldela en typ till en kolumn som i de flesta Relations databas system. Den är svagt skriven och tillåter implicit typ konvertering.  
  
- `LEFT OUTER JOIN` implementeras, men inte `RIGHT OUTER JOIN` eller `FULL OUTER JOIN` .  

- Du kan använda `RENAME TABLE` och- `ADD COLUMN` instruktioner med `ALTER TABLE` kommandot, men andra satser stöds inte, inklusive `DROP COLUMN` , `ALTER COLUMN` och `ADD CONSTRAINT` .  
  
- Du kan skapa en vy i SQLite, men därefter är vyn skrivskyddad. Det går inte att köra en `DELETE` -, `INSERT` -eller- `UPDATE` instruktion i en vy. Du kan dock skapa en utlösare som utlöses vid ett försök till `DELETE` , `INSERT` eller `UPDATE` i en vy och utföra andra åtgärder i utlösaren.  
  

Förutom listan med funktioner som inte stöds på den officiella webbplatsen för SQLite, innehåller följande wiki en lista över andra funktioner som inte stöds: [sqlite-SQL som](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql) inte stöds  
    
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
