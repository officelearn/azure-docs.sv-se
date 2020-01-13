---
title: Azure IoT Hub data kryptering i vila via Kundhanterade nycklar | Microsoft Docs
description: Kryptering av data i vila med Kundhanterade nycklar för IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 1bb55d593878026bb3e57014a317b4fc0158d734
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913139"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Kryptering av data i vila med Kundhanterade nycklar för IoT Hub

IoT Hub stöder kryptering av data i vila med Kundhanterade nycklar (CMK), som även kallas för att ta med din egen nyckel (BYOK), stöd för Azure IoT Hub. Azure IoT Hub tillhandahåller kryptering av data i vila och under överföring. Som standard använder IoT Hub Microsoft-hanterade nycklar för att kryptera data. Med CMK support har kunderna nu möjlighet att kryptera data i vila med en nyckel krypterings nyckel som hanteras av kunderna, med hjälp av [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Den här funktionen kräver att en ny IoT Hub (nivån Basic eller standard) skapas i någon av följande regioner: östra USA, västra USA 2 eller södra centrala USA. Kontakta oss via [Microsoft Support](https://azure.microsoft.com/support/create-ticket/)om du vill testa den här funktionen. Dela företagets namn och prenumerations-ID när du kontaktar Microsoft support.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)