---
title: Data kryptering i Azure Device Provisioning-tjänsten i vila via Kundhanterade nycklar | Microsoft Docs
description: Kryptering av data i vila med Kundhanterade nycklar för enhets etablerings tjänsten
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77675148"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Kryptering av data i vila med Kundhanterade nycklar för enhets etablerings tjänsten

## <a name="overview"></a>Översikt

Enhets etablerings tjänsten (DPS) stöder kryptering av data i vila med Kundhanterade nycklar (CMK), även kallat ta med din egen nyckel (BYOK). DPS tillhandahåller kryptering av data i vila och under överföring. Som standard använder DPS Microsoft-hanterade nycklar för att kryptera data. Med CMK support har kunderna nu möjlighet att kryptera data i vila med en nyckel krypterings nyckel som hanteras av kunderna, med hjälp av [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Den här funktionen kräver att en ny DPS skapas i någon av följande regioner: östra USA, västra USA 2 eller södra centrala USA. Kontakta oss via [Microsoft Support](https://azure.microsoft.com/support/create-ticket/)om du vill testa den här funktionen. Dela företagets namn och prenumerations-ID när du kontaktar Microsoft support.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om enhets etablerings tjänsten](https://docs.microsoft.com/azure/iot-dps/)

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)