---
title: "Anpassa den fjärranslutna övervakningslösning - Azure | Microsoft Docs"
description: "Den här artikeln innehåller information om hur du kan komma åt källkoden för fjärråtkomst övervakning förkonfigurerade lösningen."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 886a4412ac348869563a03d697f4363cb3dea8f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Anpassa fjärråtkomst övervakning förkonfigurerade lösning

Den här artikeln innehåller information om hur du kan använda källkoden och anpassa remote övervakningen förkonfigurerade lösningen. Den här artikeln innehåller:

* GitHub-lagringsplatser som innehåller källkoden och för mikrotjänster som utgör den förkonfigurerade lösningen.
* Vanliga scenarier för anpassning till exempel lägga till en ny typ av enhet.

## <a name="project-overview"></a>Översikt över projekt

### <a name="implementations"></a>Implementeringar

Fjärråtkomst övervakningslösning har både .NET och Java-implementeringar. Båda implementeringar ger liknande funktionalitet och förlitar sig på samma underliggande Azure-tjänster. Du kan hitta de översta GitHub-lagringsplatser som här:

* [.NET-lösning](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java-lösning](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Mikrotjänster

Om du är intresserad av en viss funktion i lösningen kan du komma åt GitHub-lagringsplatser för varje enskild mikrotjänster. Varje mikrotjänster implementerar en annan del av funktionen för lösningen. Läs mer om övergripande arkitektur i [fjärrövervaknings förkonfigurerade lösningsarkitektur](iot-suite-remote-monitoring-sample-walkthrough.md).

Den här tabellen sammanfattas aktuella tillgängligheten för varje mikrotjänster för varje språk:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Mikrotjänster      | Beskrivning | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Webbgränssnittet            | Webbprogrammet för fjärranslutna övervakningslösning. Implementerar Gränssnittet använder React.js framework. | [N/A(react.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A(react.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Hanteraren för IoT-hubb   | Hanterar kommunikation med IoT-hubben.        | [Tillgängliga](https://github.com/Azure/iothub-manager-java) | [Tillgängliga](https://github.com/Azure/iothub-manager-dotnet)   |
| Autentisering    |  Hanterar Azure Active Directory-integrering.  | Ännu ej tillgänglig | [Tillgängliga](https://github.com/Azure/pcs-auth-dotnet)   |
| Enheten simulering | Hanterar en pool av simulerade enheter. | Ännu ej tillgänglig | [Tillgängliga](https://github.com/Azure/device-simulation-dotnet)   |
| Telemetri         | Gör enhetstelemetrin tillgängliga för Användargränssnittet. | [Tillgängliga](https://github.com/Azure/device-telemetry-java) | [Tillgängliga](https://github.com/Azure/device-telemetry-dotnet)   |
| Telemetri Agent   | Analyserar telemetri dataströmmen, lagrar meddelanden från Azure IoT Hub och genererar aviseringar enligt definierade regler.  | [Tillgängliga](https://github.com/Azure/telemetry-agent-java) | [Tillgängliga](https://github.com/Azure/telemetry-agent-dotnet)   |
| UI-konfiguration         | Hanterar konfigurationsdata i användargränssnittet. | [Tillgängliga](https://github.com/azure/pcs-ui-config-java) | [Tillgängliga](https://github.com/azure/pcs-ui-config-dotnet)   |
| Lagringsadapter   |  Hanterar interaktioner med storage-tjänst.   | [Tillgängliga](https://github.com/azure/pcs-storage-adapter-java) | [Tillgängliga](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Omvänd proxy     | Exponerar privata resurser i ett hanterat sätt via en unik slutpunkt. | Ännu ej tillgänglig | [Tillgängliga](https://github.com/Azure/reverse-proxy-dotnet)   |

Java-lösning använder för närvarande .NET-autentisering, simulering och mikrotjänster omvänd proxy. Dessa mikrotjänster ersätts av Java-versioner så snart de blir tillgängliga.

## <a name="presentation-and-visualization"></a>Presentation och visualisering

I följande avsnitt beskrivs alternativ för att anpassa presentation och visualiseringar lagret i den fjärranslutna övervakningslösning:

### <a name="change-the-logo-in-the-ui"></a>Ändra logotypen i Användargränssnittet

Standard-distributionen använder Contoso företagets namn och logotyp i Användargränssnittet. Ändra dessa UI-element för att visa företagets namn och logotyp:

1. Använd följande kommando för att klona lagringsplatsen Webbgränssnittet:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Om du vill ändra namnet på företaget, öppna den `src/common/lang.js` filen i en textredigerare.

1. Leta upp följande rad i filen:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Ersätt `Contoso` med namnet på ditt företag. Exempel:

    ```js
    CONTOSO: 'YourCo',
    ```

1. Spara filen.

1. Om du vill uppdatera logotypen, lägger du till en ny SVG-fil och den `assets/icons` mapp. Befintliga logotypen är den `assets/icons/Contoso.svg` filen.

1. Öppna den `src/components/layout/leftNav/leftNav.js` filen i en textredigerare.

1. Leta upp följande rad i filen:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Ersätt `Contoso.svg` med namnet på din logotyp-fil. Exempel:

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Leta upp följande rad i filen:

    ```js
    alt="ContosoIcon"
    ```

1. Ersätt `ContosoIcon` med din `alt` text. Exempel:

    ```js
    alt="YourCoIcon"
    ```

1. Spara filen.

1. Om du vill testa ändringarna, kan du köra den uppdaterade `webui` på den lokala datorn. Information om hur du skapar och kör den `webui` lösningen lokalt, se [bygga, köra och testa lokalt](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) i den `webui` GitHub-lagringsplatsen Readme-filen.

1. Om du vill distribuera dina ändringar finns i [referens Utvecklarhandbok](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Anpassa kartan

Finns det [anpassa kartan](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) sida i GitHub för information om kartan komponenter i lösningen.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Andra alternativ för anpassning

Du kan redigera koden för att ändra presentationen och visualiseringar lagret i den fjärranslutna övervakningslösning ytterligare. De relevanta GitHub-lagringsplatser är:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Fjärrövervaknings WebUI i Azure-datorer](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>Enhetsanslutning och strömning

I följande avsnitt beskrivs alternativ för att anpassa enhetsanslutning och strömning lager i fjärranslutna övervakningslösning. [Enhetsmodeller](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) beskrivs de typer av enheter och telemetri i lösningen. Du använder enhetsmodeller för både simulerade och fysiska enheter.

Ett exempel på en fysisk enhet implementering finns [ansluta enheten till den fjärranslutna förkonfigurerade övervakningslösning](iot-suite-connecting-devices-node.md).

Om du använder en _fysisk enhet_, måste du ange klientprogrammet med en enhetsmodell som innehåller metadata och telemetri specifikation för enheten.

I följande avsnitt beskrivs enhetsmodeller med simulerade enheter:

### <a name="add-a-telemetry-type"></a>Lägga till en telemetri typ.

Enhetstyper i Contoso-demon lösningen ange telemetri som skickar varje typ av enhet. Om du vill ange ytterligare telemetri typer, kan en enhet skicka telemetri definitioner som metadata i lösningen. Om du använder det här formatet instrumentpanelen förbrukar din enhet telemetri och tillgängliga metoder dynamiskt och du behöver inte ändra Användargränssnittet. Alternativt kan du ändra typdefinitionen för enheten i lösningen.

Mer information om hur du lägger till anpassad telemetri i den _enheten simulatorn_ mikrotjänster, se [testa din lösning med simulerade enheter](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Lägg till en typ av enhet

Contoso demo lösningen definierar vissa typer av enheter exempel. Den här lösningen kan du definiera anpassade enhetstyper för att uppfylla kraven för specifika program. Företaget kan till exempel använda en industriella gateway som användarens primära enhet ansluten till lösningen.

Om du vill skapa enheten exakt så som du behöver ändra program som körs på din enhet för att matcha enhetskraven.

Mer information om hur du lägger till en ny typ av enhet i den _enheten simulatorn_ mikrotjänster, se [testa din lösning med simulerade enheter](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>Definiera anpassade metoder för simulerad enheter

Information om hur du definierar anpassade metoder för simulerad enheter i den fjärranslutna övervakningslösning finns [enhetsmodeller](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) i GitHub-lagringsplatsen.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Med hjälp av en fysisk enhet

Om du vill implementera metoder och jobb på fysiska enheter, finns i följande artiklar för IoT-hubb:

* [Förstå och anropa direkt metoder från IoT-hubb](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Schemalägga jobb på flera enheter](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Andra alternativ för anpassning

Om du vill ändra ytterligare enhetsanslutning och strömning lager i fjärranslutna övervakningslösning, kan du redigera koden. De relevanta GitHub-lagringsplatser är:

* [Enhetstelemetrin (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [Enhetstelemetrin (Java)](https://github.com/Azure/device-telemetry-java)
* [Telemetri Agent (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Telemetri Agent (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>Databearbetning och analys

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Om du vill ändra databehandling och analytics lager i fjärranslutna övervakningslösning, kan du redigera koden. De relevanta GitHub-lagringsplatser är:

* [Telemetri Agent (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [Telemetri Agent (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>Infrastruktur

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Du kan redigera koden för att ändra infrastrukturen i fjärranslutna övervakningslösning. De relevanta GitHub-lagringsplatser är:

* [IoTHub Manager (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [IoTHub Manager (Java)](https://github.com/Azure/iothub-manager-java)
* [Kortet (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [Kortet (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om resurserna som är tillgängliga för att anpassa den förkonfigurerade lösningen.

Mer information om fjärråtkomst övervakning förkonfigurerade lösningen finns [fjärråtkomst övervakning arkitektur](iot-suite-remote-monitoring-sample-walkthrough.md)

Mer information om hur du anpassar den fjärranslutna övervakningslösning finns:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->