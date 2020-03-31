---
title: Så här fungerar IP-adressen för din IoT-hubb | Microsoft-dokument
description: Förstå hur du frågar din IoT-hubb-IP-adress och dess egenskaper. IP-adressen för din IoT-hubb kan ändras under vissa scenarier, till exempel haveriberedskap eller regional redundans.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367575"
---
# <a name="iot-hub-ip-addresses"></a>IP-adresser för IoT Hub

IP-adressprefixen för offentliga IoT Hub-slutpunkter publiceras regelbundet under _AzureIoTHub-tjänsttaggen_ [service tag](../virtual-network/service-tags-overview.md).

> [!NOTE]
> För enheter som distribueras i lokala nätverk stöder Azure IoT Hub VNET-anslutningsintegrering med privata slutpunkter. Mer information finns i [IoT Hub-stöd för VNET:s.](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)


Du kan använda dessa IP-adressprefix för att styra anslutningen mellan IoT Hub och dina enheter eller nätverkstillgångar för att implementera en mängd olika nätverksisoleringsmål:

| Mål | Tillämpliga scenarier | Metod |
|------|-----------|----------|
| Se till att dina enheter och tjänster endast kommunicerar med IoT Hub-slutpunkter | [Enhet-till-moln-](./iot-hub-devguide-messaging.md)och [moln-till-enhet-meddelanden,](./iot-hub-devguide-messages-c2d.md) [direkta metoder,](./iot-hub-devguide-direct-methods.md) [enhets- och modultvillingar](./iot-hub-devguide-device-twins.md) och [enhetsströmmar](./iot-hub-device-streams-overview.md) | Använd _AzureIoTHub-_ och _EventHub-tjänsttaggar_ för att identifiera IP-adressprefix för IoT-hubben och IP-adressprefix för eventhubben och konfigurera ALLOW-regler för enheternas brandväggsinställning för dessa IP-adressprefix i enlighet med detta. släpp trafik till andra mål-IP-adresser som du inte vill att enheterna eller tjänsterna ska kommunicera med. |
| Se till att slutpunkten för IoT Hub-enheten endast tar emot anslutningar från dina enheter och nätverkstillgångar | [Enhet-till-moln-](./iot-hub-devguide-messaging.md)och [moln-till-enhet-meddelanden,](./iot-hub-devguide-messages-c2d.md) [direkta metoder,](./iot-hub-devguide-direct-methods.md) [enhets- och modultvillingar](./iot-hub-devguide-device-twins.md) och [enhetsströmmar](./iot-hub-device-streams-overview.md) | Använd IoT Hub [IP-filterfunktionen](iot-hub-ip-filtering.md) för att tillåta anslutningar från dina enheter och IP-adresser för nätverkstillgångar (se avsnittet [begränsningar).](#limitations-and-workarounds) | 
| Se till att dina rutters anpassade slutpunktsresurser (lagringskonton, servicebuss och händelsehubbar) endast kan nås från dina nätverksresurser | [Meddelanderoutning](./iot-hub-devguide-messages-d2c.md) | Följ resursens anvisningar om hur du begränsar anslutningen (till exempel via [brandväggsregler,](../storage/common/storage-network-security.md) [privata länkar](../private-link/private-endpoint-overview.md)eller [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md)). använda _AzureIoTHub-tjänsttaggar_ för att identifiera IP-adressprefix för IoT Hub och lägga till ALLOW-regler för dessa IP-prefix på resursens brandväggskonfiguration (se avsnittet [begränsningar).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Bästa praxis

* När du lägger till ALLOW-regler i enhetens brandväggskonfiguration är det bäst att tillhandahålla specifika [portar som används av tillämpliga protokoll](./iot-hub-devguide-protocols.md#port-numbers).

* IP-adressprefixen för IoT-hubb kan komma att ändras. Dessa ändringar publiceras regelbundet via tjänsttaggar innan de börjar gälla. Det är därför viktigt att du utvecklar processer för att regelbundet hämta och använda de senaste servicetaggarna. Den här processen kan automatiseras via [API:et för identifiering av tjänsttaggar](../virtual-network/service-tags-overview.md#service-tags-on-premises). Observera att IDENTIFIERINGS-API för identifiering av tjänsttaggar fortfarande är i förhandsversion och i vissa fall kanske inte ger en fullständig lista över taggar och IP-adresser. Tills identifiering API är allmänt tillgänglig, överväga att använda [tjänsten taggar i nedladdningsbara JSON-format](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Använd *AzureIoTHub.[ regionnamn]* tagg för att identifiera IP-prefix som används av IoT-hubbslutpunkter i en viss region. För att ta hänsyn till datacenterkatastrofåterställning eller [regional redundans](iot-hub-ha-dr.md) säkerställer du att anslutningen till IP-prefix för IoT Hub:s geoparregion också är aktiverad.

* Om du konfigurerar brandväggsregler i IoT Hub kan anslutningen blockeras som behövs för att köra Azure CLI- och PowerShell-kommandon mot din IoT Hub. För att undvika detta kan du lägga till ALLOW-regler för klienternas IP-adressprefix för att återaktivera CLI- eller PowerShell-klienter för att kommunicera med din IoT Hub.  


## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

* IoT Hub IP-filterfunktionen har en gräns på 10 regler. Den här gränsen och kan höjas via begäranden via Azure Customer Support. 

* Dina konfigurerade [IP-filtreringsregler](iot-hub-ip-filtering.md) tillämpas endast på IP-slutpunkterna för IoT Hub och inte på IoT-hubbens inbyggda eventhubbslutpunkt. Om du också kräver att IP-filtrering tillämpas på händelsehubben där dina meddelanden lagras kan du göra det genom att ta med din egen Event Hub-resurs där du kan konfigurera önskade IP-filtreringsregler direkt. För att göra det måste du etablera din egen Event Hub-resurs och konfigurera [meddelanderoutning](./iot-hub-devguide-messages-d2c.md) för att skicka dina meddelanden till den resursen i stället för IoT Hubs inbyggda eventhubb. Slutligen, som beskrivs i tabellen ovan, för att aktivera funktionen för meddelanderoutning måste du också tillåta anslutning från IoT Hub IP-adressprefix till din etablerade Event Hub-resurs.

* När du dirigerar till ett lagringskonto är det bara möjligt att tillåta trafik från IoT Hub IP-adressprefix när lagringskontot finns i en annan region som din IoT Hub.

## <a name="support-for-ipv6"></a>Stöd för IPv6 

IPv6 stöds för närvarande inte på IoT Hub.
