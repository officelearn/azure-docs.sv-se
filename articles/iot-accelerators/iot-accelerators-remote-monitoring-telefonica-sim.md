---
title: Integrera SIM data i fjärråtkomst övervakning lösningen - Azure | Microsoft Docs
description: Den här artikeln beskriver hur du integrerar Telefónica SIM data i Fjärrövervaknings-lösning.
services: iot-suite
suite: iot-suite
author: hegate
manager: timlt
ms.author: hegate
ms.service: iot-suite
ms.date: 05/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 590a24113d66edacc6edcfe988330f643f1aa57a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrera SIM data i Fjärrövervaknings-lösning

IoT-enheter ansluter ofta till molnet genom att använda ett SIM-kort som gör att de kan skicka dataströmmar från var som helst. Azure IoT-Fjärrövervaknings-lösning kan integrering av IoT hanteras anslutningsdata, så att operatörer kan också spåra hälsotillståndet för enheten via data från IoT SIM.

Fjärråtkomst övervakning ger out-of-box-integrering med Telefónica IoT-anslutning, vilket innebär att kunder som använder dess IoT anslutning plattform synkronisera sina enheter SIM-kort anslutningsdata till sina lösningar. Den här lösningen kan utökas för att stödja andra leverantörer av IoT-anslutning via GitHub [databasen](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

I den här guiden får du lära dig att:

* Integrera Telefónica IoT SIM data i Fjärrövervaknings-lösning
* Visa realtid telemetri
* Visa SIM data

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT integrerade installationen

### <a name="prerequisites"></a>Förutsättningar

Ytterligare Fjärrövervaknings funktionen är för närvarande under förhandsgranskning. Följ dessa steg för att synkronisera anslutningsdata till Azure Remote övervakning lösning:

1. Fylla i en begäran i [Telefónicas plats](https://iot.Telefónica.com/contact), väljer alternativet **Azure Fjärrövervaknings**, inklusive dina kontaktdata.
2. Telefónica aktiverar ditt konto.
3. Om du ännu inte har en Telefónica klient och du vill få detta eller andra IoT anslutning molnet redo tjänster finns [Telefónicas plats](https://iot.Telefónica.com/contact) och väljer alternativet **anslutning**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM-installationen
Telefónica SIM & Azure dubbla ID enhetsassociering baseras på Telefónica IoT SIM ”alias”-egenskap. 

Gå till [Telefónica IoT anslutning plattform Portal](https://m2m-movistar-es.telefonica.com/) > SIM inventering > Välj din SIM och uppdatera varje SIM ”alias” med din önskade dubbla deviceID. Den här åtgärden kan även utföras i gruppläge (se Telefónica IoT anslutning plattform användarhandböcker).

Den här åtgärden kan även utföras i gruppläge (se Telefónica IoT anslutning plattform användarhandböcker)

![Telefónica uppdatering](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Du kan följa dessa självstudier använder för att ansluta enheten till den fjärranslutna övervakning, [C](iot-accelerators-connecting-devices-linux.md) eller [nod](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visa enhetstelemetrin och SIM-egenskaper

När kontot Telefónica är korrekt konfigurerad och din enhet är ansluten, kan du visa information om enhet och SIM data.

Följande parametrar för anslutningen publiceras:

* ICCID
* IP-adress
* Nätverksanvändning
* SIM-Status
* Nätverksbaserade plats
* Förbrukade datatrafik

![Instrumentpanel](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över hur du integrerar SIM Data i Azure IoT-Fjärrövervaknings är här föreslagna nästa steg för Solution Accelerator:

* [Fungera Azure IoT-Fjärrövervaknings-lösning](iot-accelerators-remote-monitoring-explore.md)
* [Utföra avancerad övervakning](iot-accelerators-remote-monitoring-monitor.md)
* [Hantera dina enheter](iot-accelerators-remote-monitoring-manage.md)
* [Felsöka enhetsproblem](iot-accelerators-remote-monitoring-maintain.md)

