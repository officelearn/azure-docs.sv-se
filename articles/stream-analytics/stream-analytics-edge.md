---
title: "Azure Stream Analytics IoT kant (förhandsgranskning)"
description: "Skapa edge jobb i Azure Stream Analytics och distribuera dem till enheter igång Azure IoT kant."
keywords: "dataströmmen, iot, kant"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/16/2017
ms.author: jeanb
ms.openlocfilehash: f1ff8d6f64a04ab03c8170fd2b6a7c881227da2e
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics IoT kant (förhandsgranskning)

> [!IMPORTANT]
> Den här funktionen är i förhandsgranskningen. Vi rekommenderar inte används i produktionen.
 
Azure Stream Analytics (ASA) på IoT kant ger utvecklare möjlighet att distribuera nära realtid analytiska intelligence närmare till IoT-enheter så att de kan låsa upp det fulla värdet av data som genereras av enheten. Har utformats för låg latens, återhämtning, effektiv användning av nätverksbandbredd och efterlevnad och kan företag nu distribuera logik nära industriell verksamhet och kompletterar analyser av Stordata i molnet.  
Azure Stream Analytics IoT kant som körs inom den [Azure IoT kant](https://azure.microsoft.com/campaigns/iot-edge/) framework. När jobbet skapas i ASA, deploym och hantera ASA jobb med hjälp av IoT-hubb.
Den här funktionen är i förhandsvisning, om du har frågor eller feedback kan du använda [undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) att kontakta produktteamet. 

## <a name="scenarios"></a>Scenarier
![Översiktsdiagram](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Låg latens kommando- och**: till exempel produktion säkerhetssystem måste svara på användningsdata med mycket låg latens. Du kan analysera sensor data i nära realtid och utfärda kommandon när du identifiera avvikelser att stoppa en dator eller utlösa aviseringar med ASA på IoT kant.
*   **Begränsad anslutning till molnet**: Mission kritiska system, till exempel remote utvinningsmodellen utrustning, anslutna fartyg eller offshore borra måste du analysera och ta hänsyn till data, även om molnet anslutningen bryts. Din strömmande logik körs oberoende av nätverksanslutningen med ASA, och du kan välja som du skickar till molnet för vidare bearbetning eller lagring.
* **Begränsad bandbredd**: mängden data som produceras av jet motorer eller anslutna bilar kan vara så stor data måste vara filtrerade eller bearbetas före innan den skickas till molnet. Använda ASA kan du filtrera eller aggregera data som måste skickas till molnet.
* **Kompatibilitet**: regelefterlevnad kan kräva att vissa data lokalt anonym eller aggregeras innan den skickas till molnet.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-jobb i Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Vad är ett ”kant” jobb?

ASA Edge jobb kör som-modulerna i [Azure IoT kant runtime](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). De är består av två delar:
1.  En del i molnet som ansvarar för jobbdefinitionen: användare definiera indata, utdata, fråga och andra inställningar (oordnade händelser, etc.) i molnet.
2.  ASA på IoT kant-modul som körs lokalt. Det innehåller ASA komplexa händelsebearbetning-motorn och tar emot jobbdefinitionen från molnet. 

ASA använder IoT-hubb för att distribuera edge jobb till enheter. Mer information om [IoT kant distributionen visas här](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Edge-jobb](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Instruktioner för installation
De övergripande stegen beskrivs i följande tabell. Mer information ges i följande avsnitt.
|      |Steg   | Plats     | Anteckningar   |
| ---   | ---   | ---       |  ---      |
| 1   | **Skapa ett ASA edge-jobb**   | Azure Portal      |  Skapa ett nytt jobb väljer **kant** som **värdmiljön**. <br> Dessa jobb från molnet är skapat/hanteras och köras på din egen IoT-gränsenheterna.     |
| 2   | **Skapa en lagringsbehållare**   | Azure Portal       | Behållare för lagring för att spara din jobbdefinitionen där de kan nås av IoT-enheter. <br>  Du kan återanvända eventuella befintliga lagringsbehållaren.     |
| 3   | **Konfigurera din miljö för IoT kanten på dina enheter**   | Enheter      | Instruktioner för [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuera ASA på IoT Edge-enheter**   | Azure Portal      |  ASA jobbdefinitionen exporteras till vilken lagringsbehållare som du skapade tidigare.       |
Du kan följa [stegvisa självstudierna](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) att distribuera din första ASA jobb på IoT kant. Följande videoklipp hjälper dig att förstå processen för att köra ett Stream Analytics-jobb på en insticksenhet för IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]



#### <a name="create-an-asa-edge-job"></a>Skapa ett ASA Edge-jobb
1. Skapa en ny ”Stream Analytics-jobbet” från Azure-portalen. [Direktlänk så här skapar du en ny ASA här](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

> [!Note]
> Du kan skapa Edge jobb i alla regioner som stöds av ASA, **förutom i ”västra USA 2” region**.
> Den här begränsningen tas bort inom kort.

2. På skärmen skapa väljer **kant** som **värdmiljön** (se följande bild) ![skapa jobb](media/stream-analytics-edge/ASAEdge_create.png)
3. Jobbdefinitionen
    1. **Definiera inkommande Stream(s)**. Definiera en eller flera inkommande dataströmmar för jobbet.
    2. Definiera referensdata (valfritt).
    3. **Definiera utdata Stream(s)**. Definiera en eller flera utdata dataströmmar för jobbet. 
    4. **Definiera fråga**. Definiera ASA frågan i molnet med hjälp av infogade editor. Kompilatorn kontrollerar automatiskt den syntax som aktiverats för ASA kant. Du kan också testa frågan genom att ladda upp exempeldata. 
4. Ange valfria inställningar
    1. **Händelsen ordning**. Du kan konfigurera principen för out-ordning i portalen. Dokumentation för [här](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Språk**. Ange internalization format.


#### <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
En lagringsbehållare krävs för att exportera ASA kompilerad fråga och jobbkonfigurationen som. Den används för att konfigurera ASA Docker-avbildningen med specifika frågan. 
1. Följ [instruktionerna](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) att skapa ett lagringskonto från Azure-portalen. Du kan behålla alla standardalternativ att använda det här kontot med ASA.
2. Skapa en behållare för blob storage i det nyligen skapade lagringskontot:
    1. Klicka på ”BLOB”, sedan ”+ behållare”. 
    2. Ange ett namn och behålla behållaren som ”privat”


> [!Note]
> När en distribution har skapats, exporterar ASA jobbdefinitionen till en lagringsbehållare. Den här jobbdefinitionen förblir densamma under för en distribution. Följaktligen om du vill uppdatera ett jobb som körs på kanten måste redigera jobbet i ASA och sedan skapa en ny distribution i IoT-hubb.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Konfigurera din miljö för IoT kanten på dina enheter
Edge-jobb kan distribueras på enheter som kör Azure IoT kant.
För att göra detta måste du följa dessa steg:
- Skapa en Iot-hubb;
- Installera Docker- och IoT kanten runtime på dina enheter edge;
- Ange dina enheter som ”IoT kant enheter” i IoT-hubb.

Dessa steg beskrivs i dokumentationen för IoT kant [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) eller [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distributionen ASA på IoT Edge-enheter
##### <a name="add-asa-to-your-deployment"></a>Lägg till ASA för din distribution
- Öppna IoT-hubb i Azure-portalen, navigera till IoT kant Explorer och öppna bladet för din enhet.
- Välj **ange moduler**och välj **Import Azure Service IoT kant Module**.
- Välj prenumerationen och jobbet ASA kant som du skapade. Välj ditt lagringskonto. Klicka på Spara.
![Lägg till ASA modul i distributionen](media/stream-analytics-edge/set_module.png)


> [!Note]
> Det här steget ASA begär åtkomst till den valda lagringsbehållaren och skapar sedan en mapp med namnet ”EdgeJobs”. För varje distribution skapas en ny undermapp i mappen ”EdgeJobs”.
> För att distribuera projektet till gränsenheterna skapar ASA en signatur för delad åtkomst (SAS) för definitionsfilen för jobbet. SAS-nyckeln överförs på ett säkert sätt till IoT gränsenheterna med dubbla för enheten. Upphör att gälla för den här nyckeln är tre år från dag då skapades.


Mer information om IoT kant-distributioner finns i [den här sidan](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurera vägar
IoT-gräns är ett sätt att deklarativt vidarebefordra meddelanden mellan moduler och mellan moduler och IoT-hubb. Den fullständiga syntaxen beskrivs [här](https://docs.microsoft.com/azure/iot-edge/module-composition).
Namn på indata och utdata som skapats i ASA jobbet kan användas som slutpunkter för routning.  

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
Det här exemplet visar vägar i det scenario som beskrivs i följande bild. Den innehåller en kant jobb som kallas ”**ASA**”, med indata med namnet ”**temperatur**” och utdata med namnet ”**avisering**”.
![Exempel på routning](media/stream-analytics-edge/RoutingExample.png)

Det här exemplet definierar följande rutter:
- Alla meddelanden från den **tempSensor** skickas till modulen med namnet **ASA** inkommande med namnet **temperatur**,
- Alla utdata för **ASA** modulen skickas till IoT-hubb som är kopplad till den här enheten (uppströms$)
- Alla utdata för **ASA** modulen skickas till den **kontrollen** slutpunkten för den **tempSensor**.


## <a name="technical-information"></a>Teknisk information
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Aktuella begränsningar för edge projekt jämfört med molntjänster jobb
Målet är att paritet mellan kant-jobb och molnet jobb. De flesta av funktionerna i vår SQL-frågespråket stöds redan.
Men följande funktioner stöds inte ännu för edge jobb:
* Användardefinierade funktioner (UDF) och användardefinierade aggregeringar (UDA).
* Azure ML-funktioner.
* Använder mer än 14 mängder i ett enda steg.
* AVRO-formatet för in-/ utdata. Just nu stöds endast CSV och JSON.
* Komprimering av JSON-indata.
* Följande SQL-operatorer:
    * AnomalyDetection
    * Geospatiala operatorer:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTITIONEN AV
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Runtime-och maskinvarukrav
För att kunna köra ASA IoT kant måste enheter som kan köra [Azure IoT kant](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA och Azure IoT kanten använda **Docker** behållare för att tillhandahålla en bärbar lösning som körs på flera värdar OS (Windows, Linux).

ASA IoT kant görs tillgänglig som Windows- och Linux-bilder, körs på både x86 64 eller ARM arkitekturerna. 


### <a name="input-and-output"></a>Inkommande och utgående
#### <a name="input-and-output-streams"></a>Indata- och utdataströmmar
ASA Edge jobb kan få indata och utdata från andra moduler som körs på enheter som IoT. För att ansluta från och till specifika moduler, kan du ange routningskonfigurationen vid tidpunkten för distribution. Mer information beskrivs på [IoT kant modulen sammansättning dokumentationen](https://docs.microsoft.com/azure/iot-edge/module-composition).

För både indata och utdata stöds CSV och JSON-format.

För varje indata- och utdataström skapar ASA-jobb, skapas en motsvarande slutpunkt på dina distribuerade modulen. Dessa slutpunkter kan användas i vägar för din distribution.



##### <a name="reference-data"></a>Referensdata
Referensdata (även kallat en uppslagstabell) är en begränsad datamängd som är statiska eller saktas ändrar till sin natur. Den används för att utföra en sökning eller korrelera med din dataström. För att använda referensdata i Azure Stream Analytics-jobbet, använder du normalt en [referens Data ansluta](https://msdn.microsoft.com/library/azure/dn949258.aspx) i frågan. Mer information finns i [ASA dokumentationen om referensdata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

För att kunna använda referensdata för ASA Iot kant, måste du följa dessa steg: 
1. Skapa en ny inmatning för jobbet
2. Välj **referensdata** som den **källtypen**.
3. Ange sökvägen till filen. Sökvägen till filen måste vara en **absolut** sökväg på enheten ![referera skapa data](media/stream-analytics-edge/ReferenceData.png)
4. Aktivera **delade enheter** i Docker-konfigurationen och se till att enheten är aktiverad innan du påbörjar distributionen.

Mer information finns i [Docker dokumentation för Windows här](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> För närvarande stöds endast lokala referensdata.




## <a name="license-and-third-party-notices"></a>Licenser och meddelanden om tredje part
* [Azure Stream Analytics för IoT kant preview licens](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Tredjeparts-meddelande om Azure Stream Analytics på IoT kant preview](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Få hjälp
För ytterligare hjälp försök den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure Iot kant](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA på IoT kant självstudiekursen](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Skicka feedback till teamet med den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
