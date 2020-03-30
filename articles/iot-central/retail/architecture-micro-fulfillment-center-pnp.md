---
title: Azure IoT Central mikro-uppfyllelse center | Microsoft-dokument
description: Lär dig att bygga en mikro-uppfyllelse center ansökan med hjälp av vår Micro-uppfyllelse center ansökan mall i IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f752c77a6a62b9b259a8bb1869ca03ff6a19b1f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020887"
---
# <a name="micro-fulfillment-center-architecture"></a>Arkitektur för mikrouppfyllelsecenter

Micro-uppfyllelse center lösningar kan du digitalt ansluta, övervaka och hantera alla aspekter av en helt automatiserad uppfyllelse centrum för att minska kostnaderna genom att eliminera driftstopp samtidigt öka säkerheten och övergripande effektivitet. Dessa lösningar kan byggas med hjälp av en av programmallarna inom IoT Central och arkitekturen nedan som vägledning.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
- Gateway-enheter som skickar telemetri och aggregerade insikter till IoT Central
- Kontinuerlig dataexport till önskad Azure-tjänst för manipulering
- Data kan struktureras i önskat format och skickas till en lagringstjänst
- Affärsprogram kan fråga data och generera insikter som driver butiksverksamheten
 
Låt oss ta en titt på viktiga komponenter som i allmänhet spelar en roll i en mikro-uppfyllelse center lösning.

## <a name="robotic-carriers"></a>Robothållare

En mikro-uppfyllelse center lösning kommer sannolikt att ha en stor uppsättning robotbärare genererar olika typer av telemetri signaler. Dessa signaler kan intas av en gateway-enhet, aggregeras och sedan skickas till IoT Central som återspeglas av den vänstra sidan av arkitekturdiagrammet.  

## <a name="condition-monitoring-sensors"></a>Sensorer för tillståndsövervakning

En IoT-lösning börjar med en uppsättning sensorer som fångar meningsfulla signaler inifrån ditt uppfyllelsecenter. Det återspeglas av olika typer av sensorer längst till vänster i arkitekturdiagrammet ovan.

## <a name="gateway-devices"></a>Gateway-enheter

Många IoT-sensorer kan mata råsignaler direkt till molnet eller till en gateway-enhet som finns nära dem. Gateway-enheten utför dataaggregering vid kanten innan sammanfattningsstatistik skickas till ett IoT Central-program. Gateway-enheterna ansvarar också för att skicka ut kommando- och kontrollåtgärder till sensorenheterna när det är tillämpligt. 

## <a name="iot-central-application"></a>IoT Central ansökan

Azure IoT Central-programmet förtärer data från olika typer av IoT-sensorer, robotar samt gateway-enheter i uppfyllelsecentermiljön och genererar en uppsättning meningsfulla insikter.

Azure IoT Central ger också en skräddarsydd upplevelse för butiksoperatören så att de kan fjärrövervaka och hantera infrastrukturenheterna.

## <a name="data-transform"></a>Datatransformering
Azure IoT Central-programmet i en lösning kan konfigureras för att exportera råa eller aggregerade insikter till en uppsättning Azure PaaS -tjänster (Platform-as-a-Service) som kan utföra datamanipulering och berika dessa insikter innan de skickas i ett företag Program. 

## <a name="business-application"></a>Affärsprogram
IoT-data kan användas för att driva olika typer av affärsprogram som distribueras i en butiksmiljö. En uppfyllelse center manager eller anställd kan använda dessa program för att visualisera affärsinsikter och vidta meningsfulla åtgärder i realtid. Om du vill veta hur du skapar en Power BI-instrumentpanel i realtid för ditt detaljhandelsteam följer du [självstudien](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Nästa steg
* Kom igång med programmallen [Micro-uppfyllelsecenter.](https://aka.ms/checkouttemplate) 
* Ta en titt på [självstudien](https://aka.ms/mfc-tutorial) som hjälper dig att skapa en lösning med hjälp av appmallen Micro-fulfillment Center.
