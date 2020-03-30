---
title: Arkitektoniska koncept i Azure IoT Central | Microsoft-dokument
description: Den här artikeln introducerar viktiga begrepp som rör arkitekturen i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271647"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur



Den här artikeln innehåller en översikt över Microsoft Azure IoT Central-arkitekturen.

![Arkitektur på högsta nivå](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyter data med ditt Azure IoT Central-program. En enhet kan:

- Skicka mått som telemetri.
- Synkronisera inställningar med ditt program.

I Azure IoT Central anges de data som en enhet kan utbyta med ditt program i en enhetsmall. Mer information om enhetsmallar finns i [Metadatahantering](#metadata-management).

Mer information om hur enheter ansluter till ditt Azure IoT Central-program finns i [Enhetsanslutning](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-enheter

Förutom enheter som skapats med [Azure IoT SDK:er](https://github.com/Azure/azure-iot-sdks)kan du även ansluta [Azure IoT Edge-enheter](../../iot-edge/about-iot-edge.md) till ett IoT Central-program. Med IoT Edge kan du köra molnintelligens och anpassad logik direkt på IoT-enheter som hanteras av IoT Central. Med IoT Edge-körningen kan du:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Underhåll IoT Edge-säkerhetsstandarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

![Azure IoT Central med Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central gör det möjligt för följande funktioner för IoT Edge-enheter:

- Enhetsmallar för att beskriva funktionerna för en IoT Edge-enhet, till exempel:
  - Distribution manifest uppladdning kapacitet, som hjälper dig att hantera ett manifest för en flotta av enheter.
  - Moduler som körs på IoT Edge-enheten.
  - Telemetrin varje modul skickar.
  - Egenskaperna varje modul rapporterar.
  - Kommandona som varje modul svarar på.
  - Relationerna mellan en IoT Edge gateway-enhetsfunktionsmodell och kapacitetsmodell för nedströmsenheter.
  - Molnegenskaper som inte lagras på IoT Edge-enheten.
  - Anpassningar, instrumentpaneler och formulär som ingår i ditt IoT Central-program.

  Mer information finns i [Connect Azure IoT Edge-enheterna till en azure IoT Central-programartikel.](./concepts-iot-edge.md)

- Möjligheten att etablera IoT Edge-enheter i stor skala med hjälp av Azure IoT-enhetsetableringstjänst
- Regler och åtgärder.
- Anpassade instrumentpaneler och analyser.
- Kontinuerlig dataexport av telemetri från IoT Edge-enheter.

### <a name="iot-edge-device-types"></a>IoT Edge-enhetstyper

IoT Central klassificerar IoT Edge-enhetstyper enligt följande:

- Leaf-enheter. En IoT Edge-enhet kan ha underordnade bladenheter, men dessa enheter är inte etablerade i IoT Central.
- Gateway-enheter med nedströmsenheter. Både gateway-enhet och nedströmsenheter etableras i IoT Central

![IoT Central med IoT Edge Översikt](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge-mönster

IoT Central stöder följande IoT Edge-enhetsmönster:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge som bladenhet

![IoT Edge som bladenhet](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edge-enheten etableras i IoT Central och alla nedströmsenheter och deras telemetri representeras som kommer från IoT Edge-enheten. Nedströmsenheter som är anslutna till IoT Edge-enheten etableras inte i IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge gateway-enhet ansluten till nedströmsenheter med identitet

![IoT Edge med nedströms enhetsidentitet](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT Edge-enheten etableras i IoT Central tillsammans med nedströmsenheter som är anslutna till IoT Edge-enheten. Körningsstöd för etablering av underordnade enheter via gatewayen stöds för närvarande inte.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge gateway-enhet ansluten till nedströmsenheter med identitet som tillhandahålls av IoT Edge-gatewayen

![IoT Edge med nedströms enhet utan identitet](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT Edge-enheten etableras i IoT Central tillsammans med nedströmsenheter som är anslutna till IoT Edge-enheten. Körningsstöd för gateway som tillhandahåller identitet till underordnade enheter och etablering av underordnade enheter stöds för närvarande inte. Om du tar med din egen identitetsöversättningsmodul kan IoT Central stödja det här mönstret.

## <a name="cloud-gateway"></a>Moln gateway

Azure IoT Central använder Azure IoT Hub som en molngateway som möjliggör enhetsanslutning. IoT Hub gör det möjligt:

- Datainmatning i stor skala i molnet.
- Enhetshantering.
- Säker enhetsanslutning.

Mer information om IoT Hub finns i [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Mer information om enhetsanslutning i Azure IoT Central finns i [Enhetsanslutning](concepts-get-connected.md).

## <a name="data-stores"></a>Datalager

Azure IoT Central lagrar programdata i molnet. Programdata som lagras omfattar:

- Enhetsmallar.
- Enhetsidentiteter.
- Enhetens metadata.
- Användar- och rolldata.

Azure IoT Central använder ett tidsseriearum för mätdata som skickas från dina enheter. Tidsseriedata från enheter som används av analystjänsten.

## <a name="analytics"></a>Analys

Analystjänsten ansvarar för att generera anpassade rapporteringsdata som programmet visar. En operatör kan [anpassa analysen](howto-create-analytics.md) som visas i programmet. Analystjänsten bygger på insikter i Azure Time Series och [bearbetar](https://azure.microsoft.com/services/time-series-insights/) mätdata som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](tutorial-create-telemetry-rules.md) har ett nära samarbete för att automatisera uppgifter i programmet. En byggare kan definiera regler baserat på enhetstelemetri, till exempel temperaturen som överskrider ett definierat tröskelvärde. Azure IoT Central använder en strömbehandlare för att avgöra när regelvillkoren är uppfyllda. När ett regelvillkor uppfylls utlöser det en åtgärd som definierats av byggaren. En åtgärd kan till exempel skicka ett e-postmeddelande för att meddela en tekniker att temperaturen i en enhet är för hög.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT Central-program definierar enhetsmallar beteendet och funktionen hos olika typer av enheter. En mall för kylskåpsenhet anger till exempel vilken telemetri ett kylskåp skickar till ditt program.

![Mallarkitektur](media/concepts-architecture/template-architecture.png)

I en IoT Central-programenhetsmall innehåller:

- **Enhetskapacitetsmodeller** anger funktionerna för en enhet, till exempel den telemetri som skickas, de egenskaper som definierar enhetstillståndet och de kommandon som enheten svarar på. Enhetsfunktionerna är ordnade i ett eller flera gränssnitt. Mer information om modeller för enhetsfunktioner finns i dokumentationen för [IoT Plug and Play (preview).](../../iot-pnp/overview-iot-plug-and-play.md)
- **Molnegenskaper** anger egenskaperna IoT Central lagrar för en enhet. Dessa egenskaper lagras endast i IoT Central och skickas aldrig till en enhet.
- **Vyer** anger de instrumentpaneler och formulär som skaparverktyget skapar för att låta operatören övervaka och hantera enheterna.
- **Med anpassningar** kan byggaren åsidosätta några av definitionerna i enhetskapacitetsmodellen för att göra dem mer relevanta för IoT Central-programmet.

Ett program kan ha en eller flera simulerade och verkliga enheter baserat på varje enhetsmall.

## <a name="data-export"></a>Dataexport

I ett Azure IoT Central-program kan du [kontinuerligt exportera dina data](howto-export-data.md) till dina egna Azure Event Hubs och Azure Service Bus-instanser. Du kan också regelbundet exportera dina data till ditt Azure Blob-lagringskonto. IoT Central kan exportera mått, enheter och enhetsmallar.

## <a name="batch-device-updates"></a>Batch-enhetsuppdateringar

I ett Azure IoT Central-program kan du [skapa och köra jobb](howto-run-a-job.md) för att hantera anslutna enheter. Med de här jobben kan du göra massuppdateringar av enhetsegenskaper eller inställningar eller köra kommandon. Du kan till exempel skapa ett jobb för att öka fläkthastigheten för flera kylautomater.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

En [administratör kan definiera åtkomstregler](howto-manage-users-roles.md) för ett Azure IoT Central-program med hjälp av en av de fördefinierade rollerna eller genom att skapa en anpassad roll. Roller avgör vilka områden i programmet en användare har åtkomst till och vilka åtgärder de kan utföra.

## <a name="security"></a>Säkerhet

Säkerhetsfunktioner inom Azure IoT Central inkluderar:

- Data krypteras under transport och i vila.
- Autentisering tillhandahålls antingen av Azure Active Directory eller Microsoft-konto. Tvåfaktorsautentisering stöds.
- Fullständig klientisolering.
- Säkerhet på enhetsnivå.

## <a name="ui-shell"></a>Gränssnittsskal

UI-gränssnittet är ett modernt, responsivt, HTML5-webbläsarbaserat program.
En administratör kan anpassa programmets användargränssnitt genom att använda anpassade teman och ändra hjälplänkarna så att de pekar på dina egna anpassade hjälpresurser. Mer information om anpassning av användargränssnittet finns i Anpassa artikeln om [centralt gränssnitt för Azure IoT Central.](howto-customize-ui.md)

En operatör kan skapa anpassade programinstrumentpaneler. Du kan ha flera instrumentpaneler som visar olika data och växlar mellan dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen i Azure IoT Central, är det föreslagna nästa steget att lära dig mer om [enhetsanslutning](concepts-get-connected.md) i Azure IoT Central.
