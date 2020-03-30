---
title: Azure Device Provisioning Service-datakryptering i vila via kundhanterade nycklar| Microsoft-dokument
description: Kryptering av data i vila med kundhanterade nycklar för enhetsetableringstjänsten
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675148"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Kryptering av data i vila med kundhanterade nycklar för enhetsetableringstjänsten

## <a name="overview"></a>Översikt

DPS (Device Provisioning Service) stöder kryptering av data i vila med kundhanterade nycklar (CMK), även känd som Bring your own key (BYOK). DPS tillhandahåller kryptering av data i vila och under överföring. Som standard använder DPS Microsoft-hanterade nycklar för att kryptera data. Med CMK-support kan kunderna nu välja att kryptera data i vila med en nyckelkrypteringsnyckel, som hanteras av kunderna, med hjälp av [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Denna funktion kräver att en ny DPS skapas i en av följande regioner: Östra USA, Västra USA 2 eller Södra centrala USA. Om du vill prova den här funktionen kontaktar du oss via [Microsoft-support](https://azure.microsoft.com/support/create-ticket/). Dela ditt företagsnamn och prenumerations-ID när du kontaktar Microsoft-supporten.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om tjänsten för etablering av enhet](https://docs.microsoft.com/azure/iot-dps/)

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)