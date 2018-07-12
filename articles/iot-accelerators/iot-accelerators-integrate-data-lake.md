---
title: Integrera lösningen för fjärrövervakning med Azure Data Lake Store | Microsoft Docs
description: Lär dig hur du integrerar lösningen för fjärrövervakning med Azure Data Lake Store med hjälp av Azure Stream Analytics-jobb.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5ba9b5534e986be1cbe55043a9acdd981d2ed7fd
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971745"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrera lösningen för fjärrövervakning med Azure Data Lake Store

Du kan ha avancerade analyser krav utöver vad som är tillgängligt i lösningen för fjärrövervakning. Azure Data Lake Store är perfekt för det här programmet eftersom det kan lagra data från stora och skilda datauppsättningar samt integrera med Azure Data Lake Analytics för att tillhandahålla realtidsanalyser på begäran.

I den här anvisningen använder du Azure Stream Analytics-jobb att strömdata från IoT hub i lösningen för fjärrövervakning till en Azure Data Lake Store.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här anvisningen, behöver du följande:

* [Distribuerar lösningsacceleratorn för fjärrövervakning](iot-accelerators-remote-monitoring-deploy.md).
  * Lösningen för fjärrövervakning distribuerar IoT hub och Azure Stream Analytics-jobb som används i den här artikeln i din Azure-prenumeration.
* [Distribuera en Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Din Data Lake Store ska distribueras till samma region som din lösning för fjärrövervakning.
  * [Skapa en mapp](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) med namnet ”strömning” i ditt konto.

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Skapa en dedikerad konsumentgrupp i IoT hub i lösningen för fjärrövervakning. Detta ska användas av Stream Analytics-jobb för strömmande data till ditt Data Lake Store.

> [!NOTE]
> Konsumentgrupper används av program för att hämta data från Azure IoT Hub. Alla kunder använda fem utdata bör du skapa en ny konsumentgrupp. Du kan skapa upp till 32 konsumentgrupper.

1. Logga in på Azure Portal.

1. I Azure-portalen klickar du på den **Cloud Shell** knappen.

    ![Portalen Start-ikonen](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Kör detta kommando för att skapa en ny konsumentgrupp:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Använd resursgrupp och IoT hub-namn från lösningen för fjärrövervakning.

## <a name="create-stream-analytics-job"></a>Skapa Stream Analytics-jobb

Skapa ett Azure Stream Analytics-jobb för att strömma data från IoT hub till din Azure Data Lake store.

1. Klicka på **skapa en resurs**, Välj Sakernas Internet från Marketplace och på **Stream Analytics-jobbet**.

    ![Nytt Stream Analytics-jobb](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Ange ett Jobbnamn och välj lämplig prenumeration och resursgrupp.

1. Välj en plats i den nära eller i samma region som din Data Lake Store. Här använder vi östra USA.

1. Se till att lämna Hosting miljön som standard **molnet**.

1. Klicka på **Skapa**.

    ![Skapa Stream Analytics-jobb](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurera ett Stream Analytics-jobb

1. Gå till den **Stream Analytics-jobbet** i resursgruppen fjärrövervakning lösning.

1. På sidan Översikt **indata**.

    ![Översiktssidan](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klicka på **Lägg till strömindata** och välj **IoT Hub** från listrutan.

    ![Lägg till indata](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Ange ett indata-alias för på den nya inkommande fliken **IoTHub**.

1. Konsumenten grupp listrutan, Välj konsumentgrupp som du skapade tidigare. Här använder vi **streamanalyticsjob**.

    ![Välj indata](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klicka på **Spara**.

1. På sidan Översikt **utdata**.

    ![Lägg till Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klicka på **Lägg till** och välj **Data Lake Store** från listrutan.

    ![Lägg till utdata](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. På den nya utdata-fliken, anger du ett utdataalias för **DataLakeStore**.

1. Välj Data Lake Store-kontot som du skapade i föregående steg och ange mappstrukturen att sända data till arkivet.

1. Ange i fältet datum format **/streaming/ {date} / {time}**. Lämna standarddatumformatet åååå/MM/DD- och tidsformat hh.

    ![Ange mappstruktur](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klicka på **auktorisera**.

    Du måste auktorisera med Data Lake Store för att ge skrivåtkomst för Stream analytics-jobbet till filsystemet.

    ![Auktorisera Stream Analytics till Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Du ser ett popup-fönster och när popup-fönstret stängs auktorisera knappen nedtonade när auktoriseringen är klar.

    > [!NOTE]
    > Om du ser ett fel i popup-fönstret, öppna ett nytt webbläsarfönster i Incognito-läge och försök igen.

1. Klicka på **Spara**.

## <a name="edit-the-stream-analytics-query"></a>Redigera Stream Analytics-fråga

Azure Stream Analytics använder ett SQL-liknande frågespråk för att ange en Indatakällan som strömmar data, transformera data som önskade- och utdata till en mängd olika mål för lagring eller bearbetning.

1. På fliken Översikt **redigera frågan**.

    ![Redigera fråga](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. I frågeredigeraren, ersätter du [YourOutputAlias] och [YourInputAlias] platshållarna med värden som du definierade tidigare.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics-fråga](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klicka på **Spara**.
1. Klicka på **Ja** att godkänna ändringarna.

## <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

1. På fliken Översikt **starta**.

    ![Starta Stream Analytics-jobbet](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. På fliken Start jobbet **anpassad**.

1. Ange anpassad tid att gå tillbaka ett par timmar för att hämta data från när enheten har startats för direktuppspelning.

1. Klicka på **starta**.

    ![Välj anpassat datum](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Vänta tills jobbet hamnar i körningstillstånd, om du ser fel som det kan vara från din fråga, se till att kontrollera att syntaxen är korrekt.

    ![Jobb som körs](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Det direktuppspelade jobbet börjar läsa data från IoT Hub och lagra data i ditt Data Lake Store. Det kan ta några minuter innan data börjar visas i ditt Data Lake Store.

## <a name="explore-the-streaming-data"></a>Utforska strömmande data

1. Gå till ditt Data Lake Store.

1. På fliken Översikt **datautforskaren**.

1. I datautforskaren, öka detaljnivån till den **/ streaming** mapp. Mappar som skapas med åååå/MM/DD/HH format visas.

    ![Utforska strömmande Data](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Du ser json-filer med en fil per timme.

    ![Utforska strömmande Data](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Nästa steg

Azure Data Lake Analytics kan användas för att utföra analyser av stordata på ditt Data Lake Store-datauppsättningar. Läs mer på den [dokumentation om Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
