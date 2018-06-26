---
title: Fråga Avro-data med hjälp av Azure Data Lake Analytics | Microsoft Docs
description: Använd brödtext meddelandeegenskaper att vidarebefordra enhetstelemetrin till Blob storage och fråga format Avro-data som skrivs till Blob storage.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 08aed809184cbb65d632e1fb6f4b9bd25747e349
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751082"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Fråga Avro-data med hjälp av Azure Data Lake Analytics

Den här artikeln beskriver hur man frågar Avro-data för att effektivt routa meddelanden från Azure IoT Hub till Azure-tjänster. Som vi presenterade i blogginlägget [Azure IoT-hubb routning: nu med routning på meddelandetexten], IoT-hubben har stöd för routning på egenskaper eller meddelandetexten. Mer information finns i [routning på meddelandetext][Routing on message bodies]. 

IoT-hubb skriver utmaningen har som när Azure IoT Hub skickar meddelanden till Azure Blob storage, innehållet i Avro-formatet som har en meddelandeegenskap brödtext och en meddelandeegenskap. IoT-hubb stöder skriva data till Blob storage endast i formatet Avro-data och det här formatet används inte för andra slutpunkter. Mer information finns i [när du använder Azure Storage-behållare][When using Azure storage containers]. Även om Avro-formatet är bra för förvaring av data och meddelanden, är det svårt att använda den för att fråga efter data. JSON- eller CSV-format är mycket enklare för datafrågor jämförelse.

Du kan använda för både Omforma och skalning data för att åtgärda icke-relationella stort databehov och format och lösa denna utmaning har, många av stordata mönster. En av mönster ”betala per fråga”, är Azure Data Lake Analytics, som har fokus i den här artikeln. Även om du kan enkelt köra frågan i Hadoop eller andra lösningar, är Data Lake Analytics ofta bättre passar för den här metoden ”betala per fråga”. 

Det finns en ”extraktor” för Avro i U-SQL. Mer information finns i [U-SQL Avro-exempel].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Fråga efter och exportera Avro-data till en CSV-fil
I det här avsnittet fråga Avro-data och exportera dem till en CSV-fil i Azure Blob storage, men du kan enkelt placera data i andra databaser eller data lagras.

1. Ställ in Azure IoT Hub att vidarebefordra data till en slutpunkt för Azure Blob storage med hjälp av en egenskap i meddelandetexten för att välja meddelanden.

    ![Avsnittet ”anpassade slutpunkter”][img-query-avro-data-1a]

    ![Kommandot vägar][img-query-avro-data-1b]

2. Kontrollera att enheten har kodning, content-type och nödvändiga data i egenskaperna för eller i meddelandetexten som refereras till i produktdokumentationen. När du visar dessa attribut i enheten Explorer som visas här kan kontrollera du att de är korrekt.

    ![Fönstret hubb händelsedata][img-query-avro-data-2]

3. Skapa en Azure Data Lake Store-instans och en Data Lake Analytics-instans. Azure IoT-hubb dirigerar inte till en Data Lake Store-instans, men en instans av Data Lake Analytics kräver en.

    ![Data Lake Store och Data Lake Analytics-instanser][img-query-avro-data-3]

4. Konfigurera Azure Blob storage som store samma Blob-lagring som Azure IoT Hub skickar data till Data Lake Analytics.

    ![Fönstret ”datakällor”][img-query-avro-data-4]
 
5. Enligt beskrivningen i [U-SQL Avro-exempel], du behöver fyra DLL-filer. Överför filerna till en plats i din Data Lake Store-instans.

    ![Fyra överförda DLL-filer][img-query-avro-data-5] 

6. Skapa ett U-SQL-projekt i Visual Studio.
 
    ![Skapa ett U-SQL-projekt][img-query-avro-data-6]

7. Klistra in innehållet i följande skript i den nya filen. Ändra de tre markerade avsnitten: Data Lake Analytics-kontot och tillhörande DLL sökvägarna rätt sökväg för ditt lagringskonto.
    
    ![Tre avsnitt som ska ändras][img-query-avro-data-7a]

    Faktiska U-SQL-skriptet för enkel utdata till en CSV-fil:
    
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

    Det tog Data Lake Analytics fem minuter att köra följande skript, som var begränsat till 10 analytiska enheter och behandlas 177 filer. Resultatet visas i CSV-filen utdata som visas i följande bild:
    
    ![Resultaten av utdata till CSV-fil][img-query-avro-data-7b]

    ![Utdata konverteras till CSV-fil][img-query-avro-data-7c]

    Fortsätt till steg 8 för att analysera JSON.
    
8. De flesta IoT-meddelanden är i JSON-format. Genom att lägga till följande rader, att parsa meddelandet till en JSON-fil som kan du lägga till WHERE-satserna och spara endast nödvändiga data.

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
I kursen får du har lärt dig hur man frågar Avro-data för att effektivt routa meddelanden från Azure IoT Hub till Azure-tjänster.

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
