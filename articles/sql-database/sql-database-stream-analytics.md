---
title: Strömma data med hjälp av Stream Analytics-integrering (förhandsgranskning)
description: Använd Azure Stream Analytics för att strömma data till en Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820832"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Strömma data med hjälp av Azure SQL Database Stream Analytics-integrering (förhandsversion)

Användare kan nu inta, bearbeta, visa och analysera strömmande data i realtid till en tabell direkt från en SQL-databas i Azure-portalen med Hjälp av [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Den här upplevelsen möjliggör en mängd olika scenarier, till exempel ansluten bil, fjärrövervakning, bedrägeriidentifiering och många fler. I Azure-portalen kan du välja en händelsekälla (Event Hub/IoT Hub), visa inkommande realtidshändelser och välja en tabell för att lagra händelser. Du kan också skriva frågor om Dataanalysfrågespråk i portalen för att omvandla inkommande händelser och lagra dem i den valda tabellen. Den här nya startpunkten är ett tillägg till de skapande- och konfigurationsupplevelser som redan finns i Stream Analytics. Den här upplevelsen utgår från databasens kontext, så att du snabbt kan konfigurera ett Stream Analytics-jobb och navigera sömlöst mellan Azure SQL Database och Stream Analytics-upplevelserna.

![Flöde för Strömma analys](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Viktiga fördelar

- Minsta kontextväxling: Du kan börja från en SQL-databas i portalen och börja inta realtidsdata i en tabell utan att byta till någon annan tjänst. 
- Minskat antal steg: Kontexten för databasen och tabellen används för att förkonfigurera ett Stream Analytics-jobb.
- Ytterligare användarvänlighet med förhandsgranskningsdata: Förhandsgranska inkommande data från händelsekällan (Event Hub/IoT Hub) i samband med markerad tabell 


## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln behöver du följande resurser:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/). 
- En SQL-databas. Mer information finns [i Skapa en enskild databas i Azure SQL Database](sql-database-single-database-get-started.md).
- En brandväggsregel som gör att datorn kan ansluta till Azure SQL-servern. Mer information finns i [Skapa en brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Konfigurera integrering av Stream-analys

1. Logga in på Azure Portal. 
2. Navigera till din SQL-databas där du vill använda dina strömmande data. Välj **Stream-analys (förhandsgranskning)**. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Om du vill börja mata in strömmande data i den här SQL-databasen väljer du **Skapa** och ger ditt direktuppspelningsjobb ett namn och väljer sedan **Nästa: Inmatning**. 

    ![skapa Stream Analytics-jobb](media/sql-database-stream-analytics/create-job.png)

4. Ange information om händelsekällan och välj sedan **Nästa: Utdata**.

   - **Indatatyp**: Event Hub/IoT Hub
   - **Inmatningsalias:** Ange ett namn för att identifiera händelsekällan 
   - **Prenumeration:** Samma som SQL Database-prenumeration 
   - **Namnområde för händelsehubben**: Namn på namnområde 
   - **Namnet på händelsehubben**: Namn på händelsehubben i det valda namnområdet 
   - **Händelsehubbens principnamn** (Standard för att skapa nytt): Ange ett principnamn 
   - **Konsumentgrupp för eventhubb** (standard för att skapa ny): Ange ett konsumentgruppnamn  
     - Vi rekommenderar att du skapar en konsumentgrupp och en princip för varje nytt Azure Stream Analytics-jobb som du skapar härifrån. Konsumentgrupper tillåter endast fem samtidiga läsare, så att tillhandahålla en särskild konsumentgrupp för varje jobb kommer att undvika eventuella fel som kan uppstå från att överskrida denna gräns. Med en dedikerad princip kan du rotera nyckeln eller återkalla behörigheter utan att påverka andra resurser.

    ![skapa Stream Analytics-jobb](media/sql-database-stream-analytics/create-job-output.png)

5. Välj vilken tabell du vill förtära dina strömmande data till. När du är klar väljer du **Skapa**.
   - **Användarnamn**, **Lösenord:** Ange dina autentiseringsuppgifter för SQL-serverautentisering. Välj **Verifiera**.
   - **Tabell**: Välj **Skapa nytt** eller **Använd befintlig**. I det här flödet ska vi välja **Skapa**. Då skapas en ny tabell när du startar stream Analytics-jobbet.

    ![skapa Stream Analytics-jobb](media/sql-database-stream-analytics/create.png)

6. En frågesida öppnas med följande information:

   - Din **inmatning** (indatahändelser källa) som du kommer att mata data  
   - **Utdata** (utdatatabell) som lagrar transformerade data 
   - Exempel [på SAQL-fråga](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) med SELECT-sats. 
   - **Förhandsgranskning**av indata : Visar ögonblicksbild av senaste inkommande data från indatahändelser källa.  
     - Serialiseringstypen i dina data identifieras automatiskt (JSON/CSV). Du kan manuellt ändra det också till JSON / CSV / AVRO. 
     - Du kan förhandsgranska inkommande data i tabellformat eller råformat. 
     - Om dina data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna. 
     - Välj **tidsintervall** för att testa frågan mot ett visst tidsintervall för inkommande händelser. 
     - Välj **Ladda upp exempelindata** för att testa frågan genom att ladda upp en exempel-JSON/CSV-fil. Mer information om hur du testar en SAQL-fråga finns i [Testa ett Azure Stream Analytics-jobb med exempeldata](../stream-analytics/stream-analytics-test-query.md). 

    ![testfråga](media/sql-database-stream-analytics/test-query.png)


   - **Testresultat:** Välj **testfråga** och du kan se resultatet av din strömningsfråga 

    ![testresultat](media/sql-database-stream-analytics/test-results.png)

   - **Testresultatschema:** Visar schemat för resultaten av din direktuppspelningsfråga efter testning. Kontrollera att testresultatschemat matchar med utdataschemat. 

    ![schema för testresultat](media/sql-database-stream-analytics/test-results-schema.png)


   - **Utdataschema:** Detta innehåller schemat för den tabell som du valde i steg 5 (nytt eller befintligt).
     - Skapa nytt: Om du valde det här alternativet i steg 5 visas inte schemat ännu förrän du har påbörjat uppspelningsjobbet. När du skapar en ny tabell väljer du lämpligt tabellindex. Mer information om tabellindexering finns i [Grupperade och icke-klädda index beskrivna](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Använd befintliga: Om du valde det här alternativet i steg 5 visas schemat för den markerade tabellen. 
 
7. När du har redigerat & som testar frågan väljer du **Spara fråga**. Välj **Start Stream Analytics-jobb** om du vill börja inta omvandlade data till SQL-tabellen. Starta jobbet när du **start** har slutfört följande fält. 
   - **Utdatastarttid**: Detta definierar tiden för den första utdata för jobbet.  
     - Nu: Jobbet startar nu och bearbetar nya inkommande data.
     - Anpassad: Jobbet startar nu men bearbetar data från en viss tidpunkt (som kan vara tidigare eller i framtiden). Mer information finns i [Så här startar du ett Azure Stream Analytics-jobb](../stream-analytics/start-job.md).
   - **Streamingenheter:** Azure Stream Analytics prissätts av antalet enheter för direktuppspelning som krävs för att bearbeta data till tjänsten. Mer information finns i [Azure Stream Analytics priser](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Hantering av datafel för utdata:**  
     - Försök igen: När ett fel inträffar försöker Azure Stream Analytics skriva händelsen på obestämd tid tills skrivningen lyckas. Det finns ingen timeout för återförsök. Så småningom blockeras alla efterföljande händelser från bearbetning av händelsen som försöker igen. Det här alternativet är standardprincipen för hantering av utdatafel. 
     - Släpp: Azure Stream Analytics släpper alla utdatahändelse som resulterar i ett datakonverteringsfel. Utelämnade händelser kan inte återställas för bearbetning vid ett senare tillfälle. Alla tillfälliga fel (till exempel nätverksfel) görs på nytt oavsett konfigurationen av principkonfigurationen för hantering av utdatafel. 
   - **SQL Database-utdatainställningar:** Ett alternativ för att ärva partitioneringsschemat för föregående frågesteg, för att aktivera helt parallell topologi med flera författare till tabellen. Mer information finns i [Azure Stream Analytics-utdata till Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Max batchantal:** Den rekommenderade övre gränsen för antalet poster som skickas med varje bulkinfogningstransaktion.  
    Mer information om hantering av utdatafel finns [i principer för utdatafel i Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![starta jobb](media/sql-database-stream-analytics/start-job.png)

8. När du har påbörjat jobbet visas jobbet Kör i listan och du kan vidta följande åtgärder: 
   - **Starta/stoppa jobbet**: Om jobbet körs kan du stoppa jobbet. Om jobbet stoppas kan du starta jobbet. 
   - **Redigera jobb**: Du kan redigera frågan. Om du vill göra fler ändringar i jobbexej lägger du till fler indata/utdata och öppnar sedan jobbet i Stream Analytics. Alternativet Redigera är inaktiverat när jobbet körs. 
   - **Förhandsgranska utdatatabell:** Du kan förhandsgranska tabellen i SQL Query Editor. 
   - **Öppna i Stream Analytics**: Öppna jobbet i Tjänsten Stream Analytics för att visa övervakning, felsöka information om jobbet. 


    ![strömma analysjobb](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics-lösningsmönster](../stream-analytics/stream-analytics-solution-patterns.md)
