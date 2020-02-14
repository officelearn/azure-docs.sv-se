---
title: Azure Stream Analytics på IoT Edge
description: Skapa edge-jobb i Azure Stream Analytics och distribuera dem till enheter som kör Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201763"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics på IoT Edge
 
Med Azure Stream Analytics (ASA) i IoT Edge kan utvecklare distribuera analytisk intelligens närmare IoT-enheter i nära realtid och utnyttja enhetsgenererade data maximalt. Azure Stream Analytics har utformats för korta svarstider, hög elasticitet, effektiv bandbreddsanvändning och bra efterlevnad. Nu kan företag distribuera kontrollogik nära den industriella verksamheten med stordataanalyser i molnet.  

Azure Stream Analytics på IoT Edge körs i [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework. När jobbet har skapats i ASA, kan du distribuera och hantera den med hjälp av IoT Hub.

## <a name="scenarios"></a>Scenarier
![Översiktsdiagram över IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Kommando och kontroll med låg latens**: till exempel måste tillverknings säkerhets system svara på drift data med mycket låg latens. Du kan analysera sensor data i realtid och skicka kommandon när du upptäcker avvikelser för att stoppa en virtuell dator eller utlösa aviseringar med ASA på IoT Edge.
*   **Begränsad anslutning till molnet**: verksamhets kritiska system, t. ex. fjärrutvinnings utrustning, anslutna fartyg eller offshore-borrning, behöver analysera och reagera på data även när moln anslutningen är intermittent. Strömmande logik körs oberoende av nätverksanslutningen med ASA, och du kan välja vad du skickar till molnet för vidare bearbetning eller lagring.
* **Begränsad bandbredd**: den mängd data som produceras av Jet-motorer eller anslutna bilar kan vara så stora att data måste filtreras eller förbehandlas innan de skickas till molnet. Med ASA, kan du filtrera eller aggregera data som måste skickas till molnet.
* **Efterlevnad**: reglerande krav kan kräva att vissa data ska vara lokalt anonymiserats eller aggregerade innan de skickas till molnet.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-jobb i Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Vad är ett ”edge”-jobb?

ASA Edge-jobb körs i behållare som distribuerats till [Azure IoT Edge enheter](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). De är består av två delar:
1.  En del i molnet som ansvarar för jobbdefinitionen: användare definiera indata, utdata, fråga och andra inställningar (oordnade händelser, osv.) i molnet.
2.  En modul som körs på dina IoT-enheter. Det innehåller ASA-motorn och tar emot jobbdefinitionen från molnet. 

ASA använder IoT Hub för att distribuera edge-jobb till enheter. Mer information om [IoT Edge distribution kan ses här](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Azure Stream Analytics Edge-jobb](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installationsinstruktioner
De övergripande stegen beskrivs i följande tabell. Mer information ges i följande avsnitt.

|      |Steg   | Anteckningar   |
| ---   | ---   |  ---      |
| 1   | **Skapa en lagrings behållare**   | Storage-behållare används för att spara din jobbdefinition där de kan användas av dina IoT-enheter. <br>  Du kan återanvända eventuella befintliga lagringsbehållare.     |
| 2   | **Skapa ett ASA Edge-jobb**   |  Skapa ett nytt jobb, Välj **Edge** som **värd miljö**. <br> Dessa jobb finns skapas/hanteras från molnet och kör på dina egna IoT Edge-enheter.     |
| 3   | **Konfigurera din IoT Edge-miljö på din enhet (er)**   | Instruktioner för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuera ASA på IoT Edge enhet (er)**   |  ASA jobbdefinitionen exporteras till den lagringsbehållare som skapades tidigare.       |

Du kan följa de [här steg-för-steg-självstudierna](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) för att distribuera ditt första ASA-jobb på IoT Edge. Följande videoklipp hjälper dig att förstå processen för att köra ett Stream Analytics-jobb på en IoT edge-enhet:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
En lagringsbehållare krävs för att exportera ASA kompileras frågan och jobbkonfigurationen. Den används för att konfigurera ASA Docker-avbildning med din specifika fråga. 
1. Följ [de här anvisningarna](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) för att skapa ett lagrings konto från Azure Portal. Du kan behålla alla standardalternativ för att använda det här kontot med ASA.
2. Skapa en blob storage-behållare i det nyligen skapade lagringskontot:
    1. Klicka på **blobbar**och sedan på **+ container**. 
    2. Ange ett namn och behåll behållaren som **privat**.

#### <a name="create-an-asa-edge-job"></a>Skapa ett ASA Edge-jobb
> [!Note]
> Den här självstudien fokuserar på skapande av ASA-jobb med hjälp av Azure portal. Du kan också [använda Visual Studio-plugin-programmet för att skapa ett ASA Edge-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Skapa en ny ”Stream Analytics-jobbet” från Azure-portalen. [Direkt länk för att skapa ett nytt ASA-jobb här](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. På sidan Skapa väljer du **Edge** som **värd miljö** (se följande bild)

   ![Skapa Stream Analytics-jobb i Microsoft Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Jobbdefinition
    1. **Definiera indata ström (er)** . Definiera en eller flera inkommande dataströmmar för jobbet.
    2. Definiera referensdata (valfritt).
    3. **Definiera utdataströmmen**. Definiera en eller flera strömmar av utdata för jobbet. 
    4. **Definiera fråga**. Definiera ASA-frågan i molnet med hjälp av infogade-redigeraren. Kompilatorn kontrollerar automatiskt den syntax som aktiverats för ASA edge. Du kan också testa frågan genom att ladda upp exempeldata. 

4. Ange information om lagrings behållare på menyn **IoT Edge inställningar** .

5. Ange valfria inställningar
    1. **Händelse ordning**. Du kan konfigurera princip för out-ordning i portalen. Dokumentation finns [här](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Locale**. Ange internalization format.



> [!Note]
> När en distribution har skapats, exporterar ASA jobbdefinitionen till en lagringsbehållare. Den här jobbdefinitionen förblir samma under en distribution. Det betyder om du vill uppdatera ett jobb som körs på gränsen måste redigera jobbet i ASA och skapa en ny distribution i IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurera din IoT Edge-miljö på dina enheter
Edge-jobb kan distribueras på enheter som kör Azure IoT Edge.
För detta måste du följa dessa steg:
- Skapa en Iot-hubb.
- Installera Docker och IoT Edge-körningen på dina gränsenheter.
- Ange enheterna som **IoT Edge enheter** i IoT Hub.

De här stegen beskrivs i IoT Edge dokumentationen för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distribution ASA på IoT Edge-enheter
##### <a name="add-asa-to-your-deployment"></a>Lägg till ASA för din distribution
- Öppna IoT Hub i Azure Portal, gå till **IoT Edge** och klicka på den enhet som du vill använda som mål för distributionen.
- Välj **Ange moduler**och välj sedan **+ Lägg till** och välj **Azure Stream Analytics modul**.
- Välj prenumerationen och ASA Edge-jobb som du skapade. Klicka på Spara.
![lägga till ASA-modulen i din distribution](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Det här steget skapar ASA en mapp med namnet ”EdgeJobs” i storage-behållare (om den inte redan finns). För varje distribution skapas en ny undermapp i mappen ”EdgeJobs”.
> När du distribuerar jobbet till IoT Edge enheter skapar ASA en signatur för delad åtkomst (SAS) för jobb definitions filen. SAS-nyckeln skickas på ett säkert sätt till IoT Edge-enheter med hjälp av enhetstvillingen. Förfallodatum för den här nyckeln är tre år från dag då skapades. När du uppdaterar ett IoT Edge jobb kommer SAS att ändras, men avbildnings versionen ändras inte. När du har **uppdaterat**följer du distributions arbets flödet och ett uppdaterings meddelande loggas på enheten.


Mer information om IoT Edge distributioner finns på [den här sidan](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurera vägar
IoT Edge är ett sätt att deklarativt dirigera meddelanden mellan moduler och mellan moduler och IoT Hub. Den fullständiga syntaxen beskrivs [här](https://docs.microsoft.com/azure/iot-edge/module-composition).
Namnen på indata och utdata som skapats i ASA-jobbet kan användas som slutpunkter för routning.  

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
Det här exemplet visar vägar i det scenario som beskrivs i följande bild. Det innehåller ett Edge-jobb med namnet "**ASA**", med indata med namnet "**temperatur**" och utdata med namnet "**Alert**".
exempel på ![diagram över meddelanderoutning](media/stream-analytics-edge/edge-message-routing-example.png)

Det här exemplet definierar följande vägar:
- Alla meddelanden från **tempSensor** skickas till modulen **ASA** till den inmatade **temperaturen**,
- Alla utdata från **ASA** -modulen skickas till den IoT Hub som är länkad till den här enheten ($upstream).
- Alla utdata från **ASA** -modulen skickas till **tempSensor** **kontroll** slut punkt.


## <a name="technical-information"></a>Teknisk information
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuella begränsningar för IoT Edge-jobb jämfört med molnjobb
Målet är att har paritet mellan IoT Edge-jobb och molnjobb. De flesta funktioner i SQL-frågespråket stöds, vilket möjliggör körning av samma logik både i molnet och IoT Edge.
Men de följande funktionerna stöds inte ännu för edge-jobb:
* Användardefinierade funktioner (UDF) i JavaScript. UDF är tillgänglig i [ C# för IoT Edge-jobb (för](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) hands version).
* Användardefinierade aggregeringar (UDA).
* Azure ML-funktioner.
* Med hjälp av mer än 14 aggregat i ett enda steg.
* AVRO-format för indata/utdata. För närvarande stöds endast CSV och JSON.
* Följande SQL-operatorer:
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Runtime-och maskinvarukrav
Om du vill köra ASA på IoT Edge behöver du enheter som kan köra [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA och Azure IoT Edge använda **Docker** -behållare för att tillhandahålla en portabel lösning som körs på flera värd operativ system (Windows, Linux).

ASA på IoT Edge görs tillgängligt som Windows-och Linux-avbildningar som körs på både x86-64-eller ARM-arkitekturer (Advanced RISC Machines). 


### <a name="input-and-output"></a>Indata och utdata
#### <a name="input-and-output-streams"></a>Indata- och utdataströmmar
ASA Edge-jobb kan få indata och utdata från andra moduler som körs på IoT Edge-enheter. För att ansluta från och till specifika moduler, kan du ange routningskonfigurationen vid tidpunkten för distribution. Mer information finns i [dokumentationen för IoT Edge module-sammansättning](https://docs.microsoft.com/azure/iot-edge/module-composition).

CSV och JSON-format stöds för både indata och utdata.

För varje indatakort och utdataströmmen som du skapar i ASA-jobbet, skapas en motsvarande slutpunkt på din distribuerade modul. Dessa slutpunkter kan användas i vägar för din distribution.

AT finns, det går endast att strömindata och stream utdatatyper är Edge Hub. Referera till inkommande stöder referenstyp för filen. Andra utdata kan nås på ett molnjobb nedströms. Till exempel skickar ett Stream Analytics-jobb i Edge utdata till Edge Hub, som sedan kan skicka utdata till IoT Hub. Du kan använda ett andra molnvärd Azure Stream Analytics-jobb med indata från IoT Hub och utdata till Power BI eller en annan Utdatatyp.



##### <a name="reference-data"></a>Referensdata
Referensdata (även kallat en uppslagstabell) är en begränsad mängd data som är statiska eller långsamma ändrar sin natur. Den används för att utföra en sökning eller att korrelera med din dataström. Om du vill använda referens data i ditt Azure Stream Analytics jobb använder du vanligt vis en [referens data koppling](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) i din fråga. Mer information finns i [använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md).

Endast lokala referensdata stöds. När ett jobb har distribuerats till IoT Edge-enhet, laddas referensdata från sökvägen till användardefinierade.

Skapa ett jobb med referensdata i Microsoft Edge:

1. Skapa en ny indata för jobbet.

2. Välj **referens data** som **Källtyp**.

3. Ha en referens-datafil redo på enheten. Placera filen referens data på den lokala enheten för en Windows-behållare och dela den lokala enheten med Docker-behållaren. Skapa en Docker-volym för en Linux-behållare och fylla i datafilen till volymen.

4. Ange sökvägen till filen. För Windows-värd-OS och Windows-behållare använder du den absoluta sökvägen: `E:\<PathToFile>\v1.csv`. För en Windows-värd-OS och Linux-behållare eller en Linux OS-och Linux-behållare använder du sökvägen i volymen: `<VolumeName>/file1.txt`.

![Nya referensindata för Azure Stream Analytics-jobb på IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referensdata på IoT Edge update utlöses av en distribution. När utlöses, hämtar modulen ASA uppdaterade data utan att stoppa det pågående jobbet.

Det finns två sätt att uppdatera referensdata:
* Uppdatera sökvägen för referens i ASA-jobb från Azure-portalen.
* Uppdatera IoT Edge-distribution.

## <a name="license-and-third-party-notices"></a>Licens och meddelanden från tredje part
* [Azure Stream Analytics på IoT Edge licens](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Meddelande från tredje part för Azure Stream Analytics på IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Avbildnings information för Azure Stream Analytics modul 

Den här versions informationen uppdaterades senast den 2019-06-27:

- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - bas avbildning: Microsoft/dotNet: 2.1.6-Runtime – Alpine 3.7
   - systemet
      - arkitektur: amd64
      - OS: Linux
  
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - bas avbildning: Microsoft/dotNet: 2.1.6-Runtime-Bionic-arm32v7
   - systemet
      - arkitektur: arm
      - OS: Linux
  
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - bas avbildning: Microsoft/dotNet: 2.1.6-Runtime-nanoserver-1809
   - systemet
      - arkitektur: amd64
      - operativ system: Windows
      
      
## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du prova [Azure Stream Analytics-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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
