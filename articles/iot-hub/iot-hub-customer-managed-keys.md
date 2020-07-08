---
title: Azure IoT Hub data kryptering i vila via Kundhanterade nycklar | Microsoft Docs
description: Kryptering av data i vila med Kundhanterade nycklar för IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 83d2fa59654e038586a7f23eedbe7c656873f35c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976582"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Kryptering av data i vila med Kundhanterade nycklar för IoT Hub

IoT Hub stöder kryptering av data i vila med Kundhanterade nycklar (CMK), även kallat ta med din egen nyckel (BYOK). Azure IoT Hub tillhandahåller kryptering av data i vila och under överföring som de skrivs i våra data Center och dekrypterar det åt dig när du har åtkomst till den. Som standard använder IoT Hub Microsoft-hanterade nycklar för att kryptera data i vila. Med CMK kan du få ett annat lager av kryptering ovanpå standard kryptering och kan välja att kryptera data i vila med en nyckel krypterings nyckel som hanteras via din [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Detta ger dig flexibiliteten att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Om BYOK har kon figurer ATS för din IoT Hub ger vi också Double Encryption, som erbjuder ett andra skydds lager, samtidigt som du kan kontrol lera krypterings nyckeln via din Azure Key Vault.

Den här funktionen kräver att en ny IoT Hub skapas (nivån Basic eller standard). Kontakta oss via [Microsoft Support](https://azure.microsoft.com/support/create-ticket/)om du vill testa den här funktionen. Dela företagets namn och prenumerations-ID när du kontaktar Microsoft support.


## <a name="next-steps"></a>Nästa steg

* [Läs mer om IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
