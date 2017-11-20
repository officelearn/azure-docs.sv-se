---
title: "Handelssimulering med hög frekvens med Stream Analytics | Microsoft Docs"
description: "Så här kör du träning och bedömning i linjära regressionsmodeller i ett och samma Stream Analytics-jobb"
keywords: machine learning, advanced analytics, linear regression, simulation, UDA, user defined function
documentationcenter: 
services: stream-analytics
author: zhongc
manager: jhubbard
editor: cgronlun
ms.assetid: 997ccfc1-abaf-4c12-bef2-632481140f05
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/05/2017
ms.author: zhongc
ms.openlocfilehash: 0a5a1129c5b7fc693ed7c187d928a128650f28b9
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Handelssimulering med hög frekvens med Stream Analytics
Kombinationen av Azure Stream Analytics SQL-språk och JavaScripts UDF och UDA är mycket kraftfull. Användarna kan använda den här kombinationen för att utföra avancerade analyser, inklusive maskininlärning online med träning och bedömning samt tillståndskänslig processimulering. I den här artikeln finns en beskrivning av hur du utför linjär regression i ett Azure Stream Analytics-jobb som kör kontinuerlig träning och bedömning i ett scenario med högfrekvent handel.

## <a name="high-frequency-trading"></a>Handel med hög frekvens
Logikflödet för handel med hög frekvens handlar om att få offerter i realtid från börsen, bygga en förutsägelsemodell kring offerthanteringen för att bättre kunna förutspå prisfluktueringar samt att köpa och sälja i rättan tid för att kunna tjäna pengar på värdeförändringar. Därför behöver vi följande:
* Offertflöde i realtid
* En förutsägelsemodell som kan användas tillsammans med offerter i realtid
* En handelssimulering som visar resultatet (vinst/förlust) för handelsalgoritmen

### <a name="real-time-quote-feed"></a>Offertflöde i realtid
IEX tillhandahåller kostnadsfria bud och offertförfrågningar i realtid med socket.io, https://iextrading.com/developer/docs/#websockets. Det går att skriva ett enkelt konsolprogram som tar emot offerter i realtid och push-överför till händelsehubben i form av en datakälla. Stommen till programmet visas nedan. Felhanteringen har utelämnats av utrymmesskäl. Ditt projekt måste också innehålla NuGet-paket för SocketIoClientDotNet och WindowsAzure.ServiceBus.


    using Quobject.SocketIoClientDotNet.Client;
    using Microsoft.ServiceBus.Messaging;

    var symbols = "msft,fb,amzn,goog";
    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
    var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");

    socket.On(Socket.EVENT_MESSAGE, (message) =>
    {
        eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
    });

    socket.On(Socket.EVENT_CONNECT, () =>
    {
        socket.Emit("subscribe", symbols);
    });

Här följer några exempel på händelser som genereras.

    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}

>[!NOTE]
>Tidsstämpeln för händelsen är **lastUpdated** i epoktid.

### <a name="predictive-model-for-high-frequency-trading"></a>Förutsägelsemodell för handel med hög frekvens
Under den här demonstrationen använder vi en linjär modell så som den beskrivits i en artikel av Darryl Shen. http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf.

Volume Order Imbalance (VOI) är en funktion som hanterar diskrepanser mellan aktuellt bud/försäljningspris och volym samt aktuellt bud/försäljningspris/volym från förra ticket. Artikeln visar korrelationen mellan VOI och framtida prisfluktueringar. Sedan följer en beskrivning av hur man bygger en linjär modell med de senaste fem VOI-värdena och prisförändringarna för de kommande tio ticken. Modellen tränas med linjär regression och baseras på data från föregående dag. Den tränade modellen används sedan för att göra förutsägelser om prisändringar i offerter i realtid under den aktuella handelsdagen. När en tillräckligt stor prisändring förutsägs genomförs en transaktion. Beroende på inställt tröskelvärde kan du sedan förvänta dig tusentals transaktioner med samma aktie under en handelsdag.

![VOI-definition](./media/stream-analytics-high-frequency-trading/voi-formula.png)

Nu ska vi visa hur träning och förutsägelser fungerar i ett Azure Stream Analytics-jobb.

Vi börjar med att snygga upp indata. Epoktid omvandlas till datetime med **DATEADD**. **TRY_CAST** används för att tvinga datatyper utan att frågan misslyckas. Det är alltid en bra idé att omvandla inmatningsfält till de förväntade datatyperna, så att du inte får några otrevliga överraskningar i form av oväntat beteende när det gäller manipulering av värden eller jämförelser av fält.

    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* cleanup invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),

Härnäst använder vi funktionen **LAG** för att hämta värden från det senaste ticket. En timme av **LIMIT DURATION** väljs godtyckligt. Med tanke på offertfrekvensen kan man med största sannolikhet anta att det går att hitta ett tick för föregående timme.  

    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),

Vi kan nu beräkna VOI-värdet. Observera att vi för säkerhets skull filtrerar bort null-värden om det inte finns några gamla tick.

    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),

Nu kan vi använda **LAG** igen att skapa en sekvens med två på varandra följande VOI-värden, följt av tio på varandra följande medelprisvärden.

    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),

Vi omvandlar sedan dessa data till indata som kan användas i en linjär modell med två variabler. Än en gång filtrerar vi bort händelser där vi har inte alla data.

    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),

Eftersom Azure Stream Analytics inte har någon inbyggd funktion för linjär regression kan vi använda samlingarna **SUM** och **AVG** för att beräkna koefficienter för den linjära modellen.

![Formel för linjär regression](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),

För att kunna använda föregående dags modell för bedömning av aktuella händelser kopplar vi ihop offerterna med modellen. Men istället för att använda **JOIN** använder vi **UNION** för att koppla ihop modellhändelser och offerthändelser. Sedan använder vi **LAG** för att para ihop händelserna med föregående dags modell. På så sätt får vi exakt en matchning. På grund av helgen måste vi gå tre dagar tillbaka i tiden. Om du använder ett enkelt **JOIN** skulle vi få tre modeller för varje offerthändelse.

    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),

Nu kan vi göra förutsägelser och generera köp-/säljsignaler baserat på modellen, med ett tröskelvärde på 0,02. Värdet 10 betyder köp, värdet -10 betyder sälj.

    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),

### <a name="trading-simulation"></a>Handelssimulering
När vi har fått handelssignalerna på plats ska vi testa hur effektiva handelsstrategin är utan att behöva genomföra några riktiga transaktioner. Detta uppnås med hjälp av en användardefinierad sammansättning (UDA) med ett hoppande fönster som hoppar en gång i minuten. Ytterligare gruppering efter datum och having-satsen medger endast fönster för konton med händelser under en och samma dag. För ett hoppande fönster som sträcker sig över två dagar kan du använda **GROUP BY** för att dela upp grupperingarna i föregående dag och aktuell dag. **HAVING**-satsen filtrerar ut fönster som slutar på den aktuella dagen, men grupperas med föregående dag.

    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )

JavaScripts UDA initierar alla ackumulatorer i init-funktionen, beräknar tillståndsövergången för varje händelse som läggs till i fönstret och returnerar simuleringsresultaten längst ned i fönstret. Den allmänna handelsprocessen omfattar köp av aktier när en köpsignal inkommer och det inte finns något aktieinnehav, försäljning av aktier när en säljsignal inkommer och det finns ett aktieinnehav eller blankning om det inte finns något innehav. Om du har blankat och får en köpsignal görs köp för att täcka detta. I denna simulering behåller eller blankar vi aldrig 10 aktier. Transaktionskostnaden är 8 USD.


    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;

        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }

        this.accumulate = function (tradeSignal, timestamp) {

            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;

              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;

              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }

Slutligen skickas utdata till Power BI-instrumentpanelen för visualisering.

    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */

![Transaktioner](./media/stream-analytics-high-frequency-trading/trades.png)

![PNL](./media/stream-analytics-high-frequency-trading/pnl.png)


## <a name="summary"></a>Sammanfattning
Som du ser kan en realistisk handelsmodell med hög frekvens implementeras med hjälp av en ganska komplex fråga i Azure Stream Analytics. Då det inte finns någon inbyggd linjär regressionsfunktion måste vi förenkla modellen från fem indatavariabler till två. Men om du är riktigt ambitiös kan du använda algoritmer med högre dimensioner och mer avancerad uppbyggnad för att sedan implementera dessa som JavaScript UDA. Observera att större delen av frågan (det som inte är JavaScript UDA) kan testas och felsökas i Visual Studio med hjälp av [Azure Stream Analytics-verktyget för Visual Studio](stream-analytics-tools-for-visual-studio.md). När den första frågan skrevs lade författaren ner mindre än 30 minuter på testning och felsökning av frågan i Visual Studio. För närvarande går det inte att felsöka UDA i Visual Studio. Vi jobbar på att aktivera detta med tilläggsfunktionen att kunna stega igenom JavaScript-koden. Fält som når fram till UDA får fältnamn med gemena bokstäver. Detta var inget uppenbart beteende under testningen av frågefunktionerna. Från och med Azure Stream Analytics kompatibilitetsnivå 1.1 kan du dock bevara skiftläget för namnet, så att beteendet blir mer naturligt.

Jag hoppas den här artikeln fungerar som en inspiration för alla Azure Stream Analytics-användare som vill använda vår tjänst för att utföra avancerade analyser i nära nog realtid och utan avbrott. Lämna gärna feedback för att förenkla implementeringen av frågor i scenarion med avancerade analyser.
