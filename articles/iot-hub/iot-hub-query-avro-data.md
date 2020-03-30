---
title: Fråga Avro-data med hjälp av Azure Data Lake Analytics | Microsoft-dokument
description: Använd egenskaper för meddelandetext för att dirigera enhetstelemetri till Blob-lagring och fråga avro-formatdata som skrivs till Blob-lagring.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605623"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Fråga Avro-data med hjälp av Azure Data Lake Analytics

I den här artikeln beskrivs hur du frågar Avro-data för att effektivt dirigera meddelanden från Azure IoT Hub till Azure-tjänster. [Med meddelanderoutning](iot-hub-devguide-messages-d2c.md) kan du filtrera data med hjälp av omfattande frågor baserat på meddelandeegenskaper, meddelandetext, enhetstvillingtaggar och enhetstvillingegenskaper. Mer information om frågefunktionerna i Meddelanderoutning finns i artikeln om [meddelanderoutningsfrågesyntax .](iot-hub-devguide-routing-query-syntax.md)

Utmaningen har varit att när Azure IoT Hub dirigerar meddelanden till Azure Blob-lagring, skriver IoT Hub som standard innehållet i Avro-format, som har både en egenskap för meddelandetext och en meddelandeegenskap. Avro-formatet används inte för några andra slutpunkter. Även om Avro-formatet är bra för data och meddelandebevarande, är det en utmaning att använda det för att fråga data. I jämförelse är JSON- eller CSV-format mycket enklare för att fråga data. IoT Hub stöder nu att skriva data till Blob-lagring i JSON samt AVRO.

Mer information finns i [Använda Azure Storage som en routningsslutpunkt](iot-hub-devguide-messages-d2c.md#azure-storage).

För att ta itu med icke-relationella stordatabehov och format och övervinna denna utmaning kan du använda många av de stora datamönstren för både transformering och skalningsdata. Ett av mönstren, "pay per query", är Azure Data Lake Analytics, som är i fokus för den här artikeln. Även om du enkelt kan köra frågan i Hadoop eller andra lösningar, är Data Lake Analytics ofta bättre lämpad för den här metoden "betala per fråga".

Det finns en "extractor" för Avro i U-SQL. Mer information finns i [U-SQL Avro exempel](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Fråga och exportera Avro-data till en CSV-fil

I det här avsnittet frågar du Avro-data och exporterar dem till en CSV-fil i Azure Blob-lagring, även om du enkelt kan placera data i andra databaser eller datalager.

1. Konfigurera Azure IoT Hub för att dirigera data till en Azure Blob storage-slutpunkt med hjälp av en egenskap i meddelandetexten för att välja meddelanden.

   ![Avsnittet "Anpassade slutpunkter"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Routningsreglerna](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Mer information om inställningar för vägar och anpassade slutpunkter finns i [Meddelanderoutning för en IoT-hubb](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Kontrollera att enheten har kodning, innehållstyp och nödvändiga data i egenskaperna eller meddelandetexten, enligt produktdokumentationen. När du visar dessa attribut i Enhetsutforskaren, som visas här, kan du kontrollera att de är korrekt inställda.

   ![Fönstret Data i händelsehubben](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Konfigurera en Azure Data Lake Store-instans och en DataSjöanalys-instans. Azure IoT Hub dirigerar inte till en Data Lake Store-instans, men en DataSjöanalys-instans kräver en.

   ![Instanser av DataSjölagring och DataSjöanalys](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. I DataSjöanalys konfigurerar du Azure Blob-lagring som ett extra arkiv, samma Blob-lagring som Azure IoT Hub dirigerar data till.

   ![Fönstret "Datakällor"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Som diskuteras i [U-SQL Avro exempel](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), du behöver fyra DLL-filer. Ladda upp dessa filer till en plats i datasjölagringsinstansen.

   ![Fyra uppladdade DLL-filer](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Skapa ett U-SQL-projekt i Visual Studio.

   ! Skapa ett U-SQL-projekt](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Klistra in innehållet i följande skript i den nyskapade filen. Ändra de tre markerade avsnitten: ditt DataSjöanalyskonto, de associerade DLL-filsökvägarna och rätt sökväg för ditt lagringskonto.

   ![De tre avsnitt som ska ändras](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Den faktiska U-SQL-skript för enkel utdata till en CSV-fil:

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
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Det tog Data Lake Analytics fem minuter att köra följande skript, som var begränsad till 10 analytiska enheter och bearbetade 177 filer. Resultatet visas i CSV-filutdata som visas i följande bild:

    ![Resultat av utdata till CSV-fil](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Utdata konverterade till CSV-fil](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    För att tolka JSON, fortsätt till steg 8.

8. De flesta IoT-meddelanden är i JSON-filformat. Genom att lägga till följande rader kan du tolka meddelandet i en JSON-fil, vilket gör att du kan lägga till WHERE-satser och bara mata ut nödvändiga data.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
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

    Utdata visar en kolumn för `SELECT` varje objekt i kommandot.

    ![Utdata som visar en kolumn för varje objekt](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du frågar Avro-data för att effektivt dirigera meddelanden från Azure IoT Hub till Azure-tjänster.

Exempel på kompletta heltäckande lösningar som använder IoT Hub finns i dokumentationen för [Azure IoT Solution Accelerators](/azure/iot-accelerators).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [utvecklarhandboken för IoT Hub](iot-hub-devguide.md).

Mer information om meddelanderoutning i IoT Hub finns i [Skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).
