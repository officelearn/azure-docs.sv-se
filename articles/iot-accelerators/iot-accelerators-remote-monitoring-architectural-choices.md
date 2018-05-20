---
title: Fjärråtkomst övervakning lösning arkitektur val - Azure | Microsoft Docs
description: Den här artikeln beskriver arkitekturen och tekniska valen som gjorts i Fjärrövervaknings
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 607b8aeb2f986eebddf8fe13b88e7f3bc7b4494b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="remote-monitoring-architectural-choices"></a>Fjärråtkomst övervakning arkitektur alternativ

Azure IoT fjärråtkomst övervakning (RM) är en öppen källkod, MIT-licens, solution accelerator som beskriver vanliga scenarier för IoT som enhetsanslutning, hantering och bearbetning av dataströmmen så kunder kan påskynda sina utvecklingsprocessen.  RM följer rekommenderade Azure IoT-Referensarkitektur publicerade [här](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

Den här artikeln beskriver arkitekturen och tekniska valen som gjorts i varje delsystem för RM och beskrivs alternativ anses vara.  Det är viktigt att notera att tekniska valen som gjorts i RM inte är det enda sättet att implementera en fjärransluten övervakning IoT-lösning.  Teknisk implementering är en baslinje för att skapa en lyckade tillämpning och bör ändras så att de passar kunskaper, erfarenhet och lodräta programbehov för en kund-lösningsimplementering.

## <a name="architectural-choices"></a>Alternativ arkitektur

### <a name="microservices-serverless-and-cloud-native"></a>Mikrotjänster, serverlösa, och molntjänster interna

Arkitekturen rekommenderar vi för IoT-appar är moln ursprunglig mikrotjänster, och serverlösa baserade.  Olika delsystem för en IoT-program ska byggas som diskreta tjänster som är oberoende av varandra distribueras och kunna skalas oberoende av varandra.  Dessa attribut aktivera större skala, större flexibilitet vid uppdatering av enskilda delsystem och ger flexibilitet för att välja lämplig teknik på grundval av per undersystemet.  Mikrotjänster kan implementeras med flera tekniker. Till exempel använder teknik för behållare, till exempel Docker för serverlösa teknik som Azure Functions eller värd för mikrotjänster i PaaS-tjänster, till exempel Azure App Service.

## <a name="core-subsystem-technology-choices"></a>Core undersystemet tekniska alternativ

Det här avsnittet innehåller information technology valen som gjorts i RM för varje delsystem core.

![Core Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Gateway för moln
Azure IoT Hub används som Gateway för RM-moln.  IoT-hubben erbjuder säker, dubbelriktad kommunikation med enheter. Du kan lära dig mer om IoT-hubb [här](https://azure.microsoft.com/services/iot-hub/). För IoT enhetsanslutning används .NET Core och Java IoT-hubb SDK: er.  SDK: erna erbjuder omslutningar runt IoT Hub REST-API och hantera scenarier, till exempel försök igen, 

### <a name="stream-processing"></a>Strömbearbetning
Använder Azure Stream Analytics för bearbetning av komplexa regeln för RM för bearbetning av dataströmmen.  För kunder som vill ha enklare regler, ha vi också en anpassad mikrotjänster med stöd för bearbetning av enkla regler, även om denna inställning inte en del av out-of-box-distributionen. Den referens för arkitekturen rekommenderar användning av Azure Functions för enkel regel bearbetning och Azure Stream Analytics (ASA) för bearbetning av komplexa regeln.  

### <a name="storage"></a>Storage
För lagring, Cosmos DB används för alla lagringsbehov: kyla, varm lagring, lagring av regler och larm. Vi håller för närvarande flyttar till Azure blob storage som rekommenderas av den referens för arkitekturen.  Cosmos DB är den rekommenderade allmänna varmt lagringslösningen för IoT-appar om lösningar, till exempel Azure tid serien insikter och Azure Data Lake är lämpliga för många användningsområden.

### <a name="business-integration"></a>Företagsintegration
Företagsintegration i RM är begränsad till generering av larm som är placerade i varmt lagring. Ytterligare kan integrering med business utföras genom att integrera lösningen med Azure Logikappar.

### <a name="user-interface"></a>Användargränssnittet
Webben UI har byggts med JavaScript reagera.  Reagerar ger ett användargränssnitt för vanliga branschen webbramverk och liknar andra populära ramverk, till exempel Angular.  

### <a name="runtime-and-orchestration"></a>Runtime och orchestration
Programkörning valt för undersystemet implementering RM är Docker-behållare med Kubernetes (K8s) som orchestrator för vågrät skala.  Den här arkitekturen kan för enskilda skala definition per undersystemet ådrar men DevOps kostnader i uppdatera virtuella datorer och behållare kontinuerligt ur säkerhetssynpunkt.  Alternativen för Docker & K8s är värd för mikrotjänster i PaaS-tjänster (till exempel Azure App Service) eller med hjälp av Service Fabric, DC/OS, Swarm, etc. som ett orchestrator.

## <a name="next-steps"></a>Nästa steg
* Distribuera lösningen RM [här](https://www.azureiotsuite.com/).
* Utforska GitHub koden i [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) och [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Mer information om Referensarkitektur IoT [här](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
