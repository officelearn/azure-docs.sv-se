---
title: Azure Stream Analytics på IoT Edge
description: Skapa Edge-jobb i Azure Stream Analytics och distribuera dem till enheter som kör Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: 7a084b2d0582f53d4372ba3332194629ad29a4ec
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041861"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics på IoT Edge
 
Med Azure Stream Analytics (ASA) i IoT Edge kan utvecklare distribuera analytisk intelligens närmare IoT-enheter i nära realtid och utnyttja enhetsgenererade data maximalt. Azure Stream Analytics har utformats för korta svarstider, hög elasticitet, effektiv bandbreddsanvändning och bra efterlevnad. Nu kan företag distribuera kontrollogik nära den industriella verksamheten med stordataanalyser i molnet.  

Azure Stream Analytics på IoT Edge körs i [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework. När jobbet har skapats i ASA kan du distribuera och hantera det med hjälp av IoT Hub.

## <a name="scenarios"></a>Scenarier
![Diagram med hög nivå på IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Kommando och kontroll med låg latens** : till exempel måste tillverknings säkerhets system svara på drift data med mycket låg latens. Med ASA på IoT Edge kan du analysera sensor data i nära real tid och utfärda kommandon när du identifierar avvikelser för att stoppa en dator eller utlösa aviseringar.
*   **Begränsad anslutning till molnet** : verksamhets kritiska system, t. ex. fjärrutvinnings utrustning, anslutna fartyg eller offshore-borrning, behöver analysera och reagera på data även när moln anslutningen är intermittent. Med ASA körs din strömmande logik oberoende av nätverks anslutningen och du kan välja vad du skickar till molnet för vidare bearbetning eller lagring.
* **Begränsad bandbredd** : den mängd data som produceras av Jet-motorer eller anslutna bilar kan vara så stora att data måste filtreras eller förbehandlas innan de skickas till molnet. Med hjälp av ASA kan du filtrera eller aggregera de data som behöver skickas till molnet.
* **Efterlevnad** : reglerande krav kan kräva att vissa data ska vara lokalt anonymiserats eller aggregerade innan de skickas till molnet.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-jobb i Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Vad är ett "Edge"-jobb?

ASA Edge-jobb körs i behållare som distribuerats till [Azure IoT Edge enheter](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). De består av två delar:
1.  En moln del som är ansvarig för jobb definition: användare definierar indata, utdata, fråga och andra inställningar (i händelse av ordnings händelser osv.) i molnet.
2.  En modul som körs på dina IoT-enheter. Den innehåller ASA-motorn och tar emot jobb definitionen från molnet. 

ASA använder IoT Hub för att distribuera Edge-jobb till enhet (er). Mer information om [IoT Edge distribution kan ses här](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Azure Stream Analytics Edge-jobb](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installationsinstruktioner
De övergripande stegen beskrivs i följande tabell. Mer information ges i följande avsnitt.

| Steg | Kommentarer |
| --- | --- |
| **Skapa en lagringscontainer** | Lagrings behållare används för att spara din jobb definition där de kan nås av dina IoT-enheter. <br>  Du kan återanvända alla befintliga lagrings behållare. |
| **Skapa ett ASA Edge-jobb** | Skapa ett nytt jobb, Välj **Edge** som **värd miljö** . <br> De här jobben skapas/hanteras från molnet och körs på dina egna IoT Edge enheter. |
| **Konfigurera din IoT Edge-miljö på din enhet (er)** | Instruktioner för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).|
| **Distribuera ASA på IoT Edge enhet (er)** | Jobb definitionen för ASA exporteras till den lagrings behållare som skapades tidigare. |

Du kan följa de [här steg-för-steg-självstudierna](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) för att distribuera ditt första ASA-jobb på IoT Edge. Följande video bör hjälpa dig att förstå processen att köra ett Stream Analytics jobb på en IoT Edge-enhet:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Skapa en lagringscontainer
Det krävs en lagrings behållare för att exportera den kompilerade ASA-frågan och jobb konfigurationen. Den används för att konfigurera den Docker-avbildningen för ASA med din speciella fråga. 
1. Följ [de här anvisningarna](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) för att skapa ett lagrings konto från Azure Portal. Du kan behålla alla standard alternativ för att använda det här kontot med ASA.
2. Skapa en Blob Storage-behållare i det nyligen skapade lagrings kontot:
    1. Klicka på **blobbar** och sedan på **+ container** . 
    2. Ange ett namn och behåll behållaren som **privat** .

#### <a name="create-an-asa-edge-job"></a>Skapa ett ASA Edge-jobb
> [!Note]
> Den här självstudien fokuserar på att skapa ASA-jobb med Azure Portal. Du kan också [använda Visual Studio-plugin-programmet för att skapa ett ASA Edge-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Skapa ett nytt "Stream Analytics jobb" från Azure Portal. [Direkt länk för att skapa ett nytt ASA-jobb här](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. På sidan Skapa väljer du **Edge** som **värd miljö** (se följande bild)

   ![Skapa Stream Analytics jobb på gränsen](media/stream-analytics-edge/create-asa-edge-job.png)
3. Jobb definition
    1. **Definiera indata ström (er)** . Definiera en eller flera indata strömmar för jobbet.
    2. Definiera referens data (valfritt).
    3. **Definiera utdataströmmen** . Definiera en eller flera utmatnings strömmar för jobbet. 
    4. **Definiera fråga** . Definiera ASA-frågan i molnet med hjälp av den infogade redigeraren. Kompilatorn kontrollerar automatiskt den syntax som är aktive rad för ASA Edge. Du kan också testa frågan genom att ladda upp exempel data. 

4. Ange information om lagrings behållare på menyn **IoT Edge inställningar** .

5. Ange valfria inställningar
    1. **Händelse ordning** . Du kan konfigurera en princip för inaktuella principer i portalen. Dokumentation finns [här](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Locale** . Ange Internalization-formatet.



> [!Note]
> När en distribution skapas exporterar ASA jobb definitionen till en lagrings behållare. Den här jobb definitionen är oförändrad under en distributions varaktighet. Det innebär att om du vill uppdatera ett jobb som körs på gränsen måste du redigera jobbet i ASA och sedan skapa en ny distribution i IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurera din IoT Edge miljö på din enhet (er)
Edge-jobb kan distribueras på enheter som kör Azure IoT Edge.
För detta måste du följa dessa steg:
- Skapa en IoT-hubb.
- Installera Docker och IoT Edge runtime på dina gräns enheter.
- Ange enheterna som **IoT Edge enheter** i IoT Hub.

De här stegen beskrivs i IoT Edge dokumentationen för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distributions-ASA på IoT Edge enhet (er)
##### <a name="add-asa-to-your-deployment"></a>Lägg till ASA i distributionen
- Öppna IoT Hub i Azure Portal, gå till **IoT Edge** och klicka på den enhet som du vill använda som mål för distributionen.
- Välj **Ange moduler** och välj sedan **+ Lägg till** och välj **Azure Stream Analytics modul** .
- Välj prenumerationen och jobbet ASA Edge som du skapade. Klicka på Spara.
![Lägg till ASA-modul i distributionen](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Under det här steget skapar ASA en mapp med namnet "EdgeJobs" i lagrings behållaren (om den inte redan finns). För varje distribution skapas en ny undermapp i mappen "EdgeJobs".
> När du distribuerar jobbet till IoT Edge enheter skapar ASA en signatur för delad åtkomst (SAS) för jobb definitions filen. SAS-nyckeln överförs säkert till IoT Edge enheter med enhets anslutning. Den här nyckelns förfallo datum är tre år från dagen då den skapades. När du uppdaterar ett IoT Edge jobb kommer SAS att ändras, men avbildnings versionen ändras inte. När du har **uppdaterat** följer du distributions arbets flödet och ett uppdaterings meddelande loggas på enheten.


Mer information om IoT Edge distributioner finns på [den här sidan](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurera vägar
IoT Edge är ett sätt att deklarativ skicka meddelanden mellan moduler och mellan moduler och IoT Hub. Den fullständiga syntaxen beskrivs [här](https://docs.microsoft.com/azure/iot-edge/module-composition).
Namn på indata och utdata som skapats i ASA-jobbet kan användas som slut punkter för routning.  

###### <a name="example"></a>Exempel

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
I det här exemplet visas vägarna för scenariot som beskrivs i följande bild. Det innehåller ett Edge-jobb med namnet " **ASA** ", med indata med namnet " **temperatur** " och utdata med namnet " **Alert** ".
![Diagram exempel på meddelanderoutning](media/stream-analytics-edge/edge-message-routing-example.png)

I det här exemplet definieras följande vägar:
- Alla meddelanden från **tempSensor** skickas till modulen **ASA** till den inmatade **temperaturen** ,
- Alla utdata från **ASA** -modulen skickas till den IoT Hub som är länkad till den här enheten ($upstream).
- Alla utdata från **ASA** -modulen skickas till **tempSensor** **kontroll** slut punkt.


## <a name="technical-information"></a>Teknisk information
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuella begränsningar för IoT Edge jobb jämfört med moln jobb
Målet är att ha paritet mellan IoT Edge jobb och moln jobb. De flesta funktioner i SQL-frågespråket stöds, vilket möjliggör körning av samma logik både i molnet och IoT Edge.
Följande funktioner stöds dock ännu inte för Edge-jobb:
* Användardefinierade funktioner (UDF) i Java Script. UDF är tillgänglig i [C# för IoT Edge jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (för hands version).
* Användardefinierade agg regeringar (UDA).
* Azure ML-funktioner.
* Använda fler än 14 mängder i ett enda steg.
* AVRO-format för indata/utdata. Just nu stöds endast CSV och JSON.
* Följande SQL-operatorer:
    * PARTITION AV
    * GetMetadataPropertyValue
* Princip för sent införsel

### <a name="runtime-and-hardware-requirements"></a>Körnings-och maskin varu krav
Om du vill köra ASA på IoT Edge behöver du enheter som kan köra [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA och Azure IoT Edge använda **Docker** -behållare för att tillhandahålla en portabel lösning som körs på flera värd operativ system (Windows, Linux).

ASA på IoT Edge görs tillgängligt som Windows-och Linux-avbildningar som körs på både x86-64-eller ARM-arkitekturer (Advanced RISC Machines). 


### <a name="input-and-output"></a>Indata och utdata
#### <a name="input-and-output-streams"></a>Strömmar för indata och utdata
ASA Edge-jobb kan hämta indata och utdata från andra moduler som körs på IoT Edge enheter. Om du vill ansluta från och till vissa moduler kan du ange konfiguration för routning vid distribution. Mer information finns i [dokumentationen för IoT Edge module-sammansättning](https://docs.microsoft.com/azure/iot-edge/module-composition).

För både indata och utdata stöds CSV-och JSON-format.

För varje indata-och utdataström som du skapar i ditt ASA-jobb skapas en motsvarande slut punkt i den distribuerade modulen. Dessa slut punkter kan användas i distributionens vägar.

För närvarande är de enda utströmnings-och strömmande utmatnings typerna Edge Hub. Referens indata stöder referens fil typ. Andra utdata kan nås med hjälp av ett moln jobb. Till exempel skickar ett Stream Analytics jobb som finns i Edge utdata till Edge Hub, som sedan kan skicka utdata till IoT Hub. Du kan använda ett andra moln som värd Azure Stream Analytics jobb med indata från IoT Hub och utdata till Power BI eller någon annan Utdatatyp.



##### <a name="reference-data"></a>Referens data
Referens data (kallas även en uppslags tabell) är en begränsad data uppsättning som är statisk eller långsam ändring i natur. Den används för att utföra en sökning eller korrelera med din data ström. Om du vill använda referens data i ditt Azure Stream Analytics jobb använder du vanligt vis en [referens data koppling](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) i din fråga. Mer information finns i [använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md).

Endast lokala referens data stöds. När ett jobb distribueras till IoT Edge enhet laddar det referens data från den användardefinierade fil Sök vägen.

Så här skapar du ett jobb med referens data i Edge:

1. Skapa ett nytt inflöde för ditt jobb.

2. Välj **referens data** som **Källtyp** .

3. Har en referens data fil som är klar på enheten. För en Windows-behållare, placera referens data filen på den lokala enheten och dela den lokala enheten med Docker-behållaren. För en Linux-behållare skapar du en Docker-volym och fyller i data filen på volymen.

4. Ange sökvägen till filen. För Windows-värd-OS och Windows-behållare använder du den absoluta sökvägen: `E:\<PathToFile>\v1.csv` . För en Windows-värd-OS och Linux-behållare eller en Linux OS-och Linux-behållare använder du sökvägen i volymen: `<VolumeName>/file1.txt` .

![Nya referens indata för Azure Stream Analytics jobb på IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referens data på IoT Edge uppdatering utlöses av en distribution. När den har utlösts väljer modulen ASA den uppdaterade informationen utan att stoppa jobbet som körs.

Det finns två sätt att uppdatera referens data:
* Uppdatera referens data Sök vägen i ditt ASA-jobb från Azure Portal.
* Uppdatera IoT Edge-distributionen.

## <a name="license-and-third-party-notices"></a>Licens och meddelanden från tredje part
* [Azure Stream Analytics på IoT Edge licens](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Meddelande från tredje part för Azure Stream Analytics på IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Avbildnings information för Azure Stream Analytics modul 

Den här versions informationen uppdaterades senast den 2019-06-27:

- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - bas avbildning: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - systemet
      - arkitektur: amd64
      - OS: Linux
 
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - bas avbildning: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - systemet
      - arkitektur: arm
      - OS: Linux
 
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - bas avbildning: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - systemet
      - arkitektur: arm64
      - OS: Linux
      
      
## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du testa [sidan Microsoft Q&en fråga för Azure Stream Analytics](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA på IoT Edge självstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Utveckla Stream Analytics Edge-jobb med Visual Studio-verktyg](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementera CI/CD för Stream Analytics med API: er](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
