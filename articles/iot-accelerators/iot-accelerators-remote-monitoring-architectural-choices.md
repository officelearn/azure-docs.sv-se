---
title: Fjärråtkomst övervakning lösning arkitektur val - Azure | Microsoft Docs
description: Den här artikeln beskriver arkitekturen och tekniska valen som gjorts i Fjärrövervaknings
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/30/2018
ms.topic: conceptual
ms.openlocfilehash: 6c4bf0e4bf0a6c1a791cf762ec9bb44ed5c0b1bd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627696"
---
# <a name="remote-monitoring-architectural-choices"></a>Fjärråtkomst övervakning arkitektur alternativ

Azure IoT-Fjärrövervaknings solution accelerator är en öppen källkod, MIT-licens, solution accelerator som beskriver vanliga scenarier för IoT som enhetsanslutning, hantering och bearbetning av dataströmmen så kunder kan påskynda deras utveckling processen.  Fjärrövervaknings lösningen följer rekommenderade Azure IoT-Referensarkitektur publicerade [här](https://aka.ms/iotrefarchitecture).  

Den här artikeln beskriver arkitektur- eller valen som gjorts i varje delsystem för övervakning av fjärråtkomst-lösningen och beskriver alternativ anses vara.  Det är viktigt att notera att de tekniska val som görs i lösningen Fjärrövervaknings inte är det enda sättet att implementera en fjärransluten övervakning IoT-lösning.  Teknisk implementering är en baslinje för att skapa en lyckade tillämpning och bör ändras så att de passar kunskaper, erfarenhet och lodräta programbehov för en kund-lösningsimplementering.

## <a name="architectural-choices"></a>Alternativ arkitektur

### <a name="microservices-serverless-and-cloud-native"></a>Mikrotjänster, serverlösa, och molntjänster interna

Arkitekturen rekommenderar vi för IoT-appar är moln ursprunglig mikrotjänster, och serverlösa baserade.  Olika delsystem för en IoT-program ska byggas som diskreta tjänster som är oberoende av varandra distribueras och kunna skalas oberoende av varandra.  Dessa attribut aktivera större skala, större flexibilitet vid uppdatering av enskilda delsystem och ger flexibilitet för att välja lämplig teknik på grundval av per undersystemet.  Mikrotjänster kan implementeras med flera tekniker. Till exempel använder teknik för behållare, till exempel Docker för serverlösa teknik som Azure Functions eller värd för mikrotjänster i PaaS-tjänster, till exempel Azure App Service.

## <a name="core-subsystem-technology-choices"></a>Core undersystemet tekniska alternativ

Det här avsnittet innehåller information technology valen som gjorts i lösningen Fjärrövervaknings för varje delsystem core.

![Core Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Gateway för moln
Azure IoT Hub används som gateway Fjärrövervaknings lösning moln.  IoT-hubben erbjuder säker, dubbelriktad kommunikation med enheter. Du kan lära dig mer om IoT-hubb [här](https://azure.microsoft.com/services/iot-hub/). För IoT enhetsanslutning används .NET Core och Java IoT-hubb SDK: er.  SDK: erna erbjuder omslutningar runt IoT Hub REST-API och hantera scenarier, till exempel återförsök.

### <a name="stream-processing"></a>Strömbearbetning
För dataströmmen använder bearbetning Fjärrövervaknings lösningen Azure Stream Analytics för bearbetning av komplexa regeln.  För kunder som vill ha enklare regler, ha vi också en anpassad mikrotjänster med stöd för bearbetning av enkla regler, även om denna inställning inte en del av out-of-box-distributionen. Den referens för arkitekturen rekommenderar användning av Azure Functions för enkel regel bearbetning och Azure Stream Analytics (ASA) för bearbetning av komplexa regeln.  

### <a name="storage"></a>Storage
För lagring, Azure Cosmos DB används för alla lagringsbehov: kyla, varm lagring, lagring av regler och larm. Vi håller för närvarande flyttar till Azure blob storage som rekommenderas av den referens för arkitekturen.  Azure Cosmos-DB är den rekommenderade allmänna varmt lagringslösningen för IoT-appar om lösningar, till exempel Azure tid serien insikter och Azure Data Lake är lämpliga för många användningsområden.

### <a name="business-integration"></a>Företagsintegration
Företagsintegration i lösningen Fjärrövervaknings är begränsad till generering av larm som är placerade i varmt lagring. Ytterligare kan integrering med business utföras genom att integrera lösningen med Azure Logikappar.

### <a name="user-interface"></a>Användargränssnittet
Webben UI har byggts med JavaScript reagera.  Reagerar ger ett användargränssnitt för vanliga branschen webbramverk och liknar andra populära ramverk, till exempel Angular.  

### <a name="runtime-and-orchestration"></a>Runtime och orchestration
Programkörning valt för undersystemet implementering i lösningen Fjärrövervaknings är Docker-behållare med Kubernetes som orchestrator för vågrät skala.  Den här arkitekturen kan för enskilda skala definition per undersystemet ådrar men DevOps kostnader i uppdatera virtuella datorer och behållare kontinuerligt ur säkerhetssynpunkt.  Alternativ till Docker och Kubernetes kan vara värd för mikrotjänster i PaaS-tjänster (till exempel Azure App Service) eller med hjälp av Service Fabric, DC/OS, Swarm, etc. som ett orchestrator.

## <a name="next-steps"></a>Nästa steg
* Distribuera lösningen Fjärrövervaknings [här](https://www.azureiotsolutions.com/).
* Utforska GitHub koden i [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) och [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Mer information om Referensarkitektur IoT [här](https://aka.ms/iotrefarchitecture).
