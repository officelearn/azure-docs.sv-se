---
title: Genomgång av lösningen Ansluten fabrik – Azure | Microsoft Docs
description: En beskrivning av lösningsacceleratorn Ansluten fabrik i Azure IoT och dess arkitektur.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 950d248d2525f053981c8642ee2d39021b9a0494
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449930"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>Genomgång av lösningsacceleratorn Ansluten fabrik

[Lösningsacceleratorn][lnk-preconfigured-solutions] Ansluten fabrik är en implementering av en branschlösning från slutpunkt till slutpunkt som:

* Ansluter till både simulerade industriella enheter som kör OPC UA-servrar i simulerade fabriksproduktionsrader och äkta OPC UA-serverenheter. Mer information om OPC UA finns i [Vanliga frågor och svar om Ansluten fabrik](iot-accelerators-faq-cf.md).
* Visar KPI:er och OEE:er för drift för enheterna och produktionsraderna.
* Visar hur ett molnbaserat program kan användas för att interagera med OPC UA-serversystem.
* Gör att du kan ansluta dina egna OPC UA-serverenheter.
* Gör att du kan bläddra och ändra OPC UA-serverdata.
* Integreras med Azure-tjänsten Time Series Insights (TSI) för att tillhandahålla anpassade vyer av data från dina OPC UA-servrar.

Du kan använda lösningen som startpunkt för en egen implementering och [anpassa][lnk-customize] den efter dina egna affärsbehov.

Den här artikeln beskriver några av de viktigaste elementen i lösningen Ansluten fabrik, som gör det lättare att förstå hur den fungerar. Artikeln beskriver också hur data flödar genom lösningen. Med den här kunskapen kan du sedan:

* Felsöka problem i lösningen.
* Planera hur lösningen kan anpassas för att uppfylla dina behov.
* Utforma en egen IoT-lösning som använder Azure-tjänster.

Mer information finns i [Vanliga frågor och svar om Ansluten fabrik](iot-accelerators-faq-cf.md).

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i lösningsacceleratorn:

![Logisk arkitektur för Ansluten fabrik][connected-factory-logical]

## <a name="communication-patterns"></a>Kommunikationsmönster

Lösningen använder [OPC UA Pub/Sub-specifikationen](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) för att skicka OPC UA-telemetridata till IoT Hub i JSON-format. Lösningen använder [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge-modulen för detta ändamål.

Lösningen har också en OPC UA-klient som är integrerad i ett webbprogram som kan upprätta anslutningar med lokala OPC UA-servrar. Klienten använder en [omvänd proxy](https://wikipedia.org/wiki/Reverse_proxy) och får hjälp från IoT Hub med att upprätta anslutningen utan att kräva öppna portar i den lokala brandväggen. Det här kommunikationsmönstret kallas tjänstassisterad kommunikation. Lösningen använder [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge-modulen för detta ändamål.


## <a name="simulation"></a>Simulering

De simulerade stationerna och de simulerade tillverkningsverkställningssystemen (MES) bildar en fabriksproduktionsrad. De simulerade enheterna och OPC Publisher-modulen baseras på [OPC UA .NET-standarden][lnk-OPC-UA-NET-Standard] som ges ut av OPC Foundation.

OPC Proxy och OPC Publisher implementeras som moduler baserat på [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Varje simulerad produktionsrad har en gateway ansluten.

Alla simuleringskomponenter körs i Docker-containrar som finns i en virtuell Azure Linux-dator. Simuleringen konfigureras för att köra åtta simulerade produktionsrader som standard.

## <a name="simulated-production-line"></a>Simulerad produktionsrad

En produktionsrad tillverkar delar. Den består av olika stationer: en sammansättningsstation, en teststation och en förpackningsstation.

Simuleringen körs och uppdaterar de data som är tillgängliga via OPC UA-noder. Alla simulerade produktionsradstationer samordnas av MES via OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Simulerat körningssystem för tillverkning

MES övervakar varje station i produktionsraden via OPC UA för att identifiera ändringar i stationens status. Den anropar OPC UA-metoder för att kontrollera stationerna och överför en produkt från en station till nästa tills den är klar.

## <a name="gateway-opc-publisher-module"></a>Gateway OPC Publisher-modul

OPC Publisher-modulen ansluter till stationens OPC UA-servrar och prenumererar på OPC-noder som ska publiceras. Modulen:

1. Omvandlar noddata till JSON-format.
1. Krypterar JSON.
1. Skickar JSON till IoT Hub som OPC UA pub/sub-meddelanden.

OPC Publisher-modulen kräver endast en utgående https-port (443) och kan arbeta med företagets befintliga infrastruktur.

## <a name="gateway-opc-proxy-module"></a>Gateway OPC Proxy-modul

Gateway OPC UA Proxy-modulen fungerar som en tunnel för binära OPC UA-kommandon och kontrollerar meddelanden och kräver endast en utgående https-port (443). Den kan fungera med befintlig företagsinfrastruktur, inklusive webbproxyservrar.

IoT Hub-enhetsmetoder används för att överföra paketerade TCP/IP-data på programnivån så slutpunktsförtroende, datakryptering och integritet med SSL/TLS.

Det binära OPC UA-protokollet som är vidarebefordrande via själva proxyservern använder UA-autentisering och -kryptering.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Gateway OPC Publisher-modulen prenumererar på OPC UA-servernoder för att identifiera ändringar i datavärdena. Om en dataändring identifieras i någon av noderna kan modulen skicka meddelanden till Azure IoT Hub.

IoT Hub tillhandahåller en händelsekälla till Azure TSI. TSI lagrar data i 30 dagar baserat på tidsstämplar som är kopplade till meddelanden. Dessa data omfattar:

* OPC UA ApplicationUri
* OPC UA NodeId
* Nodens värde
* Tidsstämpel för källa
* OPC UA DisplayName

För närvarande att inte TSI kunder anpassar hur länge de vill behålla data för.

TSI-frågor mot noddata med ett tidsbaserade **SearchSpan** och aggregeras av **OPC UA ApplicationUri** eller **OPC UA NodeId** eller **OPC UA DisplayName**.

Om du vill hämta data för OEE och KPI-mätare och tidsseriediagrammen lösningen sammanställer data per antal händelser, **summan**, **genomsnittlig**, **Min**, och  **Max**.

Tidsserier skapas med en annan process. Lösningen beräknas OEE och KPI-värden från station grunddata och bubblar värdena för de produktionsrader, fabriker och enterprise.

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
Webbappen distribueras som en del av solution accelerator innehåller en integrerad OPC UA-klient, aviseringsbehandling och telemetrivisualisering.

## <a name="telemetry-data-flow"></a>Telemetridataflöde

![Telemetridataflöde](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Steg i flödet

1. OPC Publisher läser in OPC UA X 509-certifikat och autentiseringsuppgifter för IoT Hub från det lokala certifikatarkivet.
    - Om det behövs kommer OPC Publisher att skapa och lagra saknade certifikat och autentiseringsuppgifter i certifikatarkivet.

2. OPC Publisher registreras automatiskt i IoT Hub.
    - Använder det konfigurerade protokollet. Kan använda valfritt protokoll med stöd för IoT Hub Client SDK. Standardtypen är MQTT.
    - Protokollkommunikationen skyddas av TLS.

3. OPC Publisher läser in konfigurationsfilen.

4. OPC Publisher skapar en OPC-session för varje konfigurerad OPC UA-server.
    - Använder TCP-anslutning.
    - OPC Publisher och OPC UA-servern autentiserar varandra med hjälp av X509-certifikaten.
    - All annan OPC UA-trafik krypteras av mekanismen som konfigurerats för OPC UA-kryptering.
    - OPC Publisher skapar en OPC-prenumeration i OPC-sessionen för varje konfigurerat publiceringsintervall.
    - Skapar övervakade OPC-objekt för OPC-noderna som publiceras i OPC-prenumerationen.

5. Om värdet för en övervakad OPC-nod ändras skickar OPC UA-servern uppdateringar till OPC Publisher.

6. OPC Publisher omkodar det nya värdet.
    - Om batchbearbetning har aktiverats kan flera ändringar behandlas samtidigt.
    - Skapar ett IoT Hub-meddelande.

7. OPC Publisher skickar ett meddelande till IoT Hub.
    - Använder det konfigurerade protokollet.
    - Kommunikationen skyddas av TLS.

8. Time Series Insights (TSI) läser in meddelanden från IoT Hub.
    - Använder AMQP via TCP/TLS.
    - Det här steget utförs internt i datacentret.

9. Data blir vilande i TSD.

10. Den anslutna WebApp-fabriken i Azure App Service frågar efter de data som krävs i TSI.
    - Använder TCP/TLS för säker kommunikation.
    - Det här steget utförs internt i datacentret.

11. Webbläsaren ansluter till den anslutna WebApp-fabriken.
    - Visar instrumentpanelen för ansluten fabrik.
    - Ansluter via HTTPS.
    - Åtkomst till appen Ansluten fabrik kräver användarautentisering via Azure Active Directory.
    - Alla WebApi-anrop till appen Ansluten fabrik skyddas av antiförfalskningstokens.

12. När data uppdateras skickar den anslutna WebApp-fabriken uppdaterade data till webbläsaren.
    - Använder SignalR-protokollet.
    - Skyddas av TCP/TLS.

## <a name="browsing-data-flow"></a>Dataflöde för webbläsaren

![Dataflöde för webbläsaren](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Steg i flödet

1. OPC Proxy (serverkomponenten) startas.
    - Läser in delade åtkomstnycklar från ett lokalt arkiv.
    - Om det behövs lagras alla saknade åtkomstnycklar i arkivet.

2. OPC Proxy (serverkomponenten) registreras automatiskt i IoT Hub.
    - Läser in alla kända enheter från IoT Hub.
    - Använder MQTT via TLS socketanslutning eller säker websocket-anslutning.

3. Webbläsaren ansluter till den anslutna fabriken WebApp-ansluten fabrik instrumentpanelen visas.
    - Använder HTTPS.
    - Användaren väljer en OPC UA-server att ansluta till.

4. Den anslutna WebApp-fabriken upprättar en OPC UA-session till den valda OPC UA-servern.
    - Använder OPC UA-stacken.

5. OPC Proxy-transporten tar emot en begäran från OPC UA-stacken om att upprätta en TCP-socketanslutning till OPC UA-servern.
    - TCP-nyttolasten hämtas och används utan några ändringar.
    - Det här steget utförs internt i den anslutna WebApp-fabriken.

6. OPC Proxy (klientkomponenten) letar upp OPC Proxy-enheten (serverkomponenten) i IoT Hubs enhetsregister. Därefter anropas en enhetsmetod för OPC Proxy-enheten (serverkomponenten) i IoT Hub.
    - Använder HTTPS via TCP/TLS för att leta upp OPC Proxy.
    - Använder HTTPS via TCP/TLS för att upprätta en TCP-socketanslutning till OPC UA-servern.
    - Det här steget utförs internt i datacentret.

7. IoT Hub-anropar en enhetsmetod på OPC Proxy-enheten (serverkomponenten).
    - Använder en etablerad MQTT via TLS-socketanslutning eller säker Websocket-anslutning för att upprätta en TCP-socketanslutning till OPC UA-servern.

8. OPC Proxy (serverkomponenten) skickar TCP-nyttolasten vidare till nätverket på butiksgolvet.

9. OPC UA-servern bearbetar nyttolasten och skickar tillbaka svaret.

10. Svaret tas emot av socketen på OPC Proxy (serverkomponenten).
    - OPC Proxy skickar data som returvärde för enhetsmetoden till IoT Hub och OPC Proxy (klientkomponenten).
    - Dessa data levereras till OPC UA-stacken i appen Ansluten fabrik.

11. Den anslutna WebApp-fabriken returnerar OPC Browser UX med OPC UA-specifik information som togs emot från OPC UA-servern och som renderas i webbläsaren.
    - Även om en användare bläddrar igenom OPC-adressutrymmet och tillämpar funktioner för noder i OPC-adressutrymmet, använder klienten OPC Browser UX AJAX-anrop via HTTPS som skyddas av Antiförfalskningstoken för att hämta data från den anslutna fabriken WebApp.
    - Vid behov använder klienten det kommunikationssätt som beskrivs i steg 4 till 10 för att få till ett informationsutbyte med OPC UA-servern.

> [!NOTE]
> OPC Proxy (serverkomponenten) och OPC Proxy-komponenten (klienten) utför steg 4 till 10 för all TCP-trafik som kan kopplas ihop med OPC UA-kommunikationen.

> [!NOTE]
> OPC Proxy-kommunikationen är transparent och alla OPC UA-säkerhetsfunktioner för autentisering och kryptering gäller för OPC UA-servern och OPC UA-stacken i den anslutna WebApp-fabriken.

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill fortsätta och lära dig mer om lösningsacceleratorer i IoT:

* [Behörigheter på webbplatsen azureiotsolutions.com][lnk-permissions]
* [Distribuera en gateway på Windows eller Linux för lösningsacceleratorn Ansluten fabrik](iot-accelerators-connected-factory-gateway-deployment.md)
* [Referensimplementering för OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md).

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
