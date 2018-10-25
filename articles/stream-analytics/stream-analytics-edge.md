---
title: Azure Stream Analytics på IoT Edge (förhandsversion)
description: Skapa edge-jobb i Azure Stream Analytics och distribuera dem till enheter som kör Azure IoT Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 73b594aaabd814108dfce813b53a4ea865336e63
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985071"
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics på IoT Edge (förhandsversion)

> [!IMPORTANT]
> Den här funktionen är en förhandsversion och rekommenderas inte för användning i produktion.
 
Med hjälp av Azure Stream Analytics (ASA) på IoT Edge kan utvecklare distribuera nära realtid analytisk intelligens närmare IoT-enheter så att de kan låsa upp hela värdet av data som genereras av enheten. Azure Stream Analytics har utformats för låg latens, elasticitet och effektiv användning av bandbredd och efterlevnad. Företag kan nu distribuera logik nära industriell verksamhet och komplettera analyser av Stordata i molnet.  

Azure Stream Analytics på IoT Edge som körs inom den [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework. När jobbet har skapats i ASA, kan du distribuera och hantera ASA-jobb med hjälp av IoT Hub. Den här funktionen är en förhandsversion. Om du har några frågor eller feedback kan du använda [undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) kontakta produktteamet. 

## <a name="scenarios"></a>Scenarier
![Översiktsdiagram](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Låg latens kommando och kontroll**: till exempel tillverkar säkerhetssystem måste besvara driftdata med extremt låg latens. Du kan analysera sensor data i realtid och skicka kommandon när du upptäcker avvikelser för att stoppa en virtuell dator eller utlösa aviseringar med ASA på IoT Edge.
*   **Begränsad anslutning till molnet**: uppdragskritiska kritiska system som fjärranslutna utvinningsstrukturen utrustning, anslutna fartyg eller öppet hav Detaljgranskning måste du analysera och reagera på data även om molnet anslutningen bryts. Strömmande logik körs oberoende av nätverksanslutningen med ASA, och du kan välja vad du skickar till molnet för vidare bearbetning eller lagring.
* **Begränsad bandbredd**: mängden data som produceras av jet motorer eller anslutna bilar kan vara så stora data måste vara filtreras eller bearbetas före innan de skickas till molnet. Med ASA, kan du filtrera eller aggregera data som måste skickas till molnet.
* **Efterlevnad**: regelefterlevnad kan kräva att vissa data lokalt anonyma eller aggregeras innan den skickas till molnet.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-jobb i Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Vad är ett ”edge”-jobb?

ASA Edge-jobb kör som-modulerna i [Azure IoT Edge-körningen](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). De är består av två delar:
1.  En del i molnet som ansvarar för jobbdefinitionen: användare definiera indata, utdata, fråga och andra inställningar (oordnade händelser, osv.) i molnet.
2.  ASA på IoT Edge-modul som körs lokalt. Det innehåller ASA komplex händelsebearbetning-motorn och tar emot jobbdefinitionen från molnet. 

ASA använder IoT Hub för att distribuera edge-jobb till enheter. Mer information om [IoT Edge-distribution finns här](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Edge-jobb](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Installationsinstruktioner
De övergripande stegen beskrivs i följande tabell. Mer information ges i följande avsnitt.
|      |Steg   | Plats     | Anteckningar   |
| ---   | ---   | ---       |  ---      |
| 1   | **Skapa en lagringsbehållare**   | Azure Portal       | Storage-behållare används för att spara din jobbdefinition där de kan användas av dina IoT-enheter. <br>  Du kan återanvända eventuella befintliga lagringsbehållare.     |
| 2   | **Skapa ett ASA edge-jobb**   | Azure Portal      |  Skapa ett nytt jobb väljer **Edge** som **värdmiljö**. <br> Dessa jobb finns skapas/hanteras från molnet och kör på dina egna IoT Edge-enheter.     |
| 3   | **Konfigurera din IoT Edge-miljö på dina enheter**   | Enheter      | Anvisningar om hur [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuera ASA på IoT Edge-enheter**   | Azure Portal      |  ASA jobbdefinitionen exporteras till den lagringsbehållare som skapades tidigare.       |
Du kan följa [den här stegvisa självstudien](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) att distribuera ditt första ASA-jobb på IoT Edge. Följande videoklipp hjälper dig att förstå processen för att köra ett Stream Analytics-jobb på en IoT edge-enhet:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
En lagringsbehållare krävs för att exportera ASA kompileras frågan och jobbkonfigurationen. Den används för att konfigurera ASA Docker-avbildning med din specifika fråga. 
1. Följ [instruktionerna](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att skapa ett lagringskonto från Azure-portalen. Du kan behålla alla standardalternativ för att använda det här kontot med ASA.
2. Skapa en blob storage-behållare i det nyligen skapade lagringskontot:
    1. Klicka på **Blobar**, sedan **+ behållare**. 
    2. Ange ett namn och hålla behållaren som **privata**.

#### <a name="create-an-asa-edge-job"></a>Skapa ett ASA Edge-jobb
> [!Note]
> Den här självstudien fokuserar på skapande av ASA-jobb med hjälp av Azure portal. Du kan också [använder Visual Studio-plugin-programmet för att skapa ett ASA Edge-jobb](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Skapa en ny ”Stream Analytics-jobbet” från Azure-portalen. [Direktlänk till att skapa ett nytt ASA-jobb här](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Skapa-skärmen väljer du **Edge** som **värdmiljö** (se nedan) ![skapa jobb](media/stream-analytics-edge/ASAEdge_create.png)
3. Jobbdefinition
    1. **Definiera inkommande Stream(s)**. Definiera en eller flera inkommande dataströmmar för jobbet.
    2. Definiera referensdata (valfritt).
    3. **Definiera utdata Stream(s)**. Definiera en eller flera strömmar av utdata för jobbet. 
    4. **Definiera fråga**. Definiera ASA-frågan i molnet med hjälp av infogade-redigeraren. Kompilatorn kontrollerar automatiskt den syntax som aktiverats för ASA edge. Du kan också testa frågan genom att ladda upp exempeldata. 
4. Ange information för storage-behållaren i den **IoT Edge-inställningar** menyn.
5. Ange valfria inställningar
    1. **Händelseordning**. Du kan konfigurera princip för out-ordning i portalen. Dokumentation finns [här](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Nationella inställningar**. Ange internalization format.



> [!Note]
> När en distribution har skapats, exporterar ASA jobbdefinitionen till en lagringsbehållare. Den här jobbdefinitionen förblir samma under en distribution. Det betyder om du vill uppdatera ett jobb som körs på gränsen måste redigera jobbet i ASA och skapa en ny distribution i IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurera din IoT Edge-miljö på dina enheter
Edge-jobb kan distribueras på enheter som kör Azure IoT Edge.
För detta måste du följa dessa steg:
- Skapa en Iot-hubb.
- Installera Docker och IoT Edge-körningen på dina gränsenheter.
- Ange dina enheter som **IoT Edge-enheter** i IoT Hub.

De här stegen beskrivs i dokumentationen för IoT Edge [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distribution ASA på IoT Edge-enheter
##### <a name="add-asa-to-your-deployment"></a>Lägg till ASA för din distribution
- Öppna IoT Hub i Azure-portalen, gå till **IoT Edge** och klicka på den enhet som du vill använda för den här distributionen.
- Välj **ange moduler**och välj sedan **+ Lägg till** och välj **Azure Stream Analytics-modulen**.
- Välj prenumerationen och ASA Edge-jobb som du skapade. Klicka på Spara.
![Lägg till ASA-modul i distributionen](media/stream-analytics-edge/set_module.png)


> [!Note]
> Det här steget skapar ASA en mapp med namnet ”EdgeJobs” i storage-behållare (om den inte redan finns). För varje distribution skapas en ny undermapp i mappen ”EdgeJobs”.
> För att distribuera ditt jobb till gränsenheter skapar ASA en signatur för delad åtkomst (SAS) för definitionsfilen för jobbet. SAS-nyckeln skickas på ett säkert sätt till IoT Edge-enheter med hjälp av enhetstvillingen. Förfallodatum för den här nyckeln är tre år från dag då skapades.


Mer information om IoT Edge-distributioner finns i så här [den här sidan](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurera vägar
IoT Edge är ett sätt att deklarativt dirigera meddelanden mellan moduler och mellan moduler och IoT Hub. Den fullständiga syntaxen beskrivs [här](https://docs.microsoft.com/azure/iot-edge/module-composition).
Namnen på indata och utdata som skapats i ASA-jobbet kan användas som slutpunkter för routning.  

###### <a name="example"></a>Exempel
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Det här exemplet visar vägar i det scenario som beskrivs i följande bild. Den innehåller en edge-jobb som heter ”**ASA**”, med indata med namnet ”**temperatur**” och utdata med namnet ”**avisering**”.
![Exempel på routning](media/stream-analytics-edge/RoutingExample.png)

Det här exemplet definierar följande vägar:
- Alla meddelanden från den **tempSensor** skickas till modulen med namnet **ASA** till indata med namnet **temperatur**,
- Alla utdata för **ASA** modulen skickas till IoT-hubben som är länkad till den här enheten (uppströms$)
- Alla utdata för **ASA** modulen skickas till den **kontroll** slutpunkten för den **tempSensor**.


## <a name="technical-information"></a>Teknisk information
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Aktuella begränsningar för edge-jobb jämfört med molnjobb
Målet är att ha paritet mellan edge-jobb och jobb i molnet. De flesta SQL query language-funktioner stöds redan.
Men de följande funktionerna stöds inte ännu för edge-jobb:
* Användardefinierade funktioner (UDF) och användardefinierade aggregeringar (UDA).
* Azure ML-funktioner.
* Med hjälp av mer än 14 aggregat i ett enda steg.
* AVRO-format för indata/utdata. För närvarande stöds endast CSV och JSON.
* Följande SQL-operatorer:
    * AnomalyDetection
    * Geospatial operatorer:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Runtime-och maskinvarukrav
Om du vill köra ASA på IoT Edge, behöver du enheter som kan köra [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA och Azure IoT Edge använder **Docker** behållare för att tillhandahålla en bärbar lösning som körs på flera värd-operativsystem (Windows, Linux).

ASA på IoT Edge är tillgänglig som Windows och Linux-avbildningar, som körs på både x86 64- eller Azure Resource Manager-arkitekturer. 


### <a name="input-and-output"></a>Indata och utdata
#### <a name="input-and-output-streams"></a>Indata- och utdataströmmar
ASA Edge-jobb kan få indata och utdata från andra moduler som körs på IoT Edge-enheter. För att ansluta från och till specifika moduler, kan du ange routningskonfigurationen vid tidpunkten för distribution. Mer information beskrivs på [i dokumentationen för IoT Edge-modulen sammansättning](https://docs.microsoft.com/azure/iot-edge/module-composition).

CSV och JSON-format stöds för både indata och utdata.

För varje indatakort och utdataströmmen som du skapar i ASA-jobbet, skapas en motsvarande slutpunkt på din distribuerade modul. Dessa slutpunkter kan användas i vägar för din distribution.

AT finns, det går endast att strömindata och stream utdatatyper är Edge Hub. Referera till inkommande stöder referenstyp för filen. Andra utdata kan nås på ett molnjobb nedströms. Till exempel skickar ett Stream Analytics-jobb i Edge utdata till Edge Hub, som sedan kan skicka utdata till IoT Hub. Du kan använda ett andra molnvärd Azure Stream Analytics-jobb med indata från IoT Hub och utdata till Power BI eller en annan Utdatatyp.



##### <a name="reference-data"></a>Referensdata
Referensdata (även kallat en uppslagstabell) är en begränsad mängd data som är statiska eller långsamma ändrar sin natur. Den används för att utföra en sökning eller att korrelera med din dataström. Att göra använder referensdata i Azure Stream Analytics-jobb kan du vanligtvis använder en [referens Data ansluta](https://msdn.microsoft.com/library/azure/dn949258.aspx) i frågan. Mer information finns i den [ASA-dokumentationen om referensdata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Följ dessa steg om du vill använda referensdata för ASA på Iot Edge: 
1. Skapa en ny indata för jobbet
2. Välj **referensdata** som den **källtyp**.
3. Ange sökvägen till filen. Sökvägen till filen ska vara en **absolut** sökväg på enheten ![referera till skapande av data](media/stream-analytics-edge/ReferenceData.png)
4. Aktivera **delade enheter** i Docker-konfigurationen och se till att enheten är aktiverad innan du påbörjar distributionen.

Mer information finns i [Docker-dokumentation för Windows här](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> För tillfället stöds endast lokala referensdata.




## <a name="license-and-third-party-notices"></a>Licens och meddelanden från tredje part
* [Azure Stream Analytics på IoT Edge förhandsversionen licensen](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Tredjepartsnotis för Azure Stream Analytics i förhandsversionen av IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Få hjälp
För mer hjälp kan du prova den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA på IoT Edge-självstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Skicka feedback till teamet med den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Utveckla Stream Analytics Edge-jobb med hjälp av Visual Studio-verktyg](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
