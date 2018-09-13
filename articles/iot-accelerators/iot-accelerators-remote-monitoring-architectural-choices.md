---
title: Remote Monitoring solution arkitektoniska val – Azure | Microsoft Docs
description: Den här artikeln beskriver fram arkitekturrelaterad eller teknisk valen som gjorts i fjärrövervakning
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: 709886e77819adca961a44f65fe6402dd7d20d53
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716307"
---
# <a name="remote-monitoring-architectural-choices"></a>Fjärråtkomst övervakning arkitektoniska val

Azure IoT Remote Monitoring solution accelerator är en öppen källkod, MIT-licens, lösningsaccelerator som introducerar vanliga IoT-scenarier som enhetsanslutning, hantering av enheter och stream bearbetning, så kunder kan utveckla sina snabbare processen.  Lösningen för fjärrövervakning följer rekommenderade Azure IoT-referensarkitekturen som publicerats [här](https://aka.ms/iotrefarchitecture).  

Den här artikeln beskriver fram arkitekturrelaterad eller teknisk valen som gjorts i varje delsystem för lösningen för fjärrövervakning och beskriver alternativ anses vara.  Det är viktigt att notera att teknisk valen som gjorts i lösningen för fjärrövervakning inte är det enda sättet att implementera en IoT lösning för fjärrövervakning.  Teknisk implementering är en baslinje för att skapa en lyckade tillämpning och bör ändras så att de passar den kunskaper och erfarenhet lodrät programbehov för lösningen kundimplementering.

## <a name="architectural-choices"></a>Val av arkitektur

### <a name="microservices-serverless-and-cloud-native"></a>Mikrotjänster, utan server, och internt i molnet

Arkitekturen som vi rekommenderar för IoT-program är molnet native, mikrotjänster, och utan server baserat.  Olika delsystem av ett IoT-program ska byggas som diskreta tjänster som är oberoende av varandra distribuerbar och som kan skalas oberoende av varandra.  Dessa attribut aktivera större skala, mer flexibilitet vid uppdatering av enskilda undersystem och ger flexibilitet att välja rätt teknik på basis av per undersystem.  Mikrotjänster kan implementeras med flera tekniker. Till exempel med behållarteknik, till exempel Docker med serverlös teknik som Azure Functions eller som är värd för mikrotjänster i PaaS-tjänster, till exempel Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Teknikval för Core undersystem

Det här avsnittet beskriver de tekniska alternativen som görs i lösningen för fjärrövervakning för varje kärna delsystem.

![Core-Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Molngatewayen
Azure IoT Hub används som molgatewayen fjärrövervakning lösning.  IoT Hub ger säker, dubbelriktad kommunikation med enheter. Du kan lära dig mer om IoT Hub [här](https://azure.microsoft.com/services/iot-hub/). För IoT-enhetsanslutning används .NET Core och Java IoT Hub SDK: er.  SDK: erna erbjuder omslutningar kring IoT Hub REST API och hanterar scenarier, t.ex nya försök.

### <a name="stream-processing"></a>Strömbearbetning
Stream använder bearbetning av lösningen för fjärrövervakning Azure Stream Analytics för Regelbearbetning av komplexa.  För kunder som vill ha enklare regler, har vi också en anpassad mikrotjänst med stöd för bearbetning av enkla regler, även om den här konfigurationen är inte en del av out of box-distributionen. Referensarkitekturen rekommenderar användning av Azure Functions för bearbetning med enkel regel och Azure Stream Analytics (ASA) för komplexa Regelbearbetning.  

### <a name="storage"></a>Storage
För lagring använder lösningsacceleratorn för fjärrövervakning både Azure Time Series Insights och Azure Cosmos DB. Azure Time Series Insights lagrar meddelanden som skickas via IoT Hub från dina anslutna enheter. Solution accelerator använder Azure Cosmos DB för alla andra lagringsenheter, till exempel kall lagring, definitioner av regler, larm och konfigurationsinställningar. Azure Cosmos DB är den rekommendera Allmänt varma storage-lösningen för IoT-program även om lösningar, till exempel Azure Time Series Insights och Azure Data Lake är lämpliga för många användningsområden. Med Azure Time Series Insights kan du få djupare insikter om dina time series-sensordata genom att upptäcka trender och avvikelser, där du kan utföra rotorsaksanalyser och undvika kostsamma avbrott. 

> [!NOTE]
> Azure Time Series Insights för lösningsacceleratorn för fjärrövervakning är för närvarande i förhandsversion och är endast tillgänglig i [Välj regioner](https://azure.microsoft.com/global-infrastructure/services/). Om du distribuerar lösningsacceleratorn för fjärrövervakning utanför dessa regioner, är Cosmos DB standardalternativet för lagring.

### <a name="business-integration"></a>Business-integrering
Business-integrering i lösningen för fjärrövervakning är begränsad till generering av larm som är placerade i varma lagring. Ytterligare kan integrering med business utföras genom att integrera lösningen med Azure Logic Apps.

### <a name="user-interface"></a>Användargränssnitt
Webb Användargränssnittet skapas med JavaScript reagera.  React erbjuder ett vanligt bransch web UI-ramverk och liknar andra populära ramverk som Angular.  

### <a name="runtime-and-orchestration"></a>Runtime och dirigering
Programkörning som valts för undersystem implementering i lösningen för fjärrövervakning är Docker-behållare med Kubernetes som orchestrator för horisontell skalning.  Den här arkitekturen gör för enskilda skala definition per undersystem men tillkommer kostnader för DevOps i att skydda virtuella datorer och behållare uppdaterade ur säkerhetssynpunkt.  Alternativ till Docker och Kubernetes är värd för mikrotjänster i PaaS-tjänster (till exempel Azure App Service) eller med hjälp av Service Fabric, DC/OS, Swarm osv. som en initierare.

## <a name="next-steps"></a>Nästa steg
* Distribuera lösningen för fjärrövervakning [här](https://www.azureiotsolutions.com/).
* Utforska GitHub-koden i [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) och [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Läs mer om IoT-Referensarkitektur [här](https://aka.ms/iotrefarchitecture).
