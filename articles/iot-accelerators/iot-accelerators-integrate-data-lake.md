---
title: Strömma data från fjärrövervakning till DataSjölag - Azure | Microsoft-dokument
description: Lär dig hur du integrerar fjärrövervakningslösningen med Azure Data Lake Store med ett Azure Stream Analytics-jobb.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889242"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrera fjärrövervakningslösningen med Azure Data Lake Store

Du kan ha avancerade analyskrav utöver vad som erbjuds i lösningen för fjärrövervakning. Azure Data Lake Store är perfekt för det här programmet eftersom det kan lagra data från massiva och varierande datauppsättningar samt integrera med Azure Data Lake Analytics för att tillhandahålla analyser på begäran.

I det här inloppet använder du ett Azure Stream Analytics-jobb för att strömma data från IoT-hubben i fjärrövervakningslösningen till ett Azure Data Lake Store.

## <a name="prerequisites"></a>Krav

För att slutföra detta sätt att göra det behöver du följande:

* [Distribuera lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md).
  * Remote Monitoring-lösningen distribuerar IoT-hubben och Azure Stream Analytics-jobbet som används i den här artikeln till din Azure-prenumeration.
* [Distribuera ett Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Datasjöarkivet bör distribueras till samma region som fjärrövervakningslösningen.
  * [Skapa en mapp](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) med namnet "streaming" i ditt konto.

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Skapa en dedikerad konsumentgrupp i IoT-hubben i din lösning för fjärrövervakning. Detta kommer att användas av Stream Analytics-jobbet för att strömma data till ditt DataSjölager.

> [!NOTE]
> Konsumentgrupper används av program för att hämta data från Azure IoT Hub. Du bör skapa en ny konsumentgrupp för var femte output konsumenter. Du kan skapa upp till 32 konsumentgrupper.

1. Logga in på Azure Portal.

1. Klicka på knappen **Cloud Shell** i Azure-portalen.

    ![Ikon för start av portal](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Kör det här kommandot för att skapa en ny konsumentgrupp:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Använd resursgruppen och IoT-hubbnamnen från fjärrövervakningslösningen.

## <a name="create-stream-analytics-job"></a>Skapa stream analytics-jobb

Skapa ett Azure Stream Analytics-jobb för att strömma data från din IoT-hubb till ditt Azure Data Lake-arkiv.

1. Klicka på **Skapa en resurs,** välj Sakernas Internet på Marketplace och klicka på **Stream Analytics-jobb**.

    ![Nytt streamanalysjobb](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Ange ett jobbnamn och välj lämplig prenumerations- och resursgrupp.

1. Välj en plats i närheten eller i samma region som datasjöarkivet. Här använder vi Östra USA.

1. Se till att lämna värdmiljön som **standardmoln**.

1. Klicka på **Skapa**.

    ![Skapa stream analytics-jobb](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurera stream analytics-jobbet

1. Gå till **Stream Analytics-jobbet** i resursgruppen för lösningsresurs för fjärrövervakning.

1. Klicka på **Ingångar**på sidan Översikt .

    ![Översiktssida](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klicka på **Lägg till indata för stream** och välj **IoT Hub** i listrutan.

    ![Lägg till indata](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Ange ett indataalias för **IoTHub**på fliken Ny inmatning .

1. Välj den konsumentgrupp som du skapade tidigare i listrutan Konsumentgrupp. Här använder vi **streamanalyticsjob**.

    ![Välj indata](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klicka på **Spara**.

1. Klicka på **Utdata**på sidan Översikt .

    ![Lägg till datasjöarkiv](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klicka på **Lägg till** och välj **Datasjölagring** i listrutan.

    ![Lägg till utdata](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Ange ett utdataalias för **DataLakeStore**på fliken Ny utdata .

1. Välj det DataSjölagringskonto som du skapade i tidigare steg och ange mappstruktur för att strömma data till arkivet.

1. Ange **/streaming/{date}/{time}** i fältet Datumformat. Lämna standardformatet Datum för YYYY/MM/DD och Tidsformatet för HH.

    ![Ange mappstruktur](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klicka på **Auktorisera**.

    Du måste auktorisera med Data Lake Store för att ge Stream analytics-jobbet skrivbehörighet till filsystemet.

    ![Auktorisera Stream Analytics till DataSjö store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Du kommer att se en popup och när popup stänger Authorize knappen kommer att vara nedtonad efter auktorisering är klar.

    > [!NOTE]
    > Om du ser ett fel i popup-fönstret öppnar du ett nytt webbläsarfönster i inkognitoläge och försöker igen.

1. Klicka på **Spara**.

## <a name="edit-the-stream-analytics-query"></a>Redigera Frågan om Stream Analytics

Azure Stream Analytics använder ett SQL-liknande frågespråk för att ange en indatakälla som strömmar data, omvandlar dessa data efter behov och utdata till en mängd olika lagrings- eller bearbetningsmål.

1. Klicka på Redigera **fråga**på fliken Översikt .

    ![Redigera fråga](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. I Frågeredigeraren ersätter du platshållarna [YourOutputAlias] och [YourInputAlias] med de värden som du definierade tidigare.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Fråga om strömma analytics](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klicka på **Spara**.
1. Klicka på **Ja** om du vill acceptera ändringarna.

## <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

1. Klicka på **Start**på fliken Översikt .

    ![Starta streamanalysjobb](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Klicka på **Anpassad**på fliken Startjobb .

1. Ställ in anpassad tid för att gå tillbaka några timmar för att hämta data från när enheten har börjat strömma.

1. Klicka på **Starta**.

    ![Välj anpassat datum](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Vänta tills jobbet går till körläge, om du ser fel som det kan vara från din fråga, kontrollera att syntaxen är korrekt.

    ![Jobb som körs](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Streamingjobbet börjar läsa data från din IoT Hub och lagra data i datasjöarkivet. Det kan ta några minuter innan data börjar visas i DataSjöarkivet.

## <a name="explore-the-streaming-data"></a>Utforska strömmande data

1. Gå till din Data Lake Store.

1. Klicka på **Datautforskaren**på fliken Översikt.

1. Öka detaljnivån i datautforskaren till mappen **/streaming.** Du kommer att se mappar som skapats med YYYY /MM/DD/HH-format.

    ![Utforska strömmande data](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Du kommer att se json filer med en fil per timme.

    ![Utforska strömmande data](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Efterföljande moment

Azure Data Lake Analytics kan användas för att utföra stordataanalys på dina datauppsättningar för DataSjölager. Läs mer om [Dokumentationen för DataSjöanalys](https://docs.microsoft.com/azure/data-lake-analytics).
