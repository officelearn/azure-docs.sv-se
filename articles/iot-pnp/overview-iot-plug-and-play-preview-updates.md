---
title: Nyheter IoT Plug and Play Preview-uppdatering | Microsoft Docs
description: Lär dig vad som är nytt med IoT Plug and Play för hands versions uppdatering.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: 94cec26d883c29eb4d43d2f244a9a2b8f7aea9dd
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183181"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT Plug and Play för hands versions uppdatering

I den här artikeln beskrivs viktiga ändringar i SDK: er, bibliotek, verktyg och tjänster i IoT Plug and Play för hands versions uppdatering i juli 2020. Föregående IoT Plug and Play Preview släpptes i augusti 2019.

## <a name="digital-twins-definition-language-dtdl"></a>Digitalt flätat definitions språk (DTDL)

Den här versionen lägger till stöd för [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) och föråldrade [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview).

I följande lista visas de viktigaste skillnaderna mellan DTDL v1 och DTDL v2. I DTDL v2:

- Modell-ID: n har prefixet `dtmi` i stället för `urn` . Digitala dubbla modell identifierare (DTMI) ersätter de för- `urn` fasta digitala dubbla ID: n som används i DTDL v1. Versionen föregås nu av en `;` . Till exempel, tidigare var du använde `urn:CompanyName:Model:1` , använder du nu `dtmi:CompanyName:Model;1` .
- Ange `@context` till i `dtmi:dtdl:context;2` stället för `http://azureiot.com/v1/contexts/IoTModel.json` .
- Använd **gränssnitts** typen i stället för **CapabilityModel** typer för att modellera en enhet.
- **Komponenter** ersätter **gränssnitts** instanser. Du kan definiera **relationer** och **komponenter** som en del av innehållet i ett **gränssnitt**.
- Ett **gränssnitt** kan ärva från ett annat **gränssnitt**.
- Du kan utöka DTDL med _utöknings bara semantiska typer_. Detta utöknings Bart typ system ger större flexibilitet än hårdkodade semantiska typer som temperatur och fuktighet i DTDL v1.
- Egenskapen **displayUnit** har tagits bort.
- Du kan inte använda inledande eller efterföljande under streck i ett namn. Ledande under streck i ett namn är reserverade för system användning.

Om du vill arbeta med DTDL v1 måste du använda den tidigare versionen av SDK och Azure IoT Explorer 0,10. x. Om du vill arbeta med DTDL v2 behöver du den senaste versionen av SDK och Azure IoT Explorer 0,11. x.

## <a name="no-component-and-multiple-component-implementations"></a>Ingen komponent och implementeringar av flera komponenter

Enkla enheter som använder ett fåtal telemetri, kommandon eller egenskaper kan beskrivas i ett enda gränssnitt utan att använda komponenter. Alla befintliga enheter kan bli IoT-Plug and Play genom att presentera **modell-ID** utan några ändringar i den befintliga enhets implementeringen.

Mer komplexa enheter kan gruppera telemetri, kommandon och egenskaper på olika gränssnitt för att hantera komplexitet och aktivera åter användning på flera enheter. De här enheterna måste uppdateras för att följa en uppsättning enkla regler som definieras i [IoT plug and Play för hands versions konventioner för förhands granskning](concepts-convention.md).

## <a name="registration-and-discovery"></a>Registrering och identifiering

I den här versionen meddelar enheter sitt **modell-ID** med IoT Hub på varje anslutning. IoT Hub cachelagrar **modell-ID: t** som gör det möjligt för Server dels lösningar att hämta **modell-ID** med hjälp av egenskapen enhets lös `modelId` . **Modell-ID: t** kan också hämtas från `$metadata.$model` i den digitala dubbla.

## <a name="microsoft-defined-interfaces"></a>Microsoft-definierade gränssnitt

Följande Microsoft-definierade gränssnitt är inaktuella och publiceras inte i den nya modell databasen:

- **urn: azureiot: ModelDiscovery: DigitalTwin: 1**
- **urn: azureiot: ModelDiscovery: ModelInformation: 1**

Följande gränssnitt publiceras i den nya modell databasen: `dtmi:azure:DeviceManagement:DeviceInformation;1` tillgängligt i URL: en [ https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation ; 1? API-version = 2020-05 01 – för hands version](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

Händelse strukturen för [händelse källan](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) för **DigitalTwinChangeEvents** har ändrats till att använda **JSON-patch-** formatet. Den här ändringen är en avbrytande ändring utan stöd för bakåtkompatibilitet.

## <a name="message-routing"></a>Meddelanderoutning

Telemetri meddelanden har följande ändringar i [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) -samlingen.

Det innehåller nu en **DT-dataschema** -egenskap som är det **modell-ID** som registrerats av enheten.

Egenskapen **DT-subject** representerar komponenten som skickar telemetri.

Egenskapen **iothub-Interface-Name** är föråldrad.

## <a name="device-and-service-sdks"></a>SDK:er för tjänst och enhet

Det finns ingen bakåtkompatibilitet med tidigare för hands versioner av SDK: erna. Du måste ändra koden om du flyttar till den senaste för hands versionen av en SDK.

Med den konventionbaserade metoden behövs inga separata klient-SDK: er för enheter. I den här för hands versionen är de befintliga **DigitalTwinClient** -biblioteken inaktuella på alla språk. I stället har IoT Hub-klientens SDK: er uppdaterats med ett alternativ för att meddela **modell-ID: t**.

Enheter som inte använder komponenter kräver minimal kod ändringar – vi presenterar **modell-ID: t**. Mer komplexa enheter som använder flera komponenter kan kräva vissa återanvändbara funktioner för att implementera [konventionerna](concepts-convention.md). Enhets exempel innehåller en uppsättning funktioner som du kan återanvända i din enhets implementering.

### <a name="service-sdks"></a>Tjänst-SDK:er

Service SDK är tillgängligt i [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/pnp-preview-refresh/digitaltwins/service/readme.md) och [python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>VS Code-tillägg

Tillägget [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) ger redigerings stöd för DTDL v1, integrering med den tidigare versionen av modell lagringen och kodgenerering.

Om du behöver redigerings stöd för DTDL v2 i VS Code installerar du det nya [DTDL-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) i vs Code. Tillägget ger inte integrering med modell databasen eller kodgenerering. Att hantera modeller i databasen görs nu med ett [webb gränssnitt](https://aka.ms/iotmodelrepo) eller [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest).

## <a name="digital-twin-service-side-rest-apis"></a>Digitals dubbla REST-API: er på tjänst Sidan

Den [digitala dubbla service sidans REST-API: er](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) och nytto laster har ändrats. API: erna som stöds är:

- Hämta en digital, dubbel.
- Anropa ett kommando.
- Uppdatera en digital, dubbel med **JSON-korrigerings** nytto Last.

Befintliga REST-API: er fortsätter att stödjas i den här versionen.

## <a name="model-repository"></a>Modelldatabas

Nu finns det en enda modell lagrings plats som innehåller både offentliga publicerade modeller och privata RBAC-skyddade företags modeller. Alla modeller har en unik identifierare och är oföränderliga när de skapas.

Befintliga företags modell databaser från den tidigare versionen stöds inte i den här versionen. Du kan fortsätta att använda [Azure-certifierad för IoT](https://preview.catalog.azureiotsolutions.com/products) -webbplatsen för att hantera de gamla DTDL v1-modellerna. Du kan dock inte längre använda den här webbplatsen för att registrera, testa och certifiera enheter.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central uppdateras för tillfället för att stödja IoT Plug and Play för hands versions uppdatering.
