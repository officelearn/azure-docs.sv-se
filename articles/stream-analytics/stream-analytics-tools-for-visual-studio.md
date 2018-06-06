---
title: Använda Azure Stream Analytics-verktyg för Visual Studio
description: Den här artikeln beskriver hur du kommer igång med Azure Stream Analytics-verktyg för Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: bcb4302c3a237ec75ff670b322c9d068d8700de9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738521"
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Använda Azure Stream Analytics-verktyg för Visual Studio
Azure Stream Analytics-verktyg för Visual Studio är nu allmänt tillgänglig. Dessa verktyg aktivera en rikare upplevelse för Stream Analytics användare att felsöka samt skriva komplexa frågor och skriva även frågor lokalt. Du kan också exportera ett Stream Analytics-jobb i Visual Studio-projekt.

## <a name="introduction"></a>Introduktion
I kursen får du lära dig hur du använder Stream Analytics tools för Visual Studio för att skapa, redigera, testa lokalt, hantera och felsöka Stream Analytics-jobb. När du skapar jobbet, du kan ställa in en kontinuerlig process för integrering och distribution till Azure med hjälp av CI/CD-Nuget-paketet finns att lära dig mer för [Stream Analytics VS-verktyg för att konfigurera en CI/CD-pipeline](stream-analytics-tools-for-visual-studio-cicd.md) artikel.

När du har slutfört den här självstudiekursen kommer du att kunna:

* Bekanta dig med Stream Analytics-verktyg för Visual Studio.
* Konfigurera och distribuera ett Stream Analytics-jobb.
* TESTJOBBET lokalt med lokala exempeldata.
* Använd övervakning för att felsöka problem.
* Exportera befintliga jobb till projekt.

>[!IMPORTANT]
> Microsoft rekommenderar att du uppgradera till Azure Data Lake-verktyg för Visual Studio version 2.3.3000.4 eller senare. Tidigare versioner är inte längre tillgängliga för hämtning och nu är föråldrade. 
>
>**Vad måste jag göra?**
>
>1. Kontrollera om du använder en tidigare version än 2.3.3000.5 Azure Stream Analytics verktyg för Visual Studio. 
>   
>   ![Kontrollera version av verktyget](./media/stream-analytics-tools-for-vs/about-data-lake.png)
> 
>2. Om din version är en tidigare version av 2.3.3000.5 uppdaterar du din Azure Data Lake-verktyg för Visual Studio genom att besöka download center: 
>    - [För Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [För Visual Studio 2013 och 2015](https://www.microsoft.com/en-us/download/details.aspx?id=54630)

## <a name="prerequisites"></a>Förutsättningar
Följande förutsättningar för att kunna slutföra den här kursen behöver du:

* Slutför stegen upp till ”skapa ett Stream Analytics-jobb” i självstudiekursen [skapar en IoT-lösning med Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Installera Visual Studio 2017, Visual Studio 2015 eller Visual Studio 2013 uppdatering 4. Enterprise (Ultimate/Premium), Professional och Community-versioner stöds. Express edition stöds inte. 
* Följ den [Installationsinstruktioner](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) installera Stream Analytics-verktyg för Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt
I Visual Studio väljer **filen** > **nytt projekt**. Välj i listan mallar till vänster **Stream Analytics**, och välj sedan **Azure Stream Analytics-programmet**.
Längst ned på sidan Ange projektet **namn**, **plats**, och **lösningsnamn** som du gör för andra projekt.

![Skapa nytt projekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Projektet **avgift** genereras i **Solution Explorer**.

![Avgift projektet i Solution Explorer](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Välj rätt prenumeration
1. På den **visa** väljer du **Server Explorer** i Visual Studio.

2. Logga in med ditt Azure-konto. 

## <a name="define-input-sources"></a>Definiera indatakällor
1. I **Solution Explorer**, expandera den **indata** nod och Byt namn på **Input.json** till **EntryStream.json**. Dubbelklicka på **EntryStream.json**.

2. För **indata Alias**, ange **EntryStream**. Observera att inmatade aliaset används i Frågeskript.

3. För **källtypen**väljer **dataströmmen**.

4. För **källa**väljer **Händelsehubb**.

5. För **Service Bus Namespace**, Välj den **TollData** alternativet i den nedrullningsbara listan.

6. För **Event Hub-namn**väljer **post**.

7. För **Event Hub principnamn**väljer **RootManageSharedAccessKey** (standardvärdet).

8. För **händelse serialiseringsformat**väljer **Json**, och för **kodning**väljer **UTF8**.
   
   Dina inställningar se ut så här:
   
   ![Ange inställningar](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Längst ned på sidan Välj **spara** och slutföra guiden. Nu kan du lägga till en annan Indatakällan för att skapa avsluta dataströmmen. Högerklicka på den **indata** och väljer **nytt objekt**.
   
   ![Nytt objekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. I popup-fönstret väljer **Stream Analytics indata**, och ändra den **namn** till **ExitStream.json**. Välj **Lägg till**.
   
    ![Lägg till nytt objekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Dubbelklicka på **ExitStream.json** i projektet och följ samma steg som post-dataström för att fylla i fälten. För **Händelsehubbens namn**, se till att ange **avsluta**som visas i följande skärmbild:
   
    ![ExitStream inställningar](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Du har nu definierat två inkommande dataströmmar.
   
   ![Två indataström](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Sedan lägger du till referensindata för blob-fil som innehåller bil registreringsdata.
   
12. Högerklicka på den **indata** i projektet och följ samma process för dataströmmen indata. För **källtypen**väljer **referensdata**, och för **indata Alias**, ange **registrering**.
   
    ![Registreringsinställningar](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Välj den **lagring** konto som innehåller alternativet med **TollData**. Behållarens namn är **TollData**, och **sökvägar** är **registration.json**. Det här namnet är skiftlägeskänsligt och måste vara gemener.

14. Välj **spara** och slutföra guiden.

Nu har alla indata definierats.

## <a name="define-output"></a>Definiera målet
1. I **Solution Explorer**, expandera den **indata** nod och dubbelklickar på **Output.json**.

2. För **Utdataalias**, ange **utdata**. För **Sink**väljer **SQL-databas**.

3. För den **databasen** namn, ange **TollDataDB**.

4. För **användarnamn**, ange **tolladmin**. För **lösenord**, ange **123toll!**. För **tabell**, ange **TollDataRefJoin**.

5. Välj **Spara**.

   ![Inställningar för utdata](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Stream Analytics-fråga
Den här självstudiekursen försöker besvara frågor från flera företag som är relaterade till avgiftsbelagt data. Vi skapa frågor som kan användas i Stream Analytics för att ge relevanta svar. Innan du börjar din första Stream Analytics-jobbet utforska vi ett enkelt scenario och frågesyntaxen.

### <a name="introduction-to-stream-analytics-query-language"></a>Introduktion till Stream Analytics-frågespråket
Anta att du behöver räkna antalet fordon som anger en avgift monter. Eftersom den här dataströmmen händelser är kontinuerligt, måste du definiera en tidsperiod. Nu ska vi ändra frågan så att ”hur många fordon anger en avgift monter alla tre minuter”? Denna mätning kallas ofta för antalet rullande.

Nu ska vi titta på Stream Analytics-fråga som svar på den här frågan:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Som du ser använder Stream Analytics ett frågespråk som fungerar som SQL. Det lägger till några tillägg om du vill ange tidsrelaterade aspekter av frågan.

Mer information finns i avsnittet om [tidshantering](https://msdn.microsoft.com/library/azure/mt582045.aspx) och [fönsterhantering](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstruktioner som används i frågan på MSDN.

Nu när du har skrivit din första Stream Analytics-fråga, testa den med hjälp av exempeldatafiler i mappen TollApp på följande sökväg:

**..\TollApp\TollApp\Data**

Den här mappen innehåller följande filer:

* Entry.JSON
* Exit.JSON
* Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Fråga: Antal fordon att ange en avgift monter
Dubbelklicka i projektet, **Script.asaql** öppna skriptet i redigeraren. Klistra in skriptet i föregående avsnitt i redigeraren. Frågeredigeraren stöder IntelliSense, syntax färgläggning och en fel-markör.

![Frågeredigeraren](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Testa lokalt Stream Analytics-frågor
Du först kompilera frågan för att se om det finns några syntaxfel.

1. För att verifiera den här frågan mot exempeldata, kan du använda lokala exempeldata genom att högerklicka på indata och välja **lägga till lokala indata**.
   
   ![Lägga till lokala indata](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. I popup-fönstret väljer du exempeldata från din lokala sökväg. Välj **Spara**.
   
   ![Lägga till lokala indata](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   En fil med namnet **local_EntryStream.json** läggs automatiskt till mappen indata.
   
   ![Listan över lokal mapp för inkommande filer](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Välj **kör lokalt** i frågeredigeraren. Eller tryck på F5.
   
   ![Kör lokalt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Du kan hitta sökvägen för utdata från konsolen utdata. Tryck på valfri tangent för att öppna mappen resultat.
   
   ![Lokal körning](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Kontrollera resultaten i den lokala mappen.
   
   ![Lokal mapp resultat](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Exempelindata
Du kan också ta prov på indata från indatakällor till en lokal fil. Högerklicka på inkommande konfigurationsfilen och välj **exempeldata**. 

![Exempeldata](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Observera att du kan prova endast händelsehubbar eller IoT-hubbar just nu. Andra indatakällor stöds inte. Fyll i den lokala sökvägen till spara exempeldata i popup-dialogrutan. Välj **exempel**.

![Data exempelkonfiguration](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Du kan se förloppet på den **utdata** fönster. 

![Exempel på utdata från data](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Skicka en Stream Analytics-fråga till Azure
1. I den **frågeredigeraren**väljer **skicka till Azure** i Skriptredigeraren.

   ![Skicka den till Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Välj **skapa en ny Azure Stream Analytics-jobbet**. För **jobbnamn**, ange **TollApp**. Välj rätt **prenumeration** i den nedrullningsbara listan. Välj **skicka**.

   ![Skicka jobbet](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Starta jobbet
Nu skapas din jobb och vyn jobb öppnas automatiskt. 
1. Välj den gröna pilen för att starta jobbet.

   ![Start-knappen i jobbet](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Välj standardinställningen och **starta**.
 
   ![Starta jobb](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Du kan se jobbstatusen ändrades till **kör**, och det finns i/o-händelser.

   ![Jobbsammanfattning och mått](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Kontrollera resultaten i Visual Studio
1. Öppna Visual Studio Server Explorer och högerklicka på den **TollDataRefJoin** tabell.

2. Välj **visa tabelldata** att se resultatet av ditt jobb.
   
   ![Visa tabelldata](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Visa jobb mått
Vissa grundläggande jobbet statistik som visas i **jobbet mått**. 

![Jobbet mått](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Visa en lista med jobbet i Server Explorer
I **Server Explorer**väljer **Stream Analytics-jobb** och välj sedan **uppdatera**. Ditt jobb visas under **Stream Analytics-jobb**.

![Lista över jobb](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Öppna vyn jobb
Expandera jobbnoden och dubbelklicka på den **jobbet** noden för att öppna vyn jobb.

![Visa jobb](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportera ett befintligt projekt till ett projekt
Det finns två sätt som du kan exportera ett befintligt projekt till ett projekt.
* I **Server Explorer**under den **Stream Analytics-jobb** noden, högerklickar du på jobbnoden. Välj **exportera till nytt projekt för Stream Analytics**.
   
   ![Exportera till nytt Stream Analytics-projekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Det genererade projektet visas i **Solution Explorer**.
   
    ![Solution Explorer jobb](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* Välj i vyn jobb **generera projekt**.
   
   ![Skapa projekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar
 
* Lokal testning fungerar inte om frågan innehåller geo-spatiala funktioner.
* Redigeraren stöd är inte tillgängligt för att lägga till eller ändra JavaScript UDF.
* Lokal testning stöd inte för att spara utdata i JSON-format. 
* Stöd är inte tillgänglig för Power BI-utdata och ADLS-utdata.



## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Azure Stream Analytics](stream-analytics-get-started.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language-referens](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)


