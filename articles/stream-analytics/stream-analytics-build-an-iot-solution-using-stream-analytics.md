---
title: Skapa en IoT-lösning med hjälp av Azure Stream Analytics
description: Komma igång-kursen för Stream Analytics IoT-lösningen på ett scenario med vaktkur
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Skapa en IoT-lösning med hjälp av Stream Analytics

## <a name="introduction"></a>Introduktion
Lär dig hur du använder Azure Stream Analytics för att få realtidsinsikter från dina data i den här lösningen. Utvecklare kan enkelt kombinera dataströmmar, till exempel klicka på dataströmmar, loggar och händelser som genereras av enheten med historisk poster eller referensdata att härleda insikter som företag. Azure Stream Analytics ger inbyggd återhämtning, låg latens och skalbarhet för att hämta du upp och körs i minuter som en helt hanterad, realtid dataströmmen beräkning tjänst som finns i Microsoft Azure.

När du har slutfört den här lösningen ska du kunna:

* Bekanta dig med Azure Stream Analytics-portalen.
* Konfigurera och distribuera ett direktuppspelningsjobb.
* Tydligt verkliga problem och lösa dem med hjälp av Stream Analytics-frågespråket.
* Utveckla strömning lösningar för kunderna genom att använda Stream Analytics med förtroende.
* Använd övervakning och loggning upplevelse för att felsöka problem.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande förutsättningar för att kunna slutföra den här lösningen:
* En [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scenariot introduktion: ”Hello, avgift”!
En avgift station är en gemensam företeelse. Du hittar dem i många utsättas bryggor och tunnlar över hela världen. Varje station avgift har flera avgift kabiner. Vid manuell kabiner stoppa för att betala avgift till en attendant. Vid automatisk kabiner söker en sensor ovanpå varje monter RFID-kort som fästs på vindrutan av din fordon som du skickar avgift monter. Det är enkelt att visualisera övergången genom dessa avgift stationer som en händelse dataström som intressanta åtgärder kan utföras.

![Bild av bilar på avgift kabiner](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Inkommande data
Den här lösningen fungerar med två dataströmmar. Sensorer som installerats i ingång och utgång avgift stationer producera första dataströmmen. Den andra är en statisk sökning datamängd som har vehicle registreringsdata.

### <a name="entry-data-stream"></a>Posten dataström
Dataströmmen posten innehåller information om bilar då de anträder avgift stationer. Avsluta Datahändelser är live strömmas till en Händelsehubb kö från en Webbapp som ingår i exempelappen.

| TollID | EntryTime | LicensePlate | Status | Kontrollera | Modell | VehicleType | VehicleWeight | Avgift | Tagga |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| TollID |Avgift monter ID som unikt identifierar en avgift monter |
| EntryTime |Datum och tid för fordon som avgift monter i UTC |
| LicensePlate |Licens skylt antalet för programuppdatering |
| Status |Ett tillstånd i USA |
| Kontrollera |Tillverkaren av bil |
| Modell |Modellnumret för bilen |
| VehicleType |Antingen 1 för fordon eller 2 för kommersiella fordon |
| WeightType |Vehicle vikt i ton; 0 för fordon |
| Avgift |Värdet i USD avgift |
| Tagga |E-tagg på bil som automatiserar betalning. tomt där betalningen gjordes manuellt |

### <a name="exit-data-stream"></a>Avsluta dataström
Avsluta dataströmmen innehåller information om bilar lämnar avgift stationen. Avsluta Datahändelser är live strömmas till en Händelsehubb kö från en Webbapp som ingår i exempelappen.

| **TollId** | **ExitTime** | **LicensePlate** |
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
| TollID |Avgift monter ID som unikt identifierar en avgift monter |
| ExitTime |Datum och tid för Avsluta fordon från avgift monter i UTC |
| LicensePlate |Licens skylt antalet för programuppdatering |

### <a name="commercial-vehicle-registration-data"></a>Kommersiellt fordon registreringsdata
Lösningen använder en statisk ögonblicksbild av en databas för registrering av kommersiellt fordon. Informationen sparas som en JSON-fil i Azure blob-lagring som ingår i exemplet.

| LicensePlate | RegistrationId | Har upphört att gälla |
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
| LicensePlate |Licens skylt antalet för programuppdatering |
| RegistrationId |Registrerings-ID för programuppdatering |
| Har upphört att gälla |Registreringstillstånd för för programuppdatering: 0 om vehicle registrering är aktiv, 1 om registreringen har upphört att gälla |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Ställa in miljön för Azure Stream Analytics
Du behöver ett Microsoft Azure-prenumeration för att slutföra den här lösningen. Om du inte har ett Azure-konto kan du [begära en kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/).

Se till att följa stegen i avsnittet ”Rensa ditt Azure-konto” i slutet av den här artikeln så att du kan göra din Azure-kredit på bästa sätt.

## <a name="deploy-the-sample"></a>Distribuera exemplet 
Det finns flera resurser som enkelt kan distribueras i en resursgrupp tillsammans med några få klickningar. Lösningsdefinition finns i github-lagringsplats på [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Distribuera TollApp mallen i Azure-portalen
1. Distribuera TollApp miljön till Azure genom att använda den här länken till [distribuera TollApp Azure mallen](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Logga in på Azure portal om du blir tillfrågad.

3. Välj den prenumeration där olika resurser faktureras.

4. Ange en ny resursgrupp med ett unikt namn, till exempel `MyTollBooth`. 

5. Välj en Azure-plats.

6. Ange en **intervall** som ett antal sekunder. Det här värdet används i exempelwebbapp för hur ofta du vill skicka data till Händelsehubben. 

7. **Kontrollera** accepterar villkoren.

8. Välj **fäst på instrumentpanelen** så att du lätt kan hitta resurserna vid ett senare tillfälle.

9. Välj **inköp** att distribuera mallen exempel.

10. Efter en liten stund visas ett meddelande som bekräftar den **distributionen lyckades**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Granska Azure Stream Analytics TollApp resurser
1. Logga in på Azure Portal

2. Leta upp den resursgrupp som du angav i föregående avsnitt.

3. Kontrollera att följande resurser finns i resursgruppen:
   - En Cosmos DB-konto
   - One Azure Stream Analytics Job
   - En Azure Storage-konto
   - One Azure Event Hub
   - Two Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Granska exempel TollApp jobbet 
1. Starta från resursgruppen i föregående avsnitt, Välj Stream Analytics direktuppspelningsjobbet börjar med namnet **tollapp** (namn innehåller slumpmässiga tecken för unikhet).

2. På den **översikt** sidan för projektet, meddelande i **fråga** om du vill visa frågesyntaxen.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Om du vill skriva syftet med frågan anta att du behöver räkna antalet fordon som anger en avgift monter. Eftersom en motorväg avgift monter har en ständig ström med att ange fordon, är ingången händelser är detsamma som en dataström som inte upphör. Om du vill kvantifiera dataströmmen måste du definiera en ”tidsperiod” att mäta över. Vi förfina frågan dessutom ”hur många fordon anger en avgift monter alla tre minuter”? Detta kallas ofta för antalet rullande.

   Som du kan se använder Azure Stream Analytics ett frågespråk som som SQL och lägger till några tillägg om du vill ange tidsrelaterade aspekter av frågan.  Mer information finns i avsnittet om [tidshantering](https://msdn.microsoft.com/library/azure/mt582045.aspx) och [fönsterhantering](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstruktioner som används i frågan.

3. Granska indata för TollApp exempel jobb. EntryStream indata används i den aktuella frågan.
   - **EntryStream** indata är en anslutning till Händelsehubb som köer data som representerar varje gång en bil anger en vaktkur på plats. Skapar en webbapp som ingår i exemplet händelser och data är i kö i den här Event Hub. Observera att den här indata efterfrågas i FROM-satsen för strömmande frågan.
   - **ExitStream** indata är en anslutning till Händelsehubb som köer data som representerar varje gång en bil avslutar en vaktkur på plats. Den här strömmande indata används i senare variationer av frågesyntaxen.
   - **Registrering** indata är en Azure Blob storage-anslutning, pekar på en statisk registration.json-fil som används för sökningar efter behov. Den här referensindata används i senare variationer av frågesyntaxen.

4. Granska utdata för TollApp exempel jobbet.
   - **Cosmos DB** utdata är en Cosmos-databasen samling som tar emot utdata sink-händelser. Observera att den här utdatan används i INTO-sats för strömmande frågan.

## <a name="start-the-tollapp-streaming-job"></a>Starta direktuppspelningsjobbet TollApp
Följ dessa steg om du vill starta direktuppspelningsjobbet:

1. På den **översikt** sidan för projektet, Välj **starta**.

2. På den **startjobb** väljer **nu**.

3. Efter en liten stund när jobbet körs på den **översikt** direktuppspelningsjobbet visa sidan på **övervakning** diagram. Diagrammet ska visa flera tusen inkommande händelser och flera händelser för utdata.

## <a name="review-the-cosmosdb-output-data"></a>Granska utdata CosmosDB
1. Leta upp resursgruppen som innehåller TollApp resurser.

2. Välj Azure Cosmos-DB-konto med namnmönster **tollapp<random>-cosmos**.

3. Välj den **Data Explorer** rubriken för att öppna sidan Data Explorer.

4. Expandera den **tollAppDatabase** > **tollAppCollection** > **dokument**.

5. I listan över ID: n visas flera dokument när utdata är tillgänglig.

6. Markera varje id att granska JSON-dokumentet. Lägg märke till varje tollid windowend tid och antalet bilar från fönstret.

7. Efter en ytterligare tre minuter en annan uppsättning fyra dokument är tillgänglig, ett dokument per tollid. 


## <a name="report-total-time-for-each-car"></a>Rapporten total tid för varje bil
Den genomsnittliga tiden som krävs för en bil passerar avgift hjälper dig för att utvärdera effektiviteten för processen och customer experience.

Anslut EntryTime ström med ExitTime dataströmmen för att hitta den totala tiden. Anslut två inkommande strömmar på lika matchande TollId och LicencePlate kolumner. Den **ansluta** operator måste du ange temporal handtag som beskriver godkända tidsskillnaden mellan de kopplade händelserna. Använd den **DATEDIFF** funktion för att ange att händelser ska vara mer än 15 minuter från varandra. Gäller även de **DATEDIFF** funktion för att avsluta och posten gånger att beräkna den faktiska tid som en bil tillbringar i avgift stationen. Observera skillnaden mellan användningen av **DATEDIFF** när den används i en **Välj** instruktionen snarare än en **ansluta** villkor.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Så här uppdaterar TollApp streaming job frågesyntaxen:

1. På den **översikt** sidan för projektet, Välj **stoppa**.

2. Vänta en stund för det meddelande som jobbet har stoppats.

3. Under rubriken jobbet TOPOLOGI Välj **< > fråga**

4. Klistra in justerade strömmande SQL-frågan.

5. Välj **spara** att spara frågan. Bekräfta **Ja** spara ändringarna.

6. På den **översikt** sidan för projektet, Välj **starta**.

7. På den **startjobb** väljer **nu**.

### <a name="review-the-total-time-in-the-output"></a>Granska den totala tiden i utdata
Upprepa stegen i föregående avsnitt för att granska CosmosDB utdata från direktuppspelningsjobbet. Granska de senaste JSON-dokument. 

Det här dokumentet visar till exempel ett exempel bil med en viss licens skylt, entrytime och avsluta tid och DATEDIFF beräknade durationinminutes fältet visar avgift monter varaktighet som två minuter: 
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

## <a name="report-vehicles-with-expired-registration"></a>Rapporten fordon med utgångna registrering
Azure Stream Analytics kan använda statiska ögonblicksbilder av referensdata för att ansluta med temporala dataströmmar. Använd följande exempel fråga om du vill visa den här funktionen. Registrering-indata är en statisk blob json-fil som visar förfallodatum för licens-taggar. Genom att anslutas på licens skylt jämförs referensdata med varje fordon passerar avgift båda. 

Om en kommersiell vehicle registreras med avgift företaget, släpper den igenom avgift monter utan att ha stoppats för inspektion. Använd uppslagstabellen registrering för att identifiera alla kommersiella fordon som har upphört att gälla registreringar.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Upprepa stegen i föregående avsnitt för att uppdatera TollApp streaming job frågesyntaxen.

2. Upprepa stegen i föregående avsnitt för att granska CosmosDB utdata från direktuppspelningsjobbet. 

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
Azure Stream Analytics utformats Elastiskt skala så att den kan hantera stora mängder data. Azure Stream Analytics-fråga kan använda en **PARTITION BY** -sats som anger att det här steget ska skalas ut. **PartitionId** är en särskild kolumn som läggs för att matcha partitions-ID för inmatningen (händelsehubb).

Om du vill skala upp frågan till partitioner, redigerar du frågesyntaxen till följande kod:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Att skala upp direktuppspelningsjobbet till fler strömmande enheter:

1. **Stoppa** det aktuella jobbet. 

2. Uppdatera frågesyntaxen i den **< > frågan** sidan och spara ändringarna.

3. Under rubriken konfigurera på direktuppspelningsjobbet Välj **skala**.
   
4. Dra den **Strömningsenheter** skjutreglaget från 1 till 6. Enheter för strömning anger hur mycket datorkraft som jobbet kan ta emot. Välj **Spara**.

5. **Starta** direktuppspelningsjobbet att visa ytterligare skalan. Azure Stream Analytics fördelar arbete över fler beräkningsresurser och få bättre genomströmning partitionering arbetet över resurser i kolumnen som anges i PARTITION BY-satsen. 

## <a name="monitor-the-job"></a>Övervaka jobbet
Den **ÖVERVAKAREN** innehåller statistik om jobb som körs. Första gången konfiguration krävs för att använda lagringskontot i samma region (namnet avgift som resten av det här dokumentet).   

![Skärmbild av Övervakare](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Du kan komma åt **aktivitetsloggar** från instrumentpanelen jobbet **inställningar** samt område.

## <a name="clean-up-the-tollapp-resources"></a>Rensa TollApp resurser
1. Stoppa Stream Analytics-jobbet i Azure-portalen.

2. Leta upp resursgruppen som innehåller åtta resurser som rör TollApp mallen.

3. Välj **Ta bort resursgrupp**. Skriv namnet på resursgruppen att bekräfta borttagningen.

## <a name="conclusion"></a>Sammanfattning
Den här lösningen introducerade du till Azure Stream Analytics-tjänsten. Det visas hur du konfigurerar indata och utdata för Stream Analytics-jobbet. Lösningen beskrivs använder avgift Data scenariot vanliga problem som uppstår i utrymmet för data i rörelse och hur de kan lösas med den enkla SQL-liknande frågor i Azure Stream Analytics. Lösningen beskrivs SQL tillägget konstruktioner för att arbeta med temporala data. Den visade hur du kopplar dataströmmar, så att utöka dataströmmen med statiska referensdata och hur du skala ut en fråga för att uppnå högre genomströmning.

Den här lösningen ger en bra introduktion, är det inte fullständig på något sätt. Du kan hitta mer frågemönster med SAQL språket på [fråga exempel för vanliga Stream Analytics användningsmönster](stream-analytics-stream-analytics-query-patterns.md).
