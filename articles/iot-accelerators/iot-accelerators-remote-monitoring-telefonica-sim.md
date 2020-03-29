---
title: Integrera SIM-data i lösningen för fjärrövervakning – Azure| Microsoft-dokument
description: I den här artikeln beskrivs hur du integrerar Telefónica SIM-data i lösningen för fjärrövervakning.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442247"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrera SIM-data i lösningen för fjärrövervakning

IoT-enheter ansluter ofta till molnet med ett SIM-kort som gör att de kan skicka dataströmmar var som helst. Azure IoT Remote Monitoring-lösningen möjliggör integrering av IoT-hanterade anslutningsdata, så att operatörerna också kan spåra enhetens hälsa via data som tillhandahålls av IoT SIM-kortet.

Fjärrövervakning tillhandahåller direktintegrering med Telefónica IoT-anslutning, så att kunder som använder sin IoT-anslutningsplattform kan synkronisera sina enhets-SIM-anslutningsdata till sina lösningar. Den här lösningen kan utökas för att stödja andra IoT-anslutningsleverantörer via [GitHub-databasen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

I den här självstudiekursen får du lära du dig att:

* Integrera Telefónica IoT SIM-data i lösningen för fjärrövervakning
* Visa telemetri i realtid
* Visa SIM-data

## <a name="telefnica-iot-integration-setup"></a>Konfiguration av Integrering av Telefónica IoT

### <a name="prerequisites"></a>Krav

Den här ytterligare funktionen för fjärrövervakning är för närvarande i förhandsversion. Så här synkroniserar du anslutningsdata till Azure Remote Monitoring Solution:

1. Fyll i en begäran på [Telefónicas webbplats,](https://iot.telefonica.com/contact)välj alternativet **Azure Remote Monitoring**, inklusive dina kontaktdata.
2. Telefónica aktiverar ditt konto.
3. Om du inte är en Telefónica klient ännu och du vill njuta av detta eller andra IoT Connectivity Cloud Ready tjänster, besök [Telefónica webbplats](https://iot.telefonica.com/) och välj alternativet **Anslutning**.

### <a name="telefnica-sim-setup"></a>Konfiguration av Telefónica SIM
Telefónica SIM & Azure Twin-enhets-ID-associationen baseras på telefónica IoT SIM "alias"-egenskapen. 

Navigera till [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM-inventering > Välj sim-kort och uppdatera varje SIM-format med önskat Twin deviceID. Denna uppgift kan också göras i massläge (se Telefónica IoT Connectivity Platform användarmanualer).

Denna uppgift kan också göras i bulkläge (se Telefónica IoT Connectivity Platform användarmanualer)

![Telefónica Uppdatering](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Om du vill ansluta enheten till fjärrövervakningen kan du följa dessa självstudier med [C](iot-accelerators-connecting-devices-linux.md) eller [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visa enhetstelemetri och SIM-egenskaper

När ditt Telefónica-konto är korrekt konfigurerat och enheten är ansluten kan du visa enhetsinformation och SIM-data.

Följande anslutningsparametrar publiceras:

* ICCID
* IP-adress
* Nätverksnärvaro
* SIM-status
* Nätverksbaserad plats
* Förbrukad datatrafik

![Instrumentpanel](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över hur du integrerar SIM-data i Azure IoT Remote Monitoring föreslås här nästa steg för lösningar acceleratorer:

* [Använda Azure IoT Remote Monitoring-lösningen](quickstart-remote-monitoring-deploy.md)
* [Utföra avancerad övervakning](iot-accelerators-remote-monitoring-monitor.md)
* [Hantera dina enheter](iot-accelerators-remote-monitoring-manage.md)
* [Felsöka enhetsproblem](iot-accelerators-remote-monitoring-maintain.md)

