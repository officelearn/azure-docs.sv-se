---
title: Förstå IP-adressen för din IoT Hub | Microsoft Docs
description: Lär dig hur du frågar din IP-adress för IoT Hub och dess egenskaper. IP-adressen för din IoT Hub kan ändras under vissa scenarier som haveri beredskap eller regional redundans.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383903"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP-adresser

IP-adressprefix för IoT Hub publiceras regelbundet under *AzureIoTHub* [service tag](../virtual-network/service-tags-overview.md). För att säkerställa korrekt drift måste dina IoT-enheter ha utgående anslutning till att åtgärda prefix som anges under *AzureIoTHub* service tag. IoT-Programtjänsterna behöver dessutom ha utgående anslutning till de adressprefix som anges under *EventHub* service tag.


## <a name="best-practices"></a>Bästa praxis

* IP-adressprefix för IoT Hub kan komma att ändras. Dessa ändringar publiceras regelbundet via service tag innan de börjar att fungera. Det är därför viktigt att du utvecklar processer för att regelbundet hämta och använda de senaste service taggarna. Den här processen kan automatiseras via [API: et för identifiering av service märken](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Använd *AzureIoTHub. [ region namn]* -tagg för att identifiera IP-prefix som används av IoT Hub-slutpunkter i en speciell region. För att konto för haveri beredskap för data Center, eller [regional redundans](iot-hub-ha-dr.md) garanterar anslutning till IP-prefix för din IoT Hubs region för geo-par är också aktive rad.


## <a name="support-for-ipv6"></a>Stöd för IPv6 

IPv6 stöds för närvarande inte på IoT Hub.