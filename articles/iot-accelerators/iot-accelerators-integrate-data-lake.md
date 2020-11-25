---
title: Strömma data från fjärrövervakning till Data Lake Store – Azure | Microsoft Docs
description: Lär dig hur du integrerar lösningen för fjärrövervakning med Azure Data Lake Store att använda ett Azure Stream Analytics jobb.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: fce4da9cc9577bc9805289473d3df7647b1b0934
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000478"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrera lösningen för fjärrövervakning med Azure Data Lake Store

Du kan ha avancerade analys krav utöver vad som erbjuds i lösningen för fjärrövervakning. Azure Data Lake Store är perfekt för det här programmet eftersom det kan lagra data från massiv och skilda data uppsättningar samt integrera med Azure Data Lake Analytics för att kunna använda analyser på begäran.

I den här instruktionen ska du använda ett Azure Stream Analytics jobb för att strömma data från IoT Hub i din lösning för fjärrövervakning till en Azure Data Lake Store.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktionen behöver du följande:

* [Distribuera lösnings acceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md).
  * Lösningen för fjärrövervakning distribuerar IoT Hub och Azure Stream Analytics-jobbet som används i den här artikeln i din Azure-prenumeration.
* [Distribuera ett Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Din Data Lake Store bör distribueras till samma region som din lösning för fjärr styrning.
  * [Skapa en mapp](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) med namnet "streaming" i ditt konto.

## <a name="create-a-consumer-group"></a>Skapa en konsument grupp

Skapa en dedikerad konsument grupp i IoT-hubben i din lösning för fjärr övervakning. Detta används av Stream Analytics-jobbet för att strömma data till din Data Lake Store.

> [!NOTE]
> Konsument grupper används av program för att hämta data från Azure IoT Hub. Du bör skapa en ny konsument grupp för var femte utmatnings användare. Du kan skapa upp till 32 konsument grupper.

1. Logga in på Azure-portalen.

1. Klicka på knappen **Cloud Shell** i Azure Portal.

    ![Portalens start ikon](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Kör det här kommandot för att skapa en ny konsument grupp:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Använd resurs gruppen och IoT Hub-namn från din lösning för fjärrövervakning.

## <a name="create-stream-analytics-job"></a>Skapa Stream Analytics jobb

Skapa ett Azure Stream Analytics jobb för att strömma data från din IoT-hubb till din Azure Data Lake Store.

1. Klicka på **skapa en resurs**, Välj Sakernas Internet från Marketplace och klicka på **Stream Analytics jobb**.

    ![Nytt Stream Analytics jobb](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Ange ett jobbnamn och välj lämplig prenumeration och resurs grupp.

1. Välj en plats i närheten eller i samma region som din Data Lake Store. Här använder vi östra USA.

1. Se till att lämna värd miljön som standard **moln**.

1. Klicka på **Skapa**.

    ![Skapa Stream Analytics jobb](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurera Stream Analytics jobbet

1. Gå till **Stream Analytics jobb** i resurs gruppen för fjärr styrnings lösning.

1. På sidan Översikt klickar du på **indata**.

    ![Översikts sida](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klicka på **Lägg till Stream-indata** och välj **IoT Hub** i list rutan.

    ![Lägg till inmatare](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. På fliken ny inskrivning anger du ett indataports-alias för **IoTHub**.

1. I list rutan konsument grupp väljer du den konsument grupp som du skapade tidigare. Här använder vi **streamanalyticsjob**.

    ![Välj inmatade](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klicka på **Spara**.

1. På sidan Översikt klickar du på **utdata**.

    ![Lägg till Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klicka på **Lägg till** och välj **data Lake Store** i list rutan.

    ![Lägg till utdata](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. På fliken ny utmatning anger du ett kolumnalias för **DataLakeStore**.

1. Välj det Data Lake Store konto du skapade i föregående steg och ange mappstruktur för att strömma data till butiken.

1. I fältet datum format anger du **/streaming/{date}/{time}**. Lämna standard datum formatet ÅÅÅÅ/MM/DD och tids format för HH.

    ![Ange mappstruktur](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klicka på **auktorisera**.

    Du måste auktorisera med Data Lake Store för att ge Stream Analytics-jobbet skriv åtkomst till fil systemet.

    ![Auktorisera Stream Analytics till Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Du ser ett popup-fönster och när popup-knappen stängs blir knappen för att bli nedtonad När auktoriseringen är klar.

    > [!NOTE]
    > Om du ser ett fel i popup-fönstret öppnar du ett nytt webbläsarfönster i Incognito-läge och försöker igen.

1. Klicka på **Spara**.

## <a name="edit-the-stream-analytics-query"></a>Redigera den Stream Analytics frågan

Azure Stream Analytics använder ett SQL-liknande frågespråk för att ange en indatakälla som strömmar data, omvandlar dessa data efter behov och utdata till en mängd olika lagrings-eller bearbetnings destinationer.

1. Klicka på **Redigera fråga** på fliken Översikt.

    ![Redigera fråga](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. I Frågeredigeraren ersätter du plats hållarna [YourOutputAlias] och [YourInputAlias] med de värden som du definierade tidigare.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics fråga](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klicka på **Spara**.
1. Klicka på **Ja** för att acceptera ändringarna.

## <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics jobbet

1. Klicka på **Start** på fliken Översikt.

    ![Starta Stream Analytics jobb](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. På fliken starta jobb klickar du på **anpassad**.

1. Ange anpassad tid för att gå tillbaka några timmar för att hämta data från när enheten har börjat strömma.

1. Klicka på **Starta**.

    ![Välj anpassat datum](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Vänta tills jobbet körs, om du ser fel som det kan vara från din fråga kontrollerar du att syntaxen är korrekt.

    ![Jobb som körs](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Direkt uppspelnings jobbet börjar läsa data från IoT Hub och lagra data i Data Lake Store. Det kan ta några minuter innan data börjar visas i din Data Lake Store.

## <a name="explore-the-streaming-data"></a>Utforska strömmande data

1. Gå till din Data Lake Store.

1. Klicka på **data Utforskaren** på fliken Översikt.

1. Gå till mappen **/streaming** i data Utforskaren. Mappar som skapats med formatet ÅÅÅÅ/MM/DD visas.

    ![Skärm bild som visar sökvägen till/streaming/YYYY/MM/DD/HH-mappen.](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Du kommer att se JSON-filer med en fil per timme.

    ![Utforska strömmande data](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Nästa steg

Azure Data Lake Analytics kan användas för att utföra stor data analys på dina Data Lake Store data uppsättningar. Läs mer i [data Lake Analytics-dokumentationen](../data-lake-analytics/index.yml).