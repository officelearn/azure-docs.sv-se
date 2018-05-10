---
title: Integrera SIM data i fjärråtkomst övervakning lösningen - Azure | Microsoft Docs
description: Den här artikeln beskriver hur du integrerar Telefonica SIM data i Fjärrövervaknings-lösning.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrera SIM data i Fjärrövervaknings-lösning

## <a name="overview"></a>Översikt
IoT-enheter ansluter ofta till molnet genom att använda ett SIM-kort som gör att de kan skicka dataströmmar från var som helst. Azure IoT-Fjärrövervaknings-lösning kan integreringen av SIM hanteringsdata så att operatörer kan också spåra hälsotillståndet för enheten via data från SIM. Fjärråtkomst övervakning innehåller out-of-box-integrering med Telefonica IoT så att kunder som använder dess IoT anslutning plattform synkronisera sina enheter SIM-kort anslutningsdata lösningarna. Den här lösningen kan utökas för att stödja andra leverantörer av företagets telefon via GitHub-lagringsplatsen.
I den här guiden får du lära dig att:
* Integrera SIM data i Fjärrövervaknings-lösning
* Visa realtid telemetri
* Visa SIM data 

## <a name="telefonica-iot-integration-setup"></a>Telefonica IoT integrerade installationen

### <a name="prerequisites"></a>Förutsättningar
Följ dessa steg för att synkronisera anslutningsdata till Azure Remote övervakning lösning:

1.  Fylla i en begäran i [Telefonicas plats](https://iot.telefonica.com/contact), väljer alternativet **Azure Fjärrövervaknings**, inklusive dina kontaktdata.
2.  Telefonica aktiveras för ditt konto. 
3.  Om du ännu inte har en Telefónica klient och du vill få detta eller andra IoT anslutning molnet redo tjänster finns [Telefonicas plats](https://iot.telefonica.com/contact) och väljer alternativet **anslutning**.

### <a name="telefonica-sim-setup"></a>Telefonica SIM-installationen
Telefónica SIM & Azure dubbla ID enhetsassociering kommer att baseras på Telefónica IoT SIM ”alias”-egenskap. 

Gå till [Telefónica IoT anslutning plattform Portal](https://m2m-movistar-es.telefonica.com/) > SIM inventering > Välj din SIM och uppdatera varje SIM ”alias” med din önskade dubbla deviceID. 

Den här åtgärden kan även utföras i gruppläge (se Telefónica IoT anslutning plattform användarhandböcker)

![Telefonica uppdatering](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Du kan följa dessa självstudier använder för att ansluta enheten till den fjärranslutna övervakning, [C](iot-suite-connecting-devices-linux.md) eller [nod](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visa enhetstelemetrin och SIM-egenskaper
När Telefonica-konto är korrekt konfigurerat och din enhet är ansluten, kan du visa information om enhet och SIM-data.
Följande parametrar för anslutningen kan publiceras:
* ICCID
* IP-adress
* Nätverksanvändning
* SIM-Status
* Nätverksbaserade plats
* Förbrukade datatrafik

![Instrumentpanel](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över hur du integrerar SIM Data i Azure IoT-Fjärrövervaknings är här föreslagna nästa steg för Solution Accelerator:

* [Fungera Azure IoT-Fjärrövervaknings-lösning](iot-suite-remote-monitoring-explore.md)
* [Utföra avancerad övervakning](iot-suite-remote-monitoring-monitor.md)
* [Hantera dina enheter](iot-suite-remote-monitoring-manage.md)
* [Felsöka enhetsproblem](iot-suite-remote-monitoring-maintain.md)

