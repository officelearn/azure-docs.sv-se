---
title: Utöka Azure IoT Central med anpassade regler och meddelanden | Microsoft-dokument
description: Som lösningsutvecklare konfigurerar du ett IoT Central-program för att skicka e-postmeddelanden när en enhet slutar skicka telemetri. Den här lösningen använder Azure Stream Analytics, Azure Functions och SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158154"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Utöka Azure IoT Central med anpassade regler med Hjälp av Stream Analytics, Azure Functions och SendGrid



Den här guiden visar hur du som lösningsutvecklare utökar ditt IoT Central-program med anpassade regler och meddelanden. Exemplet visar att skicka ett meddelande till en operatör när en enhet slutar skicka telemetri. Lösningen använder en [Azure Stream Analytics-fråga](https://docs.microsoft.com/azure/stream-analytics/) för att identifiera när en enhet har slutat skicka telemetri. Stream Analytics-jobbet använder [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) för att skicka e-postmeddelanden med [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Den här guiden visar hur du utökar IoT Central utöver vad den redan kan göra med de inbyggda reglerna och åtgärderna.

I den här guiden lär du dig hur du:

* Strömma telemetri från ett IoT Central-program med hjälp av *kontinuerlig dataexport*.
* Skapa en Stream Analytics-fråga som identifierar när en enhet har slutat skicka data.
* Skicka ett e-postmeddelande med Azure Functions och SendGrid-tjänsterna.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

### <a name="iot-central-application"></a>IoT Central ansökan

Skapa ett IoT Central-program på Azure [IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Prisplan | Standard |
| Programmall | Analys i butik – tillståndsövervakning |
| Programnamn | Acceptera standardvärdet eller välj ditt eget namn |
| URL | Acceptera standard- eller välj ditt eget unika URL-prefix |
| Katalog | Din Azure Active Directory-klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

Exemplen och skärmbilderna i den här artikeln använder **usa-regionen.** Välj en plats nära dig och se till att du skapar alla dina resurser i samma region.

Den här programmallen innehåller två simulerade termostatenheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd [Azure-portalen för att skapa en resursgrupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) som heter **DetectStoppedDevices** för att innehålla andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central-program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd [Azure-portalen för att skapa ett namnområde för eventhubbar](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnområdesnamn |
| Prisnivå | Basic |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Location | USA, östra |
| Genomflödesenheter | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-jobb

Använd [Azure-portalen för att skapa ett Stream Analytics-jobb](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj jobbnamn |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Location | USA, östra |
| Värdmiljö | Molnet |
| Strömningsenheter | 3 |

### <a name="function-app"></a>Funktionsapp

Använd [Azure-portalen för att skapa en funktionsapp](https://portal.azure.com/#create/Microsoft.FunctionApp) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Appnamn    | Välj namn på funktionsapp |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Operativsystem | Windows |
| Värdplan | Förbrukningsplan |
| Location | USA, östra |
| Körningsstack | .NET |
| Lagring | Skapa ny |

### <a name="sendgrid-account"></a>SendGrid-konto

Använd [Azure-portalen för att skapa ett SendGrid-konto](https://portal.azure.com/#create/Sendgrid.sendgrid) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj ditt SendGrid-kontonamn |
| lösenord | Skapa ett lösenord |
| Prenumeration | Din prenumeration |
| Resursgrupp | DetectStoppedDevices |
| Prisnivå | F1 Kostnadsfri |
| Kontaktinformation | Fyll i nödvändig information |

När du har skapat alla nödvändiga resurser ser resursgruppen **DetectStoppedDevices** ut som följande skärmbild:

![Identifiera stoppad enhetsresursgrupp](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central-program för att kontinuerligt exportera telemetri till en händelsehubb. I det här avsnittet skapar du en händelsehubb för att ta emot telemetri från ditt IoT Central-program. Händelsehubben levererar telemetrin till ditt Stream Analytics-jobb för bearbetning.

1. I Azure-portalen navigerar du till namnområdet Event Hubs och väljer **+ Event Hub**.
1. Namnge ditt händelsenav **centralexport**och välj **Skapa**.

Namnområdet Event Hubs ser ut så här:

![Event Hubs-namnområde](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Hämta Api-nyckel för SendGrid

Din funktionsapp behöver en SendGrid API-nyckel för att skicka e-postmeddelanden. Så här skapar du en SendGrid API-nyckel:

1. Navigera till ditt SendGrid-konto i Azure-portalen. Välj sedan **Hantera** för att komma åt ditt SendGrid-konto.
1. I ditt SendGrid-konto väljer du **Inställningar**och sedan **API-nycklar**. Välj **Skapa API-nyckel:**

    ![Skapa Api-nyckel för SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. På sidan **Skapa API-nyckel** skapar du en nyckel med namnet **AzureFunctionAccess** med **fullständiga behörigheter.**
1. Anteckna API-nyckeln, du behöver den när du konfigurerar din funktionsapp.

## <a name="define-the-function"></a>Definiera funktionen

Den här lösningen använder en Azure Functions-app för att skicka ett e-postmeddelande när Stream Analytics-jobbet identifierar en stoppad enhet. Så här skapar du din funktionsapp:

1. I Azure-portalen navigerar du till **App Service-instansen** i resursgruppen **DetectStoppedDevices.**
1. Markera **+** det här om du vill skapa en ny funktion.
1. På sidan **VÄLJ EN UTVECKLINGSMILJÖ** väljer du **In-portal** och väljer sedan **Fortsätt**.
1. På sidan **SKAPA en funktion** väljer du **Webhook + API** och väljer sedan **Skapa**.

Portalen skapar en standardfunktion som kallas **HttpTrigger1:**

![Standardfunktion för HTTP-utlösare](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurera funktionsbindningar

Om du vill skicka e-post med SendGrid måste du konfigurera bindningarna för din funktion enligt följande:

1. Välj **Integrera**, välj utdata **HTTP ($return)** och välj sedan **ta bort**.
1. Välj **+ Ny utdata**och välj sedan **SendGrid**och välj sedan **Välj**. Välj **Installera** för att installera SendGrid-tillägget.
1. När installationen är klar väljer du **Använd funktionens returvärde**. Lägg till en giltig **Till-adress** för att få e-postmeddelanden.  Lägg till en giltig **Från-adress** som ska användas som e-postavsändare.
1. Välj **ny** **bredvid Inställningen för SendGrid API Key App**. Ange **SendGridAPIKey** som nyckel och SendGrid API-nyckeln som du tidigare noterade som värde. Välj sedan **Skapa**.
1. Välj **Spara** om du vill spara SendGrid-bindningarna för din funktion.

Inställningarna för integrering ser ut så här:

![Funktionsappintegreringar](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Lägga till funktionskoden

Om du vill implementera din funktion lägger du till C#-koden för att tolka den inkommande HTTP-begäran och skicka e-postmeddelanden på följande sätt:

1. Välj **funktionen HttpTrigger1** i funktionsappen och ersätt C#-koden med följande kod:

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

    Ett felmeddelande kan visas tills du sparar den nya koden.

1. Välj **Spara** för att spara funktionen.

### <a name="test-the-function-works"></a>Testa funktionen fungerar

Om du vill testa funktionen i portalen väljer du först **Loggar** längst ned i kodredigeraren. Välj sedan **Testa** till höger om kodredigeraren. Använd följande JSON som **förfrågelskropp:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Funktionsloggmeddelandena visas på **panelen Loggar:**

![Utdata för funktionslogg](media/howto-create-custom-rules/function-app-logs.png)

Efter några minuter får e-postadressen **Till** ett e-postmeddelande med följande innehåll:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Lägg till Fråga om Stream Analytics

Den här lösningen använder en Stream Analytics-fråga för att identifiera när en enhet slutar skicka telemetri i mer än 120 sekunder. Frågan använder telemetrin från händelsehubben som indata. Jobbet skickar frågeresultaten till funktionsappen. I det här avsnittet konfigurerar du Stream Analytics-jobbet:

1. I Azure-portalen navigerar du till ditt Stream Analytics-jobb, under **Jobbtopologi** väljer du **Indata**, väljer **+ Lägg till indata för stream**och väljer sedan **Händelsehubb**.
1. Använd informationen i följande tabell för att konfigurera indata med hjälp av händelsehubben som du skapade tidigare och välj sedan **Spara:**

    | Inställning | Värde |
    | ------- | ----- |
    | Inmatat alias | centraltelemetri |
    | Prenumeration | Din prenumeration |
    | Namnområde för händelsehubb | Namnområdet För händelsehubben |
    | Namn på händelsehubb | Använd befintliga - **centralexport** |

1. Under **Jobbtopologi**väljer du **Utdata**, väljer **+ Lägg till**och väljer sedan **Azure-funktion**.
1. Använd informationen i följande tabell för att konfigurera utdata och välj sedan **Spara:**

    | Inställning | Värde |
    | ------- | ----- |
    | Utdataalias | e-postanmälning |
    | Prenumeration | Din prenumeration |
    | Funktionsapp | Din funktionsapp |
    | Funktion  | HttpTrigger1 (på) |

1. Under **Jobbtopologi**väljer du **Fråga** och ersätter den befintliga frågan med följande SQL:

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
1. Om du vill starta Stream Analytics-jobbet väljer du **Översikt**, sedan **Start**, sedan **nu**och sedan **startar:**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

På azure [IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) navigerar du till det IoT Central-program som du skapade från Contoso-mallen. I det här avsnittet konfigurerar du programmet för att strömma telemetrin från dess simulerade enheter till händelsehubben. Så här konfigurerar du exporten:

1. Navigera till sidan **Dataexport,** välj **+ Nytt**och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **Spara:**

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till händelsehubbar |
    | Enabled | På |
    | Event Hubs-namnområde | Namnområdesnamn för händelsehubbar |
    | Händelsehubb | centralexport |
    | Mått | På |
    | Enheter | Av |
    | Enhetsmallar | Av |

![Konfiguration för kontinuerlig dataexport](media/howto-create-custom-rules/cde-configuration.png)

Vänta tills exportstatusen **körs** innan du fortsätter.

## <a name="test"></a>Testa

Om du vill testa lösningen kan du inaktivera den kontinuerliga dataexporten från IoT Central till simulerade stoppade enheter:

1. I det centrala IoT-programmet navigerar du till sidan **Dataexport** och väljer exportkonfigurationen **Exportera till händelsehubbar.**
1. Ställ in **Aktiverad** på **Av** och välj **Spara**.
1. Efter minst två minuter får e-postadressen **Till** en eller flera e-postmeddelanden som ser ut som följande exempelinnehåll:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Städa upp

Om du vill städa upp efter den här how-to-to-kostnaden tar du bort resursgruppen **DetectStoppedDevices** i Azure-portalen.

Du kan ta bort IoT Central-programmet från **sidan Hantering** i programmet.

## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig att:

* Strömma telemetri från ett IoT Central-program med hjälp av *kontinuerlig dataexport*.
* Skapa en Stream Analytics-fråga som identifierar när en enhet har slutat skicka data.
* Skicka ett e-postmeddelande med Azure Functions och SendGrid-tjänsterna.

Nu när du vet hur du skapar anpassade regler och meddelanden är det föreslagna nästa steget att lära dig hur du [utökar Azure IoT Central med anpassad analys](howto-create-custom-analytics.md).
