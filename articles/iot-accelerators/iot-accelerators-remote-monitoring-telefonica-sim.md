---
title: Integrera SIM-data i den Övervakningslösningen – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du integrerar Telefónica SIM-data i lösningen för fjärrövervakning.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442247"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrera SIM-data i lösningen för fjärrövervakning

IoT-enheter ansluter ofta till molnet med ett SIM-kort som gör att de kan skicka dataströmmar från var som helst. Azure IoT av lösningen för fjärrövervakning tillåter integrering av IoT hanteras anslutningsdata, så att operatörer kan också spåra hälsotillståndet för enheten via data från IoT SIM.

Fjärrövervakning ger från box-integrering med Telefónica IoT-anslutning, vilket gör att kunder som använder sin anslutning IoT-plattform synkronisera sina enheter anslutningsdata för SIM-kort i sina lösningar. Den här lösningen kan utökas för att stödja andra IoT-anslutningsleverantörer via GitHub [databasen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

I den här guiden får du lära dig att:

* Integrera Telefónica IoT SIM-data till lösningen för fjärrövervakning
* Visa telemetri i realtid
* Visa SIM-data

## <a name="telefnica-iot-integration-setup"></a>Installationsprogrammet för Telefónica IoT-integration

### <a name="prerequisites"></a>Nödvändiga komponenter

Den här ytterligare fjärrövervakning funktionen förhandsvisas just nu. Följ dessa steg för att synkronisera dina anslutningsdata till fjärransluten Övervakningslösningen för Azure:

1. Fylla en förfrågan på [Telefónicas plats](https://iot.telefonica.com/contact), Välj alternativet **Azure fjärrövervakning**, inklusive dina kontaktdata.
2. Telefónica aktiverar ditt konto.
3. Om du är inte en Telefónica klient än och du vill dra nytta av detta eller andra IoT anslutning molnet redo tjänster, besök [Telefónicas plats](https://iot.telefonica.com/) och välja alternativet **anslutning**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM-installationen
Telefónica SIM & Azure Twin ID enhetsassociering baseras på Telefónica IoT SIM ”alias”-egenskap. 

Gå till [Telefónica IoT anslutning plattform Portal](https://m2m-movistar-es.telefonica.com/) > SIM inventering > Välj din SIM och uppdatera varje SIM ”alias” med ditt önskade Twin enhets-ID. Den här uppgiften kan också göras i bulk-läge (se Telefónica IoT anslutning plattform användarhandböcker).

Den här uppgiften kan också göras i bulk-läge (se Telefónica IoT anslutning plattform användarhandböcker)

![Telefónica Update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

För att ansluta enheten till den, kan du följa de här självstudierna använder [C](iot-accelerators-connecting-devices-linux.md) eller [noden](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visa enhetstelemetri och SIM-egenskaper

När ditt Telefónica-konto är korrekt konfigurerad och din enhet är ansluten, kan du Visa enhetsinformation och SIM-data.

Följande parametrar för anslutning publiceras:

* ICCID
* IP-adress
* Nätverksanvändning
* SIM-Status
* Nätverksbaserade plats
* Förbrukade datatrafik

![Instrumentpanel](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över hur du integrerar SIM-Data i Azure IoT-fjärrövervakning är här nästa föreslagna steg för Solution Accelerator:

* [Fungera Azure IoT av lösningen för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Utföra avancerad övervakning](iot-accelerators-remote-monitoring-monitor.md)
* [Hantera dina enheter](iot-accelerators-remote-monitoring-manage.md)
* [Felsöka enhetsproblem](iot-accelerators-remote-monitoring-maintain.md)

