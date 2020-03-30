---
title: Azure Stream Analytics på IoT Edge
description: Skapa kantjobb i Azure Stream Analytics och distribuera dem till enheter som kör Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb1bd018866bda9270b78507f0462b6c4d4ea17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475900"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics på IoT Edge
 
Med Azure Stream Analytics (ASA) i IoT Edge kan utvecklare distribuera analytisk intelligens närmare IoT-enheter i nära realtid och utnyttja enhetsgenererade data maximalt. Azure Stream Analytics har utformats för korta svarstider, hög elasticitet, effektiv bandbreddsanvändning och bra efterlevnad. Nu kan företag distribuera kontrollogik nära den industriella verksamheten med stordataanalyser i molnet.  

Azure Stream Analytics på IoT Edge körs inom [Azure IoT](https://azure.microsoft.com/campaigns/iot-edge/) Edge-ramverket. När jobbet har skapats i ASA kan du distribuera och hantera det med IoT Hub.

## <a name="scenarios"></a>Scenarier
![Diagram på hög nivå för IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Kommando och kontroll med låg latens**: Till exempel måste tillverkningssäkerhetssystem svara på driftdata med extremt låg latens. Med ASA på IoT Edge kan du analysera sensordata i nära realtid och utfärda kommandon när du upptäcker avvikelser för att stoppa en dator eller utlösa aviseringar.
*   **Begränsad anslutning till molnet**: Verksamhetskritiska system, till exempel fjärrutvinningsutrustning, anslutna fartyg eller offshore-borrning, måste analysera och reagera på data även när molnanslutningen är intermittent. Med ASA körs din streaminglogik oberoende av nätverksanslutningen och du kan välja vad du skickar till molnet för vidare bearbetning eller lagring.
* **Begränsad bandbredd**: Mängden data som produceras av jetmotorer eller anslutna bilar kan vara så stor att data måste filtreras eller förbehandlas innan de skickas till molnet. Med ASA kan du filtrera eller aggregera data som måste skickas till molnet.
* **Efterlevnad**: Regelefterlevnad kan kräva att vissa data anonymiseras eller aggregeras lokalt innan de skickas till molnet.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-jobb i Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Vad är ett "kantjobb"?

ASA Edge-jobb körs i behållare som distribueras till [Azure IoT Edge-enheter](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). De består av två delar:
1.  En molndel som ansvarar för jobbdefinition: användare definierar indata, utdata, frågor och andra inställningar (i oordning händelser osv.) i molnet.
2.  En modul som körs på dina IoT-enheter. Den innehåller ASA-motorn och tar emot jobbdefinitionen från molnet. 

ASA använder IoT Hub för att distribuera kantjobb till enheter. Mer information om [IoT Edge-distributionen kan ses här](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Azure Stream Analytics Edge-jobb](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Installationsinstruktioner
Stegen på hög nivå beskrivs i följande tabell. Mer information ges i följande avsnitt.

|      |Steg   | Anteckningar   |
| ---   | ---   |  ---      |
| 1   | **Skapa en lagringsbehållare**   | Förvaringsbehållare används för att spara jobbdefinitionen där de kan nås av dina IoT-enheter. <br>  Du kan återanvända alla befintliga lagringsbehållare.     |
| 2   | **Skapa ett ASA-kantjobb**   |  Skapa ett nytt jobb, välj **Edge** som **värdmiljö**. <br> Dessa jobb skapas/hanteras från molnet och körs på dina egna IoT Edge-enheter.     |
| 3   | **Konfigurera IoT Edge-miljön på enheten/enheten**   | Instruktioner för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuera ASA på din IoT Edge-enhet**   |  ASA-jobbdefinition exporteras till lagringsbehållaren som skapats tidigare.       |

Du kan följa [den här steg-för-steg-självstudien](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) för att distribuera ditt första ASA-jobb på IoT Edge. Följande video bör hjälpa dig att förstå processen för att köra ett Stream Analytics-jobb på en IoT-kantenhet:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
En lagringsbehållare krävs för att exportera den ASA-kompilerade frågan och jobbkonfigurationen. Den används för att konfigurera ASA Docker-avbildningen med din specifika fråga. 
1. Följ [dessa instruktioner](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) för att skapa ett lagringskonto från Azure-portalen. Du kan behålla alla standardalternativ för att använda det här kontot med ASA.
2. Skapa en lagringsbehållare för blob-lagring i det nyligen skapade lagringskontot:
    1. Klicka på **Blobbar**, sedan **+ Container**. 
    2. Ange ett namn och behåll behållaren som **privat**.

#### <a name="create-an-asa-edge-job"></a>Skapa ett ASA Edge-jobb
> [!Note]
> Den här självstudien fokuserar på att skapa ASA-jobb med Azure-portalen. Du kan också [använda Visual Studio-plugin för att skapa ett ASA Edge-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Skapa ett nytt "Stream Analytics-jobb" från Azure-portalen. [Direktlänk för att skapa ett nytt ASA-jobb här](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. På skärmen skapande väljer du **Edge** som **värdmiljö** (se följande bild)

   ![Skapa Stream Analytics-jobb på Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Jobbdefinition
    1. **Definiera indataström(er)**. Definiera en eller flera indataströmmar för jobbet.
    2. Definiera referensdata (valfritt).
    3. **Definiera utdataström(er)**. Definiera en eller flera utdataströmmar för jobbet. 
    4. **Definiera fråga**. Definiera ASA-frågan i molnet med hjälp av den infogade redigeraren. Kompilatorn kontrollerar automatiskt syntaxen aktiverad för ASA-kanten. Du kan också testa frågan genom att ladda upp exempeldata. 

4. Ange information om lagringsbehållaren på menyn **Inställningar för IoT Edge.**

5. Ange valfria inställningar
    1. **Händelsebeställning**. Du kan konfigurera oordningsprincip i portalen. Dokumentation finns [här](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **När du är**på den här orten. Ange internaliseringsformat.



> [!Note]
> När en distribution skapas exporterar ASA jobbdefinitionen till en lagringsbehållare. Den här jobbdefinitionen förblir densamma under en distribution. Om du vill uppdatera ett jobb som körs på kanten måste du därför redigera jobbet i ASA och sedan skapa en ny distribution i IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurera IoT Edge-miljön på enheten/enheten
Edge-jobb kan distribueras på enheter som kör Azure IoT Edge.
För detta måste du följa dessa steg:
- Skapa en iot Hub.
- Installera Docker- och IoT Edge-körningen på dina kantenheter.
- Ställ in dina enheter som **IoT Edge-enheter** i IoT Hub.

De här stegen beskrivs i IoT Edge-dokumentationen för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distribution ASA på din IoT Edge-enhet(er)
##### <a name="add-asa-to-your-deployment"></a>Lägg till ASA i distributionen
- Öppna IoT Hub i Azure-portalen, navigera till **IoT Edge** och klicka på den enhet som du vill inrikta dig på för den här distributionen.
- Välj **Ange moduler**och välj sedan + Lägg **till** och välj Azure Stream **Analytics Module**.
- Välj den prenumeration och det ASA Edge-jobb som du har skapat. Klicka på Spara.
![Lägg till ASA-modul i distributionen](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Under det här steget skapar ASA en mapp med namnet "EdgeJobs" i lagringsbehållaren (om den inte redan finns). För varje distribution skapas en ny undermapp i mappen "EdgeJobs".
> När du distribuerar jobbet till IoT Edge-enheter skapar ASA en SAS -signatur (Shared Access Signature) för jobbdefinitionsfilen. SAS-nyckeln överförs säkert till IoT Edge-enheterna med hjälp av enhetstvilling. Utgången av denna nyckel är tre år från dagen för dess skapelse. När du uppdaterar ett IoT Edge-jobb ändras SAS, men avbildningsversionen ändras inte. När du **har uppdaterat**följer du distributionsarbetsflödet och ett uppdateringsmeddelande loggas på enheten.


Mer information om IoT Edge-distributioner finns på [den här sidan](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurera vägar
IoT Edge är ett sätt att deklarativt dirigera meddelanden mellan moduler och mellan moduler och IoT Hub. Den fullständiga syntaxen beskrivs [här](https://docs.microsoft.com/azure/iot-edge/module-composition).
Namnen på de indata och utdata som skapats i ASA-jobbet kan användas som slutpunkter för routning.  

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
I det här exemplet visas vägarna för det scenario som beskrivs i följande bild. Den innehåller ett kantjobb som kallas "**ASA**", med en ingång som heter "**temperatur**" och en utdata med namnet "**alert**".
![Exempel på diagram för meddelanderoutning](media/stream-analytics-edge/edge-message-routing-example.png)

Det här exemplet definierar följande vägar:
- Varje meddelande från **tempSensor** skickas till modulen **ASA** till ingången med namnet **temperatur**,
- Alla utgångar av **ASA-modulen** skickas till IoT Hub som är länkad till den här enheten ($upstream),
- Alla utgångar av **ASA-modulen** skickas till **kontrollslutpunkten** för **tempSensor**.


## <a name="technical-information"></a>Teknisk information
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuella begränsningar för IoT Edge-jobb jämfört med molnjobb
Målet är att ha paritet mellan IoT Edge-jobb och molnjobb. De flesta SQL-frågespråkfunktioner stöds, vilket gör det möjligt att köra samma logik på både molnet och IoT Edge.
Följande funktioner stöds dock ännu inte för kantjobb:
* Användardefinierade funktioner (UDF) i JavaScript. UDF är tillgängliga i [C# för IoT Edge-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (förhandsversion).
* Användardefinierade aggregat (UDA).
* Azure ML-funktioner.
* Använda mer än 14 aggregat i ett enda steg.
* AVRO-format för in- och utdata. För närvarande stöds endast CSV och JSON.
* Följande SQL-operatorer:
    * PARTITION AV
    * GetMetadataPropertyValue
* Policy för sen ankomst

### <a name="runtime-and-hardware-requirements"></a>Körnings- och maskinvarukrav
Om du vill köra ASA på IoT Edge behöver du enheter som kan köra [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA och Azure IoT Edge använder **Docker-behållare** för att tillhandahålla en bärbar lösning som körs på flera värdoperativsystem (Windows, Linux).

ASA på IoT Edge görs tillgängligt som Windows- och Linux-avbildningar, som körs på både x86-64- eller ARM-arkitekturer (Advanced RISC Machines). 


### <a name="input-and-output"></a>Ingång och utgång
#### <a name="input-and-output-streams"></a>In- och utdataströmmar
ASA Edge-jobb kan hämta in- och utdata från andra moduler som körs på IoT Edge-enheter. Om du vill ansluta från och till specifika moduler kan du ställa in routningskonfigurationen vid distributionen. Mer information beskrivs [i dokumentationen för IoT Edge-modulens sammansättning](https://docs.microsoft.com/azure/iot-edge/module-composition).

För både in- och utdata stöds CSV- och JSON-format.

För varje indata- och utdataström som du skapar i ASA-jobbet skapas en motsvarande slutpunkt på den distribuerade modulen. Dessa slutpunkter kan användas i distributionens vägar.

För närvarande är edgehubben endast de enda som stöds. Referensinmatning stöder referensfiltyp. Andra utdata kan nås med hjälp av ett molnjobb nedströms. Ett Stream Analytics-jobb som finns i Edge skickar till exempel utdata till Edge Hub, som sedan kan skicka utdata till IoT Hub. Du kan använda ett andra moln värd Azure Stream Analytics jobb med indata från IoT Hub och utdata till Power BI eller annan utdatatyp.



##### <a name="reference-data"></a>Referensdata
Referensdata (kallas även en uppslagstabell) är en begränsad datauppsättning som är statisk eller långsam förändras i naturen. Den används för att utföra en sökning eller för att korrelera med dataströmmen. Om du vill använda referensdata i ditt Azure Stream Analytics-jobb använder du vanligtvis en [reference data JOIN](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) i din fråga. Mer information finns i [Använda referensdata för uppslag i Stream Analytics](stream-analytics-use-reference-data.md).

Endast lokala referensdata stöds. När ett jobb distribueras till IoT Edge-enhet läses referensdata in från den användardefinierade filsökvägen.

Så här skapar du ett jobb med referensdata på Edge:

1. Skapa en ny indata för jobbet.

2. Välj **Referensdata** som **källtyp**.

3. Ha en referensdatafil klar på enheten. För en Windows-behållare placerar du referensdatafilen på den lokala enheten och delar den lokala enheten med Docker-behållaren. Skapa en Docker-volym för en Linux-behållare och fylla i datafilen till volymen.

4. Ange sökvägen till filen. Använd den absoluta sökvägen för Windows `E:\<PathToFile>\v1.csv`Host OS och Windows-behållaren: . För en Windows Host OS- och Linux-behållare eller en Linux `<VolumeName>/file1.txt`OS- och Linux-behållare använder du sökvägen i volymen: .

![Ny referensdatainmatning för Azure Stream Analytics-jobb på IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referensdata för IoT Edge-uppdateringen utlöses av en distribution. När ASA-modulen har utlösts väljer den uppdaterade data utan att stoppa jobbet som körs.

Det finns två sätt att uppdatera referensdata:
* Uppdatera referensdatasökvägen i ditt ASA-jobb från Azure-portalen.
* Uppdatera IoT Edge-distributionen.

## <a name="license-and-third-party-notices"></a>Meddelanden om licens och tredje part
* [Azure Stream Analytics på IoT Edge-licens](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Meddelande från tredje part för Azure Stream Analytics på IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Avbildningsinformation för Azure Stream Analytics-modul 

Denna versionsinformation uppdaterades senast 2019-06-27:

- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - basbild: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Plattform:
      - arkitektur: amd64
      - os: Linux
  
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - basbild: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Plattform:
      - arkitektur: arm
      - os: Linux
  
- Bild: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - basbild: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Plattform:
      - arkitektur: amd64
      - os: fönster
      
      
## <a name="get-help"></a>Få hjälp
Om du vill ha mer hjälp kan du prova [Azure Stream Analytics-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA på IoT Edge handledning](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Utveckla Stream Analytics Edge-jobb med Visual Studio-verktyg](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementera CI/CD för Stream Analytics med API:er](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
