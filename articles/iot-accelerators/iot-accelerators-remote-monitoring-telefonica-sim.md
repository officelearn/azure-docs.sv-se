---
title: Integrera SIM-data i fjärr styrnings lösningen – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du integrerar Telefónica SIM-data i lösningen för fjärrövervakning.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 21d84bea808ba5832b81d415001a3fc25a7e1630
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006771"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrera SIM-data i lösningen för fjärrövervakning

IoT-enheter ansluter ofta till molnet med ett SIM-kort som gör det möjligt för dem att skicka data strömmar från var som helst. Med Azure IoT Remote Monitoring-lösningen kan du integrera IoT-hanterade anslutnings data, så att operatörerna även kan spåra enhetens hälso tillstånd genom de data som tillhandahålls av IoT SIM.

Fjärrövervakning ger direkt integrering med Telefónica IoT-anslutning, så att kunder som använder sin IoT Connectivity-plattform synkroniserar sina enheters SIMs anslutnings data till sina lösningar. Den här lösningen kan utökas till att stödja andra IoT Connectivity-providrar via GitHub- [lagringsplatsen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

I de här självstudierna får du lära dig att

* Integrera Telefónica IoT SIM-data i lösningen för fjärrövervakning
* Visa telemetri i realtid
* Visa SIM-data

## <a name="telefnica-iot-integration-setup"></a>Konfiguration av Telefónica IoT-integrering

### <a name="prerequisites"></a>Förutsättningar

Denna ytterligare fjärr styrnings funktion är för närvarande en för hands version. Följ dessa steg om du vill synkronisera dina anslutnings data till Azure-fjärrövervakningen:

1. Fyll i en begäran på [Telefónica's plats](https://iot.telefonica.com/contact), Välj alternativet **Azure-fjärrövervakning**, inklusive dina kontakt data.
2. Telefónica aktiverar ditt konto.
3. Om du inte är en Telefónica-klient än och du vill njuta av detta eller andra IoT-anslutningar för att ansluta till molnet går du till [Telefónica's-webbplatsen](https://iot.telefonica.com/) och väljer alternativet **anslutning**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM-installation
Telefónica SIM & associationen för en delad Azure-enhets-ID baseras på Telefónica IoT SIM "alias"-egenskapen. 

Gå till [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM inventory > Välj ditt SIM och uppdatera varje SIM-"alias" med din önskade dubbla deviceID. Den här uppgiften kan också göras i Mass läge (se Telefónica IoT Connectivity Platform User Manuals).

Den här uppgiften kan också göras i Mass läge (se Telefónica IoT Connectivity Platform User Manuals)

![Telefónica-uppdatering](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Om du vill ansluta din enhet till fjärrövervakningen kan du följa de här självstudierna med hjälp av [C](iot-accelerators-connecting-devices-linux.md) eller [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visa enhetens telemetri och SIM-egenskaper

När ditt Telefónica-konto har kon figurer ATS korrekt och enheten är ansluten kan du Visa enhets information och SIM-data.

Följande anslutnings parametrar publiceras:

* ICCID
* IP-adress
* Nätverks närvaro
* SIM-status
* Nätverks baserad plats
* Förbrukad data trafik

![Skärm bild av Device Explorer-fönstret på instrument panelen för Azure IoT-fjärrövervakning. En rad som visar WeatherStation enhets information är markerad.](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över hur du integrerar SIM-data i Azure IoT Remote Monitoring föreslås följande steg för lösningar acceleratorer:

* [Använda Azure IoT-lösningen för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Utföra avancerad övervakning](iot-accelerators-remote-monitoring-monitor.md)
* [Hantera dina enheter](iot-accelerators-remote-monitoring-manage.md)
* [Felsöka enhets problem](iot-accelerators-remote-monitoring-maintain.md)

