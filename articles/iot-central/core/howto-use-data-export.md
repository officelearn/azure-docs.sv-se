---
title: Exportera data från IoT Central | Microsoft Docs
description: Hur du använder den nya data exporten för att exportera dina IoT-data till Azure och anpassade moln mål.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036563"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Exportera IoT-data till moln mål med hjälp av data export (för hands version)

> [!Note]
> Letar du efter äldre data export? Du kan hitta data export dokument [här](./howto-export-data.md). Mer information om skillnaderna mellan den nya data exporten och äldre data export finns i [jämförelse tabellen](#comparison-of-legacy-data-export-and-new-data-export).

Den här artikeln beskriver hur du använder de nya funktionerna för för hands versionen av data export i Azure IoT Central. Du kan använda den här funktionen för att kontinuerligt exportera dina filtrerade och berikade IoT-data till dina moln tjänster. Du kan använda data export för att skicka ändringar i nära real tid till andra delar av moln lösningen för insikter, analyser och lagring med varma sökvägar. 

 Du kan till exempel:
-   Exportera kontinuerligt data och egenskaps ändringar i JSON-format i nära real tid
-   Filtrera dessa data strömmar för att exportera vissa funktioner som matchar anpassade villkor
-   Utöka data strömmarna med anpassade värden och egenskaps värden från enheten
-   Skicka dessa data till mål som Azure Event Hubs, Azure Service Bus, Azure Blob Storage och webhook-slutpunkter

> [!Note]
> När du aktiverar data export får du bara data från dessa tidpunkter. För närvarande går det inte att hämta data under en tid då data exporten var avstängd. Aktivera data export tidigt om du vill behålla mer historiska data.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda data export (för hands version) måste du ha ett v3-program och du måste ha behörighet för data export.

## <a name="set-up-export-destination"></a>Konfigurera export mål

Export målet måste finnas innan du konfigurerar dina data export. Följande är tillgängliga typer av destinationer:
  - Azure Event Hubs
  - Azure Service Bus-kö
  - Azure Service Bus-ämne
  - Azure Blob Storage
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Skapa ett Event Hubs-mål

Följ dessa steg om du inte har ett befintligt Event Hubs namn område att exportera till:

1. Skapa ett [nytt Event Hubs-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Azure Event Hubs-dokument](../../event-hubs/event-hubs-create.md).

2. Skapa en Event Hub i Event Hubs namn området. Gå till ditt namn område och välj **+ Event Hub** överst för att skapa en Event Hub-instans.

3. Generera en nyckel som du kommer att använda i IoT Central för att konfigurera din data export. 
    - Klicka på Event Hub-instansen som du skapade. 
    - Klicka på **Inställningar/principer för delad åtkomst**. 
    - Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet. 
    - Kopiera antingen den primära eller sekundära anslutnings strängen. Du kommer att använda den här inställningen för att konfigurera ett nytt mål i IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Skapa en Service Bus kö eller ett ämnes mål

Följ dessa steg om du inte har ett befintligt Service Bus namn område att exportera till:

1. Skapa ett [nytt Service Bus-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Du kan läsa mer i [Azure Service Bus dokument](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Om du vill skapa en kö eller ett ämne att exportera till går du till din Service Bus-namnrymd och väljer **+ kö** eller **+ ämne**.

3. Generera en nyckel som du kommer att använda i IoT Central för att konfigurera din data export. 
    - Klicka på den kö eller det ämne som du skapade. 
    - Klicka på **Inställningar/principer för delad åtkomst**. 
    - Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet. 
    - Kopiera antingen den primära eller sekundära anslutnings strängen. Du kommer att använda den här inställningen för att konfigurera ett nytt mål i IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Skapa ett Azure Blob Storage-mål

Följ dessa steg om du inte har ett befintligt Azure Storage-konto att exportera till:

1. Skapa ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan lära dig mer om att skapa nya [Azure Blob Storage-konton](https://aka.ms/blobdocscreatestorageaccount) eller [Azure Data Lake Storage v2-lagrings konton](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Data export kan bara skriva data till lagrings konton som stöder block-blobbar. I följande lista visas kända kompatibla lagrings konto typer:

    |Prestanda nivå|Kontotyp|
    |-|-|
    |Standard|Generell användning v2|
    |Standard|Generell användning v1|
    |Standard|Blob Storage|
    |Premium|Block Blob Storage|

2. Skapa en behållare i ditt lagrings konto. Gå till ditt lagringskonto. Under **BLOB service**väljer du **Bläddra i blobbar**. Välj **+ behållare** överst för att skapa en ny behållare.

3. Skapa en anslutnings sträng för ditt lagrings konto genom att gå till **Inställningar/åtkomst nycklar**. Kopiera en av de två anslutnings strängarna.

### <a name="create-a-webhook-endpoint"></a>Skapa en webhook-slutpunkt
Du kan ange en offentligt tillgänglig HTTP-slutpunkt för data som ska exporteras till. Du kan skapa en test-webhook-slutpunkt med RequestBin. Tänk på att RequestBin har en gräns för antal begär Anden innan begär Anden får begränsas.

1. Öppna [RequestBin](https://requestbin.net/).
2. Skapa en ny RequestBin och kopiera bin-URL: en.

## <a name="set-up-data-export"></a>Konfigurera data export

Nu när du har ett mål att exportera data till, följer du dessa steg för att konfigurera data export.

1. Logga in på ditt IoT Central-program.

2. Välj **data export**i det vänstra fönstret.

    > [!Tip]
    > Om du inte ser **data export** i det vänstra fönstret har du inte behörighet att konfigurera data export i din app. Prata med en administratör för att konfigurera data export.

3. Välj knappen **+ ny export** . 

4. Ange ett visnings namn för den nya exporten och se till att den **aktiverade** växlingen är aktive rad för att data ska flöda.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Välj den typ av data som ska exporteras
Du kan välja mellan att kontinuerligt exportera olika typer av data. Här är de data typer som stöds:

| Datatyp | Beskrivning | Dataformat |
| :------------- | :---------- | :----------- |
|  Telemetri | Exportera telemetri meddelanden från enheter i nära real tid. Varje exporterat meddelande kommer att innehålla det fullständiga innehållet i det ursprungliga enhets meddelandet, normaliserat.   |  [Meddelande format för telemetri](#telemetry-format)   |
| Egenskaps ändringar | Exportera ändringar av enhets-och moln egenskaper i nära real tid. För skrivskyddade enhets egenskaper exporteras ändringar av de rapporterade värdena. För Läs-och skriv egenskaper exporteras både rapporterade och önskade värden. | [Meddelande format för egenskaps ändring](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (valfritt) Lägg till filter
Lägg till filter för att minska mängden data som exporteras utifrån filter villkor. Det finns olika typer av filter som är tillgängliga för varje typ av data som ska exporteras.

### <a name="telemetry-filters"></a>Telemetri filter
  - **Funktions filter**: om du väljer ett telemetri-objekt i list rutan, kommer den exporterade data strömmen bara att innehålla telemetri som uppfyller filter villkoret. Om du väljer en enhet eller ett moln egenskaps objekt i list rutan, kommer den exporterade data strömmen bara att innehålla telemetri från enheter som har egenskaper som matchar filter villkoret.
  - **Filter för meddelande egenskap**: enheter som använder enhets-SDK: er får skicka *meddelande egenskaper* eller *program egenskaper* för varje telemetri, som är en väska med nyckel/värde-par som vanligt vis används för att tagga meddelandet med anpassade identifierare. Du kan skapa ett filter för meddelande egenskap genom att skriva in den meddelande egenskaps nyckel som du söker efter och ange ett villkor. Endast telemetri som har meddelande egenskaper som matchar det angivna filter villkoret kommer att exporteras. Endast sträng jämförelse operatorer stöds (lika med, som inte är lika med, innehåller, innehåller inte, finns inte). [Läs mer om program egenskaper från IoT Hub dokument](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Filter för egenskaps ändringar
**Egenskaps filter**: Välj ett egenskaps objekt i list rutan och den exporterade data strömmen kommer bara att innehålla ändringar av den valda egenskapen som uppfyller filter villkoret.

## <a name="3-optional-add-enrichments"></a>3. (valfritt) Lägg till berikningar
Lägg till anrikninger för att utöka exporterade meddelanden med ytterligare metadata i nyckel/värde-par. Det här är de tillgängliga möjligheterna för telemetri och egenskaps ändringar data typer:
  - **Anpassad sträng**: lägger till en anpassad statisk sträng i varje meddelande. Ange valfri nyckel och ange valfritt sträng värde.
  - **Egenskap**: lägger till den aktuella enheten rapporterade egenskapen eller moln egenskap svärdet i varje meddelande. Ange valfri nyckel och välj en enhets-eller moln egenskap. Om det exporterade meddelandet kommer från en enhet som inte har den angivna enhets-eller moln egenskapen, kommer det exporterade meddelandet inte att ha denna berikning.

## <a name="4-add-destinations"></a>4. Lägg till mål
Skapa ett nytt mål eller Lägg till ett mål som du redan har skapat. 
  
1. Klicka på länken **skapa en ny destination** . Fyll i följande information:
    - **Målnamn**: visnings namnet för målet i IoT Central
    - **Måltyp: Välj**typ av mål. Om du inte redan har gjort det [konfigurerar du export målet](#set-up-export-destination)
    - För Azure Event Hubs, Azure Service Bus kö eller ämne, klistra in anslutnings strängen för resursen. 
    - För Azure Blob Storage klistrar du in anslutnings strängen för resursen och anger namnet på behållaren (Skift läges känsligt).
    - För webhook klistrar du in återanrops-URL: en för webhook-slutpunkten. 
    - Klicka på **Skapa**

2. Klicka på **+ destination** och välj ett mål i list rutan. Du kan lägga till upp till 5 mål i en enda export.

3. När du har konfigurerat exporten klickar du på **Spara**. Efter några minuter visas dina data i dina mål.

## <a name="export-contents-and-format"></a>Exportera innehåll och format

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage-mål

Data exporteras en gång per minut, med varje fil som innehåller grupp ändringar sedan den senaste exporterade filen. Exporterade data placeras i tre mappar i JSON-format. Standard Sök vägarna i ditt lagrings konto är:

- Telemetri: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Egenskaps ändringar: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_

Om du vill bläddra bland de exporterade filerna i Azure Portal navigerar du till filen och väljer fliken **Redigera BLOB** .

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs och Azure Service Bus destinationer

Data exporteras i nära real tid. Informationen finns i meddelande texten och är i JSON-format kodad som UTF-8. 

I meddelandets anteckningar eller system egenskaper kan du hitta `iotcentral-device-id` , `iotcentral-application-id` , `iotcentral-message-source` och `iotcentral-message-type` som har samma värden som motsvarande fält i meddelande texten.

### <a name="webhook-destination"></a>Webhook-mål
För Webhooks-mål exporteras även data i nära real tid. Informationen är i samma format som i meddelande texten för Event Hubs och Service Bus.


## <a name="telemetry-format"></a>Telemetri-format
Varje exporterat meddelande innehåller en normaliserad form av det fullständiga meddelande som enheten skickade i meddelande texten i JSON-format kodad som UTF-8. Ytterligare information som ingår i varje meddelande är:

- `applicationId`av appen IoT Central
- `messageSource`vilket är *telemetri* för att exportera telemetridata
- `deviceId`av enheten som skickade telemetri meddelandet
- `schema`är namnet och versionen för nytto Last schema
- `templateId`är enhetens mall-ID som är kopplat till enheten
- `enrichments`är alla berikningar som har ställts in på exporten
- `messageProperties`är de ytterligare delar av data som enheten skickar tillsammans med meddelandet. Detta kallas även för *program egenskaper*, [läs mer i IoT Hub dokument](../../iot-hub/iot-hub-devguide-messages-construct.md).

För Event Hubs och Service Bus exporterar IoT Central ett nytt meddelande snabbt när det får meddelandet från en enhet.

För Blob Storage grupperas och exporteras meddelanden en gång per minut.

I följande exempel visas ett exporterat telemetri-meddelande:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Format för egenskaps ändringar

Varje meddelande eller post representerar en ändring i en enhets-eller moln egenskap. För enhets egenskaper exporteras endast ändringar i det rapporterade värdet som ett separat meddelande. Ytterligare information som ingår i det exporterade meddelandet är:

- `applicationId`av appen IoT Central
- `messageSource`Det är *Egenskaper* för att exportera egenskaps data
- `messageType`som är antingen *cloudPropertyChange* eller *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`av enheten vars egenskaper har ändrats
- `schema`är namnet och versionen för nytto Last schema
- `templateId`är enhetens mall-ID som är kopplat till enheten
- `enrichments`är alla berikningar som har ställts in på exporten

För Event Hubs och Service Bus exporterar IoT Central nya meddelande data till händelsehubben eller Service Bus kö eller ämne i nära real tid.

För Blob Storage grupperas och exporteras meddelanden en gång per minut.

I följande exempel visas ett meddelande om att en exporterad egenskaps ändring mottagits i Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Jämförelse av äldre data export och ny data export
Det här är en tabell som framhäver skillnaderna mellan äldre data export och den nya data exporten. Du kan läsa mer om äldre data export [här](howto-export-data.md).

| Funktioner  | Äldre data export | Ny data export |
| :------------- | :---------- | :----------- |
| Tillgängliga data typer | Telemetri, enheter, enhetsspecifika mallar | Telemetri, egenskaps ändringar |
| Filtrering | Ingen | Beror på vilken data typ som exporteras. För telemetri, filtrera efter telemetri, meddelande egenskaper, egenskaps värden |
| Berikningar | Ingen | Utöka med en anpassad sträng eller ett egenskaps värde på enheten |
| Mål | Azure Event Hubs, Azure Service Bus köer och ämnen, Azure Blob Storage | Samma som för äldre data export och Webhooks| 
| Appar som stöds | V2, V3 | Endast v3 |
| Viktiga begränsningar | 5 exporter per app, 1 mål per export | 10 exporter – mål anslutningar per app | 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du använder den nya data exporten fortsätter du till nästa steg:

> [!div class="nextstepaction"]
> [Använda analyser i IoT Central](./howto-create-analytics.md)
