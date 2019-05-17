---
title: Utöka Azure IoT Central med anpassade regler och -meddelanden | Microsoft Docs
description: Konfigurera ett IoT Central-program för att skicka e-postmeddelanden när en enhet slutar att skicka telemetri som lösningsutvecklare. Den här lösningen använder Azure Stream Analytics och Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6140a8aea3fe0fe0a8f1c01cd1c97404c41f7a69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805988"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Utöka Azure IoT Central med anpassade regler som skickar meddelanden

Den här guiden visar dig, som en för lösningsutvecklare, hur du utökar din IoT Central-program med anpassade regler och meddelanden. Exemplet visar skickar ett meddelande till en operatör när en enhet slutar att skicka telemetri. Lösningen använder en [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) fråga för att identifiera när en enhet har stoppats skickar telemetri. Stream Analytics-jobb använder [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) att skicka meddelandet via e-post med hjälp av [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Den här guiden visar hur du utökar IoT Central utöver vad den redan gör med inbyggda regler och åtgärder.

I den här guiden lär du dig hur du:

* Stream telemetri från en IoT Central-program med *löpande dataexport*.
* Skapa ett Stream Analytics-fråga som identifierar när en enhet har stoppats skickar data.
* Skicka ett e-postmeddelande med Azure Functions och SendGrid-tjänster.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen i den här guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central-program

Skapa en IoT Central-App från den [Azure IoT Central - Mina program](https://aka.ms/iotcentral) sidan med följande inställningar:

| Inställning | Value |
| ------- | ----- |
| Betalningsplan | Betala per användning |
| Programmall | Contoso-exempel |
| Programnamn | Acceptera standardinställningarna eller välj namnet på din egen |
| Webbadress | Acceptera standardinställningarna eller välj en egen unik URL-prefix |
| Katalog | Azure Active Directory-klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Östra USA |

Exempel och skärmdumpar i den här artikeln i **USA, östra** region. Välj en plats nära dig och se till att skapa alla resurser i samma region.

### <a name="resource-group"></a>Resursgrupp

Använd den [Azure portal för att skapa en resursgrupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) kallas **DetectStoppedDevices** som innehåller de resurser som du skapar. Skapa dina Azure-resurser på samma plats som programmet IoT Central.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd den [Azure portal för att skapa ett namnområde för Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnet på ditt namnområde |
| Prisnivå | Grundläggande |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Location | Östra USA |
| Dataflödesenheter | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-jobb

Använd den [Azure portal för att skapa ett Stream Analytics-jobb](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnet på jobbet |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Location | Östra USA |
| Värdmiljö | Moln |
| Strömningsenheter | 3 |

### <a name="function-app"></a>Funktionsapp

Använd den [Azure portal för att skapa en funktionsapp](https://portal.azure.com/#create/Microsoft.FunctionApp) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Appnamn    | Välj din funktionsappens namn |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Operativsystem | Windows |
| Värdplan | Förbrukningsplan |
| Location | Östra USA |
| Körningsstack | .NET |
| Storage | Skapa ny |

### <a name="sendgrid-account"></a>SendGrid-konto

Använd den [Azure portal för att skapa ett SendGrid-konto](https://portal.azure.com/#create/Sendgrid.sendgrid) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnet på ditt SendGrid |
| Lösenord | Skapa ett lösenord |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Prisnivå | F1 Kostnadsfri |
| Kontaktinformation | Fyll i den obligatoriska informationen |

När du har skapat alla nödvändiga resurser din **DetectStoppedDevices** resursgrupp ser ut som följande skärmbild:

![Identifiera stoppade enheter resursgrupp](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central-program för att exportera telemetri kontinuerligt till en händelsehubb. I det här avsnittet skapar du en händelsehubb för att ta emot telemetri från IoT Central-program. Event hub levererar telemetri till ditt Stream Analytics-jobb för bearbetning.

1. Navigera till Event Hubs-namnområdet i Azure-portalen och välj **+ Event Hub**.
1. Namnge din event hub **centralexport**, och välj **skapa**.

Event Hubs-namnområdet ser ut som på följande skärmbild:

![Event Hubs-namnområde](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Hämta SendGrid API-nyckel

Funktionsappen måste en SendGrid API-nyckel för att skicka e-postmeddelanden. Skapa en SendGrid API-nyckel:

1. Gå till ditt SendGrid-konto i Azure-portalen. Välj sedan **hantera** tillgång till ditt SendGrid-konto.
1. I ditt SendGrid-konto, väljer **inställningar**, sedan **API-nycklar**. Välj **skapa API-nyckel**:

    ![Skapa SendGrid API-nyckel](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. På den **skapa API-nyckel** sidan, skapa en nyckel med namnet **AzureFunctionAccess** med **fullständig åtkomst** behörigheter.
1. Anteckna API-nyckel, du behöver den när du konfigurerar din funktionsapp.

## <a name="define-the-function"></a>Definiera funktionen

Den här lösningen använder en Azure Functions-app för att skicka ett e-postmeddelande när ett Stream Analytics-jobb identifierar en stoppad enhet. Skapa din funktionsapp:

1. I Azure-portalen går du till den **Apptjänst** instans i den **DetectStoppedDevices** resursgrupp.
1. Välj **+** du skapar en ny funktion.
1. På den **väljer du en UTVECKLINGSMILJÖ** väljer **i portalen** och välj sedan **Fortsätt**.
1. På den **CREATE A FUNCTION** väljer **Webhook + API** och välj sedan **skapa**.

Portalen skapar en standardfunktion som kallas **HttpTrigger1**:

![Standard HTTP-utlösta funktionen](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurera funktionsbindningar

Om du vill skicka e-postmeddelanden med SendGrid måste du konfigurera bindningarna för din funktion på följande sätt:

1. Välj **integrera**, Välj utdata **HTTP ($return)**, och välj sedan **ta bort**.
1. Välj **+ nya utdata**, välj sedan **SendGrid**, och välj sedan **Välj**. Välj **installera** att installera tillägget SendGrid.
1. När installationen är klar väljer du **Använd funktionsreturvärde**. Lägg till en giltig **till adressen** att ta emot e-postmeddelanden.  Lägg till en giltig **från adress** ska användas som e-postavsändaren.
1. Välj **nya** bredvid **SendGrid API-nyckel för Appinställningen**. Ange **SendGridAPIKey** som nyckeln och SendGrid API-nyckeln som du antecknade tidigare som värde. Välj sedan **Skapa**.
1. Välj **spara** att spara SendGrid-bindningar för din funktion.

Integrera inställningarna ser ut som på följande skärmbild:

![Funktionen app integreringar](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Lägg till Funktionskoden

För att implementera din funktion, lägger du till den C# kod för att parsa den inkommande HTTP-begäran och skicka e-postmeddelanden på följande sätt:

1. Välj den **HttpTrigger1** fungerar i din funktionsapp och Ersätt den C# kod med följande kod:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Du kan se ett felmeddelande förrän du sparar den nya koden.

1. Välj **spara** att spara funktionen.

### <a name="test-the-function-works"></a>Testa att funktionen fungerar

Välj först om du vill testa funktionen i portalen, **loggar** längst ned i kodredigeraren. Välj sedan **Test** till höger om Kodredigeraren. Använd följande JSON som den **Begärandetext**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Loggmeddelanden funktionen visas i den **loggar** panelen:

![Funktionen loggutdata](media/howto-create-custom-rules/function-app-logs.png)

Efter några minuter, den **till** e-postadress får ett e-postmeddelande med följande innehåll:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Lägg till Stream Analytics-fråga

Den här lösningen använder en Stream Analytics-fråga för att identifiera när en enhet slutar att skicka telemetri för mer än 120 sekunder. Frågan använder telemetrin från event hub som indata. Jobbet skickar resultatet av frågan i funktionsappen. I det här avsnittet ska konfigurera du ett Stream Analytics-jobb:

1. I Azure-portalen går du till ditt Stream analytics-jobb under **jobb topologi** Välj **indata**, Välj **+ Lägg till strömindata**, och välj sedan **händelse Hub**.
1. Använd informationen i följande tabell för att konfigurera indata med hjälp av den händelsehubb som du skapade tidigare och välj sedan **spara**:

    | Inställning | Value |
    | ------- | ----- |
    | Inmatat alias | centraltelemetry |
    | Prenumeration | Din prenumeration |
    | Namnområde för händelsehubb | Namnområdet för Event Hub |
    | Namn på händelsehubb | Använd befintlig - **centralexport** |

1. Under **jobb topologi**väljer **utdata**, Välj **+ Lägg till**, och välj sedan **Azure-funktion**.
1. Använd informationen i följande tabell för att konfigurera utdata och välj **spara**:

    | Inställning | Value |
    | ------- | ----- |
    | Utdataalias | emailnotification |
    | Prenumeration | Din prenumeration |
    | Funktionsapp | Din funktionsapp |
    | Funktion  | HttpTrigger1 |

1. Under **jobb topologi**väljer **fråga** och Ersätt den befintliga frågan med följande SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Välj **Spara**.
1. Starta Stream Analytics-jobbet genom att välja **översikt**, sedan **starta**, sedan **nu**, och sedan **starta**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

Navigera till den [IoT Central programmet](https://aka.ms/iotcentral) du skapade från Contoso-mall. I det här avsnittet ska konfigurera du programmet att strömma telemetri från dess simulerade enheter till din event hub. Konfigurera export:

1. Navigera till den **löpande Export av Data** väljer **+ ny**, och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till Event Hubs |
    | Enabled | På |
    | Event Hubs-namnområde | Namnet på ditt Event Hubs-namnområde |
    | Händelsehubb | centralexport |
    | Mått | På |
    | Enheter | Av |
    | Enhetsmallar | Av |

![Konfiguration för löpande export](media/howto-create-custom-rules/cde-configuration.png)

Vänta tills statusen export är **kör** innan du fortsätter.

## <a name="test"></a>Test

Om du vill testa lösningen kan du inaktivera den löpande dataexporten från IoT Central till simulerade stoppade enheter:

1. I ditt IoT Central-program, navigerar du till den **löpande Export av Data** och välj den **exportera till Event Hubs** Exportera konfiguration.
1. Ange **aktiverad** till **av** och välj **spara**.
1. När du har minst två minuter, den **till** e-postadress tar emot en eller flera e-postmeddelanden som ser ut som i följande exempel innehåll:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Standardhuvudgrenen

Standardhuvudgrenen efter den här anvisningen och undvika onödiga kostnader genom att ta bort den **DetectStoppedDevices** resursgrupp i Azure-portalen.

Du kan ta bort IoT Central-programmet från den **Management** sidan i programmet.

## <a name="next-steps"></a>Nästa steg

I den här guiden beskrivs hur du:

* Stream telemetri från en IoT Central-program med *löpande dataexport*.
* Skapa ett Stream Analytics-fråga som identifierar när en enhet har stoppats skickar data.
* Skicka ett e-postmeddelande med Azure Functions och SendGrid-tjänster.

Nu när du vet hur du skapar anpassade regler och meddelanden, föreslagna nästa steg är att lära dig hur du [visualisera och analysera dina Azure IoT Central data i en Power BI-instrumentpanel](howto-connect-powerbi.md).
