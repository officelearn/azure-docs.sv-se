---
title: 'Självstudie: Skapa och hantera ett Stream Analytics-jobb med Azure-portalen | Microsoft Docs'
description: I den här självstudien finns en omfattande illustration av hur du använder Azure Stream Analytics för att analysera bedrägliga samtal i en telefonsamtalsström.
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 6b924e0555ea7a57f8d5e5309a266b6d2fb44f44
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702535"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Skapa ett Stream Analytics-jobb för att analysera telefonsamtalsdata och visualisera resultat i en Power BI-instrumentpanel
 
I den här självstudien visas hur du använder Azure Stream Analytics för att analysera ett exempeltelefonsamtal som genereras av ett klientprogram. Telefonsamtalsdata som genereras av klientprogrammet innehåller några bedrägliga samtal och vi kommer att definiera ett Stream Analytics-jobb för att filtrera sådana samtal.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Generera exempel på telefonsamtalsdata och skicka dem till Azure Event Hubs  
> * Skapa ett Stream Analytics-jobb   
> * Konfigurera indata och utdata för jobbet  
> * Definiera en fråga för att filtrera bedrägliga samtal  
> * Testa och starta jobbet  
> * Visualisera resultat i Power BI 

## <a name="prerequisites"></a>Nödvändiga komponenter

Se till att du har följande innan du börjar:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).  
* Logga in på [Azure-portalen](https://portal.azure.com/).  
* Ladda ned telefonsamtalets händelsegeneratorapp [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) från Microsoft Download Center eller hämta källkoden från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb 

Innan Stream Analytics kan analysera dataströmmen med bedrägliga samtal ska du skicka data till Azure. I den här självstudien skickar du data till Azure med  [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs). För den här självstudien skapar du en händelsehubb och gör att händelsegeneratorappen skickar samtalsdata till den händelsehubben. Kör följande steg för att skapa en händelsehubb:

1. Logga in på Azure Portal.  
2. Välj **Skapa en resurs** > **Sakernas Internet** > **Event Hubs**.  

   ![Sök händelsehubb](media/stream-analytics-manage-job/find-eh.png)
3. Fyll i fönstret **Skapa namnområde** med följande värden:  

   |**Inställning**  |**Föreslaget värde** |**Beskrivning**  |
   |---------|---------|---------|
   |Namn     | myEventHubNS        |  Ett unikt namn för att identifiera händelsehubbens namnområde.       |
   |Prenumeration     |   \<Din prenumeration\>      |   Välj en Azure-prenumeration där du vill skapa händelsehubben.      |
   |Resursgrupp     |   MyASADemoRG      |  Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto.       |
   |Plats     |   USA, västra 2      |    Plats där händelsehubbens namnområde kan distribueras.     |

4. Använd standardalternativ för de återstående inställningarna och välj **Skapa**.  

   ![Skapa namnområde för en händelsehubb](media/stream-analytics-manage-job/create-ehns.png)

5. När namnområdet har distribuerats klart går du till **Alla resurser** > sök upp ”myEventHubNS” i listan över Azure-resurser > välj för att öppna den.  
6. Sedan väljer du **+Event Hub** > **Namn** händelsehubben "MyEventHub". Du kan använda ett annat namn. Använd standardalternativen för återstående inställningar, välj **Skapa** och vänta på att distributionen genomförs.

   ![Skapa händelsehubb](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan ett program kan skicka data till Azure Event Hubs måste händelsen ha en princip som tillåter lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1. Gå till **Event Hubs** som du skapade i föregående steg som är "MyEventHub" > välj **Policyer för delad åtkomst** från händelsehubbfönstret > välj **+Lägg till**.  
2. Ställ in principnamnet på **Mypolicy** > och välj **Hantera** > välj **Skapa**.  

   ![Skapa händelsehubb med delade åtkomstprinciper](media/stream-analytics-manage-job/create-ehpolicy.png)

3. När principen har distribuerats väljer du att öppna den, letar upp **Connection string–primary-nyckeln** och väljer att **kopiera** nästa till anslutningssträngen.  
4. Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen i nästa avsnitt.  

   Anslutningssträngen ser ut så här:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Lägg märke till att anslutningssträngen innehåller flera nyckel/värde-par som är separerade med semikolon: Endpoint, SharedAccessKeyName, SharedAccessKey och EntityPath.  

5. Ta bort paret EntityPath från anslutningssträngen (glöm inte att ta bort semikolonet som föregår det).

## <a name="start-the-event-generator-application"></a>Starta händelsegeneratorprogrammet

Innan du startar appen TelcoGenerator ska du konfigurera den så att den skickar data till de Azure Event Hubs du skapade tidigare.

1. Extrahera innehållet i filen [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Öppna filen `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` i en valfri textredigerare (Det finns mer än en .config-fil, så se till att öppna rätt fil.)  

3. Uppdatera elementet <appSettings> i config-filen med följande information:

   * Ställ in värdet för nyckeln EventHubName till värdet för EntityPath i anslutningssträngen.  
   * Ställ in värdet för nyckeln Microsoft.ServiceBus.ConnectionString till anslutningssträngen utan värdet EntityPath som är värdet du fick från steg 5 i föregående avsnitt.

4. Spara filen.  
5. Öppna sedan ett kommandofönster, ändra till mappen där du har packat upp programmet TelcoGenerator och ange följande kommando:

   ```
   telcodatagen.exe 1000 .2 2
   ```

   Kommandot stöder följande parametrar:
   * **Antal anropsdataposter per timme**.  
   * **Procentandel för sannolikhet för bedrägeri** – hur ofta appen ska simulera ett bedrägligt samtal. Värdet .2 betyder att cirka 20 % av anropsposterna ser bedrägliga ut.  
   * **Längd i timmar** – antalet timmar som appen ska köras. Du kan också stoppa appen när som helst genom att avsluta processen (Ctrl+C) på kommandoraden.

   Efter några sekunder börjar appen visa telefonsamtalsposter på skärmen och skickar dem till en händelsehubb. Telefonsamtalsdata innehåller följande fält:

   |**Spela in**  |**Definition**  |
   |---------|---------|
   |CallrecTime    |  Tidsstämpeln för samtalets starttid.       |
   |SwitchNum     |  Telefonväxeln används för att ansluta samtalet. För det här exemplet är växlarna strängar som representerar ursprungslandet (USA, Kina, Storbritannien, Tyskland eller Australien).       |
   |CallingNum     |  Uppringarens telefonnummer.       |
   |CallingIMSI     |  International Mobile Subscriber Identity (IMSI). Det är en unik identifierare för uppringaren.       |
   |CalledNum     |   Telefonnumret till mottagaren.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Det är en unik identifierare för mottagaren.       |

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb 

Nu nr du har en ström av anropshändelser kan du skapa ett Stream Analytics-jobb som läser data från händelsehubben.

1. Du skapar du ett Stream Analytics-jobb genom att gå till Azure Portal  

2. Välj **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.  

3. Fyll i bladet **Nytt Stream Analytics-jobb** med följande värden:  

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Jobbnamn     |  ASATutorial       |   Ett unikt namn för att identifiera händelsehubbens namnområde.      |
   |Prenumeration    |  \<Din prenumeration\>   |   Välj en Azure-prenumeration där du vill skapa jobbet.       |
   |Resursgrupp   |   MyASADemoRG      |   Välj **Använd befintlig** och ange ett nytt resursgruppsnamn för ditt konto.      |
   |Plats   |    USA, västra 2     |      Plats där jobbet kan distribueras. Vi rekommenderar att du placerar jobbet och händelsehubben i samma region för bästa prestanda och så att du inte ska betala för att överföra data mellan regioner.      |
   |Värdmiljö    | Molnet        |     Stream Analytics-jobb kan distribueras till molnet eller edge. Med molnet kan du distribuera till Azure Cloud, och med Edge kan du distribuera till en IoT edge-enhet.    |
   |Strömningsenheter     |    1       |      Strömningsenheter representerar de bearbetningsresurser som krävs för att köra ett jobb. Standardvärdet är inställt på 1. Mer information om skalning av strömningsenheter finns i artikeln om att [förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md).      |

   ![Skapa ett jobb](media/stream-analytics-manage-job/create-a-job.png)   

4. Använd standardalternativen för återstående inställningar, välj **Skapa** och vänta på att distributionen genomförs.

## <a name="configure-job-input"></a>Konfigurera jobbindata

Nästa steg är att definiera en indatakälla för jobbet för att läsa data. För den här självstudien använder du händelsehubben du skapade i föregående avsnitt som indata. Kör följande steg för att konfigurera indata för ditt jobb:

1. Från Azure-portalen öppnar du fönstret **Alla resurser** och letar reda på ASATutorial Stream Analytics-jobbet.  

2. I avsnittet **Jobbtopologi** i Stream Analytics-jobbfönstret väljer du alternativet **Indata**.  

3. Välj **+Lägg till strömindata** (referensindata refererar till statiska sökningsdata, som du inte kommer att använda i den här självstudien), **Event hub** och fyll sedan i följande värden i rutan:  

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias     |  CallStream       |  Ange ett eget namn som identifierar din inmatning. Indataaliaset får enbart innehålla alfanumeriska tecken, bindestreck och understreck och måste vara mellan 3-63 tecken långt.       |
   |Prenumeration    |   \<Din prenumeration\>      |   Välj en Azure-prenumeration där du skapade händelsehubben. Händelsehubben kan finnas i samma eller en annan prenumeration som Stream Analytics-jobbet.       |
   |Namnområde för händelsehubb    |  MyEventHubNS       |  Välj namnområdet för händelsehubben som du skapade i föregående avsnitt. Alla händelsehubbnamnområden som finns i din aktuella prenumeration visas i listrutan.       |
   |Namn på händelsehubb    |   MyEventHub      |  Välj händelsehubben som du skapade i föregående avsnitt. Alla händelsehubbar som finns i din aktuella prenumeration visas i listrutan.       |
   |Principnamn för Event Hub   |  Mypolicy       |  Välj händelsehubbens delade princip som du skapade i föregående avsnitt. Alla händelsehubbprinciper som finns i din aktuella prenumeration visas i listrutan.       |

   ![Konfigurera indata](media/stream-analytics-manage-job/configure-input.png) 

4. Använd standardalternativen för återstående inställningar, välj **Spara** och vänta på att distributionen genomförs.

## <a name="configure-job-output"></a>Konfigurera jobbutdata 

Det sista steget är att definiera en utdatamottagare för jobbet där det kan skriva transformerade data. För den här självstudien matar du ut resultat till Power BI och visualiserar datumet. Kör följande steg för att konfigurera utdata för ditt jobb:

1. Från Azure-portalen öppnar du fönstret **Alla resurser** och ASATutorial Stream Analytics-jobbet.  

2. I avsnittet **Jobbtopologi** i Stream Analytics-jobbfönstret väljer du alternativet **Utdata**.  

3. Välj **+Lägg till** > **Power BI** och fyll i formuläret med följande information (du kan ange ett eget namn som identifierar kolumnalias, datauppsättningsnamn och tabellnamn enligt tabellen) och välj **Auktorisera**:  

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|---------|
   |Utdataalias  |  MyPBIoutput  |
   |Namn på datauppsättning  |   ASAdataset  | 
   |Tabellnamn |  ASATable  | 

   ![Konfigurera utdata](media/stream-analytics-manage-job/configure-output.png)  

4. När du har gjort ett val för **Auktorisera** visas ett popup-fönster och du ombeds ange autentiseringsuppgifter för att autentisera ditt Power BI-konto. När auktoriseringen är klar ska du **Spara** inställningarna. 

## <a name="define-a-query-to-analyze-input-data"></a>Definiera en fråga för att analysera indata

När du har en Stream Analytics-jobbkonfiguration för att läsa en inkommande dataström är nästa steg att skapa en omvandling som analyserar data i realtid. Du definierar transformationsfrågan med [Stream Analytics-frågespråket](https://msdn.microsoft.com/library/dn834998.aspx). I den här kursen definierar du en fråga som identifierar bedrägliga anrop från telefondata. 

I det här exemplet räknar vi bedrägliga anrop som dem som kommer från samma användare men på separata platser och om längden mellan båda samtalen är fem sekunder. Samma användare kan till exempel inte legitimt ringa ett samtal från USA och Australien samtidigt. För att definiera transformationsfrågan för ditt Stream Analytics-jobb kör du följande steg:

1. Från Azure-portalen öppnar du fönstret **Alla resurser** och öppnar **ASATutorial** Stream Analytics-jobbet du skapade tidigare.  

2. I avsnittet **Jobbtopologi** i Stream Analytics-jobbfönstret väljer du alternativet **Fråga**. I popup-fönstret visas indata och utdata som är konfigurerade för jobbet, och kan du skapa en fråga för att omvandla indataströmmen.  

3. Ersätt därefter den befintliga frågan i redigeraren med följande data, så utför frågan en självkoppling med en 5-sekundersintervall med samtalsdata:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Om du vill söka efter bedrägliga anrop du bör du upprätta en självkoppling för strömmande data baserat på värdet `CallRecTime`. Du kan sedan leta efter anropsposter där värdet `CallingIMSI` (det ursprungliga numret) är samma, men värdet `SwitchNum` (ursprungsland) är olika. När du använder en JOIN-åtgärd med strömmande data måste kopplingen tillhandahålla samma begränsningar för hur långt matchningsraderna kan delas upp i tid. Eftersom strömmande data är oändliga anges tidsgränserna för relationen inom ON-satsen för kopplingen med funktionen [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   Frågan är som en vanlig SQL-koppling förutom DATEDIFF-funktionen. DATEDIFF-funktionen som används i den här frågan är specifik för Streaming Analytics och måste visas i `ON...BETWEEN`-satsen.  

4. **Spara** frågan.  

   ![definiera fråga](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>Testa frågan

Du kan testa en fråga från frågeredigeraren, och du behöver exempeldata för att testa en fråga. I den här genomgången ska du extrahera exempeldata från telefonsamtalsströmmen som kommer till händelsehubben. Kör följande steg för att testa frågan:

1. Kontrollera att TelcoGenerator-appen körs och producerar telefonsamtalsposter.  

2. I fönstret **Fråga** väljer du punkterna bredvid CallStream-indata och väljer sedan **Exempeldata från indata**. Då öppnas ett fönster där du kan ange hur mycket exempeldata som ska läsas från indataströmmen.  

   ![Exempel på inmatade data](media/stream-analytics-manage-job/sample-input-data.png)  

3. Ställ in **Minuter** på 3 och välj **OK**. Tre minuters data samplas från indataströmmen och meddelar dig när exempeldata är klara. Du kan visa status för exemplet från meddelandefältet. 

   Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempeldata och du måste skapa en ny uppsättning exempeldata. Som ett alternativ kan du hämta en .json-fil som innehåller exempeldata från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/telco.json) och sedan ladda upp .json-filen och använda den som exempeldata för CallStream-indata.  

4. Välj **Test** för att testa frågan. Du bör se utdataresultat som visas i den här skärmbilden:  

   ![Testa utdata](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Starta jobbet och visualisera utdata

1. För att starta jobbet går du till rutan **Översikt** i jobbet och väljer **Start**.  

2. Välj **Nu** som starttid för jobbutdata och välj **Start**. Jobbet startar efter några minuter och du kan visa dess status i meddelandefältet.  

3. När jobbet har genomförts går du till [Powerbi.com](https://powerbi.com/) och loggar in med ditt arbets- eller skolkonto. Om Stream Analytics-jobbfrågan genererar resultat ser du att datauppsättningen redan är skapad. Gå till fliken **Datauppsättningar** kan du visa en datauppsättning med namnet "ASAdataset".  

4. Från arbetsytan väljer du **+Skapa**. Skapa en ny instrumentpanel och ge den namnet Bedrägliga samtal. Du kommer att lägga till två paneler till instrumentpanelen. En panel används för at visa antalet bedrägliga samtal för en viss instans och den andra har en linjediagramvisualisering.  

5. Överst i fönstret väljer du **Lägg till panel** > och väljer **Anpassade strömmande data** > Nästa > välj **ASAdataset** > för Visualiseringstyp väljer du **Kort** > och Fält som **fraudulentcalls**. Välj **Nästa** > ange ett namn för sidan och välj **Använd**.  

   ![Skapa paneler](media/stream-analytics-manage-job/create-tiles.png)

6. Följ steg 4, med följande alternativ:
   * När du kommer till Visualiseringstyp väljer du Linjediagram.  
   * Lägg till en axel och välj **windowend**.  
   * Lägg till ett värde och välj **fraudulentcalls**.  
   * För **Tidsfönster att visa** väljer du de senaste 10 minuterna.  

7. Din instrumentpanel ser ut som följande skärmbild när båda panelerna har lagts till. Du kommer att märka att din PowerBI-instrumentpanel regelbundet uppdateras i takt med att det kommer nya data om din händelsehubbs avsändarprogram och Streaming Analytics-programmet körs.  

   ![Power BI-resultat](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Bädda in din PowerBI-instrumentpanel i ett webbprogram

För den här delen av självstudien använder du ett [ASP.NET](http://asp.net/)-exempelwebbprogram som skapats av PowerBI-teamet för att bädda in instrumentpanelen. Mer information om att bädda in instrumentpaneler finns i artikeln [inbäddning med Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

I den här självstudiekursen kommer vi följa instruktionerna för användaren som äger dataprogrammet. För att konfigurera programmet går du till Github-lagringsplatsen [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) och följer instruktionerna under avsnittet om **användarägda data** (använd URL:erna för omdirigering och startsida i underavsnittet **integrate-dashboard-web-app**). Eftersom vi använder exemplet med instrumentpanelen ska du använda exempelkoden integrate-dashboard-web-app som finns på [GitHub-lagringsplatsen](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User Owns Data/integrate-dashboard-web-app).
När du har fått igång programmet i webbläsaren följer du anvisningarna för att bädda in instrumentpanelen du skapade tidigare på webbsidan:

1. Välj **Logga in till Power BI**, som ger programmet åtkomst till instrumentpanelerna i ditt PowerBI-konto.  

2. Välj knappen **Hämta instrumentpaneler** som visar kontots instrumentpaneler i en tabell. Leta reda på namnet på instrumentpanelen du skapade tidigare (powerbi-embedded-dashboard) och kopiera motsvarande **EmbedUrl**.  

3. Slutligen ska du klistra in **EmbedUrl** i motsvarande textfält och välja **Bädda in instrumentpanel**. Nu kan du visa samma instrumentpanel inbäddad i ett webbprogram.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett enkelt Stream Analytics-jobb, analyserat inkommande data och presenterat resultatet i en Power BI-instrumentpanel. Om du vill veta mer om Stream Analytics-jobb kan du fortsätta till nästa självstudie:

> [!div class="nextstepaction"]
> [Köra Azure Functions från Azure Stream Analytics-jobb](stream-analytics-with-azure-functions.md)
