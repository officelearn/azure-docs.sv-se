---
title: Identifiering av bedrägerier i realtid med hjälp av Azure Stream Analytics
description: Lär dig hur du skapar en lösning för bedrägeri identifiering i real tid med Stream Analytics. Använd en Event Hub för händelse bearbetning i real tid.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ba216e41672e1d19e552b3f82a2ea65da7d3a435
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007094"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Kom igång med Azure Stream Analytics: identifiering av bedrägerier i real tid

I den här självstudien får du en bild från slut punkt till slut punkt för hur du använder Azure Stream Analytics. Lär dig att: 

* Ta strömnings händelser till en instans av Azure Event Hubs. I den här självstudien använder du en app som simulerar en ström med metadata-poster för mobil telefoner.

* Skriv SQL-liknande Stream Analytics frågor för att transformera data, aggregera information eller leta efter mönster. Du kommer att se hur du använder en fråga för att undersöka inkommande data ström och letar efter anrop som kan vara bedrägliga.

* Skicka resultaten till en utgående Sink (Storage) som du kan analysera för ytterligare insikter. I det här fallet skickar du de misstänkta anrops data till Azure Blob Storage.

I den här självstudien används exemplet på identifiering av bedrägerier i real tid baserat på telefon anrops data. Tekniken som illustreras passar också för andra typer av bedrägeri identifiering, till exempel kredit korts bedrägerier eller identitets stöld. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: telekommunikation och SIM-bedrägeri upptäckt i real tid

Ett tele bolag har en stor mängd data för inkommande samtal. Företaget vill kunna identifiera bedrägliga samtal i real tid så att de kan meddela kunder eller stänga av tjänsten för ett särskilt nummer. En typ av SIM-bedrägeri omfattar flera anrop från samma identitet ungefär samma tid, men i geografiskt olika platser. För att identifiera den här typen av bedrägerier måste företaget undersöka inkommande telefon poster och leta efter vissa mönster, i det här fallet för samtal som görs runt samma tid i olika länder/regioner. Alla telefon poster som tillhör den här kategorin skrivs till lagring för efterföljande analys.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien ska du simulera telefon samtals data med hjälp av en klient app som genererar metadata för telefonsamtal. Några av de poster som appen skapar ser ut som bedrägliga samtal. 

Se till att du har följande innan du börjar:

* Ett Azure-konto.
* Appen anropa-Event generator, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), som kan laddas ned från Microsoft Download Center. Zippa upp det här paketet till en mapp på datorn. Om du vill se käll koden och köra appen i en fel sökare kan du hämta käll koden för appen från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows kan blockera den hämtade ZIP-filen. Om du inte kan zippa upp den högerklickar du på filen och väljer **Egenskaper**. Om du ser meddelandet "den här filen kom från en annan dator och kan blockeras för att skydda den här datorn" väljer du alternativet **Häv blockering** och klickar sedan på **Använd**.

Om du vill undersöka resultatet av streaming Analytics-jobbet behöver du också ett verktyg för att visa innehållet i en Azure Blob Storage-behållare. Om du använder Visual Studio kan du använda [Azure Tools för Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) eller [Visual Studio Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Du kan också installera fristående verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Skapa ett Azure-Event Hubs för att mata in händelser

Du kan analysera en data ström genom *att mata in den i* Azure. Ett vanligt sätt att mata in data är att använda [Azure Event Hubs](../event-hubs/event-hubs-about.md), vilket gör att du kan mata in miljon tals händelser per sekund och sedan bearbeta och lagra händelse informationen. I den här självstudien skapar du en Event Hub och använder sedan appen anropa-Event generator för att skicka anrops data till den händelsehubben.

>[!NOTE]
>En mer detaljerad version av den här proceduren finns i [skapa ett Event Hubs-namnområde och en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Skapa ett namn område och händelsehubben
I den här proceduren skapar du först ett namn område för Event Hub och lägger sedan till en händelsehubben till det namn området. Namn områden för Event Hub används för att logiskt gruppera relaterade händelse buss instanser. 

1. Logga in på Azure Portal och klicka på **skapa en resurs** längst upp till vänster på skärmen.

2. Välj **Alla tjänster** på den vänstra menyn och välj **stjärnan (`*`)** bredvid **Event Hubs** i kategorin **Analytics**. Bekräfta att **Event Hubs** läggs till i **FAVORITER** på den vänstra navigeringsmenyn. 

   ![Söka efter Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Välj **Event Hubs** under **FAVORITER** på den vänstra navigeringsmenyn och välj **Lägg till** i verktygsfältet.

   ![Knappen Lägg till](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. I fönstret **skapa namn område** anger du ett namn på namn området, till exempel `<yourname>-eh-ns-demo` . Du kan använda namn områdets namn, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
5. Välj en prenumeration och skapa eller Välj en resurs grupp och klicka sedan på **skapa**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. När namn rymden har distribuerats hittar du namn området för händelsehubben i listan med Azure-resurser. 

7. Klicka på det nya namn området och klicka på **händelsehubben** i rutan namn område.

   ![Knappen Lägg till Händelsehubben för att skapa en ny händelsehubben](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Namnge den nya händelsehubben `asa-eh-frauddetection-demo` . Du kan använda ett annat namn. Om du gör det, gör du en anteckning om det, eftersom du behöver namnet senare. Du behöver inte ange andra alternativ för händelsehubben just nu.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Klicka på **Skapa**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan en process kan skicka data till en Event Hub måste händelsehubben ha en princip som tillåter lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1. I rutan händelse namn område klickar du på **Event Hubs** och klickar sedan på namnet på din nya händelsehubben.

2. I fönstret Event Hub klickar du på **principer för delad åtkomst** och sedan på **+ &nbsp; Lägg till**.

    > [!NOTE]
    > Se till att du arbetar med händelsehubben, inte Event Hub-namnområdet.

3. Lägg till en princip med namnet `asa-policy-manage-demo` och för **anspråk** väljer du **Hantera**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Klicka på **Skapa**.

5. När principen har distribuerats klickar du på den i listan över principer för delad åtkomst.

6. Leta upp rutan med etiketten **ANSLUTNINGS sträng – primär nyckel** och klicka på knappen Kopiera bredvid anslutnings strängen. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutnings strängen för nästa avsnitt när du har gjort små ändringar i den.

    Anslutnings strängen ser ut så här:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Observera att anslutnings strängen innehåller flera nyckel/värde-par, separerade med semikolon: `Endpoint` , `SharedAccessKeyName` , `SharedAccessKey` , och `EntityPath` .  


## <a name="configure-and-start-the-event-generator-application"></a>Konfigurera och starta Event Generator-programmet

Innan du startar TelcoGenerator-appen måste du konfigurera den så att den skickar anrops poster till händelsehubben som du skapade.

### <a name="configure-the-telcogenerator-app"></a>Konfigurera TelcoGenerator-appen

1. Anteckna värdet i redigeraren där du kopierade anslutnings strängen `EntityPath` och ta sedan bort `EntityPath` paret (Glöm inte att ta bort det semikolon som föregår det). 

2. I mappen där du zippade TelcoGenerator.zip-filen öppnar du telcodatagen.exe.config filen i ett redigerings program. (Det finns mer än en. config-fil så se till att du öppnar rätt.)

3. I `<appSettings>` elementet:

   * Ange värdet för `EventHubName` nyckeln till Event Hub-namnet (det vill säga till värdet för enhetens sökväg).
   * Ange värdet för `Microsoft.ServiceBus.ConnectionString` nyckeln till anslutnings strängen. 

   `<appSettings>`Avsnittet kommer att se ut som i följande exempel:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Spara filen. 

### <a name="start-the-app"></a>Starta appen

1. Öppna ett kommando fönster och ändra till mappen där TelcoGenerator-appen är zippad.

2. Ange följande kommando:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   Parametrarna är: 

   * Antal CDRs per timme. 
   * Indelnings sannolikhet för SIM-kort: hur ofta, i procent av alla anrop, som appen ska simulera ett bedrägligt anrop. Värdet 0.2 betyder att cirka 20 % av anropsposterna ser bedrägliga ut.
   * Varaktighet i timmar. Antalet timmar som appen ska köras. Du kan också stoppa appen när som helst genom att trycka på CTRL + C på kommando raden.

   Efter några sekunder börjar appen visa telefonsamtalsposter på skärmen och skickar dem till en händelsehubb.

Några av de nyckel fält som du kommer att använda i det här programmet för bedrägeri identifiering i real tid är följande:

|**Post**|**Definition**|
|----------|--------------|
|`CallrecTime`|Tidsstämpeln för samtalets starttid. |
|`SwitchNum`|Telefonväxeln används för att ansluta samtalet. I det här exemplet är växlarna strängar som representerar ursprungslandet/regionen (USA, Kina, Storbritannien, Tyskland eller Australien). |
|`CallingNum`|Uppringarens telefonnummer. |
|`CallingIMSI`|International Mobile Subscriber Identity (IMSI). Detta är den unika identifieraren för anroparen. |
|`CalledNum`|Telefonnumret till mottagaren. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Detta är den unika identifieraren för samtals mottagaren. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Skapa ett Stream Analytics jobb för att hantera strömmande data

Nu när du har en data ström med samtals händelser kan du konfigurera ett Stream Analytics jobb. Jobbet kommer att läsa data från den händelsehubben som du ställer in. 

### <a name="create-the-job"></a>Skapa jobbet 

1. Klicka på **skapa en resurs**  >  **Sakernas Internet**  >  **Stream Analytics jobb** i Azure Portal.

2. Ge jobbet `asa_frauddetection_job_demo` ett namn, ange en prenumeration, en resurs grupp och en plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betalar för att överföra data mellan regioner.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klicka på **Skapa**.

    Jobbet skapas och portalen visar jobb information. Inget körs ännu, men du måste konfigurera jobbet innan det kan startas.

### <a name="configure-job-input"></a>Konfigurera jobbindata

1. På instrument panelen eller fönstret **alla resurser** letar du reda på och väljer `asa_frauddetection_job_demo` Stream Analytics jobb. 
2. I avsnittet **Översikt** i fönstret Stream Analytics jobb klickar du på rutan **indatamängd** .

   ![Indata-ruta under topologi i fönstret strömmande analys jobb](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klicka på **Lägg till Stream-indata** och välj **Event Hub**. Fyll sedan i den nya indata-sidan med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias  |  CallStream   |  Ange ett namn för att identifiera jobbets indatatyper.   |
   |Prenumeration   |  \<Your subscription\> |  Välj den Azure-prenumeration som har händelsehubben som du skapade.   |
   |Namnområde för händelsehubb  |  ASA-händelsehubbnamnområde-ns-demo |  Ange namnet på Event Hub-namnområdet.   |
   |Namn på händelsehubb  | ASA-händelsehubbnamnområde-frauddetection-demo | Välj namnet på Händelsehubben.   |
   |Principnamn för Event Hub  | ASA-policy-hantera-demo | Välj den åtkomst princip som du skapade tidigare.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klicka på **Skapa**.

## <a name="create-queries-to-transform-real-time-data"></a>Skapa frågor för att transformera real tids data

Nu har du ett Stream Analytics jobb konfigurerat för att läsa en inkommande data ström. Nästa steg är att skapa en fråga som analyserar data i real tid. Stream Analytics stöder en enkel, deklarativ frågeplan som beskriver transformationer för bearbetning i real tid. Frågorna använder ett SQL-liknande språk som har vissa tillägg som är specifika för Stream Analytics. 

En enkel fråga kan bara läsa alla inkommande data. Du skapar dock ofta frågor som söker efter vissa data eller för relationer i data. I det här avsnittet av självstudien skapar du och testar flera frågor för att lära dig några sätt som du kan omvandla en indata ström till för analys. 

Frågorna som du skapar här visar bara omvandlade data till skärmen. I ett senare avsnitt konfigurerar du en utgående mottagare och en fråga som skriver transformerade data till denna mottagare.

Mer information om språket finns i [språk referens för Azure Stream Analytics frågor](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Hämta exempel data för att testa frågor

TelcoGenerator-appen skickar anrops poster till händelsehubben och ditt Stream Analytics-jobb har kon figurer ATS för att läsa från händelsehubben. Du kan använda en fråga för att testa jobbet för att kontrol lera att det läses på rätt sätt. Om du vill testa en fråga i Azure-konsolen behöver du exempel data. I den här genom gången extraherar du exempel data från den data ström som kommer till händelsehubben.

1. Kontrol lera att TelcoGenerator-appen körs och genererar anrops poster.
2. Gå tillbaka till fönstret strömmande analys jobb i portalen. (Om du stängde fönstret söker du efter `asa_frauddetection_job_demo` i fönstret **alla resurser** .)
3. Klicka i rutan **fråga** . Azure visar de indata och utdata som har kon figurer ATS för jobbet och gör att du kan skapa en fråga som gör att du kan omvandla indataströmmen när den skickas till utdata.
4. I rutan **fråga** klickar du på punkterna bredvid `CallStream` indata och väljer sedan **exempel data från indata**.

   ![Meny alternativ för att använda exempel data för jobb posten streaming Analytics med "exempel data från indata" valt](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Ange **minuter** till 3 och klicka sedan på **OK**. 
    
   ![Alternativ för att sampla indata strömmar med 3 minuter valt](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure-exempel 3 minuter är värda data från indataströmmen och meddelar dig när exempel data är klara. (Det här tar en kort stund.) 

Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempeldata och du måste skapa en ny uppsättning exempeldata. 

Alternativt kan du hämta en. JSON-fil som innehåller exempel data i den [från GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)och sedan ladda upp den. JSON-fil som ska användas som exempel data för `CallStream` indata. 

### <a name="test-using-a-pass-through-query"></a>Testa med en direkt fråga

Om du vill arkivera varje händelse kan du använda en direkt fråga för att läsa alla fält i händelsens nytto Last.

1. I frågefönstret anger du den här frågan:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Precis som med SQL är nyckelord inte Skift läges känsliga och blank steg är inte signifikant.

    I den här frågan `CallStream` är det alias som du angav när du skapade indatamängden. Om du har använt ett annat alias använder du det namnet i stället.

2. Klicka på **testa**.

    Stream Analytics jobbet kör frågan mot exempel data och visar utdata längst ned i fönstret. Resultatet visar att Händelsehubben och streaming Analytics-jobbet har kon figurer ATS korrekt. (Som du ser ovan skapar du en utgående mottagare som frågan kan skriva data till.)

   ![Stream Analytics jobbets utdata, med 73 poster genererade](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Det exakta antalet poster som visas beror på hur många poster som samlats in i ditt tre minuter-exempel.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Minska antalet fält med en kolumn projektion

I många fall behöver inte analysen alla kolumner från indataströmmen. Du kan använda en fråga för att projicera en mindre uppsättning av returnerade fält än i direkt frågan.

1. Ändra frågan i kod redigeraren till följande:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Klicka på **testa** igen. 

   ![Stream Analytics jobb utmatningen för projektion visar 25 poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Räkna inkommande samtal per region: rullande-fönster med agg regering

Anta att du vill räkna antalet inkommande samtal per region. När du vill utföra mängd funktioner som inventering i strömmande data måste du segmentera strömmen till temporala enheter (eftersom själva data strömmen är i praktiken oändligt). Du gör detta med hjälp av en [funktion](stream-analytics-window-functions.md)för strömnings analys fönster. Sedan kan du arbeta med data i det fönstret som en enhet.

För den här omvandlingen vill du ha en sekvens av temporala fönster som inte överlappar – varje fönster har en diskret uppsättning data som du kan gruppera och aggregera. Den här typen av fönster kallas ett rullande- *fönster*. I fönstret rullande kan du få ett antal inkommande anrop grupperade efter `SwitchNum` , som representerar landet/regionen där anropet kommer. 

1. Ändra frågan i kod redigeraren till följande:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Den här frågan använder `Timestamp By` nyckelordet i `FROM` satsen för att ange vilket tidsstämpelfält i den indataströmmen som ska användas för att definiera rullande-fönstret. I det här fallet delar fönstret data i segment efter `CallRecTime` fältet i varje post. (Om inget fält har angetts används den tidpunkt då varje händelse kommer till händelsehubben. Se "ankomst tid vs Application Time" i [Stream Analytics språk referens för frågor](/stream-analytics-query/stream-analytics-query-language-reference). 

    Projektionen innehåller `System.Timestamp` , som returnerar en tidsstämpel för slutet av varje fönster. 

    Om du vill ange att du vill använda ett rullande-fönster använder du funktionen [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) i- `GROUP BY` satsen. I funktionen anger du en tidsenhet (var som helst från en mikrosekund till en dag) och en fönster storlek (hur många enheter). I det här exemplet består fönstret rullande av 5-sekunders intervall, så du får ett antal per land/region för var 5: e sekunds värt samtal.

2. Klicka på **testa** igen. I resultatet ser du att tidsstämplar under **WindowEnd** är i steg om 5 sekunder.

   ![Stream Analytics jobbets utdata för agg regering som visar 13 poster](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Identifiera SIM-bedrägerier med en själv koppling

I det här exemplet bör du tänka på bedräglig användning för att vara anrop som kommer från samma användare men på olika platser inom 5 sekunder från varandra. Samma användare kan till exempel inte legitimt ringa ett samtal från USA och Australien samtidigt. 

Om du vill söka efter dessa fall kan du använda en själv koppling av strömmande data för att ansluta data strömmen till sig själv baserat på `CallRecTime` värdet. Sedan kan du söka efter anrops poster där `CallingIMSI` värdet (det ursprungliga numret) är detsamma, men `SwitchNum` värdet (ursprungsland/region) är inte samma.

När du använder en anslutning med strömmande data måste du ange vissa gränser för hur långt de matchande raderna kan vara åtskilda i tiden. (Som tidigare nämnts är strömmande data i praktiken oändliga.) Tids gränserna för relationen anges i `ON` kopplings instruktionen med hjälp av `DATEDIFF` funktionen. I det här fallet baseras kopplingen på ett 5 sekunders intervall anrops data.

1. Ändra frågan i kod redigeraren till följande: 

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

    Den här frågan liknar alla SQL-kopplingar förutom `DATEDIFF` funktionen i kopplingen. Den här versionen av `DATEDIFF` är bara för strömnings analys och måste visas i- `ON...BETWEEN` satsen. Parametrarna är en tidsenhet (sekunder i det här exemplet) och alias för de två källorna för kopplingen. Detta skiljer sig från standard-SQL- `DATEDIFF` funktionen.

    `WHERE`Satsen innehåller villkoret som flaggar det bedrägliga anropet: de ursprungliga växlarna är inte desamma. 

2. Klicka på **testa** igen. 

   ![Stream Analytics jobbets utdata för själv koppling, med 6 poster som skapats](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klicka på **Spara** för att spara självkopplings frågan som en del av streaming Analytics-jobbet. (Vi sparar inte exempel data.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Skapa en utgående mottagare för att lagra transformerade data

Du har definierat en händelse ström, en Event Hub-indata för inmatnings händelser och en fråga för att utföra en omvandling över strömmen. Det sista steget är att definiera en utgående mottagare för jobbet – det vill säga en plats där den transformerade strömmen skrivs till. 

Du kan använda många resurser som utgående mottagare – en SQL Server databas, Table Storage, Data Lake lagring, Power BI och till och med en annan händelsehubben. I den här självstudien ska du skriva data strömmen till Azure Blob Storage, som är ett typiskt val för insamling av händelse information för senare analys, eftersom den hanterar ostrukturerade data.

Om du har ett befintligt Blob Storage-konto kan du använda det. I den här självstudien får du lära dig hur du skapar ett nytt lagrings konto.

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob Storage-konto

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**. Fyll i sidan lagrings konto jobb med **namnet** "asaehstorage", **platsen** har angetts till "östra US", **resurs grupp** inställd på "ASA-händelsehubbnamnområde-ns-RG" (värd lagrings kontot i samma resurs grupp som streaming-jobbet för bättre prestanda). Återstående inställningar kan ha kvar standardvärdena.  

   ![Skapa ett lagrings konto i Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Gå tillbaka till fönstret strömmande analys jobb i Azure Portal. (Om du stängde fönstret söker du efter `asa_frauddetection_job_demo` i fönstret **alla resurser** .)

3. Klicka på rutan **utdata** i avsnittet **jobb sto pol Ogin** .

4. I fönstret **utdata** klickar du på **Lägg till** och väljer **Blob Storage**. Fyll sedan i den nya utmatnings sidan med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Utdataalias  |  CallStream-FraudulentCalls   |  Ange ett namn för att identifiera jobbets utdata.   |
   |Prenumeration   |  \<Your subscription\> |  Välj den Azure-prenumeration där det lagringskonto som du skapade finns. Lagringskontot kan vara i samma eller en annan prenumeration. I det här exemplet förutsätts att du har skapat lagringskontot i samma prenumeration. |
   |Lagringskonto  |  asaehstorage |  Ange namnet på det lagrings konto som du har skapat. |
   |Container  | ASA-fraudulentcalls-demo | Välj Skapa nytt och ange ett behållar namn. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klicka på **Spara**. 


## <a name="start-the-streaming-analytics-job"></a>Starta Stream Analytics-jobbet

Jobbet har nu kon figurer ATS. Du har angett en indata (händelsehubben), en omvandling (frågan för att leta efter bedrägliga anrop) och utdata (Blob Storage). Nu kan du starta jobbet. 

1. Kontrol lera att TelcoGenerator-appen körs.

2. I fönstret jobb klickar du på **Start**. I fönstret **starta jobb** , för start tid för jobb, väljer du **nu**. 

   ![Starta Stream Analytics jobbet](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Granska transformerade data

Nu har du ett komplett strömnings analys jobb. Jobbet undersöker en ström med metadata för telefonsamtal, letar efter bedrägliga telefonsamtal i real tid och skriver information om de bedrägliga anropen till Storage. 

För att slutföra den här självstudien kanske du vill titta på de data som samlas in av streaming Analytics-jobbet. Data skrivs till Azure blogg lagring i segment (filer). Du kan använda valfritt verktyg som läser Azure-Blob Storage. Som anges i avsnittet krav kan du använda Azure-tillägg i Visual Studio, eller så kan du använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/) eller [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

När du undersöker innehållet i en fil i Blob Storage ser du något som liknar följande:

   ![Azure Blob Storage med streaming Analytics-utdata](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Rensa resurser

Det finns ytterligare artiklar om att fortsätta med bedrägeri identifierings scenariot och använda de resurser som du har skapat i den här självstudien. Om du vill fortsätta kan du se förslagen under **Nästa steg**.

Men om du är klar och inte behöver de resurser som du har skapat kan du ta bort dem så att du inte debiteras onödiga Azure-avgifter. I så fall rekommenderar vi att du gör följande:

1. Stoppa Stream Analytics-jobbet. I fönstret **jobb** klickar du på **stoppa** överst.
2. Stoppa Telco Generator-appen. Tryck på CTRL + C i kommando fönstret där du startade appen.
3. Om du har skapat ett nytt Blob Storage-konto för den här självstudien tar du bort det. 
4. Ta bort Stream Analytics-jobbet.
5. Ta bort händelsehubben.
6. Ta bort Event Hub-namnområdet.

## <a name="get-support"></a>Få support

Om du behöver ytterligare hjälp kan du testa [sidan Microsoft Q&en fråga för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta med den här självstudien med följande artikel:

* [Stream Analytics och Power BI: en real tids analys instrument panel för strömmande data](stream-analytics-power-bi-dashboard.md). Den här artikeln visar hur du skickar TelCo-utdata för Stream Analytics jobb till Power BI för visualisering och analys i real tid.

Mer information om Stream Analytics i allmänhet finns i följande artiklar:

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)