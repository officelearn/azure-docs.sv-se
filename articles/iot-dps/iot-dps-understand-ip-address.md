---
title: Så här lyse IP-adressen för DPS-instansen (IoT Device Provisioning Service) | Microsoft-dokument
description: Förstå hur du frågar dps-adressen (IoT Device Provisioning Service) och dess egenskaper. IP-adressen för din DPS-instans kan ändras under vissa scenarier, till exempel haveriberedskap eller regional redundans.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284933"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT Hub DPS IP-adresser

IP-adressprefixen för de offentliga slutpunkterna för en DPS (IoT Hub Device Provisioning Service) publiceras regelbundet under _AzureIoTHub-tjänsttaggen_ [service tag](../virtual-network/service-tags-overview.md). Du kan använda dessa IP-adressprefix för att styra anslutningen mellan en IoT DPS-instans och enheter eller nätverkstillgångar för att implementera en mängd olika nätverksisoleringsmål:

| Mål | Metod |
|------|----------|
| Se till att dina enheter och tjänster endast kommunicerar med IoT Hub DPS-slutpunkter | Använd _AzureIoTHub-tjänsttaggen_ för att identifiera IoT Hub DPS-instanser. Konfigurera ALLOW-regler för enheternas och tjänsternas brandväggsinställning för dessa IP-adressprefix i enlighet med detta. Konfigurera regler för att släppa trafik till andra mål-IP-adresser som du inte vill att enheter eller tjänster ska kommunicera med. |
| Se till att din IoT Hub DPS-slutpunkt endast tar emot anslutningar från dina enheter och nätverkstillgångar | Använd IoT DPS [IP-filterfunktion](iot-dps-ip-filtering.md) för att skapa filterregler för enhets- och DPS-tjänst-API:er. Dessa filterregler kan endast användas för att tillåta anslutningar från dina enheter och IP-adresser för nätverkstillgångar (se avsnittet [begränsningar).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Bästa praxis

* När du lägger till ALLOW-regler i enhetens brandväggskonfiguration är det bäst att tillhandahålla specifika [portar som används av tillämpliga protokoll](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* IP-adressprefixen för IoT DPS-instanser kan komma att ändras. Dessa ändringar publiceras regelbundet via tjänsttaggar innan de börjar gälla. Det är därför viktigt att du utvecklar processer för att regelbundet hämta och använda de senaste servicetaggarna. Den här processen kan automatiseras via [API:et för identifiering av tjänsttaggar](../virtual-network/service-tags-overview.md#service-tags-on-premises). Api:et för identifiering av tjänsttaggar är fortfarande i förhandsversion och i vissa fall kanske det inte ger en fullständig lista över taggar och IP-adresser. Tills identifiering API är allmänt tillgänglig, överväga att använda [tjänsten taggar i nedladdningsbara JSON-format](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Använd *AzureIoTHub.[ regionnamn]* tagg för att identifiera IP-prefix som används av DPS-slutpunkter i en viss region. För att ta hänsyn till datacenterkatastrofåterställning eller [regional redundans,](../iot-hub/iot-hub-ha-dr.md)är det också aktiverat att anslutningen till IP-prefix för DPS-instansens geoparregion är aktiverad.

* Ställa in brandväggsregler för en DPS-instans kan blockera anslutningen som behövs för att köra Azure CLI- och PowerShell-kommandon mot den. För att undvika dessa anslutningsproblem kan du lägga till ALLOW-regler för klienternas IP-adressprefix för att återaktivera CLI- eller PowerShell-klienter för att kommunicera med din DPS-instans.  


## <a name="limitations-and-workarounds"></a>Begränsningar och lösningar

* DPS IP-filterfunktionen har en gräns på 100 regler. Den här gränsen och kan höjas via begäranden via Azure Customer Support. 

* Dina konfigurerade [IP-filtreringsregler](iot-dps-ip-filtering.md) tillämpas endast på dina DPS-slutpunkter och inte på de länkade IoT Hub-slutpunkterna. IP-filtrering för länkade IoT Hubs måste konfigureras separat. Mer information finns i [IoT Hub IP-filtreringsregler](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Stöd för IPv6 

IPv6 stöds för närvarande inte på IoT Hub eller DPS.

## <a name="next-steps"></a>Nästa steg

Mer information om IP-adresskonfigurationer med DPS finns i:

* [Konfigurera IP-filtrering](iot-dps-ip-filtering.md)
