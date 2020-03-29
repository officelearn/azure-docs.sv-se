---
title: Skapa en IoT-lösning med hjälp av Azure Stream Analytics
description: Komma igång självstudiekurs för Stream Analytics IoT-lösningen för ett vägtullsscenario
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426450"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Skapa en IoT-lösning med Hjälp av Stream Analytics

## <a name="introduction"></a>Introduktion
I den här lösningen får du lära dig hur du använder Azure Stream Analytics för att få insikter i realtid från dina data. Utvecklare kan enkelt kombinera dataströmmar, till exempel klickströmmar, loggar och enhetsgenererade händelser, med historiska poster eller referensdata för att härleda affärsinsikter. Azure Stream Analytics är en fullständigt hanterad beräkningstjänst för realtidsflöden som finns i Microsoft Azure och ger inbyggd återhämtning, låg latens och skalbarhet för att komma igång på några minuter.

När du har slutfört den här lösningen kan du:

* Bekanta dig med Azure Stream Analytics-portalen.
* Konfigurera och distribuera ett direktuppspelningsjobb.
* Formulera verkliga problem och lösa dem med hjälp av frågespråket Stream Analytics.
* Utveckla streaminglösningar för dina kunder genom att använda Stream Analytics med tillförsikt.
* Använd övervaknings- och loggningsupplevelsen för att felsöka problem.

## <a name="prerequisites"></a>Krav
Du behöver följande förutsättningar för att slutföra den här lösningen:
* En [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scenario introduktion: "Hej, Toll!"
En vägtullstation är ett vanligt fenomen. Du möter dem på många motorvägar, broar och tunnlar över hela världen. Varje betalstation har flera vägtullar. Vid manuella bås, slutar du att betala avgiften till en skötare. Vid automatiserade bås skannar en sensor ovanpå varje monter ett RFID-kort som fästs på vindrutan på ditt fordon när du passerar betalstationen. Det är lätt att visualisera passagen av fordon genom dessa vägtullstationer som en händelse ström över vilken intressanta operationer kan utföras.

![Bild på bilar vid betalstationer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Inkommande data
Den här lösningen fungerar med två dataströmmar. Sensorer som är installerade vid in- och utgången av vägtullstationerna producerar den första strömmen. Den andra strömmen är en statisk uppslagsdatauppsättning som har fordonsregistreringsdata.

### <a name="entry-data-stream"></a>Dataström för inmatning
Inmatningsdataströmmen innehåller information om bilar när de kommer in på betalstationer. Exit-datahändelserna direktuppströms till en eventnavet-kö från en webbapp som ingår i exempelappen.

| Avgiftsbelagdtid | EntryTime (EntryTime) | LicensSkylt | Status | Tillverkning | Modell | VehicleType (VehicleType) | Fordonsvikt | Vägtull | Tagga |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |Crv |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |Crv |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| Avgiftsbelagdtid |Betalstationens ID som unikt identifierar en betalstation |
| EntryTime (EntryTime) |Datum och tid för fordonets inträde till betalstationen i UTC |
| LicensSkylt |Fordonets registreringsnummer |
| Status |En stat i USA |
| Tillverkning |Tillverkaren av bilen |
| Modell |Modellnumret på bilen |
| VehicleType (VehicleType) |Antingen 1 för personbilar eller 2 för nyttofordon |
| Vikttyp |Fordonets vikt i ton; 0 för personbilar |
| Vägtull |Avgiftsvärdet i USD |
| Tagga |E-Tag på bilen som automatiserar betalning; tom där betalningen gjordes manuellt |

### <a name="exit-data-stream"></a>Avsluta dataström
Utgångsdataströmmen innehåller information om bilar som lämnar betalstationen. Exit-datahändelserna direktuppströms till en eventnavet-kö från en webbapp som ingår i exempelappen.

| **Vägtull** | **AvslutaTid** | **LicensSkylt** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| Avgiftsbelagdtid |Betalstationens ID som unikt identifierar en betalstation |
| AvslutaTid |Datum och tid för fordonets utgång från betalstationen i UTC |
| LicensSkylt |Fordonets registreringsnummer |

### <a name="commercial-vehicle-registration-data"></a>Registreringsuppgifter för nyttofordon
Lösningen använder en statisk ögonblicksbild av en registreringsdatabas för nyttofordon. Dessa data sparas som en JSON-fil i Azure blob storage, som ingår i exemplet.

| LicensSkylt | RegistrationId (RegistreringId) | Upphörd |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| LicensSkylt |Fordonets registreringsnummer |
| RegistrationId (RegistreringId) |Fordonets registrerings-ID |
| Upphörd |Fordonets registreringsstatus: 0 om fordonsregistreringen är aktiv, 1 om registreringen har upphört att gälla |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurera miljön för Azure Stream Analytics
För att slutföra den här lösningen behöver du en Microsoft Azure-prenumeration. Om du inte har ett Azure-konto kan du [begära en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

Var noga med att följa stegen i avsnittet "Rensa ditt Azure-konto" i slutet av den här artikeln så att du kan använda din Azure-kredit på bästa sätt.

## <a name="deploy-the-sample"></a>Distribuera exemplet
Det finns flera resurser som enkelt kan distribueras i en resursgrupp tillsammans med några klick. Lösningsdefinitionen finns i GitHub-databasen på [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Distribuera TollApp-mallen i Azure-portalen
1. Om du vill distribuera TollApp-miljön till Azure använder du den här länken för att [distribuera TollApp Azure-mall](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Logga in på Azure-portalen om du uppmanas att göra det.

3. Välj den prenumeration där de olika resurserna faktureras.

4. Ange en ny resursgrupp med ett `MyTollBooth`unikt namn, till exempel .

5. Välj en Azure-plats.

6. Ange ett **intervall** som ett antal sekunder. Det här värdet används i exempelwebbappen för hur ofta data ska skickas till Event Hub.

7. **Kontrollera** att du godkänner villkoren.

8. Välj **Fäst på instrumentpanelen** så att du enkelt kan hitta resurserna senare.

9. Välj **Inköp om** du vill distribuera exempelmallen.

10. Efter en liten stund visas ett meddelande för att bekräfta **att distributionen lyckades**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Granska Azure Stream Analytics TollApp-resurser

1. Logga in på Azure Portal

2. Leta reda på den resursgrupp som du namngav i föregående avsnitt.

3. Kontrollera att följande resurser visas i resursgruppen:
   - Ett Cosmos DB-konto
   - Ett Azure Stream-analysjobb
   - Ett Azure-lagringskonto
   - En Azure-händelsehubb
   - Två webbappar

## <a name="examine-the-sample-tollapp-job"></a>Undersök exempeljobbet TollApp
1. Med början från resursgruppen i föregående avsnitt väljer du stream analytics-direktuppspelningsjobbet som börjar med namnet **tollapp** (namnet innehåller slumpmässiga tecken för unikhet).

2. På sidan **Översikt** för jobbet lägger du märke till rutan **Fråga** för att visa frågesyntaxen.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Om du vill parafrasera syftet med frågan ska vi säga att du måste räkna antalet fordon som kommer in i en vägtullsmonter. Eftersom en motorväg vägtull monter har en kontinuerlig ström av fordon in, är dessa ingångar händelser är analoga med en ström som aldrig stannar. För att kvantifiera strömmen måste du definiera en "tidsperiod" för att mäta över. Låt oss förfina frågan ytterligare, till "Hur många fordon in i en vägtull monter var tredje minut?" Detta kallas ofta tumlande räkna.

   Som du kan se använder Azure Stream Analytics ett frågespråk som är som SQL och lägger till några tillägg för att ange tidsrelaterade aspekter av frågan.  Mer information finns i om [tidshantering](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) och [fönsterkonstruktioner](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) som används i frågan.

3. Undersök indata för tollapp-exempeljobbet. Endast Indata för EntryStream används i den aktuella frågan.
   - **EntryStream-indata** är en Event Hub-anslutning som köar data som representerar varje gång en bil går in i en vägtull på motorvägen. En webbapp som ingår i exemplet skapar händelserna och att data står i kö i den här händelsehubben. Observera att den här indata efterfrågas i FROM-satsen för direktuppspelningsfrågan.
   - **ExitStream-indata** är en Event Hub-anslutning som köar data som representerar varje gång en bil lämnar en vägtull på motorvägen. Den här direktuppspelningsindata används i senare varianter av frågesyntaxen.
   - **Registreringsindata** är en Azure Blob-lagringsanslutning som pekar på en statisk registration.json-fil som används för sökninger efter behov. Den här referensdataindataindata används i senare varianter av frågesyntaxen.

4. Undersök utdata för tollapp-exempeljobbet.
   - **Cosmos** DB-utdata är en Cosmos-databasbehållare som tar emot utdatamottagarens händelser. Observera att den här utdata används i INTO-satsen för direktuppspelningsfrågan.

## <a name="start-the-tollapp-streaming-job"></a>Starta tollapp-streamingjobbet
Så här startar du direktuppspelningsjobbet:

1. På sidan **Översikt** för jobbet väljer du **Start**.

2. Välj **Nu**i **jobbfönstret Start** .

3. Efter en stund, när jobbet körs, på **sidan Översikt för** direktuppspelningsjobbet, visar du **övervakningsdiagrammet.** Diagrammet ska visa flera tusen indatahändelser och tiotals utdatahändelser.

## <a name="review-the-cosmosdb-output-data"></a>Granska utdata för CosmosDB
1. Leta reda på resursgruppen som innehåller TollApp-resurserna.

2. Välj Azure Cosmos DB-konto med namnmönstret **tollapp\<random\>-cosmos**.

3. Välj rubriken **Data Explorer** för att öppna sidan Data Explorer.

4. Expandera **tollAppDatabase** > **tollAppCollection** > **Documents**.

5. I listan över id:er visas flera dokument när utdata är tillgänglig.

6. Välj varje id för att granska JSON-dokumentet. Lägg märke till varje vägtull, fönstertid och antalet bilar från det fönstret.

7. Efter ytterligare tre minuter finns ytterligare en uppsättning med fyra dokument tillgängliga, ett dokument per vägtull.


## <a name="report-total-time-for-each-car"></a>Rapportera total tid för varje bil
Den genomsnittliga tid som krävs för en bil att passera genom vägtull hjälper till att bedöma effektiviteten i processen och kundupplevelsen.

Om du vill hitta den totala tiden ansluter du till EntryTime-strömmen med ExitTime-strömmen. Gå med i de två indataströmmarna på motsvarande matchande TollId- och LicensePlate-kolumner. **Join-operatorn** kräver att du anger tidsmässigt spelrum som beskriver den acceptabla tidsskillnaden mellan de kopplade händelserna. Använd funktionen **DATEDIFF** för att ange att händelser inte ska vara mer än 15 minuter från varandra. Använd även **FUNKTIONEN DATEDIFF** för att avsluta och ingångstider för att beräkna den faktiska tiden som en bil tillbringar i vägtullstationen. Observera skillnaden i användningen av **DATEDIFF** när den används i en **SELECT-sats** i stället för ett **JOIN-villkor.**

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Så här uppdaterar du syntaxen för tollapp-strömmarjobbsfrågan:

1. På sidan **Översikt** för jobbet väljer du **Stoppa**.

2. Vänta en stund på meddelandet om att jobbet har stoppats.

3. Under rubriken JOBBTOPOLOGI väljer du **< > Fråga**

4. Klistra in den justerade SQL-frågan för direktuppspelning.

5. Välj **Spara** om du vill spara frågan. Bekräfta **Ja** om du vill spara ändringarna.

6. På sidan **Översikt** för jobbet väljer du **Start**.

7. Välj **Nu**i **jobbfönstret Start** .

### <a name="review-the-total-time-in-the-output"></a>Granska den totala tiden i utdata
Upprepa stegen i föregående avsnitt för att granska CosmosDB-utdata från direktuppspelningsjobbet. Granska de senaste JSON-dokumenten.

Det här dokumentet visar till exempel en exempelbil med en viss registreringsskylt, instegstid och utgångstid och fältet DATEDIFF beräknad varaktighetminuter som visar vägtullsmonterns varaktighet som två minuter:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Rapportera fordon med förfallen registrering
Azure Stream Analytics kan använda statiska ögonblicksbilder av referensdata för att ansluta till tidsmässiga dataströmmar. Använd följande exempelfråga för att demonstrera den här funktionen. Registreringsindata är en statisk blob json-fil som listar förfallodatum för licenstaggar. Genom att gå på registreringsskylten jämförs referensdata med varje fordon som passerar genom vägtullen båda.

Om ett nyttofordon är registrerat hos betalföretaget kan det passera genom vägtullsbåset utan att stoppas för inspektion. Använd tabellen registreringssökning för att identifiera alla kommersiella fordon som har upphört att gälla.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Upprepa stegen i föregående avsnitt för att uppdatera syntaxen för tollapp-jobbfrågor för direktuppspelning.

2. Upprepa stegen i föregående avsnitt för att granska CosmosDB-utdata från direktuppspelningsjobbet.

Exempel på utdata:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Skala ut jobbet
Azure Stream Analytics är utformat för att elastiskt skala så att den kan hantera stora mängder data. Azure Stream Analytics-frågan kan använda en **PARTITION BY-sats** för att tala om för systemet att det här steget skalas ut. **PartitionId** är en speciell kolumn som systemet lägger till för att matcha partitions-ID för indata (händelsehubb).

Om du vill skala ut frågan till partitioner redigerar du frågesyntaxen till följande kod:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Så här skalar du upp strömningsjobbet till fler strömningsenheter:

1. **Stoppa** det aktuella jobbet.

2. Uppdatera frågesyntaxen på sidan **< > fråga** och spara ändringarna.

3. Under rubriken KONFIGURERA för direktuppspelningsjobbet väljer du **Skala**.

4. Skjut **skjutreglaget Streaming units** från 1 till 6. Strömningsenheter definierar mängden beräkningskraft som jobbet kan ta emot. Välj **Spara**.

5. **Starta** direktuppspelningsjobbet för att demonstrera den ytterligare skalan. Azure Stream Analytics distribuerar arbete över fler beräkningsresurser och uppnår bättre dataflöde, partitionerar arbetet mellan resurser med hjälp av kolumnen som anges i PARTITION BY-satsen.

## <a name="monitor-the-job"></a>Övervaka jobbet
**MONITOR-området** innehåller statistik om det löpjobbet. Första gången konfiguration behövs för att använda lagringskontot i samma region (namn vägtull som resten av det här dokumentet).

![Övervakning av Azure Stream Analytics-jobb](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Du kan också komma åt **aktivitetsloggar** från området Inställningar för jobbinstrumentpanelen. **Settings**

## <a name="clean-up-the-tollapp-resources"></a>Rensa TollApp-resurserna
1. Stoppa Stream Analytics-jobbet i Azure-portalen.

2. Leta reda på resursgruppen som innehåller åtta resurser relaterade till TollApp-mallen.

3. Välj **Ta bort resursgrupp**. Skriv namnet på resursgruppen för att bekräfta borttagningen.

## <a name="conclusion"></a>Slutsats
Den här lösningen introducerade dig till Azure Stream Analytics-tjänsten. Den visade hur du konfigurerar indata och utdata för Stream Analytics-jobbet. Med hjälp av toll data-scenariot förklarade lösningen vanliga typer av problem som uppstår inom utrymmet för data i rörelse och hur de kan lösas med enkla SQL-liknande frågor i Azure Stream Analytics. Lösningen beskrivs SQL-tillägg konstruerar för att arbeta med tidsmässiga data. Den visade hur du ansluter dataströmmar, hur du berikar dataströmmen med statiska referensdata och hur du skalar ut en fråga för att uppnå högre dataflöde.

Även om denna lösning ger en bra introduktion, är den inte komplett på något sätt. Du kan hitta fler frågemönster med saql-språket på [Frågeexempel för vanliga Stream Analytics-användningsmönster](stream-analytics-stream-analytics-query-patterns.md).
