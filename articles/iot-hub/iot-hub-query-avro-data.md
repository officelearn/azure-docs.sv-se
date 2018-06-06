---
title: Fråga Avro-data med hjälp av Azure Data Lake Analytics | Microsoft Docs
description: Använd brödtext meddelandeegenskaperna till väg enhetstelemetrin till blob storage och fråga Avro Formatdata skrivs till blob storage.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34727972"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Fråga Avro-data med hjälp av Azure Data Lake Analytics

Den här artikeln handlar om hur man frågan Avro-data för att effektivt vidarebefordra meddelanden från Azure IoT Hub till Azure-tjänster. Följande i blogginlägget –[Azure IoT-hubb routning: nu med routning på meddelandetexten], IoT-hubben har stöd för routning på egenskaper eller meddelandetexten. Se även [routning på meddelandetext][Routing on message bodies]. 

IoT-hubb skriver utmaningen har som när Azure IoT Hub skickar meddelanden till blob storage, innehållet i Avro-format, som har både meddelandetexten och meddelandeegenskaper. Observera att IoT-hubben har bara stöd för skrivning av data för att blob-lagring i formatet Avro-data och det här formatet används inte för andra slutpunkter. Se [när du använder Azure Storage-behållare][When using Azure storage containers]. Avro-formatet är perfekt för meddelande och data bevaras, är det svårt för att fråga efter data. JSON- eller CSV-format är mycket enklare för datafrågor jämförelse.

Om du vill lösa detta problem kan du använda många av stordata mönster för både Omforma och skalning data för att åtgärda icke-relationella stordata behov och format. En av mönster, ett ”betala per fråga” mönster är Azure Data Lake Analytics (ADLA). Det är i fokus i den här artikeln. Även om du kan enkelt köra frågan i Hadoop eller andra lösningar, är ADLA ofta bättre passar för den här metoden ”betala per fråga”. Det finns en ”extraktor” för Avro i U-SQL. Se [U-SQL Avro-exempel].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Fråga efter och exportera Avro-data till en CSV-fil
Avsnittet vägleder dig genom Avro datafrågor och exportera den till en CSV-fil i Azure Blob Storage, även om du kan enkelt placera data i andra databaser eller data lagras.

1. Konfigurera Azure IoT-hubb för att vidarebefordra data till en Azure Blob Storage-slutpunkten med hjälp av en egenskap i meddelandetexten för att välja meddelanden.

    ![Skärmbild för steg 1a][img-query-avro-data-1a]

    ![Skärmbild för steg 1b][img-query-avro-data-1b]

2. Se till att enheten har kodningen content-type och nödvändiga data i egenskaperna eller meddelandetexten som refereras till i produktdokumentationen. När den visas i enheten Explorer (se nedan), kan du kontrollera att attributen är korrekt.

    ![Skärmbild för steg 2][img-query-avro-data-2]

3. Skapa ett Azure Data Lake Store (ADLS) och en Azure Data Lake Analytics-instans. Medan Azure IoT Hub inte dirigerar till ett Azure Data Lake Store, kräver en ADLA.

    ![Skärmbild för steg 3][img-query-avro-data-3]

4. ADLA, konfigurera Azure Blob Storage som store samma Blob-lagring som Azure IoT Hub skickar data till.

    ![Skärmdump i steg 4][img-query-avro-data-4]
 
5. Enligt beskrivningen i [U-SQL Avro-exempel], det finns 4 DLL-filer som behövs.  Överför filerna till en plats i din ADLS.

    ![Skärmdump i steg 5][img-query-avro-data-5] 

6. Skapa ett U-SQL-projekt i Visual Studio
 
    ![Skärmdump i steg 6][img-query-avro-data-6]

7. Kopiera innehållet i följande skript och klistra in den i den nya filen. Ändra de markerade avsnitt 3: kontot ADLA och tillhörande DLL sökvägar rätt sökväg för ditt Lagringskonto.
    
    ![Skärmbild för steg 7 a][img-query-avro-data-7a]

    Faktiska U-SQL-skriptet för enkel utdata till CSV:
    
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

    Kör skriptet nedan ADLA tog 5 minuter när begränsat till 10 analytiska enheter och bearbetas 177 filer, sammanfatta utdata till en CSV-fil.
    
    ![Skärmbild för steg 7b][img-query-avro-data-7b]

    Visa utdata kan se du Avro-innehållet har konverterats till en CSV-fil. Fortsätt till steg 8 om du vill att parsa JSON.
    
    ![Skärmbild för steg 7c][img-query-avro-data-7c]

    
8. De flesta IoT-meddelanden är i JSON-format.  Lägger till följande rader, kan du parsa meddelandet till JSON, så du kan lägga till WHERE-satserna och spara endast nödvändiga data.

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

9. Visa utdata kan se du nu kolumner för varje objekt i select-kommandot. 
    
    ![Skärmbild för steg 8][img-query-avro-data-8]

## <a name="next-steps"></a>Nästa steg
I kursen får du har lärt dig hur man frågar Avro-data för att effektivt vidarebefordra meddelanden från Azure IoT Hub till Azure-tjänster.

Exempel på fullständiga lösningar för slutpunkt till slutpunkt med IoT-hubb finns [Azure IoT-Fjärrövervaknings lösningsaccelerator][lnk-iot-sa-land].

Mer information om hur du utvecklar lösningar med IoT-hubb finns i [Utvecklarhandbok för IoT-hubb].

Läs mer om meddelanderoutning i IoT-hubb i [skicka och ta emot meddelanden med IoT-hubben][lnk-devguide-messaging].

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
[Azure IoT-hubb routning: nu med routning på meddelandetexten]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL Avro-exempel]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Utvecklarhandbok för IoT-hubb]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
