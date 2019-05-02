---
title: Identifiering av bedrägerier i realtid med Azure Stream Analytics
description: Lär dig hur du skapar en lösning för identifiering av bedrägerier i realtid med Stream Analytics. Använda en händelsehubb för händelsebearbetning i realtid.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 38353ed68469ac35f04d68e19afd11ac4b47f2ae
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943957"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Kom igång med Azure Stream Analytics: Identifiering av bedrägerier i realtid

Den här självstudien innehåller en illustration av hur du använder Azure Stream Analytics. Lär dig att: 

* Ta med direktuppspelning av händelser till en instans av Händelsehubbar i Azure. I den här självstudien använder du en app som simulerar en dataström med mobiltelefon metadataposterna.

* Skriva SQL-liknande Stream Analytics-frågor för att omvandla data, samla information eller söker efter mönster. Du ser hur du använder en fråga för att kontrollera inkommande dataströmmen och leta efter anrop som kan vara bedrägliga.

* Skicka resultaten till en utdatamottagare (lagring) som du kan analysera ytterligare insikter. I så fall skickar du anropsdata misstänkta till Azure Blob storage.

Den här självstudien används ett exempel på bedrägerier i realtid baserat på data för telefonsamtal. Tekniken illustreras är passar också för andra typer av bedrägerier, till exempel kreditkort bedrägeri eller identitetsstöld. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: Telekommunikation och SIM identifiering av bedrägerier i realtid

En telekomföretaget har en stor mängd data för inkommande anrop. Företaget vill identifiera bedrägliga samtal i realtid så att de kan meddela kunder eller stänga av tjänsten för ett specifikt nummer. En typ av SIM bedrägeri innebär att flera anrop från samma identitet ungefär samma tidpunkt men geografiskt olika platser. För att identifiera den här typen av bedrägerier, måste företaget att granska inkommande phone poster och leta efter särskilda mönster – i det här fallet för anrop som görs ungefär samma tidpunkt i olika länder/regioner. Alla poster i telefon som tillhör den här kategorin skrivs till lagring för efterföljande analys.

## <a name="prerequisites"></a>Nödvändiga komponenter

I de här självstudierna kommer du simulera telefonsamtal data med hjälp av en klientapp som genererar exempel telefonsamtal metadata. Vissa av posterna som tillverkar appen ser ut som bedrägliga samtal. 

Se till att du har följande innan du börjar:

* Ett Azure-konto.
* Anropet-händelsegeneratorappen, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), som kan laddas ned från Microsoft Download Center. Packa upp det här paketet till en mapp på datorn. Om du vill se source code och kör appen i en felsökare kan du hämta app-källkoden från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows kan blockera hämtade ZIP-filen. Om du det går inte att packa upp den, högerklicka på filen och välj **egenskaper**. Om du ser meddelandet ”den här filen kommer från en annan dator och blockeras för att skydda den här datorn” väljer du den **avblockera** alternativ och klickar sedan på **tillämpa**.

Om du vill granska resultaten för Streaming Analytics-jobb måste du också ett verktyg för att visa innehållet i en Azure Blob Storage-behållare. Om du använder Visual Studio kan du använda [Azure Tools för Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) eller [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Du kan också installera fristående verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Skapa en Azure Event Hubs för att mata in händelser

Att analysera en dataström du *mata in* den i Azure. Ett vanligt sätt att mata in data är att använda [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), där du kan hantera flera miljoner händelser per sekund och sedan bearbeta och lagra händelseinformationen. I den här självstudiekursen kommer du skapar en event hub och har anrop-händelsegeneratorappen skickar samtalsdata till den händelsehubben. Mer information om händelsehubbar finns i den [dokumentation för Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>En mer detaljerad version av den här proceduren finns [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Skapa ett namnområde och en händelsehubb
I den här proceduren måste du först skapa ett händelsehubbnamnområde och sedan du lägga till en händelsehubb i namnområdet. Händelsehubbnamnområden som används för att gruppera relaterade event bus instanser. 

1. Logga in på Azure-portalen och klicka på **skapa en resurs** > **Internet of Things** > **Event Hub**. 

2. I den **skapa namnområde** rutan Ange ett namn för namnområdet som `<yourname>-eh-ns-demo`. Du kan använda ett namn för namnområdet, men namnet måste vara giltig för en URL och det måste vara unikt i Azure. 
    
3. Välj en prenumeration och skapa eller välj en resursgrupp och sedan klicka på **skapa**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. När namnområdet har slutfört distributionen, hitta händelsehubbens namnområde i din lista över Azure-resurser. 

5. Klicka på det nya namnområdet och i fönstret namnområdet på **Event Hub**.

   ![Lägg till Event Hub-knappen för att skapa en ny händelsehubb](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Namnge den nya händelsehubben `asa-eh-frauddetection-demo`. Du kan använda ett annat namn. Om du gör notera av det, eftersom du behöver namnet senare. Du behöver inte ange några andra alternativ för event hub just nu.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Klicka på **Skapa**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan en process kan skicka data till en händelsehubb, måste event hub ha en princip som tillåter lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1.  Klicka på i fönstret händelse namnområde **Händelsehubbar** och klicka sedan på namnet på din nya event hub.

2.  Klicka på i fönstret event hub **principer för delad åtkomst** och klicka sedan på  **+ &nbsp;Lägg till**.

    >[!NOTE]
    >Kontrollera att du arbetar med event hub inte händelsehubbens namnområde.

3.  Lägg till en princip med namnet `sa-policy-manage-demo` och för **anspråk**väljer **hantera**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Klicka på **Skapa**.

5.  När principen har distribuerats, klickar du på den i listan över principer för delad åtkomst.

6.  I listrutan **ANSLUTNINGSSTRÄNG – primär nyckel** och klicka på kopieringsknappen bredvid anslutningssträngen. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen för nästa avsnitt när du har gjort mindre ändringar till den.

    Anslutningssträngen ser ut så här:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Observera att anslutningssträngen innehåller flera nyckel / värde-par, avgränsade med semikolon: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, och `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurera och starta händelsegeneratorprogrammet

Innan du startar appen TelcoGenerator måste du konfigurera den så att anropet poster skickas till händelsehubben som du skapade.

### <a name="configure-the-telcogenerator-app"></a>Konfigurera TelcoGenerator-appen

1. I redigeraren där du har kopierat anslutningssträngen, notera den `EntityPath` värdet och ta sedan bort den `EntityPath` par (inte glöm att ta bort semikolonet som föregår det). 

2. Öppna filen telcodatagen.exe.config i en redigerare i mappen där du har packat TelcoGenerator.zip-filen. (Det finns fler än en .config-filen, så var noga med att du öppnar rätt.)

3. I den `<appSettings>` element:

   * Ange värdet för den `EventHubName` avgörande för att namnet på händelsehubben (det vill säga till värdet för entitetssökväg).
   * Ange värdet för den `Microsoft.ServiceBus.ConnectionString` avgörande för att anslutningssträngen. 

   Den `<appSettings>` avsnittet kommer att se ut som i följande exempel. (För tydlighetens skull raderna omsluts och vissa tecken har tagits bort från autentiseringstoken.)

   ![Konfigurationsfilen för TelcoGenerator visar event hub-namn och anslutningssträng](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Spara filen. 

### <a name="start-the-app"></a>Starta appen
1.  Öppna ett kommandofönster och gå till mappen där appen TelcoGenerator är uppzippade.
2.  Ange följande kommando:

        ```cmd
        telcodatagen.exe 1000 0.2 2
        ```

    Parametrarna är: 

    * Antal skivor per timme. 
    * Sannolikhet för bedrägeri av SIM-kort: Hur ofta som en procentandel av alla anrop, att appen ska simulera ett bedrägligt samtal. Värdet 0.2 betyder att cirka 20 % av anropsposterna ser bedrägliga ut.
    * Varaktighet i timmar. Antal timmar som appen ska köras. Du kan också stoppa appen när som helst genom att trycka på Ctrl + C på kommandoraden.

    Efter några sekunder börjar appen visa telefonsamtalsposter på skärmen och skickar dem till en händelsehubb.

Några av de viktiga fält som du kommer att använda i det här programmet för identifiering av bedrägerier i realtid är följande:

|**Spela in**|**Definition**|
|----------|--------------|
|`CallrecTime`|Tidsstämpeln för samtalets starttid. |
|`SwitchNum`|Telefonväxeln används för att ansluta samtalet. I det här exemplet är växlarna strängar som representerar land/region för ursprung (USA, Kina, Storbritannien, Tyskland eller Australien). |
|`CallingNum`|Uppringarens telefonnummer. |
|`CallingIMSI`|International Mobile Subscriber Identity (IMSI). Det här är den unika identifieraren för anroparen. |
|`CalledNum`|Telefonnumret till mottagaren. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Det här är den unika identifieraren för mottagaren. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Skapa ett Stream Analytics-jobb för att hantera strömmande data

Nu när du har en ström av anropshändelser kan du konfigurera ett Stream Analytics-jobb. Jobbet kommer att läsa data från händelsehubben som du har konfigurerat. 

### <a name="create-the-job"></a>Skapa jobbet 

1. I Azure-portalen klickar du på **skapa en resurs** > **Internet of Things** > **Stream Analytics-jobbet**.

2. Namnge jobbet `asa_frauddetection_job_demo`, ange en prenumeration, resursgrupp och plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte behöver betala att överföra data mellan regioner.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klicka på **Skapa**.

    Jobbet har skapats och i portalen visas jobbinformation. Inget körs ännu, men – du måste konfigurera jobbet innan den kan startas.

### <a name="configure-job-input"></a>Konfigurera jobbindata

1. I instrumentpanelen eller **alla resurser** fönstret, lokaliserar och markerar den `asa_frauddetection_job_demo` Stream Analytics-jobb. 
2. I den **översikt** avsnittet i Stream Analytics-jobbfönstret klickar du på den **indata** box.

   ![Textrutan under topologi i Streaming Analytics-jobbfönstret](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klicka på **Lägg till strömindata** och välj **Event Hub**. Fyll sedan den nya inkommande sidan med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias  |  CallStream   |  Ange ett namn som identifierar jobbets indata.   |
   |Prenumeration   |  \<Din prenumeration\> |  Välj den prenumeration som har den Händelsehubb som du skapade.   |
   |Namnområde för händelsehubb  |  asa-eh-ns-demo |  Ange namnet på namnområdet för Händelsehubben.   |
   |Namn på händelsehubb  | asa-eh-frauddetection-demo | Välj namnet på din Event Hub.   |
   |Principnamn för Event Hub  | asa-policy-manage-demo | Välj den åtkomstprincip som du skapade tidigare.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klicka på **Skapa**.

## <a name="create-queries-to-transform-real-time-data"></a>Skapa frågor och transformera data i realtid

Nu har du ett Stream Analytics-jobb som inställningar för att läsa en inkommande dataström. Nästa steg är att skapa en fråga som analyserar data i realtid. Stream Analytics stöder en enkel, deklarativ frågemodell som beskriver transformationer för bearbetning i realtid. Frågorna använder en SQL-liknande språk som har vissa tillägg som är specifika för Stream Analytics. 

En enkel fråga kan bara läsa alla inkommande data. Men skapar du ofta förfrågningar som söker efter specifika data eller för relationer i data. I det här avsnittet av självstudiekursen ska du skapa och testa flera frågor för att lära dig några sätt som du kan omvandla en indataström för analys. 

De frågor som du skapar här visas bara omvandlade data på skärmen. I ett senare avsnitt får du konfigurera en utdatamottagare och en fråga som skriver transformerade data till den mottagaren.

Mer information om språk finns i [Frågespråksreferens för Azure Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Hämta exempeldata för att testa frågor

Appen TelcoGenerator skickar anrop poster till event hub och ditt Stream Analytics-jobb är konfigurerad för att läsa från event hub. Du kan använda en fråga för att testa jobbet att se till att den hämtar korrekt. Du behöver exempeldata för att testa en fråga i Azure-konsolen. Den här genomgången ska extrahera du exempeldata från strömmen som kommer till händelsehubben.

1. Kontrollera att TelcoGenerator-appen körs och producerar anrop poster.
2. Gå tillbaka till jobbfönstret för Streaming Analytics i portalen. (Om du har stängt rutan Sök efter `asa_frauddetection_job_demo` i den **alla resurser** fönstret.)
3. Klicka på den **fråga** box. Azure listar indata och utdata som är konfigurerade för jobbet, och kan du skapa en fråga som låter dig omvandla Indataströmmen som skickas till utdata.
4. I den **fråga** fönstret, klicka på punkterna bredvid den `CallStream` indata och väljer sedan **exempeldata från indata**.

   ![Menyn Alternativ för att använda exempeldata för posten Streaming Analytics-jobb med ”exempeldata från indata” valt](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Ange **minuter** till 3 och klicka sedan på **OK**. 
    
   ![Alternativ för sampling Indataströmmen med 3 minuter som har valts](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure-exempel 3 minuter leverantör av data från Indataströmmen och meddelar dig när exempeldata är klara. (Detta tar en liten stund.) 

Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempeldata och du måste skapa en ny uppsättning exempeldata. 

Alternativt kan du hämta en JSON-fil som innehåller exempeldata i den [från GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), och sedan ladda upp .json-filen ska användas som exempeldata för den `CallStream` indata. 

### <a name="test-using-a-pass-through-query"></a>Testa med hjälp av en direktfråga

Du kan använda en direktfråga för att läsa alla fält i nyttolasten för händelsen om du vill arkivera varje händelse.

1. Ange den här frågan i frågefönstret:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Precis som med SQL, nyckelord är inte skiftlägeskänsliga och blanksteg är inte särskilt stor.

    I den här frågan `CallStream` är det alias som du angav när du skapade indata. Om du använde ett annat alias kan du använda det namnet i stället.

2. Klicka på **Test**.

    Stream Analytics-jobbet körs frågan mot exempeldata och visar resultatet längst ned i fönstret. Resultatet indikerar att Event Hub och Streaming Analytics-jobbet är korrekt konfigurerade. (Enligt vad som anges, senare ska du skapa en utdatamottagare som frågan kan skriva data till.)

   ![Stream Analytics-jobbutdata, som visar 73 poster som genererats](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Det exakta antalet poster som visas beror på hur många poster har hämtats i ditt exempel 3 minuter.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Minska antalet fält med hjälp av en kolumn-projektion

I många fall behöver din analys inte alla kolumner från Indataströmmen. Du kan använda en fråga för att projicera en mindre uppsättning returnerade fält än i direktfrågan.

1. Ändra frågan i kodredigeraren till följande:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Klicka på **Test** igen. 

   ![Stream Analytics-jobbutdata för projektion visar 25 poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Antal inkommande anrop per region: Rullande fönster med aggregering

Anta att du vill räkna antalet inkommande anrop per region. När du vill utföra mängdfunktioner som inventering, i strömmande data måste du segmentera strömmen i den temporala enheter (eftersom själva dataströmmen är effektivt oändliga). Du gör detta med hjälp av ett Streaming Analytics [fönsterfunktion används](stream-analytics-window-functions.md). Du kan sedan arbeta med data i fönstret som en enhet.

Den här omvandlingen du vill ha en sekvens av temporala windows som inte överlappar varandra – varje panel kan ha en diskret uppsättning data som du kan gruppera och aggregera. Den här typen av fönstret kallas en *rullande fönster*. Du kan hämta ett antal inkommande samtal grupperade efter i fönstret rullande `SwitchNum`, som representerar landet/regionen där anropet har sitt ursprung. 

1. Ändra frågan i kodredigeraren till följande:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    Den här frågan använder den `Timestamp By` nyckelord i den `FROM` -satsen för att ange vilka tidsstämpelsfält i Indataströmmen du använder för att definiera utlösare för rullande fönster. I det här fallet fönstret delar in data i segment av den `CallRecTime` i varje post. (Om inget fält anges, används den tid som varje händelse anländer till event hub med fönsterhantering igen. Se ”tid Vs programmet ankomsttid” i [Stream Analytics Frågespråksreferens](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Projektionen innehåller `System.Timestamp`, som returnerar en tidsstämpel för slutet av varje fönster. 

    Om du vill ange att du vill använda ett rullande fönster som du använder den [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) fungera i den `GROUP BY` satsen. I funktionen anger du en tidsenhet (var som helst från en databehandlingsnoder till en dag) och en fönsterstorleken (hur många enheter). I det här exemplet rullande fönster som består av 5-sekunder intervall, så du får ett antal efter land/region för anrop som var femte sekund.

2. Klicka på **Test** igen. Observera att i resultaten tidsstämplar under **WindowEnd** finns i steg om 5-sekundersintervall.

   ![Stream Analytics-jobbutdata för aggregering 13 posterna](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Identifiera SIM bedrägerier med hjälp av en självkoppling

Överväg att bedrägliga användning ska vara anrop som kommer från samma användare men på olika platser inom 5 sekunder från varandra i det här exemplet. Samma användare kan till exempel inte legitimt ringa ett samtal från USA och Australien samtidigt. 

Om du vill söka efter dessa fall, du kan använda en självkoppling för strömmande data att ansluta till dataströmmen till sig själv baserad på den `CallRecTime` värde. Du kan sedan leta efter poster där den `CallingIMSI` värdet (det ursprungliga numret) är detsamma, men `SwitchNum` värdet (land/region för ursprung) är inte samma.

När du använder en koppling med strömmande data måste kopplingen tillhandahålla samma begränsningar för hur långt matchningsraderna kan delas upp i tid. (Som tidigare nämnts strömmande data är effektivt oändliga.) Tidsgränserna för relationen har angetts i den `ON` -satsen för kopplingen, med hjälp av den `DATEDIFF` funktion. I det här fallet baseras kopplingen på ett intervall för 5-sekundersintervall med samtalsdata.

1. Ändra frågan i kodredigeraren till följande: 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    Den här frågan är som alla SQL-koppling förutom för de `DATEDIFF` funktion i kopplingen. Den här versionen av `DATEDIFF` är specifik för Streaming Analytics och måste visas i den `ON...BETWEEN` satsen. Parametrarna är en tidsenhet (sekunder i det här exemplet) och alias två källor för kopplingen. Detta skiljer sig från standard SQL `DATEDIFF` funktion.

    Den `WHERE` satsen innehåller villkor som flaggar bedrägligt samtal: de ursprungliga växlarna inte är desamma. 

2. Klicka på **Test** igen. 

   ![Stream Analytics-jobbutdata för självkoppling, visar 6 poster som genererats](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klicka på **spara** att spara frågan självkoppling som en del av Streaming Analytics-jobbet. (Det inte att spara exempeldata.)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Skapa en utdatamottagare för att lagra transformerade data

Du har definierat en händelseström, en händelsehubb som indata för att mata in händelser och en fråga för att utföra en omvandling dataströmmen. Det sista steget är att definiera en utdatamottagare för jobbet – det vill säga en plats för att skriva transformerade dataström till. 

Du kan använda många resurser som utdatamottagarna – en SQL Server-databas, table storage, Data Lake-lagring, Power BI och även en annan händelsehubb. Den här självstudien skriver du strömmen till Azure Blob Storage, vilket är vanligt för att samla in händelseinformation för senare analys, eftersom den innehåller Ostrukturerade data.

Om du har ett befintligt blob storage-konto kan använda du som. För den här självstudien får du lära dig hur du skapar ett nytt lagringskonto.

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob Storage-konto

1. Välj **Skapa en resurs** > **Lagring** > **Lagringskonto** i det övre vänstra hörnet i Azure-portalen. Fyll jobbsidan för Lagringskontot med **namn** inställd på ”asaehstorage” **plats** inställd på ”östra USA”, **resursgrupp** inställd på ”asa-FT-ns-rg” (host lagringskontot i samma resursgrupp som Streaming-jobbet för bättre prestanda). Återstående inställningar kan ha kvar standardvärdena.  

   ![Skapa lagringskonto i Azure-portalen](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Gå tillbaka till jobbfönstret för Streaming Analytics i Azure-portalen. (Om du har stängt rutan Sök efter `asa_frauddetection_job_demo` i den **alla resurser** fönstret.)

3. I den **Jobbtopologi** klickar du på den **utdata** box.

4. I den **utdata** fönstret klickar du på **Lägg till** och välj **Blob-lagring**. Fyll sedan den nya utdata-sidan med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Utdataalias  |  CallStream-FraudulentCalls   |  Ange ett namn som identifierar jobbets utdata.   |
   |Prenumeration   |  \<Din prenumeration\> |  Välj den Azure-prenumeration där det lagringskonto som du skapade finns. Lagringskontot kan vara i samma eller en annan prenumeration. I det här exemplet förutsätts att du har skapat lagringskontot i samma prenumeration. |
   |Lagringskonto  |  asaehstorage |  Ange namnet på det lagringskonto du skapade. |
   |Container  | asa-fraudulentcalls-demo | Välj Skapa nytt och ange ett behållarnamn. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klicka på **Spara**. 


## <a name="start-the-streaming-analytics-job"></a>Starta Streaming Analytics-jobbet

Jobbet har nu konfigurerats. Du har angett indata (händelsehubb), en transformation (frågan för att leta efter bedrägliga samtal) och utdata (blob storage). Du kan nu starta jobbet. 

1. Kontrollera att TelcoGenerator-appen körs.

2. I jobbfönstret klickar du på **starta**. I den **startjobb** fönstret för jobbets utdata starttid, väljer **nu**. 

   ![Starta Stream Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Granska transformerade data

Nu har du en fullständig Streaming Analytics-jobbet. Jobbet undersöka en dataström med telefonsamtal metadata, letar du efter bedrägliga samtal i realtid och skriva information om dessa bedrägliga samtal till lagring. 

Den här kursen kan du titta på de data som inhämtas av Streaming Analytics-jobbet. Data skrivs till Azure BLOB-lagring i segment (filer). Du kan använda ett verktyg som läser Azure Blob Storage. Enligt vad som anges i avsnittet förutsättningar, du kan använda Azure-tillägg i Visual Studio eller du kan använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

När du undersöker innehållet i en fil i blob storage, se något som liknar följande:

   ![Azure blob storage med Streaming Analytics-utdata](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Rensa resurser

Det finns andra artiklar som fortsätta med scenariot för identifiering av bedrägerier och använda de resurser som du har skapat i den här självstudien. Om du vill fortsätta se förslag under **nästa steg**.

Om du är klar och du inte behöver resurserna som du har skapat, du kan dock ta bort dem så att du inte debiteras onödiga Azure. I så fall föreslår vi att du gör följande:

1. Stoppa Streaming Analytics-jobbet. I den **jobb** fönstret klickar du på **stoppa** högst upp.
2. Stoppa Telco Generator-app. Tryck på Ctrl + C i kommandofönstret där du startade appen.
3. Om du har skapat en ny blob storage-konto för den här självstudien kan du ta bort den. 
4. Ta bort Streaming Analytics-jobbet.
5. Ta bort händelsehubben.
6. Ta bort händelsehubbens namnområde.

## <a name="get-support"></a>Få support

För mer hjälp kan du prova den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta den här självstudien med följande artikel:

* [Stream Analytics och Power BI: En instrumentpanel för analys i realtid för strömmande data](stream-analytics-power-bi-dashboard.md). Den här artikeln visar hur du skickar TelCo utdata från Stream Analytics-jobb till Power BI för visualisering i realtid och analys.

Mer information om Stream Analytics i allmänhet finns i följande artiklar:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
