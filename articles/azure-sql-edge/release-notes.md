---
title: Viktig information för Azure SQL Edge
description: Viktig information om vad som är nytt eller vad som har ändrats i Azure SQL Edge-avbildningarna
keywords: versions anteckningar SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 3306e51fe2fdbb2586be9684432d8f8c310afe95
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900592"
---
# <a name="azure-sql-edge-release-notes"></a>Viktig information om Azure SQL Edge 

I den här artikeln beskrivs vad som är nytt och vad som har ändrats med varje ny version av Azure SQL Edge.

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge-1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001549"></a>SQL-motor versions nummer – 15.0.2000.1549

### <a name="whats-new"></a>Nyheter
1. Ubuntu 18,04-baserade behållar avbildningar. 
2. Stöd för `IGNORE NULL` och `RESPECT NULL` syntax med `LAST_VALUE()` och- `FIRST_VALUE()` funktioner. 
3. Tillförlitlighets förbättringar för förutsägelse med ONNX.
4. Stöd för principbaserad rensning av datakvarhållning.      
   - Stöd för optimerad rensning för grupperade columnstore-index.
5. Stöd för nya funktioner 
   - Snabb återställning
   - Automatisk justering av frågor

### <a name="fixes"></a>Korrigeringar
1. Ytterligare fel meddelanden och information om fel sökning av TSQL streaming-åtgärder. 
2. Förbättringar för att bevara batteri tiden i vilo läge. 
3. TSQL-strömmande motor korrigerar: 
   - Rensa för stoppat strömmande jobb 
   - Korrigeringar för förbättringar av lokalisering och Unicode-hantering
4. Princip baserad rensning för datakvarhållning
   - Korrigeringar för skapande och rensnings scenarier för bevarande principer.
5. Korrigeringar för aktiviteter i bakgrunden för att förbättra energi besparingarna för låg energi läge.


## <a name="ctp-23"></a>CTP 2,3
### <a name="sql-engine-build-number---15020001549"></a>SQL-motor versions nummer – 15.0.2000.1549
### <a name="whats-new"></a>Nyheter
1. Stöd för anpassade ursprung i funktionen Date_Bucket (). 
2. Stöd för BacPac-filer som en del av SQL-distributionen.
3. Stöd för principbaserad rensning av datakvarhållning.      
   - DDL-stöd för att aktivera bevarande princip 
   - Rensa lagrade procedurer och bakgrunds rensnings uppgift
   - Utökade händelser för att övervaka rensnings aktiviteter

### <a name="fixes"></a>Korrigeringar
1. Ytterligare fel meddelanden och information om fel sökning av TSQL streaming-åtgärder. 
2. Förbättringar för att bevara batteri tiden i vilo läge. 
3. TSQL-strömmande motor korrigerar: 
   - Åtgärda problem med den fastnade vattenstämpeln med hoppande-fönstret 
   - Åtgärda undantags hantering i ramverk för att se till att det samlas in som användar åtgärds fel


## <a name="ctp-22"></a>CTP 2,2
### <a name="sql-engine-build-number---15020001546"></a>SQL-motor versions nummer – 15.0.2000.1546
### <a name="whats-new"></a>Nyheter
1. Stöd för icke-rot behållare 
2. Stöd för insamling av användnings data och diagnostikdata 
3. Strömnings uppdateringar för T-SQL
   - Stöd för Unicode-tecken för strömmande objekt namn

### <a name="fixes"></a>Korrigeringar
1. Strömnings uppdateringar för T-SQL
   - Förbättringar av processen rensa
   - Förbättringar av loggning och diagnostik
2. Prestanda förbättring för data inmatning

## <a name="ctp-21"></a>CTP 2,1 
### <a name="sql-engine-build-number---15020001545"></a>SQL-motor versions nummer – 15.0.2000.1545
### <a name="fixes"></a>Korrigeringar
1. Åtgärda problemet med ONNX-modeller för att hantera CPUID-problem i ARM 
2. Korrigera för att förbättra hanteringen av en felsökväg vid start av TSQL streaming 
3. Åtgärda det felaktiga värdet för fördröjning av vattenstämpel i jobb mått när det inte finns några data. 
4. Åtgärda problemet med utmatnings kort när kortet har ett variabelt schema mellan batchar.  

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>SQL-motor versions nummer – 15.0.2000.1401
### <a name="whats-new"></a>Nyheter
1.  Produkt namnet har uppdaterats till Azure SQL Edge
1.  Date_bucket funktion

    i.  Stöd för datum, tid, DateTime-typ
3.  FÖRUTSÄG med ONNX
    
    i.  KÖRNINGs parametern krävs för ONNX 
    
4.  Stöd för TSQL-direktuppspelning (begränsad förhands granskning) 
 
### <a name="known-issues"></a>Kända problem

1. <b>Problem:</b> Potentiella fel vid användning av DACPAC vid start på grund av tids inställnings problem.

    <b>Lösning:</b> Omstarten av SQL Server eller containern försöker tillämpa DACPAC igen och bör åtgärda problemet
### <a name="request-support"></a>Support för begäran
1. Du kan begära support på [support Sidan](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Se till att följande fält är markerade: 
    * Typ av problem – teknisk 
    * Tjänst-IoT Edge
    * Problem typ-mitt problem relaterar till en IoT Edge-modul
    * Underordnad problem – Azure SQL Edge

   ![Exempel på support ärende](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>SQL-motor versions nummer – 15.0.2000.1331
### <a name="whats-new"></a>Nyheter
1. Date_bucket funktion
    
    i. Stöd för DateTimeOffset-typ
2. FÖRUTSÄG med ONNX-modeller

    i. nvarchar-stöd
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>SQL-motor versions nummer – 15.0.2000.1247
### <a name="whats-new"></a>Nyheter
1.  FÖRUTSÄG med ONNX-modeller
 
    i.  Stöd för varchar
    
    ii. Migrering till ONNX runtime version 1,0 
2.  Funktions stöd – följande funktioner är aktiverade:

    i.   CDC-stöd

    ii.  Historik tabell med komprimering

    iii. Högre skalnings faktor för logg läsning framåt

    a.  Batch-läge ES filter mottagnings

    v.   Läs optimeringar i förväg
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>SQL-motor versions nummer – 15.0.2000.1147
### <a name="whats-new"></a>Nyheter
1. Distribution av Azure IOT-portalen 

    i.   Stöd för distribution av AMD64-och ARM-avbildningar

    ii.  Stöd för att skapa strömmande jobb

    iii. DACPAC-distribution
2. FÖRUTSÄG med ONNX-modeller

    i. Stöd för numerisk typ
3. Funktions stöd – följande funktioner är aktiverade:

    i.  Mottagnings agg regering till kolumn lagrings sökning

    ii. God go – runda genomsökningar
4. Arbets plats-och minnes förbruknings minskning
