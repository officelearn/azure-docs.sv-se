---
title: 'Självstudie: ta emot enhets data via Azure IoT Hub'
description: I den här självstudien får du lära dig hur du aktiverar routning av enhets data från IoT Hub till Azure API för FHIR via IoT Connector.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: aeed0c90eeecd59e23a1d87a3ebc1e1a836a84ec
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116842"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Självstudie: ta emot enhets data via Azure IoT Hub

IoT Connector ger dig möjlighet att mata in data från IoMT-enheter (Internet of Medicine saker) till Azure API för FHIR. [Deploy IoT Connector (för hands version) med Azure Portal](iot-fhir-portal-quickstart.md) snabb start visade ett exempel på en enhet som hanteras av Azure IoT Central [Skicka telemetri](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) till IoT Connector. IoT Connector kan också arbeta med enheter som tillhandahålls och hanteras via Azure IoT Hub. Den här självstudien visar hur du ansluter och dirigerar enhets data från Azure IoT Hub till IoT Connector.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API för FHIR-resurs med minst en IoT-koppling – [distribuera IoT Connector (för hands version) med Azure Portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub resurs som är ansluten till verkliga eller simulerade enheter – [skapa en IoT Hub med hjälp av Azure Portal](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Om du använder ett Azure IoT Hub simulerat enhets program kan du välja mellan olika språk och system som stöds.

## <a name="get-connection-string-for-iot-connector-preview"></a>Hämta anslutnings sträng för IoT Connector (för hands version)

Azure IoT Hub kräver en anslutnings sträng för att säkert ansluta till din IoT-anslutning. Skapa en ny anslutnings sträng för din IoT-anslutning enligt beskrivningen i [skapa en anslutnings sträng](iot-fhir-portal-quickstart.md#generate-a-connection-string). Behåll den här anslutnings strängen som ska användas i nästa steg.

IoT Connector använder en instans av Azure Event Hub under huven för att ta emot enhets meddelanden. Den anslutnings sträng som skapades ovan är i princip anslutnings strängen till den här underliggande Händelsehubben.

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Ansluta Azure IoT Hub med IoT Connector (förhands granskning)

Azure IoT Hub stöder en funktion som kallas [meddelanderoutning](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) som ger möjlighet att skicka enhets data till olika Azure-tjänster som Händelsehubben, lagrings konto och Service Bus. IoT Connector använder den här funktionen för att ansluta och skicka enhets data från Azure IoT Hub till händelse navets slut punkt.

> [!NOTE] 
> Vid detta tillfälle kan du bara använda PowerShell-eller CLI-kommandot för att [skapa meddelanderoutning](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) eftersom IoT-anslutningens händelsehubben inte finns på kund prenumerationen, och därför inte visas för dig via Azure Portal. Trots att meddelande väg objekt har lagts till med hjälp av PowerShell eller CLI, är de synliga i Azure Portal och kan hanteras därifrån.

Att konfigurera en meddelanderoutning består av två steg.

### <a name="add-an-endpoint"></a>Lägga till en slutpunkt
I det här steget definieras en slut punkt som IoT Hub dirigerar data till. Skapa den här slut punkten med hjälp av antingen [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell-kommandot eller [AZ IoT Hub routing-Endpoint Create CLI-](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) kommando baserat på din preferens.

Här är listan över parametrar som ska användas med kommandot för att skapa en slut punkt:

|PowerShell-parameter|CLI-parameter|Beskrivning|
|---|---|---|
|ResourceGroupName|resource-group|Resurs grupps namnet för din IoT Hub-resurs.|
|Namn|hubb-namn|Namnet på din IoT Hub-resurs.|
|EndpointName|slut punkt-namn|Använd ett namn som du vill tilldela till slut punkten som skapas.|
|EndpointType|slut punkt-typ|Typ av slut punkt som IoT Hub måste ansluta till. Använd literal-värdet "EventHub" för PowerShell och "eventhub" för CLI.|
|EndpointResourceGroup|slut punkt-resurs grupp|Resurs grupps namn för din IoT Connectors Azure API för FHIR-resurs. Du kan hämta det här värdet från översikts sidan för Azure API för FHIR.|
|EndpointSubscriptionId|slut punkt-prenumerations-ID|Prenumerations-ID för IoT-anslutningens Azure API för FHIR-resurs. Du kan hämta det här värdet från översikts sidan för Azure API för FHIR.|
|Begär|connection-string|Anslutnings sträng till IoT-anslutningen. Använd det värde som du fick i föregående steg.|

### <a name="add-a-message-route"></a>Lägg till en meddelande väg
I det här steget definieras en meddelande väg som använder den slut punkt som skapades ovan. Skapa en väg med antingen [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell-kommandot eller [AZ IoT Hub Route skapa](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI-kommando baserat på din preferens.

Här är listan över parametrar som ska användas med kommandot för att skapa en slut punkt:

|PowerShell-parameter|CLI-parameter|Beskrivning|
|---|---|---|
|ResourceGroupName|g|Resurs grupps namnet för din IoT Hub-resurs.|
|Namn|hubb-namn|Namnet på din IoT Hub-resurs.|
|EndpointName|slut punkt-namn|Namnet på den slut punkt som du har skapat ovan.|
|RouteName|flödes namn|Ett namn som du vill tilldela till den meddelande väg som skapas.|
|Källa|typ av källa|Typ av data som ska skickas till slut punkten. Använd litteralt värde för "DeviceMessages" för PowerShell och "DeviceMessages" för CLI.|

## <a name="send-device-message-to-iot-hub"></a>Skicka enhets meddelande till IoT Hub

Använd din enhet (verklig eller simulerad) om du vill skicka ett exempel på ett meddelande om hjärtat-pris som visas nedan till Azure IoT Hub. Det här meddelandet dirigeras till IoT Connector där meddelandet omvandlas till en FHIR observations resurs och lagras i Azure-API: et för FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Se till att skicka enhets meddelandet som följer de [mappar](iot-mapping-templates.md) som kon figurer ATS med din IoT-anslutning.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visa enhets data i Azure API för FHIR

Du kan visa de FHIR observations resurser som skapats av IoT Connector på Azure API för FHIR med Postman. Konfigurera din [Postman för att få åtkomst till Azure API för FHIR](access-fhir-postman-tutorial.md) och gör en `GET` begäran till `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` att Visa observations FHIR resurser med ett hjärta pris värde som skickats i ovanstående exempel meddelande.

> [!TIP]
> Se till att användaren har rätt åtkomst till Azure API för FHIR-dataplan. Använd [Azure Role Based Access Control](configure-azure-rbac.md) för att tilldela nödvändiga data Plans roller.


## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden ställer du in Azure-IoT Hub för att dirigera enhets data till IoT Connector. Välj mellan nästa steg för att lära dig mer om IoT Connector:

Förstå olika stadier i data flödet i IoT Connector.

>[!div class="nextstepaction"]
>[Dataflöde i IoT-anslutningsprogram](iot-data-flow.md)

Lär dig hur du konfigurerar IoT Connector med hjälp av mallar för enhets-och FHIR-mappning.

>[!div class="nextstepaction"]
>[Mallar för mappning i IoT-anslutningsprogram](iot-mapping-templates.md)

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.

