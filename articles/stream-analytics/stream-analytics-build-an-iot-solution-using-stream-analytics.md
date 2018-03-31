---
title: Skapa en IoT-lösning med hjälp av Stream Analytics | Microsoft Docs
description: Komma igång-kursen för Stream Analytics IoT-lösningen på ett scenario med vaktkur
keywords: IOT-lösningen, fönstrets funktioner
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/21/2018
ms.author: sngun
ms.openlocfilehash: b36833a9fe35f14eba6d9e397eb0958b716b313b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Skapa en IoT-lösning med hjälp av Stream Analytics

## <a name="introduction"></a>Introduktion
I kursen får du lära dig hur du använder Azure Stream Analytics för att få realtidsinsikter från dina data. Utvecklare kan enkelt kombinera dataströmmar, till exempel klicka på dataströmmar, loggar och händelser som genereras av enheten med historisk poster eller referensdata att härleda insikter som företag. Azure Stream Analytics ger inbyggd återhämtning, låg latens och skalbarhet för att hämta du upp och körs i minuter som en helt hanterad, realtid dataströmmen beräkning tjänst som finns i Microsoft Azure.

När du har slutfört den här självstudiekursen kommer du att kunna:

* Bekanta dig med Azure Stream Analytics-portalen.
* Konfigurera och distribuera ett direktuppspelningsjobb.
* Tydligt verkliga problem och lösa dem med hjälp av Stream Analytics-frågespråket.
* Utveckla strömning lösningar för kunderna genom att använda Stream Analytics med förtroende.
* Använd övervakning och loggning upplevelse för att felsöka problem.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande förutsättningar för att kunna slutföra den här självstudiekursen:

* Den senaste versionen av [Azure PowerShell](/powershell/azure/overview)
* Visual Studio 2017 2015, eller den kostnadsfria [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* En [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)
* Administratörsbehörighet på datorn
* Ladda ned källkoden för TollApp sensor dataanalys från den [azure stream analytics GitHub-lagringsplatsen.](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp). Den här lagringsplatsen innehåller exempeldata och frågor som du ska använda i nästa avsnitt. 

## <a name="scenario-introduction-hello-toll"></a>Scenariot introduktion: ”Hello, avgift”!
En avgift station är en gemensam företeelse. Du hittar dem i många utsättas bryggor och tunnlar över hela världen. Varje station avgift har flera avgift kabiner. Vid manuell kabiner stoppa för att betala avgift till en attendant. Vid automatisk kabiner söker en sensor ovanpå varje monter RFID-kort som fästs på vindrutan av din fordon som du skickar avgift monter. Det är enkelt att visualisera övergången genom dessa avgift stationer som en händelse dataström som intressanta åtgärder kan utföras.

![Bild av bilar på avgift kabiner](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Inkommande data
Den här kursen fungerar med två dataströmmar. Sensorer som installerats i ingång och utgång avgift stationer producera första dataströmmen. Den andra är en statisk sökning datamängd som har vehicle registreringsdata.

### <a name="entry-data-stream"></a>Posten dataström
Dataströmmen posten innehåller information om bilar då de anträder avgift stationer.

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
Avsluta dataströmmen innehåller information om bilar lämnar avgift stationen.

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
I självstudiekursen använder en statisk ögonblicksbild av en databas för registrering av kommersiellt fordon.

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
Den här kursen behöver ett Microsoft Azure-prenumeration. Microsoft erbjuder kostnadsfri utvärderingsversion för Microsoft Azure-tjänster.

Om du inte har ett Azure-konto kan du [begära en kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Om du vill registrera dig för en kostnadsfri utvärderingsversion, behöver du en mobil enhet som kan ta emot textmeddelanden och ett giltigt kreditkort.
> 
> 

Se till att följa stegen i avsnittet ”Rensa ditt Azure-konto” i slutet av den här artikeln så att du kan göra din Azure-kredit på bästa sätt.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Etablera Azure-resurser som krävs för kursen
Den här kursen kräver två händelsehubbar att ta emot *post* och *avsluta* dataströmmar. Azure SQL Database visas resultatet av Stream Analytics-jobb. Azure Storage lagrar referensdata om vehicle registreringar.

Du kan använda skriptet Setup.ps1 i mappen TollApp på GitHub för att skapa alla nödvändiga resurser. Att tid rekommenderar vi att du kör den. Om du vill veta mer om hur du konfigurerar dessa resurser i Azure portal finns ”konfigurera självstudiekurs resurser i Azure-portalen”-tillägg.

Hämta och spara stödfiler [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) mappar och filer.

Öppna en **Microsoft Azure PowerShell** fönstret *som administratör*. Om du inte ännu har Azure PowerShell, följ instruktionerna i [installera och konfigurera Azure PowerShell](/powershell/azure/overview) att installera den.

Eftersom Windows blockerar automatiskt .ps1, .dll och .exe-filer, måste du ange körningsprincipen innan du kör skriptet. Kontrollera att Azure PowerShell-fönstret körs *som administratör*. Kör **Set-ExecutionPolicy obegränsad**. När du uppmanas ange **Y**.

![Skärmbild av ”Set-ExecutionPolicy obegränsad” körs i Azure PowerShell-fönster](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Kör **Get-ExecutionPolicy** att se till att kommandot fungerade.

![Skärmbild av ”Get-ExecutionPolicy” körs i Azure PowerShell-fönster](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Gå till den katalog som innehåller skript och generator program.

![Skärmbild av ”cd .\TollApp\TollApp” körs i Azure PowerShell-fönster](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Typen **.\\ Setup.ps1** om du vill konfigurera ditt Azure-konto, skapa och konfigurera alla nödvändiga resurser och börja Generera händelser. Skriptet hämtar slumpmässigt en region att skapa dina resurser. För att explicit ange en region, kan du skicka den **-plats** parameter som i följande exempel:

**. \\Setup.ps1-platsen ”centrala USA”**

![Skärmbild av sidan för Azure-inloggning](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Skriptet öppnas den **logga In** sidan för Microsoft Azure. Ange autentiseringsuppgifterna för ditt konto.

> [!NOTE]
> Om ditt konto har åtkomst till flera prenumerationer, blir du ombedd att ange prenumerationsnamnet på som du vill använda för kursen.
> 
> 

Skriptet kan ta flera minuter att köra. När den är klar utdata ska se ut som följande skärmbild.

![Skärmbild av utdata från skriptet i Azure PowerShell-fönster](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Ett annat fönster som liknar följande skärmbild visas också. Det här programmet skickar händelser till Azure Event Hubs, vilket krävs för att köra guiden. Därför inte stoppa programmet eller stänga det här fönstret förrän du har avslutat guiden.

![Skärmbild av ”skicka event hub data”](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Du ska kunna se dina resurser i Azure-portalen nu. Gå till <https://portal.azure.com>, och logga in med autentiseringsuppgifterna för ditt konto. Observera att för närvarande vissa funktioner använder den klassiska portalen. De här stegen att tydligt anges.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Azure-portalen klickar du på **fler tjänster** längst ned i fönstret vänstra management. Typen **händelsehubbar** i fältet, ser du ett nytt Event Hub-namnområde som börjar med **tolldata**. Den här namesapce skapas med skriptet Setup.ps1. Du ser två händelsehubbar med namnet **post** och **avsluta** skapas i det här namnområdet.

### <a name="azure-storage-container"></a>Azure Storage-behållare
Bläddra till storage-konton från Azure-portalen, bör du se ett lagringskonto som börjar med **tolldata**. Klicka på den **tolldata** behållare för att se den överförda JSON-filen som har vehicle registreringsdata.

### <a name="azure-sql-database"></a>Azure SQL Database
1. Gå tillbaka till Azure portal på den första fliken som har öppnats i webbläsaren. Klicka på **SQL-databaser** på vänster sida av Azure portal och se SQL-databasen som ska användas i guiden och klicka på **tolldatadb**.
   
    ![Skärmbild av den SQL-databasen har skapats](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. Kopiera servernamnet utan portnumret (*servername*. database.windows.net, till exempel).
    ![Skärmbild av skapade SQL-databas db](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Ansluta till databasen från Visual Studio
Använda Visual Studio för att få åtkomst till resultatet av frågan i Utdatadatabasen.

Anslut till SQL-databasen (målet) från Visual Studio:

1. Öppna Visual Studio och klicka sedan på **verktyg** > **Anslut till databas**.
2. Om du blir tillfrågad, klickar du på **Microsoft SQL Server** som en datakälla.
   
    ![Ändra dialogrutan för datakälla](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. I den **servernamn** fältet, klistra in det namn som du kopierade i föregående avsnitt från Azure-portalen (det vill säga *servername*. database.windows.net).
4. Klicka på **använda SQL Server-autentisering**.
5. Ange **tolladmin** i den **användarnamn** fält och **123toll!** i den **lösenord** fältet.
6. Klicka på **Välj eller ange ett databasnamn**, och välj **TollDataDB** som databas.
   
    ![Lägg till dialogrutan anslutning](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Klicka på **OK**.
8. Öppna Server Explorer.
   
    ![Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. Se fyra tabeller i databasen TollDataDB.
   
    ![Tabeller i databasen TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Händelsen generator: TollApp exempelprojektet
PowerShell-skriptet startar automatiskt att skicka händelser med hjälp av programmet TollApp exempel. Du behöver inte utföra några ytterligare steg.

Men om du är intresserad av implementeringsdetaljer du hittar källkoden för programmet TollApp i GitHub [exempel/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Skärmbild av exempelkod som visas i Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
1. Klicka på grönt plustecken i det övre vänstra hörnet på sidan för att skapa ett nytt Stream Analytics-jobb i Azure-portalen. Välj **Intelligence + analys** och klicka sedan på **Stream Analytics-jobbet**.
   
    ![Knappen Ny](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Ange ett jobbnamn, verifiera prenumerationen är korrekt och sedan skapa en ny resursgrupp i samma region som Event hub-lagring (standard är södra centrala USA för skriptet).
3. Klicka på **fäst på instrumentpanelen** och sedan **skapa** längst ned på sidan.
   
    ![Skapa Stream Analytics-jobbet alternativ](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definiera indatakällor
1. Jobbet skapar och öppnar sidan jobb. Eller klicka skapade analytics-jobbet på portalens instrumentpanel.

2. Klicka på den **indata** att definiera källdata.
   
    ![Fliken indata](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. Klicka på **lägga till indata**.
   
    ![Lägg till ett indata-alternativ](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Ange **EntryStream** som **INMATADE ALIASET**.
5. Datakällans typ är **dataström**
6. Källan är **händelsehubb**.
7. **Service bus-namnrymd** ska vara en TollData i nedrullningsbara ned.
8. **Namnet på händelsehubben** ska anges till **post**.
9. **Namnet på händelsehubben princip*är **RootManageSharedAccessKey** (standardvärdet).
10. Välj **JSON** för **händelse SERIALISERINGSFORMAT** och **UTF8** för **KODNING**.
   
    Dina inställningar kommer att se ut:
   
    ![Händelsehubbsinställningar](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. Klicka på **skapa** längst ned på sidan om du vill avsluta guiden.
    
    Nu när du har skapat posten dataströmmen ska du följa samma steg för att skapa avsluta dataströmmen. Se till att ange värden som på följande skärmbild.
    
    ![Inställningar för dataströmmen avsluta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Du har definierat två inkommande dataströmmar:
    
    ![Definierade inkommande dataströmmar i Azure-portalen](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    Därefter ska du lägga till referensindata för blob-fil som innehåller bil registreringsdata.
11. Klicka på **lägga till**, och följ samma process för dataströmmen indata men välj **REFERENSDATA** i stället för **dataströmmen** och **indata Alias** är **registrering**.

12. Storage-konto som börjar med **tolldata**. Behållarens namn bör vara **tolldata**, och **SÖKVÄGAR** ska vara **registration.json**. Det här namnet är skiftlägeskänsligt och måste vara **gemener**.
    
    ![Inställningar för lagring av blogg](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Klicka på **skapa** och slutföra guiden.

Nu har alla indata definierats.

## <a name="define-output"></a>Definiera målet
1. Välj på översiktsfönstret Stream Analytics-jobbet **utdata**.
   
    ![Fliken utmatning och alternativet ”Lägg till utdata”](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. Klicka på **Lägg till**.
3. Ange den **utdataalias** till ”utdata” och sedan **Sink** till **SQL-databas**.
3. Välj namnet på servern som användes i avsnittet ”ansluta till databasen från Visual Studio” i artikeln. Databasnamnet är **TollDataDB**.
4. Ange **tolladmin** i den **användarnamn** fältet **123toll!** i den **lösenord** fältet och **TollDataRefJoin** i den **tabell** fältet.
   
    ![Inställningar för SQL-databas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. Klicka på **Skapa**.

## <a name="azure-stream-analytics-query"></a>Azure Stream analytics-fråga
Den **FRÅGAN** fliken innehåller en SQL-fråga som omvandlar inkommande data.

![En fråga till fliken fråga](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

Den här självstudiekursen försöker besvara frågor från flera företag som är relaterade till avgiftsbelagt data och konstruktioner Stream Analytics-frågor som kan användas i Azure Stream Analytics för att tillhandahålla en relevant svar.

Innan du börjar din första Stream Analytics-jobbet utforska vi några scenarier och frågesyntaxen.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introduktion till Azure Stream Analytics-frågespråket
- - -
Anta att du behöver räkna antalet fordon som anger en avgift monter. Eftersom detta är en ständig ström av händelser, måste du definiera en ”tidsperiod”. Nu ska vi ändra frågan så att ”hur många fordon anger en avgift monter alla tre minuter”?. Detta kallas ofta för antalet rullande.

Nu ska vi titta på Azure Stream Analytics-fråga som svar på den här frågan:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Som du kan se använder Azure Stream Analytics ett frågespråk som som SQL och lägger till några tillägg om du vill ange tidsrelaterade aspekter av frågan.

Mer information finns i avsnittet om [tidshantering](https://msdn.microsoft.com/library/azure/mt582045.aspx) och [fönsterhantering](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstruktioner som används i frågan på MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Testa Azure Stream Analytics-frågor
Nu när du har skrivit ditt första Azure Stream Analytics-fråga, är det dags att testa med exempeldatafiler i mappen TollApp på följande sökväg:

**..\\TollApp\\TollApp\\Data**

Den här mappen innehåller följande filer:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Fråga 1: Antal fordon att ange en avgift monter
1. Öppna Azure-portalen och gå till skapade Azure Stream Analytics-jobbet. Klicka på den **FRÅGAN** fliken och klistra in frågan från föregående avsnitt.

2. För att verifiera den här frågan mot exempeldata, ladda upp data till EntryStream indata genom att klicka på den ... symbol och välja **ladda upp exempeldata från filen**.

    ![Skärmbild av filen Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. I fönstret som visas markerar filen (Entry.json) på den lokala datorn och på **OK**. Den **Test** ikon kommer nu belysa och vara klickbara.
   
    ![Skärmbild av filen Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. Kontrollera att resultatet av frågan är korrekt:
   
    ![Testresultaten](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Fråga 2: Rapporten total tid för varje bil att passera avgift monter
Den genomsnittliga tiden som krävs för en bil passerar avgift hjälper dig för att utvärdera effektiviteten för processen och customer experience.

För att hitta den totala tiden måste du ansluta till EntryTime ström med ExitTime dataströmmen. Du kommer att ansluta till dataströmmar för TollId och LicencePlate kolumner. Den **ansluta** operator måste du ange temporal handtag som beskriver godkända tidsskillnaden mellan de kopplade händelserna. Du kommer att använda **DATEDIFF** funktion för att ange att händelser ska vara mer än 15 minuter från varandra. Du kan också använda den **DATEDIFF** funktion för att avsluta och posten gånger att beräkna den faktiska tid som en bil tillbringar i avgift stationen. Observera skillnaden mellan användningen av **DATEDIFF** när den används i en **Välj** instruktionen snarare än en **ansluta** villkor.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Testa den här frågan genom att uppdatera frågan på den **FRÅGAN** för jobbet. Lägga till testfil för **ExitStream** precis som **EntryStream** angavs ovan.
   
2. Klicka på **Test**.

3. Markera kryssrutan för att testa frågan och visa utdata:
   
    ![Utdata för testet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Fråga 3: Rapportera alla kommersiella fordon med utgångna registrering
Azure Stream Analytics kan använda statiska ögonblicksbilder av data för att ansluta med temporala dataströmmar. Använd följande exempel fråga om du vill visa den här funktionen.

Om en kommersiell vehicle registreras med avgift företaget, släpper den igenom avgift monter utan att ha stoppats för inspektion. Du använder kommersiellt fordon registrering uppslagstabell för att identifiera alla kommersiella fordon som har upphört att gälla registreringar.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

Du måste definiera en källa för referensdata som du redan har gjort för att testa en fråga med hjälp av referensdata.

Testa den här frågan genom att klistra in frågan i den **FRÅGAN** klickar du på **testa**, och ange två indatakällor registreringen exempeldata och klicka på **testa**.  
   
![Utdata för testet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet
Nu är det dags att slutföra konfigurationen och starta jobbet. Spara frågan från fråga 3, som resultat som matchar schemat för den **TollDataRefJoin** utdatatabellen.

Gå till jobbet **INSTRUMENTPANELEN**, och klicka på **starta**.

![Skärmbild av knappen Start på jobb-instrumentpanel](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

I dialogrutan som öppnas, ändra den **starta utdata** dags att **anpassad tid**. Ändra timme till en timme före aktuell tid. Den här ändringen gör att alla händelser från event hub bearbetas eftersom du startade att generera händelser i början av kursen. Klicka på den **starta** för att starta jobbet.

![Val av anpassade tid](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Startar jobbet kan ta några minuter. Du kan se status på sidan på den översta nivån för Stream Analytics.

![Skärmbild av status för jobbet](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Kontrollera resultaten i Visual Studio
1. Öppna Visual Studio Server Explorer och högerklicka på den **TollDataRefJoin** tabell.
2. Klicka på **visa tabelldata** att se resultatet av ditt jobb.
   
    ![Val av ”Visa Data för tabell” i Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Skala ut Azure Stream Analytics-jobb
Azure Stream Analytics utformats Elastiskt skala så att den kan hantera stora mängder data. Azure Stream Analytics-fråga kan använda en **PARTITION BY** -sats som anger att det här steget ska skalas ut. **PartitionId** är en särskild kolumn som läggs för att matcha partitions-ID för inmatningen (händelsehubb).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Stoppa det aktuella jobbet och uppdatera frågan i den **FRÅGAN** och öppna den **inställningar** redskap i instrumentpanelen för jobbet. Klicka på **skala**.
   
    **STREAMING enheter** anger hur mycket datorkraft som jobbet kan ta emot.
2. Ändra nedrullningsbara från 1 från 6.
   
    ![Skärmbild av du väljer 6 enheter för strömning](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. Gå till den **utdata** fliken och ändra namnet på SQL-tabellen till **TollDataTumblingCountPartitioned**.

Om du startar jobbet nu kan Azure Stream Analytics distribuera arbete i fler beräkningsresurser och få bättre genomströmning. Observera att programmet TollApp även skickar händelser som har partitionerats med TollId.

## <a name="monitor"></a>Övervaka
Den **ÖVERVAKAREN** innehåller statistik om jobb som körs. Första gången konfiguration krävs för att använda lagring konto i samma region (namnet avgift som resten av det här dokumentet).   

![Skärmbild av Övervakare](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Du kan komma åt **aktivitetsloggar** från instrumentpanelen jobbet **inställningar** samt område.


## <a name="conclusion"></a>Sammanfattning
Den här självstudiekursen introducerade du till Azure Stream Analytics-tjänsten. Det visas hur du konfigurerar indata och utdata för Stream Analytics-jobbet. Med avgift Data scenariot beskrivs kursen vanliga problem som uppstår i utrymmet för data i rörelse och hur de kan lösas med den enkla SQL-liknande frågor i Azure Stream Analytics. Kursen beskrivs SQL tillägget konstruktioner för att arbeta med temporala data. Den visade hur du kopplar dataströmmar, så att utöka dataströmmen med statiska referensdata och hur du skala ut en fråga för att uppnå högre genomströmning.

Även om den här kursen ger en bra introduktion, är det inte fullständig på något sätt. Du kan hitta mer frågemönster med SAQL språket på [fråga exempel för vanliga Stream Analytics användningsmönster](stream-analytics-stream-analytics-query-patterns.md).
Referera till den [onlinedokumentation](https://azure.microsoft.com/documentation/services/stream-analytics/) lära dig mer om Azure Stream Analytics.

## <a name="clean-up-your-azure-account"></a>Rensa ditt Azure-konto
1. Stoppa Stream Analytics-jobbet i Azure-portalen.
   
    Skriptet Setup.ps1 skapar två händelsehubbar och en SQL-databas. Följande anvisningar hjälper dig att ta bort resurser i slutet av kursen.
2. Ange i ett PowerShell-fönster **.\\ CleanUp.ps1** att starta det skript som tar bort resurser som används i självstudiekursen.
   
   > [!NOTE]
   > Resurser som identifieras av namnet. Kontrollera att varje objekt granska noggrant innan du bekräftar borttagningen.
   > 
   > 


