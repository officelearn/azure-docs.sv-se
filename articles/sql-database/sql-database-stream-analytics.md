---
title: Strömma data med hjälp av Azure SQL Database Stream Analytics integration (för hands version)
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
ms.openlocfilehash: fb889f14c3370e1297f98110903c64e93e09c946
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687072"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Strömma data med hjälp av Azure SQL Database Stream Analytics integration (för hands version)

Användarna kan nu mata in, bearbeta, Visa och analysera real tids strömnings data i en tabell direkt från en SQL-databas i Azure Portal med hjälp av [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Den här upplevelsen möjliggör en mängd olika scenarier, till exempel ansluten bil, fjärrövervakning, bedrägeri identifiering och många fler. I Azure Portal kan du välja en händelse källa (Event Hub/IoT Hub), Visa inkommande real tids händelser och välja en tabell för att lagra händelser. Du kan också skriva Stream Analytics frågor om språk frågor i portalen för att transformera inkommande händelser och lagra dem i den valda tabellen. Den nya start punkten är förutom de skapande-och konfigurations upplevelser som redan finns i Stream Analytics. Den här upplevelsen börjar från databasens kontext, så att du snabbt kan skapa ett Stream Analytics jobb och navigera sömlöst mellan Azure SQL Database och Stream Analytics upplevelser.

![Stream Analytics flöde](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Viktiga fördelar

- Minsta kontext växling: du kan starta från en SQL Database i portalen och börja mata in real tids data i en tabell utan att växla till någon annan tjänst. 
- Minskat antal steg: kontexten för din databas och tabell används för att förkonfigurera ett Stream Analytics jobb.
- Enklare att använda med förhands gransknings data: Förhandsgranska inkommande data från händelse källan (Händelsehubben/IoT Hub) i kontexten för den valda tabellen 


## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen i den här artikeln behöver du följande resurser:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 
- En SQL-databas. Mer information finns i [skapa en enskild databas i Azure SQL Database](sql-database-single-database-get-started.md).
- En brand Väggs regel som gör att datorn kan ansluta till Azure SQL-servern. Mer information finns i [skapa en brand Väggs regel på server nivå](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Konfigurera Stream Analytics-integrering

1. Logga in på Azure Portal. 
2. Navigera till din SQL-databas där du vill mata in strömmande data. Välj **Stream Analytics (för hands version)** . 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Om du vill börja mata in dina strömmande data i den här SQL-databasen, väljer du **skapa** och ger ditt strömnings jobb ett namn och väljer sedan **Nästa: indata**. 

    ![Skapa Stream Analytics jobb](media/sql-database-stream-analytics/create-job.png)

4. Ange information om händelse källan och välj sedan **Nästa: utdata**.

   - **Indatatyp**: Event Hub/IoT Hub
   - **Indataområde**: Ange ett namn för att identifiera din händelse källa 
   - **Prenumeration**: samma som SQL Database prenumeration 
   - **Event Hub-namn område**: namn på namn område 
   - **Event Hub-namn**: namnet på händelsehubben i det valda namn området 
   - **Princip namn för Event Hub** (standard för att skapa ny): Ange ett princip namn 
   - **Konsument grupp för Event Hub** (standard för att skapa ny): ge ett konsument grupp namn  
     - Vi rekommenderar att du skapar en konsument grupp och en princip för varje nytt Azure Stream Analytics jobb som du skapar härifrån. Konsument grupper tillåter endast fem samtidiga läsare, så att en dedikerad konsument grupp för varje jobb kommer att undvika eventuella fel som kan uppstå från att överskrida den gränsen. Med en dedikerad princip kan du rotera nyckeln eller återkalla behörigheter utan att påverka andra resurser.

    ![Skapa Stream Analytics jobb](media/sql-database-stream-analytics/create-job-output.png)

5. Välj vilken tabell som du vill mata in strömmande data till. När du är färdig väljer du **skapa**.
   - **Användar namn**, **lösen ord**: Ange dina autentiseringsuppgifter för SQL Server-autentisering. Välj **Verifiera**.
   - **Tabell**: Välj **Skapa ny** eller **Använd befintlig**. I det här flödet väljer vi **skapa**. När du startar Stream Analytics-jobbet skapas en ny tabell.

    ![Skapa Stream Analytics jobb](media/sql-database-stream-analytics/create.png)

6. En frågenod öppnas med följande information:

   - Din **indata** (källa för indata-händelser) som du ska mata in data från  
   - Din **utmatning** (utdataparameter) som kommer att lagra transformerade data 
   - Exempel på [SAQL-fråga](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) med SELECT-instruktion. 
   - **Förhands granskning av indata**: visar ögonblicks bilder av senaste inkommande data från källan för indata.  
     - Serialiserings typen i dina data identifieras automatiskt (JSON/CSV). Du kan manuellt ändra den och JSON/CSV/AVRO. 
     - Du kan förhandsgranska inkommande data i tabell format eller RAW-format. 
     - Om data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna. 
     - Välj **Select Time-intervall** om du vill testa frågan mot ett speciellt tidsintervall inkommande händelser. 
     - Välj **Ladda upp exempel indata** för att testa frågan genom att ladda upp en JSON/CSV-fil. Mer information om hur du testar en SAQL-fråga finns i [testa ett Azure Stream Analytics jobb med exempel data](../stream-analytics/stream-analytics-test-query.md). 

    ![Testa fråga](media/sql-database-stream-analytics/test-query.png)


   - **Test resultat**: Välj **test fråga** och du kan se resultatet av din strömnings fråga 

    ![test resultat](media/sql-database-stream-analytics/test-results.png)

   - **Schema för test resultat**: visar schemat för resultatet av din strömnings fråga efter testning. Kontrol lera att resultatet av test resultatet är detsamma som i schemat för utdata. 

    ![schema för test resultat](media/sql-database-stream-analytics/test-results-schema.png)


   - **Utdata schema**: det här innehåller schemat för den tabell som du valde i steg 5 (ny eller befintlig).
     - Skapa nytt: om du har valt det här alternativet i steg 5, ser du inte schemat ännu förrän du startar direkt uppspelnings jobbet. När du skapar en ny tabell väljer du lämpligt tabell index. Mer information om tabell indexering finns i [klustrade och inklustrade index som beskrivs](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Använd befintlig: om du har valt det här alternativet i steg 5 visas schemat för den valda tabellen. 
 
7. När du är färdig med redigeringen & du testa frågan, väljer du **Spara fråga**. Välj **start Stream Analytics jobb** för att börja mata in transformerade data i SQL-tabellen. När du har slutfört följande fält **startar** du jobbet. 
   - **Start tid för utdata**: Detta definierar tiden för jobbets första utdata.  
     - Nu: jobbet kommer att startas nu och bearbeta nya inkommande data.
     - Anpassad: jobbet kommer att startas nu men kommer att bearbeta data från en viss tidpunkt (som kan vara i det förflutna eller framtiden). Mer information finns i [så här startar du ett Azure Stream Analytics jobb](../stream-analytics/start-job.md).
   - **Enheter för strömning**: Azure Stream Analytics priss ätts efter antalet enheter för strömning som krävs för att bearbeta data till tjänsten. Mer information finns i [Azure Stream Analytics prissättning](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Fel hantering av utgående data**:  
     - Försök igen: när ett fel inträffar skriver Azure Stream Analytics försök att skriva händelsen oändligt tills skrivningen lyckas. Det finns ingen tids gräns för återförsök. Till sist blockeras alla efterföljande händelser från bearbetningen av händelsen som försöker igen. Det här alternativet är standard principen för fel hantering av utdata. 
     - Drop: Azure Stream Analytics ignorerar eventuella utdata-händelser som resulterar i ett data konverterings fel. Utelämnade händelser kan inte återställas för bearbetning vid ett senare tillfälle. Alla tillfälliga fel (t. ex. nätverks fel) görs nya försök oavsett princip konfigurationen för hantering av utdata. 
   - **SQL Database inställningar för utdata**: ett alternativ för att ärva partitionerings schema i föregående fråga-steg för att aktivera helt parallell topologi med flera skrivare till tabellen. Mer information finns i [Azure Stream Analytics utdata till Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Max antal batchar**: den rekommenderade övre gränsen för antalet poster som skickas med varje Mass infognings transaktion.  
    Mer information om fel hantering av utdata finns [i principer för utgående fel i Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![starta jobb](media/sql-database-stream-analytics/start-job.png)

8. När du har startat jobbet visas jobbet som körs i listan och du kan utföra följande åtgärder: 
   - **Starta/stoppa jobbet**: om jobbet körs kan du avbryta jobbet. Om jobbet har stoppats kan du starta jobbet. 
   - **Redigera jobb**: du kan redigera frågan. Om du vill göra fler ändringar i jobbet kan du lägga till fler indata/utdata och sedan öppna jobbet i Stream Analytics. Redigerings alternativet inaktive ras när jobbet körs. 
   - **Förhandsgranska utdata tabell**: du kan förhandsgranska tabellen i SQL-Frågeredigeraren. 
   - **Öppna i Stream Analytics**: öppna jobbet i Stream Analytics-tjänsten om du vill visa övervakning, felsöka information om jobbet. 


    ![Stream Analytics-jobb](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Nästa steg

- [Azure Stream Analytics dokumentation](https://docs.microsoft.com/azure/stream-analytics/)
- [Mönster för Azure Stream Analytics lösning](../stream-analytics/stream-analytics-solution-patterns.md)
