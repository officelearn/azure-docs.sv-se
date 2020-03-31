---
title: Identifiering av bedrägerier i realtid med hjälp av Azure Stream Analytics
description: Läs om hur du skapar en lösning för bedrägeriidentifiering i realtid med Stream Analytics. Använd en händelsehubb för händelsebearbetning i realtid.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c0b2943e1f0d7f2386ec09da03d297a570eede7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276486"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Kom igång med Azure Stream Analytics: Bedrägeriidentifiering i realtid

Den här självstudien innehåller en heltäckande illustration av hur du använder Azure Stream Analytics. Lär dig att: 

* För in strömmande händelser i en instans av Azure Event Hubs. I den här självstudien ska du använda en app som simulerar en ström av metadataposter för mobiltelefoner.

* Skriv SQL-liknande Stream Analytics-frågor för att omvandla data, samla information eller leta efter mönster. Du kommer att se hur du använder en fråga för att undersöka den inkommande strömmen och leta efter samtal som kan vara bedrägliga.

* Skicka resultaten till en utdatamottagare (lagring) som du kan analysera för ytterligare insikter. I det här fallet skickar du misstänkta samtalsdata till Azure Blob-lagring.

Den här självstudien använder exemplet med bedrägeriidentifiering i realtid baserat på telefonsamtalsdata. Den teknik som illustreras lämpar sig även för andra typer av bedrägeriupptäckt, såsom kreditkortsbedrägerier eller identitetsstöld. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: Upptäckt av telekommunikations- och SIM-bedrägerier i realtid

Ett telekommunikationsföretag har en stor mängd data för inkommande samtal. Företaget vill upptäcka bedrägliga samtal i realtid så att de kan meddela kunder eller stänga av tjänsten för ett visst nummer. En typ av SIM-bedrägeri innebär flera samtal från samma identitet ungefär samtidigt men på geografiskt olika platser. För att upptäcka den här typen av bedrägerier måste företaget granska inkommande telefonregister och leta efter specifika mönster – i det här fallet för samtal som görs ungefär samtidigt i olika länder/regioner. Alla telefonregister som tillhör den här kategorin skrivs till lagring för efterföljande analys.

## <a name="prerequisites"></a>Krav

I den här självstudien simulerar du telefonsamtalsdata med hjälp av en klientapp som genererar exempel på telefonsamtalsmetadata. Vissa av de poster som appen producerar ser ut som bedrägliga samtal. 

Se till att du har följande innan du börjar:

* Ett Azure-konto.
* Call-event generator app, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), som kan laddas ner från Microsoft Download Center. Packa upp paketet i en mapp på datorn. Om du vill se källkoden och köra appen i en felsökare kan du hämta appens källkod från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows kan blockera den nedladdade ZIP-filen. Om du inte kan packa upp den högerklickar du på filen och väljer **Egenskaper**. Om meddelandet "Den här filen kom från en annan dator och kan vara blockerad för att skydda den här datorn" markerar du alternativet **Ta bort blockering** och klickar sedan på **Använd**.

Om du vill undersöka resultatet av streaminganalysjobbet behöver du också ett verktyg för att visa innehållet i en Azure Blob Storage-behållare. Om du använder Visual Studio kan du använda [Azure Tools för Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) eller Visual Studio Cloud [Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Du kan också installera fristående verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Skapa en Azure Event Hubs för att få plats med händelser

Om du vill analysera en dataström *indetar* du den i Azure. Ett typiskt sätt att använda data är att använda [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), som låter dig inta miljontals händelser per sekund och sedan bearbeta och lagra händelseinformation. För den här självstudien skapar du en händelsehubb och låter sedan ringhändelsegeneratorappen skicka samtalsdata till händelsehubben. Mer information om händelsehubbar finns i [Azure Service Bus-dokumentationen](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>En mer detaljerad version av den här proceduren finns i [Skapa ett namnområde för händelsehubbar och en händelsehubb med Azure-portalen](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Skapa ett namnområde och en händelsehubb
I den här proceduren skapar du först ett namnområde för händelsehubben och sedan lägger du till en händelsehubb i namnområdet. Namnområden för händelsehubben används för att logiskt gruppera relaterade händelsebussinstanser. 

1. Logga in på Azure-portalen och klicka på **Skapa en resurs** längst upp till vänster på skärmen.

2. Välj **Alla tjänster** på den vänstra menyn och välj **stjärnan (`*`)** bredvid **Event Hubs** i kategorin **Analytics**. Bekräfta att **Event Hubs** läggs till i **FAVORITER** på den vänstra navigeringsmenyn. 

   ![Söka efter Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Välj **Event Hubs** under **FAVORITER** på den vänstra navigeringsmenyn och välj **Lägg till** i verktygsfältet.

   ![Knappen Lägg till](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. Ange **Create namespace** ett namnområdesnamn som `<yourname>-eh-ns-demo`. Du kan använda vilket namn som helst för namnområdet, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
5. Välj en prenumeration och skapa eller välj en resursgrupp och klicka sedan på **Skapa**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. När namnområdet har distribuerats hittar du namnområdet för händelsehubben i listan över Azure-resurser. 

7. Klicka på det nya namnområdet och klicka på **Händelsehubben**i namnområdesfönstret .

   ![Knappen Lägg till händelsehubb för att skapa en ny händelsehubb](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Namnge den nya `asa-eh-frauddetection-demo`händelsehubben . Du kan använda ett annat namn. Om du gör det, anteckna det, eftersom du behöver namnet senare. Du behöver inte ange några andra alternativ för händelsehubben just nu.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Klicka på **Skapa**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan en process kan skicka data till en händelsehubb måste händelsehubben ha en princip som ger lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1. Klicka på **Händelsehubbar** i fönstret för händelsenamnområde och klicka sedan på namnet på den nya händelsehubben.

2. Klicka på Principer för **delad åtkomst** i händelsenavrutan och klicka sedan på ** + &nbsp;Lägg till**.

    > [!NOTE]
    > Kontrollera att du arbetar med händelsehubben, inte namnområdet för händelsehubben.

3. Lägg till `asa-policy-manage-demo` en princip som heter och för **Anspråk**väljer du **Hantera**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Klicka på **Skapa**.

5. När principen har distribuerats klickar du på den i listan över principer för delad åtkomst.

6. Leta reda på rutan MED NAMNET **CONNECTION STRING-PRIMARY KEY** och klicka på kopieringsknappen bredvid anslutningssträngen. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen för nästa avsnitt när du har gjort några små ändringar i den.

    Anslutningssträngen ser ut så här:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Observera att anslutningssträngen innehåller flera nyckelvärdespar, avgränsade `Endpoint` `SharedAccessKeyName`med `SharedAccessKey`semikolon: , , och `EntityPath`.  


## <a name="configure-and-start-the-event-generator-application"></a>Konfigurera och starta händelsegeneratorprogrammet

Innan du startar TelcoGenerator-appen måste du konfigurera den så att den skickar samtalsposter till händelsehubben som du skapade.

### <a name="configure-the-telcogenerator-app"></a>Konfigurera telcogenerator-appen

1. I redigeraren där du kopierade anslutningssträngen `EntityPath` antecknar du `EntityPath` värdet och tar sedan bort paret (glöm inte att ta bort semikolon som föregår det). 

2. Öppna filen telcodatagen.exe.config i en redigerare i mappen där du packade upp filen TelcoGenerator.zip. (Det finns mer än en config-fil, så se till att du öppnar den rätta.)

3. I `<appSettings>` elementet:

   * Ange värdet för `EventHubName` nyckeln till händelsenavnamnet (det vill än värdet för entitetssökvägen).
   * Ange värdet för `Microsoft.ServiceBus.ConnectionString` nyckeln till anslutningssträngen. 

   Avsnittet `<appSettings>` ser ut som följande exempel:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Spara filen. 

### <a name="start-the-app"></a>Starta appen

1. Öppna ett kommandofönster och ändra till mappen där TelcoGenerator-appen packas upp.

2. Ange följande kommando:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   Parametrarna är: 

   * Antal CDR per timme. 
   * SIM-kort bedrägeri sannolikhet: Hur ofta, som en procentandel av alla samtal, att appen ska simulera ett bedrägligt samtal. Värdet 0.2 betyder att cirka 20 % av anropsposterna ser bedrägliga ut.
   * Varaktighet i timmar. Antalet timmar som appen ska köra. Du kan också stoppa appen när som helst genom att trycka på Ctrl+C på kommandoraden.

   Efter några sekunder börjar appen visa telefonsamtalsposter på skärmen och skickar dem till en händelsehubb.

Några av de viktiga fält som du kommer att använda i det här programmet för bedrägeriidentifiering i realtid är följande:

|**Spela in**|**Definition**|
|----------|--------------|
|`CallrecTime`|Tidsstämpeln för samtalets starttid. |
|`SwitchNum`|Telefonväxeln används för att ansluta samtalet. I det här exemplet är växlarna strängar som representerar ursprungslandet/ursprungsregionen (USA, Kina, Storbritannien, Tyskland eller Australien). |
|`CallingNum`|Uppringarens telefonnummer. |
|`CallingIMSI`|International Mobile Subscriber Identity (IMSI). Detta är den unika identifieraren för anroparen. |
|`CalledNum`|Telefonnumret till mottagaren. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Detta är den unika identifieraren för samtalsmottagaren. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Skapa ett Stream Analytics-jobb för att hantera strömmande data

Nu när du har en ström av samtalshändelser kan du konfigurera ett Stream Analytics-jobb. Jobbet läser data från händelsehubben som du konfigurerar. 

### <a name="create-the-job"></a>Skapa jobbet 

1. Klicka på Skapa **ett** > **resurs-Internet för Saker** > **Stream Analytics-jobb**i Azure-portalen .

2. Namnge jobbet, `asa_frauddetection_job_demo`ange en prenumeration, resursgrupp och plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betalar för att överföra data mellan regioner.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klicka på **Skapa**.

    Jobbet skapas och portalen visar jobbinformation. Ingenting körs ännu, men du måste konfigurera jobbet innan det kan startas.

### <a name="configure-job-input"></a>Konfigurera jobbindata

1. Leta reda på och välj jobbet `asa_frauddetection_job_demo` Stream Analytics i instrumentpanelen eller fönstret Alla **resurser.** 
2. Klicka på rutan **Indata** i avsnittet **Översikt** i jobbfönstret Stream Analytics.

   ![Indataruta under Topologi i jobbfönstret Strömmande analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klicka på **Lägg till indata för stream** och välj **Händelsehubb**. Fyll sedan på den nya inmatningssidan med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias  |  CallStream   |  Ange ett namn för att identifiera jobbets indata.   |
   |Prenumeration   |  \<Din prenumeration\> |  Välj den Azure-prenumeration som har händelsehubben som du skapade.   |
   |Namnområde för händelsehubb  |  asa-eh-ns-demo |  Ange namnet på namnområdet Händelsehubben.   |
   |Namn på händelsehubb  | asa-eh-bedrägeridetection-demo | Välj namnet på händelsehubben.   |
   |Principnamn för Event Hub  | asa-policy-manage-demo | Välj den åtkomstprincip som du skapade tidigare.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klicka på **Skapa**.

## <a name="create-queries-to-transform-real-time-data"></a>Skapa frågor för att omvandla realtidsdata

Nu har du konfigurerat ett Stream Analytics-jobb för att läsa en inkommande dataström. Nästa steg är att skapa en fråga som analyserar data i realtid. Stream Analytics stöder en enkel, deklarativ frågemodell som beskriver omvandlingar för bearbetning i realtid. Frågorna använder ett SQL-liknande språk som har vissa tillägg som är specifika för Stream Analytics. 

En enkel fråga kan bara läsa alla inkommande data. Du skapar dock ofta frågor som söker efter specifika data eller relationer i data. I det här avsnittet av självstudien skapar och testar du flera frågor för att lära dig några sätt på vilka du kan omvandla en indataström för analys. 

De frågor du skapar här visar bara de transformerade data till skärmen. I ett senare avsnitt konfigurerar du en utdatamottagare och en fråga som skriver de transformerade data till diskhon.

Mer information om språket finns i Språkreferens för [Azure Stream Analytics-frågor](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Hämta exempeldata för testningsfrågor

TelcoGenerator-appen skickar samtalsposter till händelsehubben och ditt Stream Analytics-jobb är konfigurerat för att läsa från händelsehubben. Du kan använda en fråga för att testa jobbet för att se till att det läses korrekt. Om du vill testa en fråga i Azure-konsolen behöver du exempeldata. För den här genomgången extraherar du data från den dataström som kommer in i händelsehubben.

1. Kontrollera att TelcoGenerator-appen körs och producerar samtalsposter.
2. Gå tillbaka till jobbfönstret Strömma analytics i portalen. (Om du stängde fönstret `asa_frauddetection_job_demo` söker du efter i fönstret **Alla resurser.)**
3. Klicka på rutan **Fråga.** Azure listar de indata och utdata som är konfigurerade för jobbet och låter dig skapa en fråga som låter dig omvandla indataströmmen när den skickas till utdata.
4. Klicka på punkterna bredvid indata `CallStream` i **frågefönstret** och välj sedan **Exempeldata från indata**.

   ![Menyalternativ för att använda exempeldata för jobbinmatningen för Strömmande analytics, med "Exempeldata från indata" markerade](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Ställ in **minuter** till 3 och klicka sedan på **OK**. 
    
   ![Alternativ för provtagning av indataström med 3 minuter valda](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure-exempel på 3 minuters data från indataströmmen och meddelar dig när exempeldata är klara. (Detta tar en kort stund.) 

Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempeldata och du måste skapa en ny uppsättning exempeldata. 

Som ett alternativ kan du hämta en .json-fil som innehåller exempeldata [från GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)och sedan ladda `CallStream` upp den där Json-filen som ska användas som exempeldata för indata. 

### <a name="test-using-a-pass-through-query"></a>Testa med hjälp av en direktfråga

Om du vill arkivera alla händelser kan du använda en direktfråga för att läsa alla fält i nyttolasten för händelsen.

1. Ange den här frågan i frågefönstret:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Precis som med SQL är nyckelord inte skiftlägeskänsliga och blanktecken är inte signifikant.

    I den `CallStream` här frågan är det alias som du angav när du skapade indata. Om du använde ett annat alias använder du det namnet i stället.

2. Klicka på **Testa**.

    Stream Analytics-jobbet kör frågan mot exempeldata och visar utdata längst ned i fönstret. Resultaten visar att eventhubben och jobbet Strömmande analys är korrekt konfigurerade. (Som nämnts, senare ska du skapa en utdatamottagare som frågan kan skriva data till.)

   ![Stream Analytics-jobbutdata, med 73 skapade poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Det exakta antalet poster som visas beror på hur många poster som fångades i ditt 3-minutersurval.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Minska antalet fält med hjälp av en kolumnprojektion

I många fall behöver analysen inte alla kolumner från indataströmmen. Du kan använda en fråga för att projicera en mindre uppsättning returnerade fält än i direktfrågan.

1. Ändra frågan i kodredigeraren till följande:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Klicka på **Testa** igen. 

   ![Stream Analytics jobbutdata för projektion visar 25 poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Räkna inkommande samtal efter region: Tumlande fönster med aggregering

Anta att du vill räkna antalet inkommande samtal per region. När du vill utföra aggregerade funktioner som att räkna i strömmande data måste du segmentera strömmen till temporala enheter (eftersom själva dataströmmen i själva verket är oändlig). Det gör du med hjälp av [en streaminganalys-fönsterfunktion](stream-analytics-window-functions.md). Du kan sedan arbeta med data i fönstret som en enhet.

För den här omvandlingen vill du ha en sekvens av temporala fönster som inte överlappar varandra – varje fönster har en diskret uppsättning data som du kan gruppera och aggregera. Den här typen av fönster kallas ett *Tumbling-fönster*. I fönstret Tumbling kan du få ett antal inkommande `SwitchNum`samtal grupperade efter , vilket representerar det land/den region där anropet har sitt ursprung. 

1. Ändra frågan i kodredigeraren till följande:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Den här `Timestamp By` frågan använder `FROM` nyckelordet i satsen för att ange vilket tidsstämpelfält i indataströmmen som ska användas för att definiera fönstret Tumbling. I det här fallet delar fönstret upp data `CallRecTime` i segment efter fältet i varje post. (Om inget fält anges används den tid då varje händelse anländer till händelsehubben. Se "Ankomsttid kontra programtid" i [Referens för frågespråk för Strömanalys](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    Projektionen `System.Timestamp`innehåller , som returnerar en tidsstämpel för slutet av varje fönster. 

    Om du vill ange att du vill använda ett Tumbling-fönster `GROUP BY` använder du funktionen [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) i satsen. I funktionen anger du en tidsenhet (allt från en mikrosekt till en dag) och en fönsterstorlek (hur många enheter). I det här exemplet består Tumbling-fönstret av 5 sekunders intervall, så du får en räkning per land/region för var 5 sekunders samtal.

2. Klicka på **Testa** igen. I resultaten märker du att tidsstämplarna under **WindowEnd** är i steg om 5 sekunder.

   ![Stream Analytics jobbutdata för aggregering som visar 13 poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Identifiera SIM-bedrägerier med hjälp av en självkoppling

I det här exemplet bör du överväga att bedräglig användning är anrop som kommer från samma användare men på olika platser inom 5 sekunder från varandra. Samma användare kan till exempel inte legitimt ringa ett samtal från USA och Australien samtidigt. 

Om du vill söka efter dessa ärenden kan du använda en självkoppling av `CallRecTime` strömmande data för att ansluta strömmen till sig själv baserat på värdet. Du kan sedan söka efter `CallingIMSI` samtalsposter där värdet (ursprungsnumret) är detsamma, men `SwitchNum` värdet (ursprungsland/ursprungsregion) inte är detsamma.

När du använder en koppling med strömmande data måste kopplingen ge vissa gränser för hur långt de matchande raderna kan separeras i tid. (Som nämnts tidigare är strömmande data i själva verket oändliga.) Tidsgränsen för relationen anges inuti `ON` kopplingens sats `DATEDIFF` med hjälp av funktionen. I det här fallet baseras kopplingen på ett 5-sekunders intervall med samtalsdata.

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

    Den här frågan är som `DATEDIFF` vilken SQL-koppling som helst förutom funktionen i kopplingen. Den här `DATEDIFF` versionen av är specifik för Streaming `ON...BETWEEN` Analytics och den måste visas i satsen. Parametrarna är en tidsenhet (sekunder i det här exemplet) och aliasen för de två källorna för kopplingen. Detta skiljer sig från `DATEDIFF` den vanliga SQL-funktionen.

    Satsen `WHERE` innehåller villkoret att flagga det bedrägliga samtalet: de ursprungliga växlarna är inte desamma. 

2. Klicka på **Testa** igen. 

   ![Stream Analytics-jobbutdata för självkoppling, med 6 skapade poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klicka på **Spara** om du vill spara självanslutningsfrågan som en del av jobbet Stream Analytics. (Exempeldata sparas inte.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Skapa en utdatamottagare för att lagra transformerade data

Du har definierat en händelseström, en händelsehubbinmatning till inmatning av händelser och en fråga för att utföra en omvandling över strömmen. Det sista steget är att definiera en utdatamottagare för jobbet, det vill ha en plats att skriva den transformerade strömmen till. 

Du kan använda många resurser som utdatamottagare – en SQL Server-databas, tabelllagring, Datasjölagring, Power BI och till och med en annan händelsehubb. För den här självstudien skriver du strömmen till Azure Blob Storage, vilket är ett typiskt val för att samla in händelseinformation för senare analys, eftersom den rymmer ostrukturerade data.

Om du har ett befintligt blob storage-konto kan du använda det. För den här självstudien får du lära dig hur du skapar ett nytt lagringskonto.

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob Storage-konto

1. Välj Skapa ett > **resurslagringslagringskonto** > **Storage account** **Create a resource**i det övre vänstra hörnet i Azure-portalen . Fyll i jobbsidan för lagringskonto med **Namn** inställt på "asaehstorage", **Plats** inställd på "Östra USA", **Resursgrupp** inställd på "asa-eh-ns-rg" (värd för lagringskontot i samma resursgrupp som streamingjobbet för ökad prestanda). Återstående inställningar kan ha kvar standardvärdena.  

   ![Skapa lagringskonto i Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Gå tillbaka till jobbfönstret Strömma analytics i Azure-portalen. (Om du stängde fönstret `asa_frauddetection_job_demo` söker du efter i fönstret **Alla resurser.)**

3. Klicka på rutan **Utdata** i avsnittet **Jobbtopologi.**

4. Klicka på **Lägg till** i fönstret **Utdata** och välj **Blob-lagring**. Fyll sedan i sidan Ny utdata med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Utdataalias  |  CallStream-bedrägliga samtal   |  Ange ett namn för att identifiera projektets utdata.   |
   |Prenumeration   |  \<Din prenumeration\> |  Välj den Azure-prenumeration där det lagringskonto som du skapade finns. Lagringskontot kan vara i samma eller en annan prenumeration. I det här exemplet förutsätts att du har skapat lagringskontot i samma prenumeration. |
   |Lagringskonto  |  asaehstorage (asaehstorage) |  Ange namnet på det lagringskonto som du skapade. |
   |Container  | asa-fraudulentcalls-demo | Välj Skapa nytt och ange ett behållarnamn. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klicka på **Spara**. 


## <a name="start-the-streaming-analytics-job"></a>Starta streaminganalysjobbet

Jobbet är nu konfigurerat. Du har angett en indata (händelsehubben), en omvandling (frågan för att söka efter bedrägliga anrop) och en utdata (blob-lagring). Nu kan du börja jobbet. 

1. Kontrollera att TelcoGenerator-appen körs.

2. Klicka på **Start**i jobbfönstret . Välj **Nu**för starttid för jobbutdata i fönstret **Startjobb.** 

   ![Starta Stream Analytics-jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Undersök de transformerade data

Du har nu ett komplett Streaming Analytics-jobb. Jobbet undersöker en ström av telefonsamtal metadata, letar efter bedrägliga telefonsamtal i realtid, och skriva information om dessa bedrägliga samtal till lagring. 

För att slutföra den här självstudien kanske du vill titta på de data som samlas in av streaming analytics-jobbet. Data skrivs till Azure Blog Storage i segment (filer). Du kan använda alla verktyg som läser Azure Blob Storage. Som anges i avsnittet Förutsättningar kan du använda Azure-tillägg i Visual Studio eller använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

När du undersöker innehållet i en fil i blob-lagring visas ungefär så här:

   ![Azure blob-lagring med Streaming Analytics-utdata](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Rensa resurser

Det finns ytterligare artiklar som fortsätter med scenariot för bedrägeriidentifiering och använder de resurser som du har skapat i den här självstudien. Om du vill fortsätta läser du förslagen under **Nästa steg**.

Men om du är klar och du inte behöver de resurser du har skapat kan du ta bort dem så att du inte ådrar dig onödiga Azure-avgifter. I så fall föreslår vi att du gör följande:

1. Stoppa streaminganalysjobbet. Klicka på **Stoppa** högst upp i fönstret **Jobb.**
2. Stoppa Telco Generator-appen. Tryck på Ctrl+C i kommandofönstret där du startade appen.
3. Om du har skapat ett nytt blob storage-konto bara för den här självstudien tar du bort det. 
4. Ta bort jobbet Strömmande analytics.
5. Ta bort händelsehubben.
6. Ta bort namnområdet för händelsehubben.

## <a name="get-support"></a>Få support

Om du vill ha mer hjälp kan du prova [Azure Stream Analytics-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta den här självstudien med följande artikel:

* [Stream Analytics och Power BI: En instrumentpanel för analys](stream-analytics-power-bi-dashboard.md)i realtid för strömmande data . Den här artikeln visar hur du skickar TelCo-utdata för Stream Analytics-jobbet till Power BI för visualisering och analys i realtid.

Mer information om Stream Analytics i allmänhet finns i följande artiklar:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
