# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Komma igång med Azure Stream Analytics: att upptäcka bedrägerier i realtid

Den här självstudiekursen innehåller en slutpunkt till slutpunkt illustration av hur du använder Azure Stream Analytics. Lär dig att: 

* Ta strömning händelser till en instans av Händelsehubbar i Azure. I den här kursen ska du använda en app som vi tillhandahåller som simulerar en dataström med metadata för mobiltelefon poster.

* Skriva SQL-liknande Stream Analytics-frågor för att omvandla data, samla information eller söker efter mönster. Du ser hur du använder en fråga för att undersöka den inkommande dataströmmen och leta efter anrop som kan vara falsk.

* Skicka resultaten till utdatamottagare (lagring) som du kan analysera för ytterligare information. I så fall måste skickar du anropsdata misstänkta till Azure Blob storage.

I den här självstudiekursen använda vi exemplet med baserat på telefonsamtal data att upptäcka realtid bedrägerier. Men den teknik som beskriver vi är passar också för andra typer av att upptäcka bedrägerier, till exempel kreditkort bedrägeri eller identitetsstöld. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: Telekommunikation och SIM att upptäcka bedrägerier i realtid

Ett telekommunikation företag har stora mängder data för inkommande samtal. Företaget vill identifiera bedrägliga anrop i realtid så att de kan meddela kunder eller stänga av tjänsten för ett visst tal. En typ av SIM bedrägeri innebär att flera anrop från samma identitet vid ungefär samma tidpunkt men sig på geografiskt skilda platser. För att identifiera den här typen av bedrägerier, måste företaget att granska inkommande phone poster och leta efter specifika mönster – i det här fallet för anrop som görs vid ungefär samma tidpunkt i olika länder. Alla telefonen poster som tillhör den här kategorin skrivs till lagring för efterföljande analys.

## <a name="prerequisites"></a>Krav

I den här självstudiekursen kommer du simulera telefonsamtal data med hjälp av ett klientprogram som genererar exempel telefonsamtal metadata. Vissa av posterna som ger appen ser ut som bedrägliga anrop. 

Innan du börjar bör du kontrollera att du har följande:

* Ett Azure-konto.
* Anropet händelse generator app. Du kan få detta genom att hämta den [TelcoGenerator.zip filen](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) från Microsoft Download Center. Packa upp paketet till en mapp på datorn. Om du vill visa källan Platskod och kör appen i en felsökare kan du få app källkod från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows kan blockera hämtade ZIP-filen. Om du går inte att packa upp den, högerklicka på filen och välj **egenskaper**. Om meddelandet ”den här filen kommer från en annan dator och kan ha blockerats för att skydda den här datorn”, väljer du den **avblockera** alternativ och klickar sedan på **tillämpa**.

Om du vill granska resultatet av Streaming Analytics-jobbet måste du också ett verktyg för att visa innehållet i en Azure Blob Storage-behållare. Om du använder Visual Studio, kan du använda [Azure Tools för Visual Studio](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) eller [Visual Studio Cloud Explorer](https://docs.microsoft.com/en-us/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Du kan också installera fristående verktyg som [Azure Lagringsutforskaren](http://storageexplorer.com/) eller [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Skapa ett Azure event hubs för att mata in händelser

Att analysera en dataström du *infognings-* till Azure. Ett vanligt sätt att mata in data är att använda [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), där du kan mata in miljontals händelser per sekund och sedan bearbeta och lagra händelseinformationen om. För den här självstudiekursen skapar en händelsehubb och har appen anropet händelse generator skicka anropsdata till den event hub. Mer information om händelsehubbar finns i [Azure Service Bus-dokumentationen](https://docs.microsoft.com/en-us/azure/service-bus/).

>[!NOTE]
>En mer detaljerad version av den här proceduren finns [skapa ett namnområde för Händelsehubbar och en händelsehubb med hjälp av Azure portal](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Skapa ett namnområde och händelse-hubb
I den här proceduren du först skapa en event hub-namnområde och sedan lägga till en händelsehubb i detta namnområde. Event hub namnområden används för att gruppera relaterade händelser-bussen instanser. 

1. Logga in på Azure portal och klicka på **ny** > **Sakernas Internet** > **Händelsehubb**. 

2. I den **skapa namnområdet** rutan Ange ett namn för namnområdet som `<yourname>-eh-ns-demo`. Du kan använda valfritt namn för namnområdet, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
3. Välj en prenumeration och skapa eller välja en resursgrupp och sedan klicka på **skapa**. 

    ![Skapa ett namnområde för event hub](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png)
 
4. När namnområdet distribution har slutförts, kan du hitta event hub namnområdet i din lista över Azure-resurser. 

5. Klicka på det nya namnområdet och i fönstret namnområdet på **Händelsehubb**.

    ![Knappen Lägg till Händelsehubben för att skapa en ny händelsehubb ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Namn på ny händelsehubb `sa-eh-frauddetection-demo`. Du kan använda ett annat namn. Om du vill anteckna, eftersom du måste ha namnet senare. Du behöver inte ange andra alternativ för händelsehubben just nu.

    ![Bladet för att skapa en ny händelsehubb](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png)
    
 
7. Klicka på **Skapa**.
### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan en process kan skicka data till en händelsehubb, måste händelsehubben ha en princip som tillåter åtkomstbehörighet. Åtkomstprincipen producerar en anslutningssträng som innehåller auktoriseringsinformation om.

1.  Klicka på i fönstret händelse namnområde **Händelsehubbar** och klicka sedan på namnet på den nya händelsehubben.

2.  Klicka på i fönstret event hub **principer för delad åtkomst** och klicka sedan på  **+ &nbsp;Lägg till**.

    >[!NOTE]
    >Kontrollera att du arbetar med händelsehubb, inte event hub namnområde.

3.  Lägg till en princip med namnet `sa-policy-manage-demo` och **anspråk**väljer **hantera**.

    ![Bladet för att skapa en ny händelse hubb princip](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png)
 
4.  Klicka på **Skapa**.

5.  När principen har distribuerats, klickar du på den i listan med principer för delad åtkomst.

6.  I listrutan **sträng-primära ANSLUTNINGSNYCKEL** och klicka på kopieringsknappen bredvid anslutningssträngen. 
    
    ![Kopiera den primära anslutning strängnyckeln från principen för åtkomst](./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png)
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen för nästa avsnitt, när du har mindre ändringar.

    Anslutningssträngen ser ut så här:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=sa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=sa-eh-frauddetection-demo

    Observera att anslutningssträngen innehåller flera nyckel-värdepar, avgränsade med semikolon: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, och `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurera och starta programmet händelse generator

Innan du börjar TelcoGenerator app måste konfigurera du den så att den ska skicka anrop poster till event hub du skapat.

### <a name="configure-the-telcogeneratorapp"></a>Konfigurera TelcoGeneratorapp

1.  I Redigeraren för dit du kopierade anslutningssträngen, notera den `EntityPath` värdet och ta sedan bort den `EntityPath` par (inte glöm att ta bort semikolonet som föregår den). 

2.  Öppna filen telcodatagen.exe.config i en textredigerare i mappen där du unzipped TelcoGenerator.zip-filen. (Det finns fler än en .config-fil, så att du öppnat rätt.)

3.  I den `<appSettings>` element:

    * Värdet för den `EventHubName` nyckeln till händelsehubbens namn (det vill säga till värdet för entiteten sökvägen).
    * Ange värdet för den `Microsoft.ServiceBus.ConnectionString` nyckel i anslutningssträngen. 

    Den `<appSettings>` avsnitt ser ut som i följande exempel. (För tydlighetens skull vi omsluten raderna och ta bort vissa tecken från autentiseringstoken.)

    ![Konfigurationsfilen för TelcoGenerator app visar namn och -anslutningssträngen för hubben](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Spara filen. 

### <a name="start-the-app"></a>Starta appen
1.  Öppna ett kommandofönster och gå till mappen där appen TelcoGenerator är uppackade.
2.  Ange följande kommando:

        telcodatagen.exe 1000 .2 2

    Parametrarna är: 

    * Antal skivor per timme. 
    * SIM-kort bedrägeri sannolikhet: Hur ofta som en procentandel av alla anrop, att appen ska simulera ett bedrägliga anrop. Värdet.2 innebär som cirka 20% av anrop poster ser falska.
    * Varaktighet i timmar. Antal timmar som appen ska köras. Du kan även stoppa appen helst genom att trycka på Ctrl + C på kommandoraden.

    Appen startar efter några sekunder att telefonsamtal poster på skärmen när den skickar dem till händelsehubben.

Vissa viktiga fält som du ska använda i realtid bedrägeri identifiering programmet är följande:

|**Post**|**Definition**|
|----------|--------------|
|`CallrecTime`|Tidsstämpel för anropet starttid. |
|`SwitchNum`|Telefonväxeln används för att ansluta anropet. I det här exemplet är växlarna strängar som representerar landet där ursprung (USA Kina, Storbritannien, Tyskland eller Australien). |
|`CallingNum`|Telefonnumret för anroparen. |
|`CallingIMSI`|Internationella Mobile Subscriber Identity (IMSI). Det här är den unika identifieraren för anroparen. |
|`CalledNum`|Anropet mottagarens telefonnummer. |
|`CalledIMSI`|Internationella Mobile Subscriber Identity (IMSI). Det här är den unika identifieraren för anropet mottagaren. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Skapa ett Stream Analytics-jobb för att hantera strömmande data

Nu när du har en dataström med anropet händelser kan ställa du in ett Stream Analytics-jobb. Jobbet ska läsa data från event hub som du skapat. 

### <a name="create-the-job"></a>Skapa jobbet 

1. I Azure-portalen klickar du på **ny** > **Sakernas Internet** > **Stream Analytics-jobbet**.

2. Namnge jobbet `sa_frauddetection_job_demo`, ange en prenumeration, resursgrupp och plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betala för att överföra data mellan regioner.

    ![Skapa nytt Stream Analytics-jobb](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png)

3. Klicka på **Skapa**.

    Jobbet har skapats och portalen visar jobbinformation. Inget kör ännu, men – du måste konfigurera jobbet innan den kan startas.

### <a name="configure-job-input"></a>Konfigurera jobbet indata

1. I instrumentpanelen eller **alla resurser** rutan, lokaliserar och markerar den `sa_frauddetection_job_demo` Stream Analytics-jobbet. 
2. I den **jobbet topologi** avsnitt i fönstret Stream Analytics-jobbet klickar du på den **indata** rutan.

    ![Textrutan under topologi i fönstret Streaming Analytics-jobb](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klicka på  **+ &nbsp;Lägg till** och fyll sedan i fönstret med dessa värden:

    * **Ett inmatat alias**: Använd namnet `CallStream`. Om du använder ett annat namn, notera den eftersom du behöver senare.
    * **Typ av datakälla**: Välj **dataströmmen**. (**Referensdata** refererar till statisk sökning data som du inte använder i den här självstudiekursen.)
    * **Källan**: Välj **händelsehubb**.
    * **Importera alternativet**: Välj **Använd händelsehubb från aktuell prenumeration**. 
    * **Service bus-namnrymd**: Välj händelsen hubb namnområdet som du skapade tidigare (`<yourname>-eh-ns-demo`).
    * **Händelsehubb**: Välj händelsehubben som du skapade tidigare (`sa-eh-frauddetection-demo`).
    * **Namnet på händelsehubben princip**: Välj den åtkomstprincip som du skapade tidigare (`sa-policy-manage-demo`).

    ![Skapa nya indata för Streaming Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png)

4. Klicka på **Skapa**.

## <a name="create-queries-to-transform-real-time-data"></a>Skapa frågor för att omvandla data i realtid

Du har nu ett Stream Analytics-jobb som ställts in för att läsa en inkommande dataström. Nästa steg är att skapa en omvandling som analyserar data i realtid. Det gör du genom att skapa en fråga. Stream Analytics stöder en enkel, deklarativ frågemodell som beskriver omvandlingarna för realtidsbearbetning. Frågorna använder en SQL-liknande språk som har vissa tillägg som är specifika för stream analytics. 

En enkel fråga kan bara läsa alla inkommande data. Men skapa du ofta frågor som ser ut för specifika data eller relationer i data. I det här avsnittet av kursen, skapa och testa flera frågor för att lära dig några sätt som du kan omvandla en indataström för analys. 

Frågor som du skapar här visas bara omvandlade data på skärmen. I ett senare avsnitt får du konfigurera utdatamottagare och en fråga som skriver omvandlade data till som mottagare.

Mer information om språk finns i [referens för Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Hämta exempeldata för att testa frågor

TelcoGenerator appen skickar poster för anrop till händelsehubben och Stream Analytics-jobbet har konfigurerats för att läsa från event hub. Du kan använda en fråga för att testa jobbet att se till att läsa på rätt sätt. Om du vill testa en fråga i Azure-konsolen behöver exempeldata. Den här genomgången ska du extrahera exempeldata från strömmen som kommer till händelsehubben.

1. Se till att appen TelcoGenerator körs och producerar anropet poster.
2. Gå tillbaka till fönstret Streaming Analytics-jobbet i portalen. (Om du har stängt fönstret kan du söka efter `sa_frauddetection_job_demo` i den **alla resurser** fönstret.)
3. Klicka på den **frågan** rutan. Azure visar in- och utgångar som är konfigurerade för jobbet och kan du skapa en fråga som kan du omvandla Indataströmmen som skickas till utdata.
4. I den **frågan** rutan klickar du på punkter bredvid den `CallStream` indata och välj sedan **exempeldata från indata**.

    ![Menyn Alternativ exempeldata för posten Streaming Analytics-jobb med ”exempeldata från indata” markerad](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)

    Då öppnas ett fönster där du kan ange hur mycket exempeldata som kan definieras enligt hur lång tid för att läsa Indataströmmen.

5. Ange **minuter** till 3 och klicka sedan på **OK**. 
    
    ![Alternativ för provtagning Indataströmmen, med ”3 minuter” markerad.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure exempel 3 minuter kan du se från Indataströmmen och meddelar dig när exempeldata är klar. (Detta tar en stund.) 

Exempeldata lagras tillfälligt och är tillgänglig när du har frågefönster öppnas. Om du stänger frågefönstret exempeldata ignoreras och måste du skapa en ny uppsättning exempeldata. 

Alternativt kan du hämta en JSON-fil som innehåller exempeldata [från GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), och sedan ladda upp den JSON-fil som ska användas som exempeldata för den `CallStream` indata. 

### <a name="test-using-a-pass-through-query"></a>Testa med en direktfråga

Du kan använda en direktfråga för att läsa alla fält i nyttolasten för händelsen om du vill arkivera varje händelse.

1. Ange den här frågan i frågefönstret:

        SELECT 
            *
        FROM 
            CallStream

    >[!NOTE]
    >Precis som med SQL, nyckelord är inte skiftlägeskänsliga och blanksteg är inte viktig.

    I den här frågan `CallStream` är alias som du angav när du skapade indata. Om du använde ett annat alias kan du använda det namnet i stället.

2. Klicka på **Test**.

    Stream Analytics-jobbet körs frågan mot exempeldata och visar utdata längst ned i fönstret. Du ser att händelsehubben och Streaming Analytics-jobbet är korrekt konfigurerade. (Som anges senare ska du skapa utdatamottagare som frågan kan skriva data till.)

    ![Stream Analytics-jobbet på utdata som visar 73 poster som skapas](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Det exakta antalet poster som visas beror på hur många poster som har hämtats i ditt exempel 3 minuter.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Minska antalet fält med en kolumn projektion

I många fall måste inte dina analyser alla kolumner i Indataströmmen. Du kan använda en fråga för att projicera en mindre uppsättning returnerade fält än i direktfrågan.

1. Ändra frågan i kodredigeraren för till följande:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
        FROM 
            CallStream

2. Klicka på **Test** igen. 

    ![Stream Analytics-jobbet utdata för projektion som visar 25 poster som genereras](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Antal inkommande anrop per region: rullande fönster med aggregering

Anta att du vill räkna antalet inkommande anrop per region. När du vill utföra mängdfunktioner som inventering, med strömmande data, måste du segmentera dataströmmen i temporal enheter (eftersom dataströmmen själva är effektivt oändliga). Du gör detta med hjälp av en Streaming Analytics [fönstret funktionen](stream-analytics-window-functions.md). Du kan arbeta med data i fönstret som en enhet.

Den här omvandlingen du vill ha en sekvens av temporala windows som inte överlappar varandra – varje fönster har en separat uppsättning data som du kan gruppera och aggregering. Den här typen av fönstret kallas en *rullande fönster* . I fönstret rullande du antalet inkommande samtal grupperade efter `SwitchNum`, som motsvarar det land där anropet kommer från. 

1. Ändra frågan i kodredigeraren för till följande:

        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Den här frågan använder den `Timestamp By` nyckelord i den `FROM` -sats för att ange vilka tidsstämpelfältet i Indataströmmen definition rullande fönster. I det här fallet fönstret delas upp data i segment av den `CallRecTime` i varje post. (Om inget fält har angetts används den tid som varje händelse anländer till händelsehubben med fönsterhantering igen. Se ”tid Vs programmet ankomsttid” i [strömma Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Projektionen som innehåller `System.Timestamp`, som returnerar en tidsstämpel för slutet av varje fönster. 

    Om du vill ange att du vill använda en rullande fönster som du använder den [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) fungera i den `GROUP BY `satsen. I funktionen anger du en tidsenhet (var som helst från en mikrosekundnivå till en dag) och en fönsterstorlek (hur många enheter). I det här exemplet består rullande fönster av 5 sekunder intervall, så du får ett antal per land för samtal som var femte sekund.

2. Klicka på **Test** igen. Observera att i resultaten tidsstämplar under **WindowEnd** finns i steg 5 sekunder.

    ![Stream Analytics-jobbet utdata för aggregering, visar 13 poster som genereras](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Identifiera SIM bedrägeri med hjälp av en självkoppling

I det här exemplet Vi anser att bedrägliga användning ska anrop som kommer från samma användare men på olika platser inom 5 sekunder från varandra. Samma användare kan till exempel legitimt ringa ett samtal från USA och Australien på samma gång. 

Om du vill söka efter dessa fall, du kan använda en självkoppling för strömmande data att ansluta till dataströmmen till sig själv baserat på den `CallRecTime` värde. Du kan sedan söka efter anropet poster där den `CallingIMSI` värde (det ursprungliga nummer) är detsamma, men `SwitchNum` värdet (land för ursprung) är inte samma.

När du använder en koppling med strömmande data måste kopplingen ange vissa begränsningar på hur långt matchande rader kan delas upp i tid. (Som tidigare nämnts strömmande data är effektivt oändlig.) Tid gränser för relationen har angetts i den `ON` -satsen för en koppling med hjälp av den `DATEDIFF` funktion. I det här fallet baseras kopplingen på ett samtal data 5 sekunder tidsintervall.

1. Ändra frågan i kodredigeraren för till följande: 

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

    Den här frågan är precis som alla SQL-koppling utom för den `DATEDIFF` funktionen i kopplingen. Detta är en version av `DATEDIFF` som är specifika för Streaming Analytics och det måste visas i den `ON...BETWEEN` satsen. Parametrarna är en tidsenhet (sekunder i det här exemplet) och alias två källor för kopplingen. (Detta skiljer sig från standard SQL `DATEDIFF` funktionen.) 

    Den `WHERE` -instruktionen innehåller villkor som flaggar bedrägliga anropet: de ursprungliga växlarna inte är samma. 

2. Klicka på **Test** igen. 

    ![Stream Analytics-jobbet utdata för självkoppling, visar 6 poster som genereras](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klicka på **Spara**. Detta sparar självkoppling frågan som en del av Streaming Analytics-jobbet. (Den inte spara exempeldata.)

    ![Spara Stream Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png)

## <a name="create-an-output-sink-to-store-transformed-data"></a>Skapa utdatamottagare för att lagra omvandlade data

Du har definierat en händelseström, en händelsehubb som indata för att mata in händelser och en fråga för att genomföra en omvandling över dataströmmen. Det sista steget är att definiera utdatamottagare för jobbet – det vill säga en plats för att skriva transformerade dataström till. 

Du kan använda många resurser som utdata sänkor – SQL Server-databasen, tabellagring, Data Lake lagring, Power BI och en annan händelsehubb. För den här kursen ska du skriva dataströmmen till Azure Blob Storage, vilket är ett vanliga alternativ för att samla in händelseinformation för senare analys, eftersom den innehåller Ostrukturerade data.

Om du har en befintlig blob storage-konto kan använda du som. För den här kursen finns visar om hur du skapar ett nytt lagringskonto för den här kursen.

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob Storage-konto

1. Gå tillbaka till fönstret Streaming Analytics-jobb på Azure-portalen. (Om du har stängt fönstret kan du söka efter `sa_frauddetection_job_demo` i den **alla resurser** fönstret.)
2. I den **jobbet topologi** klickar du på den **utdata** rutan. 
3. I den **utdata** rutan klickar du på  **+ &nbsp;Lägg till** och fyll sedan i fönstret med dessa värden:

    * **Ett utdataalias**: Använd namnet `CallStream-FraudulentCalls`. 
    * **Sink**: Välj **Blob storage**.
    * **Importalternativ**: Välj **använda blob storage från aktuell prenumeration**.
    * **Lagringskontot**. Välj **Skapa nytt lagringskonto.**
    * **Lagringskontot** (andra rutan). Ange `YOURNAMEsademo`, där `YOURNAME` är ditt namn eller en annan unik sträng. Namnet kan använda bara gemena bokstäver och siffror, och den måste vara unikt i Azure. 
    * **Behållaren**. Ange `sa-fraudulentcalls-demo`.
    Lagringskontonamn och behållarnamn används tillsammans för att ge en URI för blobblagring, så här: 

    `http://yournamesademo.blob.core.windows.net/sa-fraudulentcalls-demo/...`
    
    ![”Nya utdata” fönstret för Stream Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png)
    
4. Klicka på **Skapa**. 

    Azure skapar lagringskontot och skapar automatiskt en nyckel. 

5. Stäng den **utdata** fönstret. 

## <a name="start-the-streaming-analytics-job"></a>Starta Streaming Analytics-jobbet

Jobbet har nu konfigurerats. Du har angett indata (händelsehubben), en omvandling (frågan ska sökas efter bedrägliga anrop) och utdata (blobblagring). Nu kan du starta jobbet. 

1. Kontrollera att TelcoGenerator appen körs.

2. Klicka på i fönstret jobb **starta**.

    ![Starta Stream Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-output.png)

3. I den **startjobb** fönstret för utdata starttid, Välj **nu**. 

4. Klicka på **starta**. 

    ![”Starta jobbet” fönstret för Stream Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-job-blade.png)

    Azure meddelar dig när jobbet har startat och i fönstret jobb visas status som **kör**.

    ![Stream Analytics-jobbstatusen, visar ”körs”](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-running-status.png)
    

## <a name="examine-the-transformed-data"></a>Granska omvandlade data

Nu har du en fullständig Streaming Analytics-jobbet. Jobbet undersöka en dataström med metadata för telefonsamtal, söker efter bedrägliga telefonsamtal i realtid och skriva information om dessa falska anrop till lagring. 

Du kanske vill titta på de data som fångas av Streaming Analytics-jobb för den här kursen. Data skrivs till Azure-bloggen Storage i segment (filer). Du kan använda ett verktyg som läser Azure Blob Storage. Enligt beskrivningen i avsnittet förutsättningar, du kan använda Azure tillägg i Visual Studio och du kan använda ett verktyg som [Azure Lagringsutforskaren](http://storageexplorer.com/) eller [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). 

När du undersöker innehållet i en fil i blob storage finns något som liknar följande:

![Azure blob storage med Streaming Analytics-utdata](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Rensa resurser

Vi har ytterligare artiklar som fortsätta med scenario för upptäckt av bedrägerier och som använder de resurser som du har skapat i den här kursen. Om du vill fortsätta finns förslag under **nästa steg** senare.

Men om du är klar och du inte behöver de resurser som du har skapat, du kan ta bort dem så att du inte debiteras onödiga Azure. Vi rekommenderar i så fall kan du göra följande:

1. Stoppa Streaming Analytics-jobbet. I den **jobb** rutan klickar du på **stoppa** längst upp.
2. Stoppa anger Generator app. Tryck på Ctrl + C i kommandofönstret som du startade appen.
3. Ta bort om du har skapat en ny blob storage-konto för den här kursen. 
4. Ta bort Streaming Analytics-jobbet.
5. Ta bort händelsehubben.
6. Ta bort namnområdet event hub.

## <a name="get-support"></a>Få support

För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta den här självstudiekursen med följande artikel:

* [Strömma analyser och Power BI: en analys i realtid instrumentpanel för strömmande data](stream-analytics-power-bi-dashboard.md). Den här artikeln visar hur du skickar utdata till Stream Analytics-jobb anger till Power BI för visualisering i realtid och analys.

Mer information om Stream Analytics i allmänhet finns dessa artiklar:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
