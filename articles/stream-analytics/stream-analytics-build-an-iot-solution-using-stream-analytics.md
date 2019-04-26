---
title: Skapa en IoT-lösning med hjälp av Azure Stream Analytics
description: Kom igång-självstudiekurs för Stream Analytics IoT-lösning på ett vaktkur-scenario
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f372c2a85a9a03c7ead779bd4db64722891c9a4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201524"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Skapa en IoT-lösning med Stream Analytics

## <a name="introduction"></a>Introduktion
I den här lösningen kan du lära dig hur du använder Azure Stream Analytics för att få insikter i realtid från dina data. Utvecklare kan enkelt kombinera strömmar av data, till exempel klicka-strömmar, loggar och händelser som genereras av enheten med historiska poster eller referensdata att härleda affärsinsikter. Som en fullständigt hanterad realtidsbearbetning av strömmar beräkning tjänst som finns i Microsoft Azure, ger Azure Stream Analytics inbyggd återhämtning, låg latens och skalbarhet för att komma igång på bara några minuter.

När du har slutfört den här lösningen, kan du:

* Bekanta dig med Azure Stream Analytics-portalen.
* Konfigurera och distribuera en strömningsuppgift.
* Stöd för verkliga problem och lösa dem med hjälp av Stream Analytics-frågespråket.
* Utveckla strömningslösningar för dina kunder med hjälp av Stream Analytics med tillförsikt.
* Använd övervaknings- och loggning upplevelse för att felsöka problem.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande förutsättningar för att kunna slutföra den här lösningen:
* En [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introduktion för scenariot: ”Hello, avgift”!
En avgift station är ett vanligt fenomen. Du får dem på många motorvägar, bryggor och tunnlar över hela världen. Varje station avgift har flera avgift kabiner. Vid manuell kabiner stop om du vill betala avgift för att en attendant. Vid automatisk kabiner genomsöker en sensor ovanpå varje visade ett RFID-kort som fästs på vindrutan din fordonets som du skickar avgift monter. Det är enkelt att visualisera passage genom dessa avgift stationer som en händelseström som intressanta åtgärder kan utföras.

![Bild av bilar på avgift kabiner](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Inkommande data
Den här lösningen fungerar med två dataströmmar. Sensorer som installerats i ingångs- och avsluta avgift stationer producerar första dataströmmen. Den andra är en statisk lookup-datauppsättning som innehåller vehicle registreringsdata.

### <a name="entry-data-stream"></a>Post-dataström
Dataströmmen posten innehåller information om bilar då de anträder avgift stationer. Avsluta Datahändelser är live strömmat till en Event Hub-kön från en Webbapp som ingår i exempelappen.

| TollID | EntryTime | LicensePlate | Status | Skapa | Modell | VehicleType | VehicleWeight | Avgift | Tagga |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |MARKERA |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |MARKERA |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4 x 4 |1 |0 |6 |321987654 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| TollID |Avgift monter-ID som unikt identifierar en avgift monter |
| EntryTime |Datum och tid för inmatning av vehicle till avgift monter i UTC |
| LicensePlate |Licens lj antalet fordonets |
| Status |Ett tillstånd i USA |
| Skapa |Tillverkaren av bil |
| Modell |Modellnumret för bilen |
| VehicleType |Antingen 1 för fordon eller 2 för nyttofordon |
| WeightType |Vehicle vikt i ton; 0 för fordon |
| Avgift |Avgift värdet i USD |
| Tagga |E-tagg på bil som automatiserar betalning. tomt om betalningen har utförts manuellt |

### <a name="exit-data-stream"></a>Avsluta dataström
Avsluta dataströmmen innehåller information om bilar lämnar avgift stationen. Avsluta Datahändelser är live strömmat till en Event Hub-kön från en Webbapp som ingår i exempelappen.

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
| TollID |Avgift monter-ID som unikt identifierar en avgift monter |
| ExitTime |Datum och tid för Avsluta fordonets från avgift monter i UTC |
| LicensePlate |Licens lj antalet fordonets |

### <a name="commercial-vehicle-registration-data"></a>Nyttofordon registreringsdata
Lösningen använder en statisk ögonblicksbild av en nyttofordon registreringsdatabas. Dessa data sparas som en JSON-fil till Azure bloblagringen som ingår i det här exemplet.

| LicensePlate | RegistrationId | Har upphört att gälla |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| SÄKERHETS 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Här är en kort beskrivning av kolumnerna:

| Kolumn | Beskrivning |
| --- | --- |
| LicensePlate |Licens lj antalet fordonets |
| RegistrationId |Fordonets registrerings-ID |
| Har upphört att gälla |Registreringsstatus fordonets: 0 om vehicle registreringen är aktiv, 1 om registreringen har upphört att gälla |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurera miljön för Azure Stream Analytics
För att slutföra den här lösningen, behöver du ett Microsoft Azure-prenumeration. Om du inte har ett Azure-konto, kan du [begär en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

Var noga med att följa stegen i avsnittet ”Rensa upp din Azure-konto” i slutet av den här artikeln så att du använder din Azure-kredit på bästa sätt.

## <a name="deploy-the-sample"></a>Distribuera exemplet
Det finns flera resurser som enkelt kan distribueras i en resursgrupp tillsammans med några få klick. Definitionen för lösningen finns i GitHub-lagringsplats på [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Distribuera TollApp mallen i Azure portal
1. Distribuera TollApp miljön till Azure genom att använda den här länken till [distribuera TollApp Azure-mall](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Logga in på Azure portal om du tillfrågas.

3. Välj den prenumeration där de olika resurserna faktureras.

4. Ange en ny resursgrupp med ett unikt namn, till exempel `MyTollBooth`.

5. Välj en Azure-plats.

6. Ange en **intervall** som ett antal sekunder. Det här värdet används i exempelwebbappen för hur ofta du vill skicka data till Event Hub.

7. **Kontrollera** att godkänna de allmänna villkoren.

8. Välj **fäst på instrumentpanelen** så att du lätt kan hitta resurserna vid ett senare tillfälle.

9. Välj **köp** att distribuera exempelmallen.

10. Efter en liten stund visas ett meddelande som bekräftar den **distributionen lyckades**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Granska Azure Stream Analytics TollApp-resurser
1. Logga in på Azure-portalen

2. Leta upp den resursgrupp som du angav i föregående avsnitt.

3. Kontrollera att följande resurser finns i resursgruppen:
   - En Cosmos DB-konto
   - Ett Azure Stream Analytics-jobb
   - En Azure Storage-konto
   - En Azure-Händelsehubb
   - Två Webbappar

## <a name="examine-the-sample-tollapp-job"></a>Granska TollApp exempeljobb
1. Från och med resursgruppen i föregående avsnitt, Välj det direktuppspelade Stream Analytics-jobbet börjar med namnet **tollapp** (namn innehåller slumpmässiga tecken för unikhet).

2. På den **översikt** sidan för projektet, Observera de **fråga** visas frågesyntaxen.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   För att parafrasera syftet med frågan anta att du behöver räkna antalet fordon som anger en avgift monter. Eftersom en Målwebbplatsens avgift monter har en ständig ström med att ange fordon, är ingång händelser är detsamma som en dataström som inte upphör. Om du vill kvantifiera dataströmmen, måste du definiera en ”tidsperiod” att mäta över. Nu ska vi förfina frågan ytterligare, som ”hur många fordon ange en avgift monter var tredje minut”? Detta kallas ofta för antalet rullande.

   Som du ser använder Azure Stream Analytics ett frågespråk som liknar SQL och lägger till några tillägg om du vill ange tidsrelaterade aspekter av frågan.  Mer information finns i avsnittet om [tidshantering](https://msdn.microsoft.com/library/azure/mt582045.aspx) och [fönsterhantering](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstruktioner som används i frågan.

3. Granska indata för exempeljobb TollApp. Endast EntryStream indata används i den aktuella frågan.
   - **EntryStream** indata är en Event Hub-anslutning som köar data som representerar varje gång som en bil anger en vaktkur på väg. En webbapp som är en del av exemplet skapar händelser och dessa data är i kö i det här. Observera att denna indata efterfrågas i FROM-satsen för strömmande frågan.
   - **ExitStream** indata är en Event Hub-anslutning som köar data som representerar varje gång som en bil avslutar en vaktkur på väg. Den här strömmande indata används i senare varianter av frågesyntaxen.
   - **Registrering** indata är en Azure Blob storage-anslutning som pekar på en statisk registration.json-fil som används för sökningar efter behov. Den här referensindata används i senare varianter av frågesyntaxen.

4. Granska utdata för jobbet TollApp exemplet.
   - **Cosmos DB** utdata är en samling för Cosmos-databas som tar emot utdatahändelserna för mottagare. Observera att dessa utdata används i INTO-sats för strömmande frågan.

## <a name="start-the-tollapp-streaming-job"></a>Starta direktuppspelningsjobbet TollApp
Följ dessa steg för att starta direktuppspelningsjobbet:

1. På den **översikt** sidan för projektet, väljer **starta**.

2. På den **startjobb** väljer **nu**.

3. Efter en liten stund när jobbet körs på den **översikt** sidan för strömningsuppgift, visa den **övervakning** graph. Diagrammet ska visa flera tusen inmatningshändelser och tiotals utdata-händelser.

## <a name="review-the-cosmosdb-output-data"></a>Granska utdata för CosmosDB
1. Leta upp den resursgrupp som innehåller TollApp-resurser.

2. Välj Azure Cosmos DB-konto med namnmönstret **tollapp\<slumpmässiga\>-cosmos**.

3. Välj den **Datautforskaren** rubrik för att öppna sidan med Datautforskaren.

4. Expandera den **tollAppDatabase** > **tollAppCollection** > **dokument**.

5. I listan med ID: n visas flera docs när utdata är tillgänglig.

6. Välj varje id att granska JSON-dokumentet. Lägg märke till varje tollid windowend tid och antalet bilar från fönstret.

7. Efter en ytterligare tre minuter en annan uppsättning fyra dokument som är tillgänglig, ett dokument per tollid.


## <a name="report-total-time-for-each-car"></a>Rapporten total tid för varje bil
Genomsnittlig tid som krävs för en bil att passera avgift hjälper dig för att utvärdera effektiviteten för processen och kundupplevelsen.

Anslut EntryTime dataströmmen med ExitTime stream för att hitta den totala tiden. Ansluta två indataströmmar på lika med matchande TollId och LicencePlate kolumner. Den **ansluta** operatorn måste du ange temporala spelrum som beskriver godkända tidsskillnaden mellan de kopplade händelserna. Använd den **DATEDIFF** funktionen för att ange att händelser ska vara mer än 15 minuter från varandra. Gäller även de **DATEDIFF** funktionen för att avsluta och posten tider för att beräkna den faktiska tid som en bil tillbringar i avgift stationen. Observera skillnaden mellan användningen av **DATEDIFF** när den används i en **Välj** instruktionen snarare än en **ansluta** villkor.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Så här uppdaterar TollApp streaming job frågesyntax:

1. På den **översikt** sidan för projektet, väljer **stoppa**.

2. Vänta en stund för meddelanden som jobbet har stoppats.

3. Under rubriken JOBBTOPOLOGI Välj **< > fråga**

4. Klistra in justerade strömmande SQL-frågan.

5. Välj **spara** att spara frågan. Bekräfta **Ja** att spara ändringarna.

6. På den **översikt** sidan för projektet, väljer **starta**.

7. På den **startjobb** väljer **nu**.

### <a name="review-the-total-time-in-the-output"></a>Granska den totala tiden i utdata
Upprepa stegen i föregående avsnitt för att granska CosmosDB-utdata från det direktuppspelade jobbet. Granska de senaste JSON-dokument.

Det här dokumentet visar exempelvis en exemplet bil med en viss licens skylt entrytime och avsluta tid och fältet DATEDIFF beräknade durationinminutes som visar avgift monter tidslängd som två minuter:
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
Azure Stream Analytics kan använda statisk ögonblicksbilder av referensdata för att ansluta med temporala dataströmmar. Använd följande exempel-fråga för att demonstrera den här funktionen. Registrering-indata är en statisk blob json-fil som visar förfallodatum för licens-taggar. Genom att koppla på licens lj jämförs referensdata varje fordon som passerar genom avgift båda.

Om ett nyttofordon är registrerat med avgift företaget, kan det passera avgift monter utan att ha stoppats för granskning. Använd uppslagstabell registrering för att identifiera alla nyttofordon som har upphört att gälla registreringar.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Upprepa stegen i föregående avsnitt för att uppdatera TollApp streaming job frågesyntax.

2. Upprepa stegen i föregående avsnitt för att granska CosmosDB-utdata från det direktuppspelade jobbet.

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
Azure Stream Analytics är avsett att Elastiskt skala så att den kan hantera stora mängder data. Azure Stream Analytics-fråga kan använda en **PARTITION BY** -satsen för att berätta för systemet att det här steget skalas ut. **PartitionId** är en särskild kolumn som systemet lägger till för att matcha partitions-ID för indata (händelsehubb).

Om du vill skala ut frågan till partitioner, redigerar du frågesyntaxen till följande kod:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Att skala upp det direktuppspelade jobbet till fler enheter för strömning:

1. **Stoppa** det aktuella jobbet.

2. Uppdatera frågesyntaxen i den **< > fråga** sidan och spara ändringarna.

3. Under rubriken konfigurera på direktuppspelningsjobbet Välj **skala**.

4. Dra den **enheter för strömning** skjutreglaget från 1 till 6. Enheter för strömning anger hur mycket datorkraft som jobbet kan ta emot. Välj **Spara**.

5. **Starta** det direktuppspelade jobbet att demonstrera ytterligare skalning. Azure Stream Analytics distribuerar arbete mellan flera beräkningsresurser och få bättre genomströmning partitionering arbetet över resurser med hjälp av kolumnen anges i PARTITION BY-satsen.

## <a name="monitor-the-job"></a>Övervaka jobbet
Den **ÖVERVAKAREN** området innehåller statistik om det pågående jobbet. Första gången konfiguration krävs för att använda lagringskontot i samma region (namnet avgift som resten av det här dokumentet).

![Azure Stream Analytics-jobb och övervakning](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Du kan komma åt **aktivitetsloggar** från instrumentpanelen för jobbet **inställningar** samt området.

## <a name="clean-up-the-tollapp-resources"></a>Rensa resurserna som TollApp
1. Stoppa Stream Analytics-jobb i Azure-portalen.

2. Leta upp den resursgrupp som innehåller åtta resurser som rör TollApp mallen.

3. Välj **Ta bort resursgrupp**. Skriv namnet på resursgruppen för att bekräfta borttagningen.

## <a name="conclusion"></a>Sammanfattning
Den här lösningen introducerade dig till Azure Stream Analytics-tjänsten. Det visas hur du konfigurerar indata och utdata för Stream Analytics-jobb. Lösningen beskrivs använder avgift Data-scenariot, vanliga problem som kan uppstå i utrymmet för data i rörelse och hur de kan lösas med enkla SQL-liknande frågor i Azure Stream Analytics. Lösningen beskrivs SQL-tillägg-konstruktioner för att arbeta med temporala data. Den visade hur du kopplar dataströmmar, hur du utöka dataströmmen med statiska referensdata och hur du skalar ut en fråga för att uppnå högre dataflöde.

Även om den här lösningen ger en bra introduktion, är det inte klar på något sätt. Du kan hitta mer frågemönster med SAQL språk på [fråga exempel för vanliga mönster för användning av Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
