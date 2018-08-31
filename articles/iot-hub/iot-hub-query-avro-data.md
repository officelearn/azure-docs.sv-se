---
title: Fråga Avro-data med hjälp av Azure Data Lake Analytics | Microsoft Docs
description: Använda brödtext meddelandeegenskaper för att dirigera enhetstelemetri till Blob storage och fråga efter data för Avro-format som skrivs till Blob storage.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: a17df39c55b5c02c83e3f0b74a91d7109ddb4d3d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188952"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Fråga Avro-data med hjälp av Azure Data Lake Analytics

Den här artikeln beskrivs hur du frågar Avro-data för att effektivt dirigera meddelanden från Azure IoT Hub till Azure-tjänster. Som vi presenterades i blogginlägget [Azure IoT Hub-meddelande och routning: nu med routning på meddelandetext], IoT-hubb har stöd för routning på egenskaper eller meddelandetexten. Mer information finns i [routning på meddelandekroppen][Routing on message bodies]. 

Utmaningen har som när Azure IoT Hub skickar meddelanden till Azure Blob storage, skriver IoT Hub innehållet i Avro-format, vilket har både ett meddelande brödtext egenskapen och ett meddelande. Skrivdata till Blob storage stöds av IoT Hub i Avro-data-format och det här formatet används inte för andra slutpunkter. Mer information finns i [när du använder Azure Storage-behållare][When using Azure storage containers]. Avro-formatet är perfekt för data och meddelandet konservering, är det svårt att använda den för att köra frågor mot data. Däremot är det mycket enklare för att fråga data med JSON- eller CSV-format.

Du kan använda många av big data-mönster för både omvandling samt skala data för att åtgärda icke-relationella stordata behov och format och lösa denna utmaning. En av mönster ”betala per fråga”, är Azure Data Lake Analytics, vilket är fokus i den här artikeln. Även om du enkelt kan utföra frågan i Hadoop eller andra lösningar är ofta bättre Data Lake Analytics lämpligt för den här metoden för ”betala per fråga”. 

Det finns en ”extraktor” för Avro i U-SQL. Mer information finns i [U-SQL-Avro-exempel].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Fråga efter och exportera Avro-data till en CSV-fil
I det här avsnittet ska du fråga efter data med Avro och exportera den till en CSV-fil i Azure Blob storage, men du kan enkelt placera data i andra databaser eller datalager.

1. Konfigurera Azure IoT Hub att vidarebefordra data till en Azure Blob storage-slutpunkt med hjälp av en egenskap i meddelandetexten och välj meddelanden.

    ![Avsnittet ”anpassade slutpunkter”][img-query-avro-data-1a]

    ![Kommandot vägar][img-query-avro-data-1b]

2. Kontrollera att enheten har kodning, innehållstyp och nödvändiga data i egenskaperna eller meddelandets brödtext som refereras till i produktdokumentationen. När du visar dessa attribut i Device Explorer som visas här kan kontrollera du att de är korrekt inställda.

    ![Fönstret Event Hub-Data][img-query-avro-data-2]

3. Konfigurera en Azure Data Lake Store-instans och en Data Lake Analytics-instans. Azure IoT Hub dirigerar inte till en Data Lake Store-instans, men kräver en Data Lake Analytics-instans.

    ![Data Lake Store och Data Lake Analytics-instanser][img-query-avro-data-3]

4. Konfigurera Azure Blob storage som ett ytterligare store, samma Blob storage som Azure IoT Hub skickar data till i Data Lake Analytics.

    ![Panelen ”datakällor”][img-query-avro-data-4]
 
5. Enligt beskrivningen i [U-SQL-Avro-exempel], du behöver fyra DLL-filer. Överför filerna till en plats i din Data Lake Store-instans.

    ![Fyra överförda DLL-filer][img-query-avro-data-5] 

6. Skapa ett U-SQL-projekt i Visual Studio.
 
    ![Skapa ett U-SQL-projekt][img-query-avro-data-6]

7. Klistra in innehållet i följande skript i den nya filen. Ändra tre markerade avsnitt: Data Lake Analytics-kontot och associerade DLL sökvägarna rätt sökväg för ditt lagringskonto.
    
    ![Tre avsnitt som ska ändras][img-query-avro-data-7a]

    Det faktiska U-SQL-skriptet för enkel utdata till en CSV-fil:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Det tog Data Lake Analytics fem minuter att köra följande skript, som var begränsat till 10 analysenheter och behandlas 177 filer. Resultatet visas i utdata för CSV-filen som visas i följande bild:
    
    ![Resultatet av utdata till CSV-fil][img-query-avro-data-7b]

    ![Utdata som konverteras till CSV-fil][img-query-avro-data-7c]

    Om du vill parsa JSON, fortsätter du till steg 8.
    
8. De flesta IoT-meddelanden är i JSON-format. Genom att lägga till följande rader, kan du parsa meddelandet till en JSON-fil som kan lägga till WHERE-satserna och spara endast nödvändiga data.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    Utdata visar en kolumn för varje objekt i den `SELECT` kommando. 
    
    ![Utdata visar en kolumn för varje objekt][img-query-avro-data-8]

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att fråga Avro-data för att effektivt dirigera meddelanden från Azure IoT Hub till Azure-tjänster.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt som använder IoT Hub finns [Azure IoT lösningsacceleratorn för fjärrövervakning][lnk-iot-sa-land].

Mer information om hur du utvecklar lösningar med IoT Hub finns det [utvecklarhandboken för IoT Hub].

Läs mer om meddelanderoutning i IoT Hub i [skicka och ta emot meddelanden med IoT Hub][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Azure IoT Hub-meddelande och routning: nu med routning på meddelandetext]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL-Avro-exempel]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Utvecklarhandboken för IoT Hub]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
