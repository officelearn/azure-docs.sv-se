---
title: Azure IoT Hub-datakryptering i vila via kundhanterade nycklar| Microsoft-dokument
description: Kryptering av data i vila med kundhanterade nycklar för IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370584"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Kryptering av data i vila med kundhanterade nycklar för IoT Hub

IoT Hub stöder kryptering av data i vila med kundhanterade nycklar (CMK), även känd som Bring your own key (BYOK), support för Azure IoT Hub. Azure IoT Hub tillhandahåller kryptering av data i vila och under överföring. Som standard använder IoT Hub Microsoft-hanterade nycklar för att kryptera data. Med CMK-support kan kunderna nu välja att kryptera data i vila med en nyckelkrypteringsnyckel, som hanteras av kunderna, med hjälp av [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Den här funktionen kräver att du skapar en ny IoT Hub (grundläggande nivå eller standardnivå), i någon av följande regioner: Östra USA, Västra USA 2, Södra centrala USA eller US Gov. Om du vill prova den här funktionen kontaktar du oss via [Microsoft-support](https://azure.microsoft.com/support/create-ticket/). Dela ditt företagsnamn och prenumerations-ID när du kontaktar Microsoft-supporten.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
