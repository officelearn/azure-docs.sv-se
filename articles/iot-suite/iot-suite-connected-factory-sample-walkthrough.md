---
title: "Genomgång av ansluten fabrikslösning i Azure IoT Suite | Microsoft Docs"
description: "En beskrivning av den förkonfigurerade lösningen Ansluten fabrik i Azure IoT och dess arkitektur."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 3011fd608ba83561c319e57c8a7b5a4f3c4c2284
ms.contentlocale: sv-se
ms.lasthandoff: 05/26/2017


---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Genomgång av den förkonfigurerade lösningen Ansluten fabrik

IoT Suites [förkonfigurerade lösning][lnk-preconfigured-solutions] är en implementering av en branschlösning från slutpunkt till slutpunkt som:

* Ansluter till både simulerade industriella enheter som kör OPC UA-servrar i simulerade fabriksproduktionsrader och äkta OPC UA-serverenheter. Mer information om OPC UA finns i [Vanliga frågor och svar][lnk-faq].
* Visar KPI:er och OEE:er för drift för enheterna och produktionsraderna.
* Visar hur ett molnbaserat program kan användas för att interagera med OPC UA-serversystem.
* Gör att du kan ansluta dina egna OPC UA-serverenheter.
* Gör att du kan bläddra och ändra OPC UA-serverdata.
* Integreras med Azure-tjänsten Time Series Insights (TSI) för att tillhandahålla anpassade vyer av data från dina OPC UA-servrar.

Du kan använda lösningen som startpunkt för en egen implementering och [anpassa][lnk-customize] den efter dina egna affärsbehov.

Den här artikeln beskriver några av de viktigaste elementen i den anslutna fabrikslösningen så att du förstår hur den fungerar. Med den här kunskapen kan du sedan:

* Felsöka problem i lösningen.
* Planera hur lösningen kan anpassas för att uppfylla dina behov.
* Utforma en egen IoT-lösning som använder Azure-tjänster.

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i den förkonfigurerade lösningen:

![Logisk arkitektur för ansluten fabrik][connected-factory-logical]

## <a name="simulation"></a>Simulering

De simulerade stationerna och de simulerade tillverkningsverkställningssystemen (MES) bildar en fabriksproduktionsrad. De simulerade enheterna och OPC Publisher-modulen baseras på [OPC UA .NET-standarden][lnk-OPC-UA-NET-Standard] som ges ut av OPC Foundation.

OPC Proxy och OPC Publisher implementeras som moduler baserat på [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Varje simulerad produktionsrad har en särskild gateway ansluten.

Alla simuleringskomponenter körs i Docker-behållare som finns i en virtuell Azure Linux-dator. Simuleringen konfigureras för att köra åtta simulerade produktionsrader som standard.

## <a name="simulated-production-line"></a>Simulerad produktionsrad

En produktionsrad tillverkar delar. Den består av olika stationer: en sammansättningsstation, en teststation och en förpackningsstation.

Simuleringen körs och uppdaterar data som exponeras via OPC UA-noderna. Alla simulerade produktionsradstationer samordnas av MES via OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Simulerat körningssystem för tillverkning

MES övervakar varje station i produktionsraden via OPC UA för att identifiera ändringar i stationens status. Den anropar OPC UA-metoder för att kontrollera stationerna och överför en produkt från en station till en annan tills det är klart.

## <a name="gateway-opc-publisher-module"></a>Gateway OPC Publisher-modul

OPC Publisher-modulen ansluter till stationens OPC UA-servrar och prenumererar på OPC-noder som ska publiceras. Modulen omvandlar noddata till JSON-format, krypterar dem och skickar dem till IoT Hub som OPC UA pub-/sub-meddelanden.

OPC Publisher-modulen kräver endast en utgående https-port (443) och kan arbeta med företagets befintliga infrastruktur.

## <a name="gateway-opc-proxy-module"></a>Gateway OPC Proxy-modul

Gateway OPC UA Proxy-modulen fungerar som en tunnel för binära OPC UA-kommandon och kontrollerar meddelanden och kräver endast en utgående https-port (443). Den kan fungera med befintlig företagsinfrastruktur, inklusive webbproxyservrar.

Den använder IoT Hub-enhetsmetoder för att överföra paketerade TCP/IP-data på programnivån och garanterar på så sätt slutpunktsförtroende, datakryptering och integritet med SSL/TLS.

Det binära OPC UA-protokollet som är vidarebefordrande via själva proxyservern använder UA-autentisering och -kryptering.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Gateway OPC Publisher-modulen prenumererar på OPC UA-servernoder för att identifiera ändringar i datavärdena. Om en dataändring identifieras i någon av noderna kan modulen skicka meddelanden till Azure IoT Hub.

IoT Hub tillhandahåller en händelsekälla till Azure TSI. TSI lagrar data i 30 dagar baserat på tidsstämplar som är kopplade till meddelanden. Dessa data omfattar:

* OPC UA ApplicationUri
* OPC UA NodeId
* Nodens värde
* Tidsstämpel för källa
* OPC UA DisplayName

För närvarande tillåter inte TSI att kunder anpassar hur länge de vill behålla data.

TSI-frågor mot noddata med ett SearchSpan (Time.From, Time.To) och aggregeras av OPC UA ApplicationUri eller OPC UA NodeId eller OPC UA DisplayName.

Om du vill hämta data för OEE- och KPI-mätare och tidsseriediagram aggregeras data via antal händelser, Summa, Medel, Min och Max.

Tidsserier skapas med en annan process. OEE och KPI:er beräknas från stationsgrunddata och används för topologin (produktionsrader, fabriker, företag) i programmet.

Dessutom beräknas tidsserier för OEE- och KPI-topologi i appen när en visad tidsangivelse är klar. Dagsvyn uppdateras exempelvis varje hel timme.

Tidsserievyn för noddata kommer direkt från TSI med en sammansättning för tidsangivelse.

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-IoT Hub] tar emot data som skickas från OPC Publisher-modulen till molnet och gör dem tillgängliga för Azure TSI-tjänsten. 

IoT Hub ansvarar även för följande uppgifter i lösningen:
- Underhåller ett identitetsregister som lagrar ID för alla OPC Publisher-moduler och OPC Proxy-moduler.
- Det används som en transportkanal för dubbelriktad kommunikation för OPC Proxy-modulen.

## <a name="azure-storage"></a>Azure Storage
Lösningen använder Azure Blob Storage som disklagring för den virtuella datorn och för att lagra distribueringsdata.

## <a name="web-app"></a>Webbapp
Webbappen distribueras som en del av den förkonfigurerade lösningen består av en integrerad OPC UA-klient, aviseringsbehandling och telemetrivisualisering.

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill fortsätta och lära dig mer om IoT Suite:

* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
